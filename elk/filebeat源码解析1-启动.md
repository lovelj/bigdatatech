## 1.filebeat是什么

根据官网介绍（https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-overview.html），Filebeat 是一种轻量型的用于转发和汇总日志与文件的工具。通过配置监控文件，filebeat记录并保持每个监控文件的状态，收集监控文件的变化，发送到Elasticsearch或者logstash索引。

## 2.工作原理

参考https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-overview.html和https://www.elastic.co/guide/en/beats/filebeat/current/how-filebeat-works.html 官网介绍了filebeat的工作原理。简单来说：对于配置文件（filebeat.yml）中prospectors配置的每个日志文件，Filebeat启动harvester。每个harvester读取一个日志文件的内容，日志数据发送到spooler（后台处理程序），spooler汇集的事件和聚合数据发送到配置的输出目标（例如：es或者logstash等）。

## 3.启动过程

了解filebeat是什么和怎么工作的以后，我们来看filebeat的启动过程。

filebeat的main.go

```
package main
import (
	"os"
	"github.com/elastic/beats/filebeat/cmd"
)
func main() {
	if err := cmd.RootCmd.Execute(); err != nil {
		os.Exit(1)
	}
}
```

进入到filebeat/cmd执行

```
package cmd

import (
	"flag"

	"github.com/spf13/pflag"

	"github.com/elastic/beats/filebeat/beater"

	cmd "github.com/elastic/beats/libbeat/cmd"
)

// Name of this beat
var Name = "filebeat"

// RootCmd to handle beats cli
var RootCmd *cmd.BeatsRootCmd

func init() {
	var runFlags = pflag.NewFlagSet(Name, pflag.ExitOnError)
	runFlags.AddGoFlag(flag.CommandLine.Lookup("once"))
	runFlags.AddGoFlag(flag.CommandLine.Lookup("modules"))

	RootCmd = cmd.GenRootCmdWithRunFlags(Name, "", beater.New, runFlags)
	RootCmd.PersistentFlags().AddGoFlag(flag.CommandLine.Lookup("M"))
	RootCmd.TestCmd.Flags().AddGoFlag(flag.CommandLine.Lookup("modules"))
	RootCmd.SetupCmd.Flags().AddGoFlag(flag.CommandLine.Lookup("modules"))
	RootCmd.AddCommand(cmd.GenModulesCmd(Name, "", buildModulesManager))
}
```

RootCmd 在这一句初始化RootCmd = cmd.GenRootCmdWithRunFlags(Name, "", beater.New, runFlags)

beater.New跟进去看到是filebeat.go func New(b *beat.Beat, rawConfig *common.Config) (beat.Beater, error) {...}

现在进入GenRootCmdWithRunFlags方法，一路跟进去到GenRootCmdWithSettings，真正的初始化是在这个方法里面。

忽略前面的一段初始化值方法，看到RunCmd的初始化在：

```
rootCmd.RunCmd = genRunCmd(settings, beatCreator, runFlags)
```

进入getRunCmd，看到执行代码

```
err := instance.Run(settings, beatCreator)
```

跟到\elastic\beats\libbeat\cmd\instance\beat.go的Run方法

```
b, err := NewBeat(name, idxPrefix, version)
```

这里新建了beat

在方法末尾

```
return b.launch(settings, bt)
```

调用了启动方法

进入launch，在经过了初始化配置之后

```
err := b.InitWithSettings(settings)
```

在launch的末尾

```
return beater.Run(&b.Beat)
```

beat开始启动

因为启动的是filebeat，我们到filebeat.go的Run方法

```
func (fb *Filebeat) Run(b *beat.Beat) error {
       var err error
       config := fb.config

       if !fb.moduleRegistry.Empty() {
              err = fb.loadModulesPipelines(b)
              if err != nil {
                     return err
              }
       }

       waitFinished := newSignalWait()
       waitEvents := newSignalWait()

       // count active events for waiting on shutdown
       wgEvents := &eventCounter{
              count: monitoring.NewInt(nil, "filebeat.events.active"),
              added: monitoring.NewUint(nil, "filebeat.events.added"),
              done:  monitoring.NewUint(nil, "filebeat.events.done"),
       }
       finishedLogger := newFinishedLogger(wgEvents)

       // Setup registrar to persist state
       registrar, err := registrar.New(config.RegistryFile, config.RegistryFilePermissions, config.RegistryFlush, finishedLogger)
       if err != nil {
              logp.Err("Could not init registrar: %v", err)
              return err
       }

       // Make sure all events that were published in
       registrarChannel := newRegistrarLogger(registrar)

       err = b.Publisher.SetACKHandler(beat.PipelineACKHandler{
              ACKEvents: newEventACKer(finishedLogger, registrarChannel).ackEvents,
       })
       if err != nil {
              logp.Err("Failed to install the registry with the publisher pipeline: %v", err)
              return err
       }

       outDone := make(chan struct{}) // outDone closes down all active pipeline connections
       crawler, err := crawler.New(
              channel.NewOutletFactory(outDone, wgEvents).Create,
              config.Inputs,
              b.Info.Version,
              fb.done,
              *once)
       if err != nil {
              logp.Err("Could not init crawler: %v", err)
              return err
       }

       // The order of starting and stopping is important. Stopping is inverted to the starting order.
       // The current order is: registrar, publisher, spooler, crawler
       // That means, crawler is stopped first.

       // Start the registrar
       err = registrar.Start()
       if err != nil {
              return fmt.Errorf("Could not start registrar: %v", err)
       }

       // Stopping registrar will write last state
       defer registrar.Stop()

       // Stopping publisher (might potentially drop items)
       defer func() {
              // Closes first the registrar logger to make sure not more events arrive at the registrar
              // registrarChannel must be closed first to potentially unblock (pretty unlikely) the publisher
              registrarChannel.Close()
              close(outDone) // finally close all active connections to publisher pipeline
       }()

       // Wait for all events to be processed or timeout
       defer waitEvents.Wait()

       // Create a ES connection factory for dynamic modules pipeline loading
       var pipelineLoaderFactory fileset.PipelineLoaderFactory
       if b.Config.Output.Name() == "elasticsearch" {
              pipelineLoaderFactory = newPipelineLoaderFactory(b.Config.Output.Config())
       } else {
              logp.Warn(pipelinesWarning)
       }

       if config.OverwritePipelines {
              logp.Debug("modules", "Existing Ingest pipelines will be updated")
       }

       err = crawler.Start(b.Publisher, registrar, config.ConfigInput, config.ConfigModules, pipelineLoaderFactory, config.OverwritePipelines)
       if err != nil {
              crawler.Stop()
              return err
       }

       // If run once, add crawler completion check as alternative to done signal
       if *once {
              runOnce := func() {
                     logp.Info("Running filebeat once. Waiting for completion ...")
                     crawler.WaitForCompletion()
                     logp.Info("All data collection completed. Shutting down.")
              }
              waitFinished.Add(runOnce)
       }

       // Register reloadable list of inputs and modules
       inputs := cfgfile.NewRunnerList(management.DebugK, crawler.InputsFactory, b.Publisher)
       reload.Register.MustRegisterList("filebeat.inputs", inputs)

       modules := cfgfile.NewRunnerList(management.DebugK, crawler.ModulesFactory, b.Publisher)
       reload.Register.MustRegisterList("filebeat.modules", modules)

       var adiscover *autodiscover.Autodiscover
       if fb.config.Autodiscover != nil {
              adapter := fbautodiscover.NewAutodiscoverAdapter(crawler.InputsFactory, crawler.ModulesFactory)
              adiscover, err = autodiscover.NewAutodiscover("filebeat", b.Publisher, adapter, config.Autodiscover)
              if err != nil {
                     return err
              }
       }
       adiscover.Start()

       // Add done channel to wait for shutdown signal
       waitFinished.AddChan(fb.done)
       waitFinished.Wait()

       // Stop reloadable lists, autodiscover -> Stop crawler -> stop inputs -> stop harvesters
       // Note: waiting for crawlers to stop here in order to install wgEvents.Wait
       //       after all events have been enqueued for publishing. Otherwise wgEvents.Wait
       //       or publisher might panic due to concurrent updates.
       inputs.Stop()
       modules.Stop()
       adiscover.Stop()
       crawler.Stop()

       timeout := fb.config.ShutdownTimeout
       // Checks if on shutdown it should wait for all events to be published
       waitPublished := fb.config.ShutdownTimeout > 0 || *once
       if waitPublished {
              // Wait for registrar to finish writing registry
              waitEvents.Add(withLog(wgEvents.Wait,
                     "Continue shutdown: All enqueued events being published."))
              // Wait for either timeout or all events having been ACKed by outputs.
              if fb.config.ShutdownTimeout > 0 {
                     logp.Info("Shutdown output timer started. Waiting for max %v.", timeout)
                     waitEvents.Add(withLog(waitDuration(timeout),
                            "Continue shutdown: Time out waiting for events being published."))
              } else {
                     waitEvents.AddChan(fb.done)
              }
       }

       return nil
}
```

构造了registrar和crawler，用于监控文件状态变更和数据采集。然后

```
err = crawler.Start(b.Publisher, registrar, config.ConfigInput, config.ConfigModules, pipelineLoaderFactory, config.OverwritePipelines)
```

crawler开始启动采集数据

```
for _, inputConfig := range c.inputConfigs {
       err := c.startInput(pipeline, inputConfig, r.GetStates())
       if err != nil {
              return err
       }
}
```

crawler的Start方法里面根据每个配置的输入调用一次startInput

```
func (c *Crawler) startInput(
       pipeline beat.Pipeline,
       config *common.Config,
       states []file.State,
) error {
       if !config.Enabled() {
              return nil
       }

       connector := channel.ConnectTo(pipeline, c.out)
       p, err := input.New(config, connector, c.beatDone, states, nil)
       if err != nil {
              return fmt.Errorf("Error in initing input: %s", err)
       }
       p.Once = c.once

       if _, ok := c.inputs[p.ID]; ok {
              return fmt.Errorf("Input with same ID already exists: %d", p.ID)
       }

       c.inputs[p.ID] = p

       p.Start()

       return nil
}
```

根据配置的input，构造log/input

```
func (p *Input) Run() {
       logp.Debug("input", "Start next scan")

       // TailFiles is like ignore_older = 1ns and only on startup
       if p.config.TailFiles {
              ignoreOlder := p.config.IgnoreOlder

              // Overwrite ignore_older for the first scan
              p.config.IgnoreOlder = 1
              defer func() {
                     // Reset ignore_older after first run
                     p.config.IgnoreOlder = ignoreOlder
                     // Disable tail_files after the first run
                     p.config.TailFiles = false
              }()
       }
       p.scan()

       // It is important that a first scan is run before cleanup to make sure all new states are read first
       if p.config.CleanInactive > 0 || p.config.CleanRemoved {
              beforeCount := p.states.Count()
              cleanedStates, pendingClean := p.states.Cleanup()
              logp.Debug("input", "input states cleaned up. Before: %d, After: %d, Pending: %d",
                     beforeCount, beforeCount-cleanedStates, pendingClean)
       }

       // Marking removed files to be cleaned up. Cleanup happens after next scan to make sure all states are updated first
       if p.config.CleanRemoved {
              for _, state := range p.states.GetStates() {
                     // os.Stat will return an error in case the file does not exist
                     stat, err := os.Stat(state.Source)
                     if err != nil {
                            if os.IsNotExist(err) {
                                   p.removeState(state)
                                   logp.Debug("input", "Remove state for file as file removed: %s", state.Source)
                            } else {
                                   logp.Err("input state for %s was not removed: %s", state.Source, err)
                            }
                     } else {
                            // Check if existing source on disk and state are the same. Remove if not the case.
                            newState := file.NewState(stat, state.Source, p.config.Type, p.meta)
                            if !newState.FileStateOS.IsSame(state.FileStateOS) {
                                   p.removeState(state)
                                   logp.Debug("input", "Remove state for file as file removed or renamed: %s", state.Source)
                            }
                     }
              }
       }
}
```

input开始根据配置的输入路径扫描所有符合的文件，并启动harvester

```
func (p *Input) scan() {
       var sortInfos []FileSortInfo
       var files []string

       paths := p.getFiles()

       var err error

       if p.config.ScanSort != "" {
              sortInfos, err = getSortedFiles(p.config.ScanOrder, p.config.ScanSort, getSortInfos(paths))
              if err != nil {
                     logp.Err("Failed to sort files during scan due to error %s", err)
              }
       }

       if sortInfos == nil {
              files = getKeys(paths)
       }

       for i := 0; i < len(paths); i++ {

              var path string
              var info os.FileInfo

              if sortInfos == nil {
                     path = files[i]
                     info = paths[path]
              } else {
                     path = sortInfos[i].path
                     info = sortInfos[i].info
              }

              select {
              case <-p.done:
                     logp.Info("Scan aborted because input stopped.")
                     return
              default:
              }

              newState, err := getFileState(path, info, p)
              if err != nil {
                     logp.Err("Skipping file %s due to error %s", path, err)
              }

              // Load last state
              lastState := p.states.FindPrevious(newState)

              // Ignores all files which fall under ignore_older
              if p.isIgnoreOlder(newState) {
                     err := p.handleIgnoreOlder(lastState, newState)
                     if err != nil {
                            logp.Err("Updating ignore_older state error: %s", err)
                     }
                     continue
              }

              // Decides if previous state exists
              if lastState.IsEmpty() {
                     logp.Debug("input", "Start harvester for new file: %s", newState.Source)
                     err := p.startHarvester(newState, 0)
                     if err == errHarvesterLimit {
                            logp.Debug("input", harvesterErrMsg, newState.Source, err)
                            continue
                     }
                     if err != nil {
                            logp.Err(harvesterErrMsg, newState.Source, err)
                     }
              } else {
                     p.harvestExistingFile(newState, lastState)
              }
       }
}
```

在harvest的Run看到一个死循环读取message，预处理之后交由forwarder发送到目标输出

```
message, err := h.reader.Next()
```

```
h.sendEvent(data, forwarder) 
```