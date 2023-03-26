### Example 2 from [here](https://medium.com/nerd-for-tech/pyspark-spark-operator-amazon-eks-big-data-on-steroids-7d1ccedb765b)

`eksctl create cluster -f eks.yaml`

`kubectl create ns spark`

```
helm repo add spark-operator https://googlecloudplatform.github.io/spark-on-k8s-operator
helm repo update
helm install spark-operator spark-operator/spark-operator --namespace spark-operator --create-namespace --set sparkJobNamespace=spark
```


```
eksctl create iamserviceaccount \
--name spark \
--namespace spark \
--cluster analytics-k8s \
--attach-policy-arn arn:aws:iam::829404019274:policy/Spark-on-eks-nyt \
--approve --override-existing-serviceaccounts \
--region eu-north-1
```

`kubectl apply -f job.yaml -n spark`

