## Amazon Personalize Model Evaluation
Many customer encounter challenge on Personalize Model evaluation, which the model with good performance but end-user don’t like it. This blog provided a methodology to evaluate on Personalize.  esp. on diversity, novelty and serendipity. And take MovieLens data as example, demo and walkthrough it step by step.

This sample used MovieLens 100K movie ratings which take as stable benchmark dataset. 100,000 ratings from 1000 users on 1700 movies. Released 4/1998. You can find detail information https://files.grouplens.org/datasets/movielens/.

This sample includes notebook as following:
* [01-data-preparation.ipynb](01-data-preparation.ipynb)
* [02-personalize_ranking_movielens.ipynb](02-personalize_ranking_movielens.ipynb)
* [03-personalize_ranking_movielens-userpca-exp.ipynb](03-personalize_ranking_movielens-userpca-exp.ipynb)
* [04-personalize_ranking_movielens-no-user-baseline.ipynb](04-personalize_ranking_movielens-no-user-baseline.ipynb)
* [05-offline-evaluation.ipynb](05-offline-evaluation.ipynb)

### Prepare Dataset
First of all, we need to prepare MovieLens Dataset. Observe user data, item data, and rating data. You can reference [01-data-preparation.ipynb](01-data-preparation.ipynb) for detail information.

### Create Personalize Solutions and Campaigns 
Secondly, we will create the dataset group, prepare and upload the user data, item data, and interaction data to S3. After those dataset is ready, Personalize can start to create solution, and campaigns to interact. You can reference [02-personalize_ranking_movielens.ipynb](02-personalize_ranking_movielens.ipynb) for detail information.

In the same time, we will conduct experiement, like [03-personalize_ranking_movielens-userpca-exp.ipynb](03-personalize_ranking_movielens-userpca-exp.ipynb) and [04-personalize_ranking_movielens-no-user-baseline.ipynb](04-personalize_ranking_movielens-no-user-baseline.ipynb) as baseline to compare the performance with each other.

### Evaluation Model Beyond Accurancy 
Last but not least, we will evalution the model beyound accurancy esp. on diversity, novelty and serendipity. We use the function as following to evaluate diversity, novelty and serendipity:
```
def diversity(pred, item_db):
    d = 0 
    for i, p1 in enumerate(pred): 
        for j, p2 in enumerate(pred):
            if j > i: 
                dist = sum(abs(item_db.get_contents_by_id(p1) - item_db.get_contents_by_id(p2))) 
                d += dist
    return d 

def novelty(pred, item_db):
    d = 0 
    for i, p in enumerate(pred):
        d += 1/(math.log(item_db.get_popularity_by_id(p)+2,2)+1)
    return d


def serendipity(pred, groud_truth, uid, user_db, item_db): 
    up = user_db.get_user_profile(uid)
    up_norm = [1 if i > 0 else 0 for i in up ]
    dist_total = 0 
    for p in pred:
        if p in groud_truth:
            contents = item_db.get_contents_by_id(p)
            dist = sum(abs(up_norm - contents))   
            dist_total += dist
    return  dist_total / len(pred)

```
You can reference [05-offline-evaluation.ipynb](05-offline-evaluation.ipynb) for detail information. 

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

