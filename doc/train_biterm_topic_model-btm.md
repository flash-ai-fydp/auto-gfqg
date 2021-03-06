# Training BTM Topic Model

BTM is a probabalistic graphical model that's inspired by LDA and designed to be effective at topic modeling of very short texts. The crucial insight in BTM is to directly modelword co-occurrence patterns as being generated from the topic distribution. In contrast, LDA models the generation process of the _document_ instead of _word pairs_.

The [BTM code is on GitHub](https://github.com/xiaohuiyan/BTM) and the [paper is online](http://www.bigdatalab.ac.cn/~lanyanyan/papers/2013/WWW2013-yan.pdf).

We chose to use BTM instead of the Deep Autoencoder Topic Model (DATM) as presented originally in the [RevUP paper](http://oa.upm.es/42192/1/INVE_MEM_2015_226779.pdf). [DATM](https://www.prhlt.upv.es/workshops/iwes15/pdf/iwes15-kumar-d'haro.pdf) is a restricted botlzman machine with a modified cost function that encourages the model to learn latent topics that are _selective_ (i.e. coherent and strongly encode the latent topic) and _sparse_ (i.e. ensuring that sentences do not express a plethora of latent topics).

Our decision for using BTM versus DATM is motivated by practical reasons. Since this proof-of-concept work was restricted to a short timeframe (one working week), it was important to not become bogged-down in deep, detailed work that would not yield benefits for realizing the entire concept. As a result, we searched for existing, off-the-shelf topic modeling programs to use.

A key insight gleaned from the RevUP research is that traditional topic models fail on short texts due to information sparisty. Specifically, the RevUP authors conclude that the main failing of document-oriented topic models (i.e. LDA) applied to short texts is an inhert sparisty of document-word co-occurrences. In a traditional document, one expects there to be multiple instances of many words. However, in a short text or sentence, it's unlikely that we'd encounter any word more than once or twice. As a result, a document-based topic modeling algorithm will only have a few words to use per short text during learning.

Importantly, the main method for circumventing this data-sparsity issue is to model language patterns as they occur across the entire corpus. And to assume that the topics directly influence these language patterns, rather than influnce the documents and then assume that the documents influence language patterns. These ideas and new set of assumptions underlie the BTM algorithm.

Given the fact that the BTM code is readily available and is well-suited to topic modeling for short texts, it serves as an excellent substitute for DATM.

### Commands

In order to re-train the topic model as it's used in this project, do the following:

First, clone the `BTM` repo:
```
git clone git@github.com:xiaohuiyan/BTM.git
```

Second, copy the `biology.txt` file to the `BTM/sample-data/` directory unde the name of `doc_info.txt`:

```
cp $DEV/auto-gfqg/data/from_authors/biology.txt $DEV/BTM/sample-data/doc_info.txt
```

Third, modify the `runExample.sh` script such that `K=25`. This BASH command will do the trick:

```
sed -i 's/K=50/K=25/g' $DEV/BTM/script/runExample.sh
```

Finally, to execute the script, move into the `script/` directory:
```
cd $DEV/BTM/script/
time ./runExample.sh
```

After executing, the `output` directory in the root of the `BTM` repository will have all relevant information. Importantly, the learned model parameters are in `output/model/` as the files:
* `k25.pw_z`: conditional word probability given a topic
* `k25.pz`: prior probability of each topic
* `k25.pz_d`: posterior topic probability for each sentence

The `voca.txt` file consists of every unique word in the corpus and the `doc_wids.txt` file consists of indicies of all words that occur in each sentence.

### NOTE
Make sure that `DEV` is where you checked out the `auto-gfqg` and `BTM` repositories.
