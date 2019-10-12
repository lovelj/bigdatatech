### 0.背景

1）对spark有一定了解

2）对tif文件格式有一定了解

### 1.是什么

![img](file:///E:\Temp\msohtmlclip1\01\clip_image002.jpg)

Geotrellis是一个基于spark的高性能栅格数据计算框架。与geomesa和geowave不同，Geotrellis支持栅格数据计算，且更侧重计算能力。

### 2.如何工作

####  读取

支持单幅影像的整体读取和分块读取为rdd，Geotrellis通过解析tif文件实现数据分块。

1）读取本地文件使用GeoTiffReader，这个类会读取tif文件到内存

2）读取影像到RDD，读取本地文件的方式只适合实验用，在大数据场景下读取到RDD更方便利用spark的计算能力。Geotrellis支持读取tif文件和TileLayer(Geotrellis概念，详见官网文档)。示例代码如下：

`val mrdd  = sc.hadoopMultibandGeoTiffRDD(datapath)//tif文件`

`val hadooplayer= HadoopLayerReader(datastore)(sc).read[SpatialKey, MultibandTile,TileLayerMetadata[SpatialKey]](LayerId(layername,level))//TileLayer数据源，datastore为不同存储类型的datastore`

####  分块

以一幅TIF影像为例，`sc.hadoopMultibandGeoTiffRDD`调用`HadoopGeoTiffRDD.spatialMultiband`实现数据分块，核心逻辑在

```
def apply[I, K, V](
    path: Path,
    uriToKey: (URI, I) => K,
    options: Options,
    geometry: Option[Geometry] = None
  )(implicit sc: SparkContext, rr: RasterReader[Options, (I, V)]): RDD[(K, V)] = {

    val conf = new SerializableConfiguration(configuration(path, options))
    val pathString = path.toString // The given path as a String instead of a Path


    options.maxTileSize match {
      case Some(maxTileSize) =>
        if (options.numPartitions.isDefined) logger.warn("numPartitions option is ignored")
        val infoReader = HadoopGeoTiffInfoReader(pathString, conf, options.tiffExtensions)

        infoReader.readWindows(
          infoReader.geoTiffInfoRDD.map(new URI(_)),
          uriToKey,
          maxTileSize,
          options.partitionBytes.getOrElse(DefaultPartitionBytes),
          options,
          geometry)

      case None =>
        sc.newAPIHadoopRDD(
          conf.value,
          classOf[BytesFileInputFormat],
          classOf[Path],
          classOf[Array[Byte]]
        ).mapPartitions(
          _.map { case (p, bytes) =>
            val (k, v) = rr.readFully(ByteBuffer.wrap(bytes), options)
            uriToKey(p.toUri, k) -> v
          },
          preservesPartitioning = true
        )
    }
  }

```



#### 并行

上述接口读取为RDD[(ProjectedExtent, MultibandTile)]，利用spark支持的并行方法实现影像的并行处理

### 3.坑

1）reference.conf打包时使用拼接方式
2）hbase存储只支持2.x版本
3）geotrellis通过解析tiftag的方式实现并行读取，对于扩展的tag支持存在问题（2.3.1及以前版本），只能解析标准格式tif文件