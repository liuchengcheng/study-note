# 理解query_then_fetch和dfs_query_then_fetch

## 相关性打分

ES使用的打分算法包含了称之为“TF-IDF”的统计信息来帮助计算处于那个索引中的文档的相关性。

TFIDF基本思想就是“一个项在文档中出现的次数越多，那么这个文档更加相关；但相关性会被这个项在整个文档库中的次数削弱”。

稀有项出现在相对少的文档中，那么任何查询匹配了一个稀有项的相关性就变得很高。相反，平常项到处都有，他们的相关性就低了。

当用户执行一个搜索时，ES面对一个有趣的困境。你的查询需要找到所有相关的文档，但是这些文档分布在你的cluster中的任何数目的shard中。

每个shard是一个Lucene的索引，保存了自身的TF和DF统计信息。一个shard只知道在其自身中出现的次数，而非整个cluster。

但是相关算法使用了TF-IDF，它需要知道对于整个索引的而不是对每个shard的TF和DF么？

## 默认搜索类型：query_then_fetch

答案：是也不是。默认情形下，ES会使用一个称之为Query then fetch的搜索类型。它运作的方式如下：

1. 发送查询到每个shard
2. 找到所有匹配的文档，并使用本地的Term/Document Frequency信息进行打分
3. 对结果构建一个优先队列（排序，标页等）
4. 返回关于结果的元数据到请求节点。注意，实际文档还没有发送，只是分数
5. 来自所有shard的分数合并起来，并在请求节点上进行排序，文档被按照查询要求进行选择
6. 最终，实际文档从他们各自所在的独立的shard上检索出来
7. 结果被返回给用户

这个系统一般是能够良好地运作的。大多数情形下，你的索引有足够的文档来平滑Term/Document frequency统计信息。因此，尽管每个shard不一定拥有完整的关于整个cluster的frequency信息，结果仍然足够好，因为fequency在每个地方基本上是类似的。
但是在我们开头提起的那个查询，默认搜索类型有时候会失败。

### dfs_query_then_fetch

在上篇文章中，我们默认建立了一个索引，ES通常使用5个shard。接着插入了5个文档进入索引，向ES发送请求返回相关结果和准确的分数。其结果并不是很公平，对吧？

这是由于默认的搜索类型导致的，每个shard仅仅包含一个或者两个文档（ES使用hash确保随机分布）。当我们要求ES计算分数时候，每个shard仅仅拥有关于五个文档的一个很窄的视角。所以分数是不准确的。

幸运的是，ES并没有让你无所适从。如果你遇到了这样的打分偏离的情形，ES提供了一个称为“DFS Query Then Fetch”。这个过程基本和Query Then Fetch类型，除了它执行了一个预查询来计算整体文档的frequency。

预查询每个shard，询问Term和Document frequency
发送查询到每隔shard
找到所有匹配的文档，并使用全局的Term/Document Frequency信息进行打分
对结果构建一个优先队列（排序，标页等）
返回关于结果的元数据到请求节点。注意，实际文档还没有发送，只是分数
来自所有shard的分数合并起来，并在请求节点上进行排序，文档被按照查询要求进行选择
最终，实际文档从他们各自所在的独立的shard上检索出来
结果被返回给用户
如果我们使用这个新的搜索类型，那么获得的结果更加合理了（这些都一样的）：

作者：Not_GOD
链接：https://www.jianshu.com/p/c7529b98993e
來源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

```sql
$ curl -XGET 'localhost:9200/startswith/test/_search?pretty=true&search_type=dfs_query_then_fetch' -d '{
        "query": {
        "match_phrase_prefix": {
           "title": {
             "query": "d",
             "max_expansions": 5
           }
         }
       }
     }' | grep title

      "_score" : 1.9162908, "_source" : {"title":"dzone"}
      "_score" : 1.9162908, "_source" : {"title":"data"}
      "_score" : 1.9162908, "_source" : {"title":"drunk"}
      "_score" : 1.9162908, "_source" : {"title":"drive"}
```

## 结论

当然，更好准确性不是免费的。预查询本身会有一个额外的在shard中的轮询，这个当然会有性能上的问题（跟索引的大小，shard的数量，查询的频率等）。在大多数情形下，是没有必要的，拥有足够的数据可以解决这样的问题。

但是有时候，你可能会遇到奇特的打分场景，在这些情况中，知道如何使用DFS query then fetch去进行搜索执行过程的微调还是有用的。


