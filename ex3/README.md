### example 3
see [在Amazon EKS上部署Zeppelin和Spark分析平台](https://aws.amazon.com/cn/blogs/china/deploy-zeppelin-and-spark-analysis-platform-on-amazon-eks/)

`eksctl create cluster -f eks.yaml`

- `kubectl create ns spark`
- `kubectl create serviceaccount spark -n spark`
- `kubectl create clusterrolebinding spark-role --clusterrole=edit --serviceaccount=spark:spark --namespace=spark`

- `kubectl apply -f zeppelin-server.yaml`

### todo
set notebook storage in s3

```
  ...
  SPARK_MASTER: k8s://https://kubernetes.default.svc
  SPARK_HOME: /spark
  zeppelin-site.xml: |-
    <?xml version="1.0"?>
    <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
    <configuration>
      <property>
          <name>zeppelin.interpreter.connect.timeout</name>
          <value>300000</value>
          <description>Interpreter process connect timeout in msec.</description>
      </property>
      <property>
          <name>zeppelin.interpreter.output.limit</name>
          <value>10240000</value>
          <description>Output message from interpreter exceeding the limit will be truncated</description>
      </property>
            <property>
          <name>zeppelin.interpreter.lifecyclemanager.class</name>
          <value>org.apache.zeppelin.interpreter.lifecycle.TimeoutLifecycleManager</value>
          <description>LifecycleManager class for managing the lifecycle of interpreters, by default interpreter will
          be closed after timeout</description>
      </property>
      <property>
          <name>zeppelin.interpreter.lifecyclemanager.timeout.checkinterval</name>
          <value>600000</value>
          <description>Milliseconds of the interval to checking whether interpreter is time out</description>
      </property>
      <property>
          <name>zeppelin.interpreter.lifecyclemanager.timeout.threshold</name>
          <value>10800000</value>
          <description>Milliseconds of the interpreter timeout threshold, by default it is 1 hour</description>
      </property>
      <property>
          <name>zeppelin.notebook.s3.bucket</name>
          <value>your_bucket</value>
          <description>bucket name for notebook storage</description>
      </property>
      <property>
          <name>zeppelin.notebook.s3.user</name>
          <value>zeppelin</value>
          <description>user name for s3 folder structure</description>
      </property>
      <property>
          <name>zeppelin.notebook.storage</name>
          <value>org.apache.zeppelin.notebook.repo.S3NotebookRepo</value>
          <description>notebook persistence layer implementation</description>
      </property>
    </configuration>
```

### not used yet

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


```
bin/spark-submit \
--master k8s://https://259FCB47D8C4D86D4CB8EFAEC74E244D.gr7.eu-north-1.eks.amazonaws.com \
--deploy-mode cluster \
--name spark-pi \
--class org.apache.spark.examples.SparkPi \
--conf spark.executor.instances=5 \
--conf spark.kubernetes.namespace=spark \
--conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
--conf spark.kubernetes.container.image=jiajidev/spark-py:3.3.1 \
local:///opt/spark/examples/jars/spark-examples_2.12-3.3.1.jar
```