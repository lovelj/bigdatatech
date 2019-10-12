我们在filebeat的配置文件的input路径会使用符号“*”来匹配任意字符，比如D:\*.txt即表示匹配D盘所有txt后缀的文件，那如果我们需要改变匹配的策略，需要修改什么地方的代码？

在本系列第一篇(filebeat源码解析1-启动)中提及启动时会扫描所有路径

在log/input.Run()方法调用Input.scan()

进入scan方法看到

```
paths := p.getFiles()
```

getFiles方法即获取所有采集文件路径的方法

```
func (p *Input) getFiles() map[string]os.FileInfo {
       paths := map[string]os.FileInfo{}

       for _, path := range p.config.Paths {
              matches, err := filepath.Glob(path)
              if err != nil {
                     logp.Err("glob(%s) failed: %v", path, err)
                     continue
              }

       OUTER:
              // Check any matched files to see if we need to start a harvester
              for _, file := range matches {

                     // check if the file is in the exclude_files list
                     if p.isFileExcluded(file) {
                            logp.Debug("input", "Exclude file: %s", file)
                            continue
                     }

                     // Fetch Lstat File info to detected also symlinks
                     fileInfo, err := os.Lstat(file)
                     if err != nil {
                            logp.Debug("input", "lstat(%s) failed: %s", file, err)
                            continue
                     }

                     if fileInfo.IsDir() {
                            logp.Debug("input", "Skipping directory: %s", file)
                            continue
                     }

                     isSymlink := fileInfo.Mode()&os.ModeSymlink > 0
                     if isSymlink && !p.config.Symlinks {
                            logp.Debug("input", "File %s skipped as it is a symlink.", file)
                            continue
                     }

                     // Fetch Stat file info which fetches the inode. In case of a symlink, the original inode is fetched
                     fileInfo, err = os.Stat(file)
                     if err != nil {
                            logp.Debug("input", "stat(%s) failed: %s", file, err)
                            continue
                     }

                     // If symlink is enabled, it is checked that original is not part of same input
                     // It original is harvested by other input, states will potentially overwrite each other
                     if p.config.Symlinks {
                            for _, finfo := range paths {
                                   if os.SameFile(finfo, fileInfo) {
                                          logp.Info("Same file found as symlink and originap. Skipping file: %s", file)
                                          continue OUTER
                                   }
                            }
                     }

                     paths[file] = fileInfo
              }
       }

       return paths
}
```

根据配置的路径path使用

```
matches, err := filepath.Glob(path)
```

获取所有匹配到的文件全路径