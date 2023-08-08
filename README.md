## KDD EvalRS 2023

### Metric@CustomerN for Reclist

This repo extends the `EvalRecList` class to support personalized thresholds for top-k metrics, as proposed in [Metric@CustomerN](https://reclist.io/kdd2023-cup/papers/EVALRS2023_paper_5.pdf).

We calculate user-level listens per day*, choose a quantile q, and then set the user's personalized k $u_k=quantile q of listens/day$. This ends up making the metrics much more challenging, since instead of doing hit rate at 100, we're doing hit rate at 7 or 25 or 2, depending on the user.

*The dataset doesn't contain duplicate entries for user-track pairs even if the user listens to the track multuple times, so even though we know *how many times* they listened to a track, it's not easy for us to tell *when* the subsequent listens are, so our estimates of listens/day are impacted by this limitation of the dataset.

Our results are easily extensible to the equality-difference metric variants. 

We've implemented:
- hit rate at user's median number of songs/day
- hit rate at user's p90 number of songs/day
- MRR at user's median number of songs/day
- MRR at user's p90 number of songs/day

We used the predictions from the pretrained model as described [here](https://github.com/RecList/evalRS-KDD-2023/blob/main/notebooks/merlin_tf_tutorial/evalrs_kdd_2023_reclist_eval_on_saved_preds.ipynb).

Our demonstration notebook shows how much more challenging the metrics are:
- hit rate at 100 is 4.8% while hit rate at median listens/day is 0.03%
- hit rate at p90 listens/day should theoretically be better than at median, though this is not evident when the metrics are rounded to four decimal places

We implemented this method by simply masking out the results of the top 100 and passing the masked predictions to the existing metric functions that expect predictions dataframes. Thus this method can be extended to any metric expecting a predictions dataframe of the same format, including the slice-based methods.

Distribution of user-level median listens/day:
- mean         4.976734
- std          4.474199
- min          1.000000
- 25%          2.500000
- 50%          4.000000
- 75%          6.000000
- max        274.000000
