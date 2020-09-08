# Grapher: Graph-based Keyword Extraction Algorithms

<p align="center">
  <img src="./asset/topic_rank_fig.png" width="800">
  <br><i>Fig 1: TopicRank visualization (Bougouin et al.,13) </i>
</p>


*Grapher*, a quick python library to work on graph-based unsurpervised keyword extraction algorithms.

## Get Started
Clone and install

```shell script
git clone https://github.com/asahi417/grapher
cd grapher
pip install .
```

## Basic Usage
*Grapher* retrieves keywords from a document with various graph-based algorithms:
- [TextRank, Mihalcea et al., 04](https://web.eecs.umich.edu/~mihalcea/papers/mihalcea.emnlp04.pdf)
- [SingleRank, Wan et al., 08](https://aclanthology.info/pdf/C/C08/C08-1122.pdf)
- [TopicRank, Bougouin et al.,13](http://www.aclweb.org/anthology/I13-1062)
- [PositionRank, Florescu et al.,18](http://people.cs.ksu.edu/~ccaragea/papers/acl17.pdf)
- [MultipartiteRank, Boudin 18](https://arxiv.org/pdf/1803.08721.pdf)

All the algorithms can be simply used as below.

```python
>>> import grapher
>>> model = grapher.TopicRank()  # any algorithm listed above
>>> sample = '''
We propose a novel unsupervised keyphrase extraction approach that filters candidate keywords using outlier detection.
It starts by training word embeddings on the target document to capture semantic regularities among the words. It then
uses the minimum covariance determinant estimator to model the distribution of non-keyphrase word vectors, under the
assumption that these vectors come from the same distribution, indicative of their irrelevance to the semantics
expressed by the dimensions of the learned vector representation. Candidate keyphrases only consist of words that are
detected as outliers of this dominant distribution. Empirical results show that our approach outperforms state
of-the-art and recent unsupervised keyphrase extraction methods.
'''
>>> model.get_keywords(sample, n_keywords=2)
[
 {'count': 1,
  'lemma': 'word embedding',
  'n_source_tokens': 120,
  'offset': [[22, 23]],
  'pos': 'NOUN NOUN',
  'raw': 'word embeddings',
  'score': 0.13759301735957652,
  'stemmed': 'word embed'},
 {'count': 1,
  'lemma': 'novel unsupervised keyphrase extraction approach',
  'n_source_tokens': 120,
  'offset': [[4, 8]],
  'pos': 'ADJ ADJ NOUN NOUN NOUN',
  'raw': 'novel unsupervised keyphrase extraction approach',
  'score': 0.13064559025892963,
  'stemmed': 'novel unsupervis keyphras extract approach'}
]
```

### Algorithm with LDA prior
You can also use algorithms with LDA prior:
- [TopicalPageRank, Liu et al.,10](http://nlp.csai.tsinghua.edu.cn/~lzy/publications/emnlp2010.pdf)
- [SingleTopicalPageRank, Sterckx et al.,15](https://core.ac.uk/download/pdf/55828317.pdf)

Those require to compute LDA on a corpus beforehand, and can be used in a same way once you've set the model. 
```python
>>> import grapher
>>> model = grapher.TopicalPageRank()
>>> test_sentences = ['documentA', 'documentB', 'documentC']
>>> model.train(test_sentences)
>>> model.get_keywords('document to get keyword')
``` 

LDA files can be loaded on the fly from a checkpoint

```python
>>> import grapher
>>> model = grapher.TopicalPageRank()
>>> test_sentences = ['documentA', 'documentB', 'documentC']
>>> model.train(test_sentences, export_directory='./tmp')
>>> model.load('./tmp')
```

### Benchamrk on [SemEval-2010](https://www.aclweb.org/anthology/S10-1004.pdf)
Run a benchamrk on [SemEval-2010](https://www.aclweb.org/anthology/S10-1004.pdf) dataset, processed by 
[Boudin et al., 16](https://www.aclweb.org/anthology/W16-3917.pdf). We use Lvl 4 processed dataset.

|       Model      |    F1 (P/R) @5    |    F1 (P/R) @10   |     F1 (P/R) @15    | Approx time (sec) |
|:----------------:|:-----------------:|:-----------------:|:-------------------:|:-----------------:|
|     TextRank     | 0.058 (0.2/0.034) | 0.102 (0.2/0.068) | 0.181 (0.266/0.137) |              8.51 |
|    SingleRank    | 0.058 (0.2/0.034) | 0.153 (0.3/0.103) | 0.181 (0.266/0.137) |             18.99 |
|     TopicRank    | 0.117 (0.4/0.068) | 0.154 (0.3/0.103) |  0.181 (0.26/0.137) |             41.43 |
| MultipartiteRank | 0.117 (0.4/0.068) | 0.154 (0.3/0.103) |  0.181 (0.26/0.137) |            410.52 |