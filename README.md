# Large Scale Cover Detection in Digital Music Libraries using Metadata, Lyrics and Audio Features


Source code and supplementary materials for the paper "Correya, Albin, Romain Hennequin, and Mickaël Arcos. "Large-Scale Cover Song Detection in Digital Music Libraries Using Metadata, Lyrics and Audio Features." arXiv preprint arXiv:1808.10351 (2018)".

This repo contains scripts to run text-based experiments for cover song detection task on the [MillionSongDataset (MSD)](https://labrosa.ee.columbia.edu/millionsong/)
which is imported into an [Elasticsearch (ES)](https://www.elastic.co/blog/what-is-an-elasticsearch-index) index as described in the above mentioned paper.
# Requirements

Install python dependencies from the requirements.txt file

```
$ pip install -r requirements.txt
```

# Setup

* Use [ElasticMSD](https://github.com/deezer/elasticmsd) scripts to setup your local Elasticsearch index of MSD.
* Fill your ES db credentials (host, port and index) as a environment variable in your local system. 
Check [templates.py](templates.py) file.

## Datasets

The following datasets have corresponding mapping with MSD tracks. These data are ingested to the ES index in an update operation

* [Second Hand Songs (SHS)](https://labrosa.ee.columbia.edu/millionsong/secondhand) dataset. Check the ./data folder
* For lyrics we used the [musiXmatch (MXM)](https://labrosa.ee.columbia.edu/millionsong/musixmatch) dataset

# Usage

## Modular mode

In this section, you can have a glimpse on how to use these classes and various methods for doing experiments

```python
#import modules
from es_search import SearchModule
from experiments import Experiments
import templates as presets

# Initiaite es search class
es = SearchModule(presets.uri_config)

# search method by msd_track title in view mode
results = es.search_by_exact_title('Listen To My Babe', 'TRPIIKF128F1459A09', mode='view')

#You can also use the experiment class to automate particular experiments for a method
#Initiate experiment class with the instance of SearchModule and path to the dataset as arguments
exp = Experiments(es, './data/test_shs.csv')

#run the song title match experiment with top 100 results
results = exp.run_song_title_match_task(size=100)

#compute evaluation metrics for the task
mean_avg_precison = exp.mean_average_precision(results)

#reset the preset if you want to do another experiment on the same same SearchModule instance.
exp.reset_preset()

results = exp.run_mxm_lyrics_search_task(size=1000)

mean_avg_precison = exp.mean_average_precision(results)

```

## Evaluation tasks

Some examples for using functions in evaluations.py script to reproduce the results mentioned in the paper
```python
from evaluations import *

#Evaluation task on SHS train set against the whole MSD (1 x 999,999 songs)
shs_train_set_evals(size=100, method="msd_title", mode="msd", with_duplicates=True)

#You can specify various prune sizes and methods as parameters
shs_train_set_evals(size=1000, method="mxm_lyrics", mode="msd", with_duplicates=False)

#You can run the same experiment only on the SHS train set against itself by specifying "mode" param as "shs" (1 x 12,960)
shs_train_set_evals(size=100, method="msd_title", mode="shs", with_duplicates=True)

#In same way you can do the evaluation experiments on SHS test sets
shs_test_set_evals(size=100, method="title_mxm_lyrics", with_duplicates=True)

```


If you don't want to care about how the module works and you only need results various experiments, then this is for you. 
It's a wrapper around the modules to run automated experiments and save the results to a .log file or a json_template. 
The experiments are multi-threaded and able to run from terminal using command-line arguments.

```bash
$ python evaluations.py -m test -t -1 -e msd -d 0 -s 100

    -m : (type: string) Choose between "train" or "test" modes
    -t : (type: int) No of threads
    -e : (type: int) Choose between "msd"
    -d : (type: boolean) include duplicates
    -s : (type: int) Required pruning size for the experiments

```

# Cite

If you use these work, please cite our paper.

```
Correya, Albin, Romain Hennequin, and Mickaël Arcos. "Large-Scale Cover Song Detection in Digital Music Libraries Using Metadata, Lyrics and Audio Features." arXiv preprint arXiv:1808.10351 (2018).
```

Bibtex format
```
@article{correya2018large,
  title={Large-Scale Cover Song Detection in Digital Music Libraries Using Metadata, Lyrics and Audio Features},
  author={Correya, Albin and Hennequin, Romain and Arcos, Micka{\"e}l},
  journal={arXiv preprint arXiv:1808.10351},
  year={2018}
}
```