---
layout: post
title: 使用 Stanford CoreNLP 来进行命名实体识别
date: 2020-09-05 15:11 +0800
---

**命名实体识别** （Named Entity Recognition，NER）是自然语言处理的一个分支，
所谓 **命名实体** 是指文本中具有特定意义的实体，主要包括人名、地名、机构名、专有名词等。

目前，命名实体识别做的好的自然语言处理框架有两个：
[spaCy](https://spacy.io/) 和 [StanfordNLP](https://stanfordnlp.github.io/stanfordnlp/) ，
其中 StanfordNLP 可以很好的支持中文，因此我们选择使用 StanfordNLP。

StanfordNLP 的前身是 [Stanford CoreNLP](https://stanfordnlp.github.io/CoreNLP/) ，
这是一个用 Java 编写的自然语言处理框架，其已经发展了多年，在 StanfordNLP 诞生之前，
其他人开发了很多 Python 第三方包用于在 Python 中使用 Stanford CoreNLP，
而 StanfordNLP 则是 Stanford CoreNLP 的 Python 实现版，由于其开发时间较短，目前还没有完全实现
Stanford CoreNLP 的所有功能，但是它提供了一个接口用于访问 Stanford CoreNLP，以此来间接的使用
Stanford CoreNLP 的所有功能。

由于到目前为止，StanfordNLP 还没有实现 NER 功能，所以要想使用 NER，
可以采用 StanfordNLP 作为客户端连接 Stanford CoreNLP 服务端的模式进行使用，
因此，需要先下载和运行 Stanford CoreNLP，具体参考以下步骤：

1. ``wget https://nlp.stanford.edu/software/stanford-corenlp-full-2018-10-05.zip``
2. ``unzip stanford-corenlp-full-2018-10-05.zip``
3. ``cd stanford-corenlp-full-2018-10-05``
4. ``java -mx4g -cp "*" edu.stanford.nlp.pipeline.StanfordCoreNLPServer -preload tokenize,ssplit,pos,lemma,ner,parse,depparse -status_port 9000 -port 9000 -timeout 15000 &``
