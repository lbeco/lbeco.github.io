# elasticSearch

官方文档：

https://www.elastic.co/guide/cn/elasticsearch/guide/current/index.html

基本上官方文档写得十分清楚了，就是有些翻译腔。

手写的看不清

https://jishuin.proginn.com/p/763bfbd34e53



ES 是一个开源的高扩展的分布式全文搜索引擎它基于Lucene。

Lucene 是 Elasticsearch所基于的 Java 库，它引入了按段搜索的概念。



## 索引

es采用倒排索引实现查找，每一个索引一经创建将会不变。

Elasticsearch 基于 Lucene, 这个 java 库引入了 *按段搜索* 的概念。 每一 *段* 本身都是一个倒排索引， 但 *索引* 在 Lucene 中除表示所有 *段* 的集合外， 还增加了 *提交点* 的概念 — 一个列出了所有已知段的文件。

插入文件过程：

1. 新文档被收集到内存索引缓存
2. 不时地, 缓存被 *提交* ：
   - 一个新的段—一个追加的倒排索引—被写入磁盘。
   - 一个新的包含新段名字的 *提交点* 被写入磁盘。
   - 磁盘进行 *同步* — 所有在文件系统缓存中等待的写入都刷新到磁盘，以确保它们被写入物理文件。
3. 新的段被开启，让它包含的文档**可见以被搜索**。
4. 内存缓存被清空，等待接收新的文档。



当段过多的时候，es会进行段合并。合并进程选择一小部分大小相似的段，并且在后台将它们合并到更大的段中。这并不会中断索引和搜索。



## 持久化

![New documents are added to the in-memory buffer and appended to the transaction log](https://www.elastic.co/guide/cn/elasticsearch/guide/current/images/elas_1106.png)

TransLog负责保证持久化，存储在磁盘中。

文档进入内存缓冲区后，就会同步在translog内追加。当发生refresh时，内存缓冲区被写入段中，但是Translog还是继续增加。

当发生flush后，日志文件全部被刷入磁盘。

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWcyMDE4LmNuYmxvZ3MuY29tL2Jsb2cvODU3MDY0LzIwMTkwOC84NTcwNjQtMjAxOTA4MDkxMTI4MzYyMzUtMTk0MjY0Mzk2Ni5wbmc?x-oss-process=image/format,png)
