# ICE: Item Concept Embedding via Textual Information
## 1. Introduction
The ICE toolkit is designed to embed the concepts of items into an embedding representation such that the resulted embeddings can be compared in terms of overall conceptual similarity regardless of item types ([ICE: Item Concept Embedding via Textual Information](http://dl.acm.org/citation.cfm?doid=3077136.3080807), SIGIR 2017). For example, a song can be used to retrieve conceptually similar songs (homogeneous) as well as conceptually similar concepts (heterogeneous).

In specific, ICE incorporates items and their representative concepts (words extracted from the item's textual information) using a heterogeneous network and then learns the embeddings for both items and concepts in terms of the shared concept words. Since items are defined in terms of concepts, adding expanded concepts into the network allows the learned embeddings to be used to retrieve conceptually more diverse and yet relevant results.

### 1.1. System Requirements
- gcc 6.4
- python3
- cython

### 1.2. Getting Started
#### Download:
```
$ git clone https://github.com/cnclabs/ICE
$ cd ./ICE/ICE
```

#### Compile command line interface:
```
$ make ice
```
#### Compile python3 API:
This is an alternative way to use the toolkit via its APIs. For the usage, please refer to Section 2.2.2.
```
$ make python
```
[Note: The API is only tested with Python 3.]

## 2. Usages
### 2.1. ICE Network Construction
Users need to provide an entity-text network and a text-text network to construct an ICE network. For more details, please refer to our [paper](http://dl.acm.org/citation.cfm?doid=3077136.3080807).

#### Entity-text network format: "item word weight"
```
Toy_Story toys 1
Toy_Story stuffed_animals 1
Star_Wars jedi 1
Star_Wars rebel 1
```
#### Text-text network format: "word word weight"
```
toys toys 1
toys stuffed_animals 1
stuffed_animals toys 1
stuffed_animals stuffed_animals 1
jedi jedi 1
rebel rebel 1
```
##### Run:
```
$ python3 construct_graph.py -et ../data/movie_et.edge -tt ../data/movie_tt.edge -ice movie_ice.edge
```
##### Parameters:
```
    -et <string>, --et_network <string>
        Input Entity-text Network
    -tt <string>, --tt_network <string>
        Input Text-text Network
    -ice <string>, --ice_network <string>
        Output ICE Network
```
For sample files, please see `data/movie_et.edge` and `data/movie_tt.edge`.

### 2.2. ICE Embedding Learning
#### 2.2.1 Command line interface usage
##### Run:
```
./ice -train movie_ice.edge -save movie.embd -dim 4 -sample 10 -neg 5 -thread 1 -alpha 0.025
```
##### Parameters:
```
Options:
    -train <string>
        Path to the network used for embedding learning
    -save <string>
        Path to save the embedding file
    -dim <int>
        Dimension of embedding; default is 64
    -neg <int>
        Number of negative examples; default is 5
    -sample <int>
        Number of training samples *Million; default is 10
    -thread <int>
        Number of training threads; default is 1
    -alpha <float>
        Initial learning rate; default is 0.025
```


#### 2.2.2 python3 API usage
After compiling, please use `python3 example.py` for running the following codes.
```python
from pyICE import pyICE

ice = pyICE()
network = {
    'MAYDAY': {'Taiwanese': 1, 'rock': 1,'band': 1},
    'MAYDAY@': {'Taiwanese': 1, 'rock': 1, 'band': 1},
    'Sodagreen': {'Taiwanese': 1, 'indie': 1, 'pop_rock': 1, 'band': 1},
    'SEKAI_NO_OWARI': {'Japanese': 1, 'indie': 1, 'pop_rock': 1, 'band': 1},
    'The_Beatles': {'England': 1, 'rock': 1, 'pop': 1}
}
ice.load_dict(network)
ice.init(dimension=4)
ice.train(sample=11, neg=5, alpha=0.025, workers=1)
ice.save_weights(model_name='example.embd')
```

## 3. Experimental Results
Here, we report the average performance based on 10 embeddings trained under the same setting. For more details, please refer to our [paper](http://dl.acm.org/citation.cfm?doid=3077136.3080807).
- IMDB word-to-movie retrieval task:
    - Graph construction: 20 representative words per item and 5 expanded words per representative word.
    - Embedding learning: dim=256, sample=200, neg=2

|    Genre   | Horror | Thriller | Western | Action | Short | Sci-Fi | Average |
|:----------:|:------:|:--------:|:-------:|:------:|:-----:|:------:|:-------:|
| Precision@50  |  0.322 |   0.206  |  0.318  |  0.449 | 0.100 |  0.386 |  0.297  |
| Precision@100  |  0.316 |   0.203  |  0.281  |  0.423 | 0.080 |  0.382 |  0.281  |


## 4. Citation
```
@inproceedings{Wang:2017:IIC:3077136.3080807,
    author = {Wang, Chuan-Ju and Wang, Ting-Hsiang and Yang, Hsiu-Wei and Chang, Bo-Sin and Tsai, Ming-Feng},
    title = {ICE: Item Concept Embedding via Textual Information},
    booktitle = {Proceedings of the 40th International ACM SIGIR Conference on Research and Development in Information Retrieval},
    series = {SIGIR '17},
    year = {2017},
    isbn = {978-1-4503-5022-8},
    location = {Shinjuku, Tokyo, Japan},
    pages = {85--94},
    numpages = {10},
    url = {http://doi.acm.org/10.1145/3077136.3080807},
    doi = {10.1145/3077136.3080807},
    acmid = {3080807},
    publisher = {ACM},
    address = {New York, NY, USA},
    keywords = {concept embedding, conceptual retrieval, information network, textual information},
} 
```

