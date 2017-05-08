# movie-recommender
Movielens는 사용자들로부터 수집한 영화평점 데이터를 제공한다.
위 예제에서는 movilens 1M,백만개의 데이터를 사용한다.

실행환경
- jupyter(python 3.5)

이 프로젝트는 아래와 같은 파이썬 라이브러리를 사용한다.
- numpy
- Pandas
- matplotlib
- scipy
- scikit-learn

# movielens Start (Pandas)
Pandas 라이브러리를 사용하여 위 데이터로부터 의미있는 데이터분석을 할 수 있다.

1. 남/녀 성별을 기준으로 영화 평점 순위

![img1](/img/img1.png)

2. 영화 평점의 편차가 큰 순위(호불호가 극명하게 갈리는 영화)

![img2](/img/img2.png)

# 데이터분포분석

1. 유저당 영화평가 횟수

![img3](/img/img3.png)

2. 영화당 평가받은 횟수

![img4](/img/img4.png

# Movie Recommender(collarborative filtering)
1. 협력필터링(collarborative filtering)

협력필터링은 크게 User-based 협력필터링, Item-based 협력필터링으로 나누어진다.
User-based 협력필터링은 사용자가 평가한 영화평점결과 ( vector )와
다른 사용자가 평가한 영화평점결과 ( vector ) 간의 유사도(similarity)를 비교하여 유사도가 높은 사용자가 높게 평가한 영화를 사용자에게 추천해주는 알고리즘이다.
Item-based 협력필터링은 사용자가 영화평점을 남긴 데이터를 기반으로 사용자가 액션영화에 높은 평점을 주었다면, 사용자에게 '액션' 장르의 영화를 추천하는 알고리즘 방식이다.
User-based 협력필터링 알고리즘에서 두 vector간의 distance(또는 similarity)를 비교하게 되는데,
이 때 similarity evaluation 방법으로 다음과 같은 방법을 사용할 수 있다.
- Minkowski distance
Minkowski distance는 여러가지 distance 계산의 일반식이다. distance 측정방법으로 주로 absolute distance, Euclidean distance방법이 사용된다.

- Cosine
두 벡터간의 거리가 아닌 두 벡터가 이루는 각도를 유사도 측정지표로 사용하는 방식이다. 두 벡터가 서로 이루는 각도가 적을수록 유사도가 높게 측정된다.

- Jaccard coefficient
Jaccard 측정방법은 데이터의 feature(attribute)가 비대칭 속성인경우 두 벡터간의 유사도를 측정하는 지표로써 사용된다.

위 movilens 데이터분석에는 Euclidean distance, Cosine 방법을 사용하여 두 벡터간의 유사도를 측정하였다.

2. 주요 소스코드

<pre><code>
def eval_prediction( predict_users,  n_users=50 ):
    ## evaluation
    ds = pd.merge(eval_ratings, 
                       ratings[['movie_id','rating']].groupby(['movie_id']).mean().reset_index(), 
                       on='movie_id', how='left')

    ds = ds.rename(columns= {'rating_x':'rating', 'rating_y':'mean_rating'})

    st = time.time()
    ## udpate to predict_rating 
    distance_functions = [ ('euclidean',distance_euclidean), ('cosine', distance_cosine) ]
    for name, func in distance_functions:
        ds[name] = 0
        for user_id in predict_users:
            # key: movie id, value : predicted_rating
            for x in predictRating(user_id, n_users, func):
                ds.loc[(ds.user_id==user_id) & (ds.movie_id==x[0]),name]=x[1]
    print('elapsed', round(time.time()-st,2), 'sec')
    #전체 dataFrame에서 predict를 수행한 user에 해당하는 tuple만 리턴.
    return ds[ds.euclidean+ds.cosine>0]
</code></pre>

![img5](/img/img5.png)
