# Kafkaのセットアップ

https://strimzi.io/quickstarts/

## Strimziのデプロイ

セットアップ:

```shell
minikube start --memory=4096
kubectl create namespace kafka
kubectl create -f 'https://strimzi.io/install/latest?namespace=kafka' -n kafka
```

起動チェック:

```shell
kubectl get pod -n kafka --watch
kubectl logs deployment/strimzi-cluster-operator -n kafka -f
```

## Kafkaクラスタのデプロイ

セットアップ:

```shell
kubectl apply -f https://strimzi.io/examples/latest/kafka/kafka-persistent-single.yaml -n kafka 
```

起動するまで待つ:

```shell
kubectl wait kafka/my-cluster --for=condition=Ready --timeout=300s -n kafka 
```

メッセージの送信:

```
$ kubectl -n kafka run kafka-producer -ti --image=quay.io/strimzi/kafka:0.39.0-kafka-3.6.1 --rm=true --restart=Never -- bin/kafka-console-producer.sh --bootstrap-server my-cluster-kafka-bootstrap:9092 --topic my-topic
If you don't see a command prompt, try pressing enter.
>Hello strimzi!
>Hoge!
```

メッセージの受信:

```
$ kubectl -n kafka run kafka-consumer -ti --image=quay.io/strimzi/kafka:0.39.0-kafka-3.6.1 --rm=true --restart=Never -- bin/kafka-console-consumer.sh --bootstrap-server my-cluster-kafka-bootstrap:9092 --topic my-topic --from-beginning
If you don't see a command prompt, try pressing enter.
Hello strimzi!
Hoge!
```

# kafka-uiのセットアップ

参考: https://gist.github.com/ashishmaurya/e192cdf44fdeeb459f0bfa09877dee22

```
kubectl apply -n kafka -f kafka-ui.yml
minikube service kafka-ui-service -n kafka
```