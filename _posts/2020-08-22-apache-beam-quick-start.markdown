---
layout: post
title:  "Apache Beam 快速上手"
date:   2020-08-22 10:04:34 +0800
---
[Apache Beam](https://beam.apache.org/) 是一种大数据处理标准，由谷歌于 2016 年创建。

Apache Beam 的数据处理流程如下图所示：

![线性管道](https://beam.apache.org/images/design-your-pipeline-linear.svg)

*具有三个顺序转换的线性管道*

Apache Beam 中有三个重要的基本概念：**Pipeline**、**PCollection**、以及 **Transform**。

- Pipeline（管道）：像上图这样一个完整的处理流程被称作 Pipeline。
- PCollection：一种数据结构，我们可以对其进行各类转换操作，如解析、过滤、聚合等。
- Transform（转换）：用于将一个 PCollection 转换成另一个 PCollection。Apache Beam 提供了许多内置的转换函数。

像上图这种线性管道的 Python 实现方式如下所示：

```text
[Final Output PCollection] = ([Initial Input PCollection] | [First Transform]
              | [Second Transform]
              | [Third Transform])
```

除此之外，还可以使用分支管道，其数据处理流程如下图所示：

![分支管道](https://beam.apache.org/images/design-your-pipeline-multiple-pcollections.svg)

*一个分支管道，将两个转换应用于数据库表中的行*

像上图这种分支管道的 Python 实现方式如下所示：

```text
[PCollection of database table rows] = [Database Table Reader] | [Read Transform]
[PCollection of 'A' names] = [PCollection of database table rows] | [Transform A]
[PCollection of 'B' names] = [PCollection of database table rows] | [Transform B]
```

当然，你也可以创建自己的 **复合转换（composite transforms）**，以将多个子步骤嵌套在一个较大的转换中。
一个复合转换的例子如下所示：

```python
# The CountWords Composite Transform inside the WordCount pipeline.
class CountWords(beam.PTransform):

    def expand(self, pcoll):
        return (pcoll
            # Convert lines of text into individual words.
            | 'ExtractWords' >> beam.ParDo(ExtractWordsFn())
            # Count the number of times each word occurs.
            | beam.combiners.Count.PerElement()
            # Format each word and count into a printable string.
            | 'FormatCounts' >> beam.ParDo(FormatCountsFn()))
```

常用的内置的转换函数如下：

- `Create`：用于从内存 **列表** 中创建 PCollection。
- `Map(fn)`：使用 `fn` 函数对 PCollection 中的每一个元素做 **一对一** 的转换处理。
- `Flatten`：
- `FlatMap(fn)`：使用 `fn` 函数对 PCollection 中的每一个元素做 **一对多** 的转换处理，并将结果合并成一个 PCollection。
- `ParDo(fn)`：和 `FlatMap(fn)` 类似，只是它的 `fn` 参数必须是一个 `DoFn` 的 **子类**。
- `GroupByKey`：
- `CoGroupByKey`：对多个二元组 PCollection 按相同键进行合并，如输入的是 `(k, v)` 和 `(k, w)`，则输出 `(k, (iter<v>, iter<w>))`。
- `Distinct`：对 PCollection 进行 **去重**。

最后，通过一个简单的例子来结束本文的内容：

```python
import apache_beam as beam

def split_words(text, delimiter=None):
    return text.split(delimiter)

with beam.Pipeline() as pipeline:
    plants = (
        pipeline
        | 'Gardening plants' >> beam.Create([
            '🍓Strawberry,🥕Carrot,🍆Eggplant',
            '🍅Tomato,🥔Potato',
        ])
        | 'Split words' >> beam.FlatMap(split_words, delimiter=',')
        | beam.Map(print)
    )
```

这将输出：

```text
🍓Strawberry
🥕Carrot
🍆Eggplant
🍅Tomato
🥔Potato
```

有关 Apache Beam 更多的内容，可参考 [Apache Beam 编程指南](https://beam.apache.org/documentation/programming-guide/) 。
