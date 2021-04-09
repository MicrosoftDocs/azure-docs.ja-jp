---
title: Azure API Management のセルフホステッド ゲートウェイにローカル メトリックとログを構成する | Microsoft Docs
description: Kubernetes クラスター上の Azure API Management のセルフホステッド ゲートウェイにローカル メトリックとログを構成する方法について説明します
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/01/2021
ms.author: apimpm
ms.openlocfilehash: 2b66663c9ee8033bcb12bfac57964ea0eafecdac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594164"
---
# <a name="configure-local-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Azure API Management のセルフホステッド ゲートウェイにローカル メトリックとログを構成する

この記事では、Kubernetes クラスターに展開された[セルフホステッド ゲートウェイ](./self-hosted-gateway-overview.md)に、ローカル メトリックとログを構成する方法について詳しく説明します。 クラウド メトリックとログを構成する方法については、[この記事](how-to-configure-cloud-metrics-logs.md)を参照してください。 

## <a name="metrics"></a>メトリック
セルフホステッド ゲートウェイでは、メトリックの収集と集計のための統一プロトコルとなっている、[StatsD](https://github.com/statsd/statsd) がサポートされています。 このセクションでは、StatsD を Kubernetes にデプロイし、StatsD を使用してメトリックを出力するようにゲートウェイを構成し、[Prometheus](https://prometheus.io/) を使用してメトリックを監視する手順について説明します。 

### <a name="deploy-statsd-and-prometheus-to-the-cluster"></a>StatsD と Prometheus をクラスターにデプロイする

次に示すのは、セルフホステッド ゲートウェイがデプロイされている Kubernetes クラスターに StatsD と Prometheus をデプロイするための YAML 構成のサンプルです。 また、それぞれに対して [サービス](https://kubernetes.io/docs/concepts/services-networking/service/)も作成します。 セルフホステッド ゲートウェイにより、StatsD サービスにメトリックが公開されます。 そのサービスを介して Prometheus ダッシュボードにアクセスします。   

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: sputnik-metrics-config
data:
  statsd.yaml: ""
  prometheus.yaml: |
    global:
      scrape_interval:     3s
      evaluation_interval: 3s
    scrape_configs:
      - job_name: 'prometheus'
        static_configs:
          - targets: ['localhost:9090']
      - job_name: 'test_metrics'
        static_configs:
          - targets: ['localhost:9102']
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sputnik-metrics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sputnik-metrics
  template:
    metadata:
      labels:
        app: sputnik-metrics
    spec:
      containers:
      - name: sputnik-metrics-statsd
        image: mcr.microsoft.com/aks/hcp/prom/statsd-exporter
        ports:
        - name: tcp
          containerPort: 9102
        - name: udp
          containerPort: 8125
          protocol: UDP
        args:
          - --statsd.mapping-config=/tmp/statsd.yaml
          - --statsd.listen-udp=:8125
          - --web.listen-address=:9102
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      - name: sputnik-metrics-prometheus
        image: mcr.microsoft.com/oss/prometheus/prometheus
        ports:
        - name: tcp
          containerPort: 9090
        args:
          - --config.file=/tmp/prometheus.yaml
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      volumes:
        - name: sputnik-metrics-config-files
          configMap:
            name: sputnik-metrics-config
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-statsd
spec:
  type: NodePort
  ports:
  - name: udp
    port: 8125
    targetPort: 8125
    protocol: UDP
  selector:
    app: sputnik-metrics
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-prometheus
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 9090
    targetPort: 9090
  selector:
    app: sputnik-metrics
```

構成を `metrics.yaml` という名前のファイルに保存し、次のコマンドを使用してすべてをクラスターにデプロイします。

```console
kubectl apply -f metrics.yaml
```

デプロイが完了したら、次のコマンドを実行してポッドが実行されていることを確認します。 使用するポッド名は異なることに注意してください。 

```console
kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
sputnik-metrics-f6d97548f-4xnb7        2/2     Running   0          1m
```

次のコマンドを実行して、サービスが実行されていることを確認します。 StatsD サービスの `CLUSTER-IP` と `PORT` をメモしておきます。後で必要になります。 Prometheus ダッシュボードには、`EXTERNAL-IP` と `PORT` を使用してアクセスできます。

```console
kubectl get services
NAME                         TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE
sputnik-metrics-prometheus   LoadBalancer   10.0.252.72   13.89.141.90    9090:32663/TCP               18h
sputnik-metrics-statsd       NodePort       10.0.41.179   <none>          8125:32733/UDP               18h
```

### <a name="configure-the-self-hosted-gateway-to-emit-metrics"></a>メトリックを出力するようにセルフホステッド ゲートウェイを構成する

StatsD と Prometheus の両方がデプロイされたので、StatsD を使用してメトリックの生成を開始するようにセルフホステッド ゲートウェイの構成を更新できます。 この機能は、オプションが追加された、セルフホステッド ゲートウェイのデプロイの ConfigMap で `telemetry.metrics.local` キーを使用して有効または無効にすることができます。 次に、使用可能なオプションの詳細を示します。

| フィールド  | Default | 説明 |
| ------------- | ------------- | ------------- |
| telemetry.metrics.local  | `none` | StatsD を使用したログ記録を有効にします。 値は `none`、`statsd` が可能です。 |
| telemetry.metrics.local.statsd.endpoint  | 該当なし | StatsD エンドポイントを指定します。 |
| telemetry.metrics.local.statsd.sampling  | 該当なし | メトリックのサンプリング レートを指定します。 値は、0 - 1 の間にできます。 例: `0.5`|
| telemetry.metrics.local.statsd.tag-format  | 該当なし | StatsD エクスポーターの[タグ付け形式](https://github.com/prometheus/statsd_exporter#tagging-extensions)。 値は、`none`、`librato`、`dogStatsD`、`influxDB` が可能です。 |

サンプル構成を次に示します。

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.metrics.local: "statsd"
        telemetry.metrics.local.statsd.endpoint: "10.0.41.179:8125"
        telemetry.metrics.local.statsd.sampling: "1"
        telemetry.metrics.local.statsd.tag-format: "dogStatsD"
```

上記の構成でセルフホステッド ゲートウェイのデプロイの YAML ファイルを更新し、次のコマンドを使用して変更を適用します。 

```console
kubectl apply -f <file-name>.yaml
 ```

最新の構成変更を取得するには、次のコマンドを使用してゲートウェイのデプロイを再開します。

```console
kubectl rollout restart deployment/<deployment-name>
```

### <a name="view-the-metrics"></a>メトリックを表示する

すべてのデプロイと構成が完了したので、セルフホステッド ゲートウェイで StatsD を通じてメトリックが報告されるはずです。 Prometheus により StatsD からメトリックが取得されます。 Prometheus サービスの `EXTERNAL-IP` と `PORT` を使用して、Prometheus ダッシュボードにアクセスします。 

セルフホステッド ゲートウェイを使用していくつか API 呼び出しを行います。すべてが正しく構成されている場合は、以下のメトリックが表示されるはずです。

| メトリック  | 説明 |
| ------------- | ------------- |
| Requests  | 期間内の API 要求の数 |
| DurationInMS | ゲートウェイが要求を受信した時点から、応答全体が送信された時点までのミリ秒数 |
| BackendDurationInMS | バックエンドの IO 全体 (接続バイト、送信バイト、受信バイト) に費やされたミリ秒数  |
| ClientDurationInMS | クライアントの IO 全体 (接続バイト、送信バイト、受信バイト) に費やされたミリ秒数  |

## <a name="logs"></a>ログ

セルフホステッド ゲートウェイでは、既定でログが `stdout` と `stderr` に出力されます。 次のコマンドを使用すると、ログを簡単に表示できます。

```console
kubectl logs <pod-name>
```

セルフホステッド ゲートウェイが Azure Kubernetes Service にデプロイされている場合は、[コンテナーに対する Azure Monitor](../azure-monitor/containers/container-insights-overview.md) を有効にして、ワークロードから `stdout` と `stderr` を収集し、Log Analytics でログを表示することができます。 

セルフホステッド ゲートウェイでは、`localsyslog`、`rfc5424`、`journal` などの多数のプロトコルもサポートされています。 次の表に、サポートされているすべてのオプションの概要を示します。 

| フィールド  | Default | 説明 |
| ------------- | ------------- | ------------- |
| telemetry.logs.std  | `text` | 標準ストリームへのログ記録を有効にします。 値は `none`、`text`、`json` が可能です。 |
| telemetry.logs.local  | `none` | ローカル ログ記録を有効にします。 値は `none`、`auto`、`localsyslog`、`rfc5424`、`journal` が可能です。  |
| telemetry.logs.local.localsyslog.endpoint  | 該当なし | localsyslog エンドポイントを指定します。  |
| telemetry.logs.local.localsyslog.facility  | 該当なし | localsyslog [ファシリティ コード](https://en.wikipedia.org/wiki/Syslog#Facility)を指定します。 例: `7` 
| telemetry.logs.local.rfc5424.endpoint  | 該当なし | rfc5424 エンドポイントを指定します。  |
| telemetry.logs.local.rfc5424.facility  | 該当なし | [rfc5424](https://tools.ietf.org/html/rfc5424) あたりのファシリティ コードを指定します。 例: `7`  |
| telemetry.logs.local.journal.endpoint  | 該当なし | ジャーナル エンドポイントを指定します。  |

ローカル ログのサンプル構成を次に示します。

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.logs.std: "text"
        telemetry.logs.local.localsyslog.endpoint: "/dev/log"
        telemetry.logs.local.localsyslog.facility: "7"
```
 
## <a name="next-steps"></a>次のステップ

* セルフホステッド ゲートウェイの詳細については、[Azure API Management のセルフホステッド ゲートウェイの概要](self-hosted-gateway-overview.md)に関する記事を参照してください
* [クラウドでログを構成して永続化する](how-to-configure-local-metrics-logs.md)ことについて学習する
