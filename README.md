# nginxlog-to-fluentd

* コンテナネットワークを作成する
```sh
docker network create fluentd
```

*  fluentdコンテナを起動する。ホストネットワークにポートを転送しなくても良いかもしれない。カレント＄pwdのdataディレクトリをマウント。

```sh
docker run -d --rm --network fluentd -p 24224:24224 -p 24224:24224/udp -v $(pwd)/data:/fluentd/log --name fluentd fluent/fluentd:v1.3-debian-1
```

* nginxコンテナを起動する。Dockerのログドライバにfluentdドライバを指定。宛先fluentdの指定、ログに付くタグ指定をする
```sh
docker run -itd --rm --network fluentd -p 80:80 --log-driver=fluentd --log-opt fluentd-address=172.18.0.2:24224 --log-opt tag="docker.{{.Name}}" --name nginx nginx
```

### 送信するfluentdを作るならば
```sh
docker run -d --rm -v ./conf:/fluentd/etc --network fluentd --name fluentd fluent/fluentd:v1.3-debian-1 fluentd -c /fluentd/etc/td-agent.conf
```
