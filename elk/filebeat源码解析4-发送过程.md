在本系列第一篇(filebeat源码解析1-启动)最后提到harvest.send方法发送了数据到output，继续跟进去，到err := forwarder.Send(data)

```
func (f *Forwarder) Send(data *util.Data) error {
       ok := f.Outlet.OnEvent(data)
       if !ok {
              logp.Info("Input outlet closed")
              return errors.New("input outlet closed")
       }

       return nil
}
```

调用Outlet.OnEvent发送data

点进去发现是一个接口

```
type Outlet interface {
       OnEvent(data *util.Data) bool
}
```

经过调试观察，elastic\beats\filebeat\channel\outlet.go实现了这个接口

forward调用的正是这个outlet

```
func (o *outlet) OnEvent(d *util.Data) bool {
       if !o.isOpen.Load() {
              return false
       }

       event := d.GetEvent()
       if d.HasState() {
              event.Private = d.GetState()
       }

       if o.wg != nil {
              o.wg.Add(1)
       }

       o.client.Publish(event)
       return o.isOpen.Load()
}
```

通过client.Publish发送数据，client也是一个接口

```
type Client interface {
       Publish(Event)
       PublishAll([]Event)
       Close() error
}
```

调试之后，client使用的是elastic\beats\libbeat\publisher\pipeline\client.go的client对象，publish方法即发送日志的方法，如果需要在发送前改造日志格式，可在这里添加代码，如下面的解析日志代码。

```
func (c *client) publish(e beat.Event) {
       var (
              event   = &e
              publish = true
              log     = c.pipeline.logger
       )

       c.onNewEvent()

       if !c.isOpen.Load() {
              // client is closing down -> report event as dropped and return
              c.onDroppedOnPublish(e)
              return
       }

       if c.processors != nil {
              var err error

              event, err = c.processors.Run(event)
              publish = event != nil
              if err != nil {
                     // TODO: introduce dead-letter queue?

                     log.Errorf("Failed to publish event: %v", err)
              }
       }

       if event != nil {
              e = *event
       }

       open := c.acker.addEvent(e, publish)
       if !open {
              // client is closing down -> report event as dropped and return
              c.onDroppedOnPublish(e)
              return
       }

       if !publish {
              c.onFilteredOut(e)
              return
       }

       //解析日志
       error:=ParselogMsg(event)
       if error!=nil{
              log.Errorf("###出现错误")
       }
       //

       e = *event
       pubEvent := publisher.Event{
              Content: e,
              Flags:   c.eventFlags,
       }

       if c.reportEvents {
              c.pipeline.waitCloser.inc()
       }

       var published bool
       if c.canDrop {
              published = c.producer.TryPublish(pubEvent)
       } else {
              published = c.producer.Publish(pubEvent)
       }

       if published {
              c.onPublished()
       } else {
              c.onDroppedOnPublish(e)
              if c.reportEvents {
                     c.pipeline.waitCloser.dec(1)
              }
       }
}
```

