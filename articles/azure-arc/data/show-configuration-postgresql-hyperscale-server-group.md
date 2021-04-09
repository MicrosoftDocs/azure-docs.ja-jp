---
title: Arc 対応 PostgreSQL Hyperscale サーバー グループの構成を表示する
titleSuffix: Azure Arc enabled data services
description: Arc 対応 PostgreSQL Hyperscale サーバー グループの構成を表示する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a268cd6b2fa3da6846554e3d1b170298abec7f18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93279403"
---
# <a name="show-the-configuration-of-an-arc-enabled-postgresql-hyperscale-server-group"></a>Arc 対応 PostgreSQL Hyperscale サーバー グループの構成を表示する

この記事では、サーバー グループの構成を表示する方法について説明します。 そのために、あなたが自問する可能性がある質問をいくつか予測し、それらに答えます。 場合によっては、複数の有効な答えがあることがあります。 この記事では、最も一般的または有益なものを紹介します。 それらの質問をテーマ別にグループ化します。

- Kubernetes の観点から
- Azure Arc 対応データ サービスの観点から

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-a-kubernetes-point-of-view"></a>Kubernetes の観点から

### <a name="how-many-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale で使用されるポッドの数はいくつですか?

Postgres の種類の Kubernetes リソースを一覧表示します。 次のコマンドを実行します。

```console
kubectl get postgresqls [-n <namespace name>]
```

このコマンドの出力では、作成されたサーバー グループの一覧が表示されます。 それぞれにポッド数が示されます。 次に例を示します。

```output
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      6h34m
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
```

この例では、2 つのサーバー グループが作成され、それぞれが 3 つのポッド (1 コーディネーター + 2 ワーカー) 上で実行されていることが示されています。 つまり、この Azure Arc データ コントローラーに作成されたサーバー グループでは、6 つのポッドが使用されています。

### <a name="what-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループで使用されているポットは何ですか?

次を実行します。

```console
kubectl get pods [-n <namespace name>]
```

これにより、ポッドの一覧が返されます。 サーバー グループによって使用されているポッドが、それらのサーバー グループに付けた名前に基づいて表示されます。 次に例を示します。

```console 
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-vdltm   1/1     Running   0          6d8h
control-h6kc9        2/2     Running   0          6d8h
controldb-0          2/2     Running   0          6d8h
controlwd-96sbn      1/1     Running   0          6d8h
logsdb-0             1/1     Running   0          6d8h
logsui-7wkg2         1/1     Running   0          6d8h
metricsdb-0          1/1     Running   0          6d8h
metricsdc-28ffl      1/1     Running   0          6d8h
metricsui-k7qsh      1/1     Running   0          6d8h
mgmtproxy-gd84z      2/2     Running   0          6d8h
postgres01-0         3/3     Running   0          6h50m
postgres01-1         3/3     Running   0          6h50m
postgres01-2         3/3     Running   0          6h50m
postgres02-0         3/3     Running   0          22h
postgres02-1         3/3     Running   0          22h
postgres02-2         3/3     Running   0          22h
```

この例で、作成される 2 つのサーバー グループがホストされる 6 つのポッドは次のようになります。
- `postgres01-0`
- `postgres01-1`
- `postgres01-2`
- `postgres02-0`
- `postgres02-1`
- `postgres02-2`  

### <a name="what-server-group-pod-is-used-for-what-role-the-server-group"></a>どのサーバー グループ ポッドがどのサーバー グループのロールに使用されていますか?

`-0` でサフィックスが付けられたポッド名はすべて、コーディネーター ノードを表します。 1 以上の `-x` でサフィックスが付けられたノード名はすべて、ワーカー ノードです。 上の例について説明します。
- コーディネーターは、`postgres01-0`、`postgres02-0` です
- ワーカーは `postgres01-2`、`postgres01-2`、`postgres02-1`、`postgres02-2` です

### <a name="what-is-the-status-of-the-pods"></a>ポッドの状態は何ですか?

`kubectl get pods` を実行して、`STATUS` 列を確認します

### <a name="what-persistent-volume-claims-pvcs-are-being-used"></a>どのような永続ボリューム要求 (PVC) が使用されていますか? 

使用される PVC と、どれがデータ、ログ、およびバックアップに使用されるかを理解するには、次を実行します。 

```console
kubectl get pvc [-n <namespace name>]
```

既定で、PVC の名前のプレフィックスはその使用法を示しています。

- `backups-`: バックアップに使用される PVC です
- `data-`: データ ファイルに使用される PVC です
- `logs-`: トランザクション ログ/WAL ファイルに使用される PVC です

次に例を示します。

```output
NAME                                            STATUS   VOLUME              CAPACITY   ACCESS MODES   STORAGECLASS    AGE
backups-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    local-pv-485e37db   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    local-pv-9d3d4a15   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    local-pv-7b8dd819   1938Gi     RWO            local-storage   6d6h
...
data-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-3c1a8cc5   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-8303ab19   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-55572fe6   1938Gi     RWO            local-storage   6d6h
...
logs-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-5e852b76   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-55d309a7   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-5ccd02e6   1938Gi     RWO            local-storage   6d6h
...
```


## <a name="from-an-azure-arc-enabled-data-services-point-of-view"></a>Azure Arc 対応データ サービスの観点から:

* Arc データ コントローラーに作成されるサーバー グループの数はいくつですか?
* それらの名前は何ですか?
* それらが使用するワーカー ノードの数はいくつですか?
* それらが実行する Postgres のバージョンは何ですか?

次のいずれかのコマンドを使用します。
- **Kubectl を使用する:**

   ```console
   kubectl get postgresqls [-n <namespace name>]
   ``` 

   たとえば、次の出力が生成されます。ここで各行が `servergroup` です。 下の表示の名前の構造は、次のように形成されています。

   `<Name-Of-Custom-Resource-Definition>`/`<Server-Group-Name>`

   ```output
   NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
   postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      7h15m
   postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
   ```

   上記の出力は、Postgres バージョン 12 の 2 つのサーバー グループを示しています。 代わりに、バージョンが Postgres 11 の場合、CRD の名前は postgresql-11.arcdata.microsoft.com になります。

   それらはそれぞれ、3 つのノード/ポッド(1 コーディネーターと 2 ワーカー) で実行されます。

- **azdata を使用する:**

次のコマンドを実行します。 出力には、kubectl で示される同様の情報が示されます。

   ```console
   azdata arc postgres server list

   `output
   Name        State    Workers
   ----------  -------  ---------
   postgres01  Ready    2
   postgres02  Ready    2
   ```


### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-group"></a>使用されているメモリと仮想コアの量はどれくらいですか、およびグループに対してどのような拡張機能が作成されましたか?

次のコマンドのいずれかを実行します

**Kubectl を使用する:**

Kubectl を使用して Postgres リソースを記述します。 これを行うには、その種類 (先述のように、対応する Postgres バージョンの Kubernetes リソースの名前 (CRD)) とサーバー グループの名前が必要です。
このコマンドの一般的な形式は次のとおりです。

```console
kubectl describe <CRD name>/<server group name> [-n <namespace name>]
```

次に例を示します。

```console
kubectl describe postgresql-12/postgres02
```

このコマンドによって、サーバー グループの構成が示されます。

```output
Name:         postgres02
Namespace:    arc
Labels:       <none>
Annotations:  <none>
API Version:  arcdata.microsoft.com/v1alpha1
Kind:         postgresql-12
Metadata:
  Creation Timestamp:  2020-08-31T21:01:07Z
  Generation:          10
  Resource Version:    569516
  Self Link:           /apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02
  UID:                 8a9cd118-361b-4a2e-8a9d-5f9257bf6abb
Spec:
  Engine:
    Extensions:
      Name:  citus
      Name:  pg_stat_statements
  Scale:
    Workers:  2
  Scheduling:
    Default:
      Resources:
        Limits:
          Cpu:     4
          Memory:  1024Mi
        Requests:
          Cpu:     1
          Memory:  512Mi
  Service:
    Type:  NodePort
  Storage:
    Data:
      Class Name:  local-storage
      Size:        5Gi
    Logs:
      Class Name:  local-storage
      Size:        5Gi
Status:
  External Endpoint:  10.0.0.4:31066
  Ready Pods:         3/3
  State:              Ready
Events:               <none>
```

>[!NOTE]
>2020 年 10 月のリリースより前では、前の例の `Workers` は `Shards` でした。 詳細については、「[リリースノート - Azure Arc 対応データ サービス (プレビュー)](release-notes.md)」を参照してください。

先述の `servergroup` の説明のいくつかの特定の重要ポイントに注目してみましょう。 それにより、このサーバー グループについて何がわかりますか?

- これは Postgres のバージョン 12 です。 
   > ```json
   > Kind:         `postgresql-12`
   > ```
- 2020 年 8 月中に作成されました。
   > ```json
   > Creation Timestamp:  `2020-08-31T21:01:07Z`
   > ```
- このサーバー グループには、2 つの Postgres 拡張 `citus` と `pg_stat_statements` が作成されました。
   > ```json
   > Engine:
   >    Extensions:
   >      Name:  `citus`
   >      Name:  `pg_stat_statements`
   > ```
- それは 2 つのワーカー ノードを使用しています
   > ```json
   > Scale:
   >    Workers:  `2`
   > ```
- ノードあたり 1 CPU/仮想コアと 512 MB の RAM の使用が保証されています。 4 つ以上の CPU/仮想コアと 1024 MB のメモリを使用します。
   > ```json
   > Scheduling:
   >    Default: 
   >      Resources:
   >        Limits:
   >          Cpu:     4
   >          Memory:  1024Mi
   >        Requests:
   >          Cpu:     1
   >          Memory:  512Mi
   > ```
 - クエリに使用でき、問題ありません。 すべてのノードが稼働しています。
   > ```json
   > Status:
   >  ...
   >  Ready Pods:         3/3
   >  State:              Ready
   > ```

**azdata を使用する:**

コマンドの一般的な形式は次のとおりです。

```console
azdata arc postgres server show -n <server group name>
```

次に例を示します。

```console
azdata arc postgres server show -n postgres02
```

kubectl によって返されるものとよく似た形式と内容で、以下の出力が返されます。

```console
{
  "apiVersion": "arcdata.microsoft.com/v1alpha1",
  "kind": "postgresql-12",
  "metadata": {
    "creationTimestamp": "2020-08-31T21:01:07Z",
    "generation": 10,
    "name": "postgres02",
    "namespace": "arc",
    "resourceVersion": "569516",
    "selfLink": "/apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02",
    "uid": "8a9cd118-361b-4a2e-8a9d-5f9257bf6abb"
  },
  "spec": {
    "engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_stat_statements"
        }
      ]
    },
    "scale": {
      "workers": 2
    },
    "scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "1",
            "memory": "512Mi"
          }
        }
      }
    },
    "service": {
      "type": "NodePort"
    },
    "storage": {
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
  },
  "status": {
    "externalEndpoint": "10.0.0.4:31066",
    "readyPods": "3/3",
    "state": "Ready"
  }
}
```

## <a name="next-steps"></a>次のステップ
- [Azure Arc 対応 PostgreSQL Hyperscale の概念を確認する](concepts-distributed-postgres-hyperscale.md)
- [サーバー グループをスケールアウト (ワーカーノードを追加) する方法について確認する](scale-out-postgresql-hyperscale-server-group.md)
- [サーバー グループのスケールアップ/スケールダウン (メモリや仮想コアの増減) の方法について確認する](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- [ストレージ構成について確認する](storage-configuration.md)
- [データベース インスタンスを監視する方法を確認する](monitor-grafana-kibana.md)
- [Azure Arc 対応の PostgreSQL Hyperscale サーバー グループで PostgreSQL 拡張機能を使用する](using-extensions-in-postgresql-hyperscale-server-group.md)
- [Azure Arc 対応の PostgreSQL Hyperscale サーバー グループのセキュリティを構成する](configure-security-postgres-hyperscale.md)
