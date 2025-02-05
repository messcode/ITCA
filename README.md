# ITCA - Guide the ambiguous outcome labels combination for multi-class classification
**ITCA**  (Information-theoretic classification accuracy) is a criterion that guides data-driven combination of ambiguous outcome labels in multi-class classification (see [ITCA documentation](https://messcode.github.io/ITCA/) for detailed guides).



## Installation
Requirements:

- python >= 3.6
- numpy: https://pypi.org/project/numpy/
- scikit-learn: https://pypi.org/project/scikit-learn/
- [optional] pygraphviz: https://pypi.org/project/pygraphviz/ (for visualization)

Install from PyPI by running (in the command line):

``` shell
pip install itca
```

Install from source code:

``` shell
   git clone https://github.com/JSB-UCLA/ITCA.git
   cd ITCA
   python setup.py install
```

ITCA is easy to use.

``` python
import numpy as np
from sklearn.discriminant_analysis import LinearDiscriminantAnalysis
from itca import itca, compute_y_dist, bidict, GreedySearch
# ===================  Data ========================
# `X` is the feature matrix, a 2D numpy array of size (n_smaples, n_features).
# `y_obs` is the observed labels, a 1D numpy array of size (n_samples, ) that takes values 
# in [0, 1, 2] (the observed classes number K0=3).
# The combination is represented by a bidirectional disctionary `bidict`.
# `bidict({0:0, 1:0, 2:1, 3:2})` indactes the mapping from the orignal labels to the combined labels.
true_combination = bidict({0:0, 1:0, 2:1, 3:2})
X1 = np.array([[0., 0.]]) + np.random.randn(200,  2)
X2 = np.array([[1.5, 1.5]]) + np.random.randn(200, 2)
X3 = np.array([[-1.5, 1.5]]) + np.random.randn(200, 2)
X = np.concatenate([X1, X2, X3])  # data matrix
y_true = np.concatenate([np.ones(200) * i for i in 
range(3)]).astype(int)            # true lables K^*=3
y_obs = true_combination.reverse_map(y_true) # observed labels K_0=4
#===========  Classsification algorithm =============
# `clf` can be any sklearn classifcation algorithm or any classifcation algorithm that implements 
# `clf.fit(X, y)` for fitting and `clf.predict(X)` for prediction.  
clf = LinearDiscriminantAnalysis()
# =================== Evaluate s-ITCA on the true combination ================
clf.fit(X, true_combination.map(y_obs))
y_pred = clf.predict(X)
itca(y_obs, y_pred, true_combination, compute_y_dist(y_obs))
# ============= Search class combination =============
gs = GreedySearch(class_type='ordinal')
gs.search(X, y_obs, clf, verbose=False, early_stop=True)
gs.selected # show the selected class combination
#>>>{0: 0, 1: 0, 2: 1, 3: 2}|ITCA=0.8807|
```
Please see the [tutorial](https://messcode.github.io/ITCA/tutorials.html)  for more details.
## Troubleshooting
For visualization, ITCA requires `pygraphviz` package. If you have trouble installing `pygraphviz`, please refer to [pygraphviz](https://pygraphviz.github.io/documentation/stable/install.html) for detailed installation guides.
Please make sure that `dot` is in your `PATH` environment variable. If you are using Windows, please refer to [Graphviz](https://graphviz.org/download/) for detailed installation guides.
## Citation
```latex
@article{JMLR:v23:21-1150,
  author  = {Chihao Zhang and Yiling Elaine Chen and Shihua Zhang and Jingyi Jessica Li},
  title   = {Information-theoretic Classification Accuracy: A Criterion that Guides Data-driven Combination of Ambiguous Outcome Labels in Multi-class Classification},
  journal = {Journal of Machine Learning Research},
  year    = {2022},
  volume  = {23},
  number  = {341},
  pages   = {1--65},
  url     = {http://jmlr.org/papers/v23/21-1150.html}
}
```


## Contribute
- Issue tracker:  https://github.com/messcode/ITCA/issues
- Source code:
	- https://github.com/JSB-UCLA/ITCA
	- https://github.com/messcode/ITCA (the devlopmental version)

## Contact
If you are having any issues, comments regarding this project, please feel free to contact zhang.dabiao11@gmail.com
