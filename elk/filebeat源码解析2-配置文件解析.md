上一篇文章提到filebeat的配置文件解析，我们进入到configure方法看下配置文件的解析过程

调用 cfgfile.Load方法解析到cfg对象，进入load方法

```
func Load(path string, beatOverrides *common.Config) (*common.Config, error) {
       var config *common.Config
       var err error

       cfgpath := GetPathConfig()

       if path == "" {
              list := []string{}
              for _, cfg := range configfiles.List() {
                     if !filepath.IsAbs(cfg) {
                            list = append(list, filepath.Join(cfgpath, cfg))
                     } else {
                            list = append(list, cfg)
                     }
              }
              config, err = common.LoadFiles(list...)
       } else {
              if !filepath.IsAbs(path) {
                     path = filepath.Join(cfgpath, path)
              }
              config, err = common.LoadFile(path)
       }
       if err != nil {
              return nil, err
       }

       if beatOverrides != nil {
              config, err = common.MergeConfigs(
                     defaults,
                     beatOverrides,
                     config,
                     overwrites,
              )
              if err != nil {
                     return nil, err
              }
       } else {
              config, err = common.MergeConfigs(
                     defaults,
                     config,
                     overwrites,
              )
       }

       config.PrintDebugf("Complete configuration loaded:")
       return config, nil
}
```

如果不输入配置文件，使用configfiles定义文件

```
configfiles = common.StringArrFlag(nil, "c", "beat.yml", "Configuration file, relative to path.config")
```

如果输入配置文件进入else分支

```
config, err = common.LoadFile(path)
```

根据配置文件构造config对象在

```
c, err := yaml.NewConfigWithFile(path, configOpts...)
```

