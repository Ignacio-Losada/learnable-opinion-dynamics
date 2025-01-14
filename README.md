# Learning Opinion Dynamics From Social Traces

This repository contains code and data related to the paper ***"Learning Opinion Dynamics From Social Traces"*** by Corrado Monti, Gianmarco De Francisci Morales and Francesco Bonchi, published at KDD 2020. If you use the provided data or code, we would appreciate a citation to the paper:

```
@inproceedings{monti2020learningopinion,
  title={Learning Opinion Dynamics From Social Traces},
  author={Monti, Corrado and De Francisci Morales, Gianmarco and Bonchi, Francesco},
  booktitle={Proceedings of the 26th ACM SIGKDD International Conference on Knowledge Discovery \& Data Mining},
  year={2020}
}
```

Here you will find instructions about (i) using the algorithm we designed (ii) obtaining the Reddit data set we collected (iii) how to reproduce our experiments.

## Model implementation

In order to use our model, we provide [our implementation in `src/model`](src/model). To use our model on your data, just clone this repo, install the dependencies, and then our model:

```
git clone https://github.com/Ignacio-Losada/learnable-opinion-dynamics.git
cd learnable-opinion-dynamics
conda env create -f environment.yml
conda activate opinion-dynamics 
```

You can then use it in Python:

```python
import sys
path_to_github_repo = r'C:\Users\Ignac\Github\learnable-opinion-dynamics' 
sys.path.insert(1, path_to_github_repo)

from src.model import learn_opinion_dynamics
```

Check its documentation [online](http://corradomonti.github.io/learnable-opinion-dynamics) or with:

```
>>> help(learn_opinion_dynamics)
```

For instance, to use it on a simple graph, with 3 nodes, 2 features and 2 time steps, and then print the estimated positions of each feature:

```
>>> u_v_t_weights = ([0, 1, 0, 1], [1, 0, 1, 1])
>>> v_a_t_weights = ([0, 0, 0, 1], [2, 1, 0, 1], [0, 0, 1, 1], [2, 1, 1, 1])
>>> res = learn_opinion_dynamics(N=3, Q=2, T=2, u_v_t_weights=u_v_t_weights, v_a_t_weights=v_a_t_weights)
>>> print(res.w)
```

## Provided data set

[In `data/reddit`, we provide the Reddit data set we gathered.](data/reddit)

To build this data, we consider the 51 subreddits most similar to `r/politics` according to [this](https://www.shorttails.io/interactive-map-of-reddit-and-subreddit-similarity-calculator); the time stamps are the months between January 2008 and December 2017; for the users, we consider only those posting a minimum of 10 comments per month on r/politics for at least half of the considered months, which gives us 375 users.

Our input files are:

- `edges_user.tsv.bz2` contains the interactions among considered users. Each row (t, u, v, w) indicates that user v replied to user u during time step t, for w times.

- `edges_feature.tsv.bz2` contains the user-subreddit interactions. Each row (t, u, a, w) indicates that user u participated in subreddit a during time step t, for w times.

Our validation data is:

- `feature_scores.tsv.bz2` contains the summary statistics for scores received by each user on each subreddit in each timestep. Specifically, it contains the sum of positive scores, the number of positively scored comments, the sum of negative scores, and the number of negatively scored comments.

- `interaction_scores.tsv.bz2` contains data about each interaction between considered users.

All the data files are TSV compressed with `bz2` and can be easily opened with pandas:

```
>>> pd.read_csv("edges_user.tsv.bz2", names=("yearmonth", "parent", "author", "count"), header=None, sep='\t')
>>> pd.read_csv("edges_feature.tsv.bz2", names=("yearmonth", "author", "subreddit", "count"), header=None, sep='\t')
>>> pd.read_csv("feature_scores.tsv.bz2", sep='\t')
>>> pd.read_csv("interaction_scores.tsv.bz2", sep='\t')
```

## Reproducibility

In order to reproduce our experiments, we provide [our scripts in `src/experiments`](src/experiments). They need a larger set of dependencies, listed in `src/experiments/requirements.txt`. In particular, we use [MLflow](https://github.com/mlflow/mlflow) to organize parameters and experiment results. To run both sets of experiments, do:

```
cd src/experiments/
pip install -r requirements.txt
mlflow ui --port 8765 &
python experiments_synthetic.py
python experiments_reddit.py
```

Then, you can use the MLflow User Interface at `localhost:8765` to inspect the results of each experiment as they are produced.
