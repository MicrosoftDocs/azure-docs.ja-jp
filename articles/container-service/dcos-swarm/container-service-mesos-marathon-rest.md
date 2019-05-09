---
title: (非推奨) Marathon REST API を使用した Azure DC/OS クラスターの管理
description: Marathon REST API を使用して、Azure Container Service DC/OS クラスターにコンテナーをデプロイします。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: fd109a72b092e963bc4fda7894bf67f998b7d0c5
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58309809"
---
# <a name="deprecated-dcos-container-management-through-the-marathon-rest-api"></a>(非推奨) Marathon REST API を使用した DC/OS コンテナー管理

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS はクラスター化されたワークロードをデプロイし、スケールするための環境を提供すると共に、基礎となるハードウェアを抽象化します。 DC/OS に加え、コンピューティング ワークロードのスケジュールと実行を管理するフレームワークもあります。 一般的な各種ワークロードに対応したフレームワークはいくつかありますが、このドキュメントでは、Marathon REST API を使用してコンテナーのデプロイを作成し、スケールする方法について説明します。 

## <a name="prerequisites"></a>前提条件

これらの例を見ていく前に、Azure コンテナー サービスで構成された DC/OS クラスターが必要です。 また、このクラスターへのリモート接続も必要です。 これらの項目の詳細については、次の記事を参照してください。

* [Azure コンテナー サービス クラスターのデプロイ](container-service-deployment.md)
* [Azure コンテナー サービス クラスターに接続する](../container-service-connect.md)

## <a name="access-the-dcos-apis"></a>DC/OS API にアクセスする
Azure Container Service クラスターに接続したら、http:\//localhost:local-port を通じて DC/OS と関連の REST API にアクセスできます。 このドキュメントの例では、ポート 80 にトンネリングしていることを前提としています。 たとえば、Marathon エンドポイントには、http:\//localhost/marathon/v2/ で始まる URI でアクセスできます。 

さまざまな API の詳細については、[Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) と [Chronos API](https://mesos.github.io/chronos/docs/api.html) に関する Mesosphere ドキュメントと [Mesos Scheduler API](http://mesos.apache.org/documentation/latest/scheduler-http-api/) に関する Apache ドキュメントを参照してください。

## <a name="gather-information-from-dcos-and-marathon"></a>DC/OS と Marathon から情報を収集する
DC/OS クラスターにコンテナーをデプロイする前に、DC/OS エージェントの名前や状態など、DC/OS クラスターに関する情報を収集します。 DC/OS REST API の `master/slaves` エンドポイントに情報を照会してください。 問題がなければ、DC/OS エージェントの一覧と各エージェントのプロパティが返されます。

```bash
curl http://localhost/mesos/master/slaves
```

次に、Marathon `/apps` エンドポイントを利用し、DC/OS クラスターに対する現在のアプリケーション デプロイを確認します。 これが新しいクラスターであれば、アプリ用の空の配列が表示されます。

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Docker 形式のコンテナーのデプロイ
Docker 形式のコンテナーは、対象のデプロイについて記述された JSON ファイルを利用して、Marathon REST API 経由でデプロイします。 次の例では、クラスター内のプライベート エージェントに Nginx コンテナーがデプロイされます。 

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Docker 形式のコンテナーをデプロイするには、アクセスできる場所に JSON ファイルを保存します。 次に、コンテナーをデプロイするために、次のコマンドを実行します。 JSON ファイルの名前を指定します (この例では `marathon.json`)。

```bash
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

次のように出力されます。

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

ここで、アプリケーションについて Marathon にクエリを実行すると、この新しいアプリケーションが出力に表示されます。

```bash
curl localhost/marathon/v2/apps
```

## <a name="reach-the-container"></a>コンテナーへの到達

クラスター内のプライベート エージェントのいずれかのコンテナーで Nginx が稼働していることを確認できます。 コンテナーを実行しているホストとポートを確認するには、実行中のタスクの Marathon に対してクエリを実行します。 

```bash
curl localhost/marathon/v2/tasks
```

出力で `host` の値 (`10.32.0.x` と同様の IP アドレス) と `ports` の値を調べます。


次に、クラスターの管理 FQDN への SSH ターミナル接続 (トンネル接続ではなく) を行います。 接続後は、次の要求を行って、`host` と `ports` の正しい値を代入します。

```bash
curl http://host:ports
```

Nginx サーバーの出力は、次のようになります。

![コンテナーから Nginx](./media/container-service-mesos-marathon-rest/nginx.png)




## <a name="scale-your-containers"></a>コンテナーのスケール
Marathon API を利用して、アプリケーションのデプロイをスケールアウトまたはスケールインすることができます。 前の例では、アプリケーションの 1 つのインスタンスをデプロイしました。 それをアプリケーションの 3 つのインスタンスにスケールアウトしてみましょう。 これを行うには、次の JSON テキストを使って JSON ファイルを作成し、それをアクセス可能な場所に保存します。

```json
{ "instances": 3 }
```

トンネル接続から次のコマンドを実行して、アプリケーションをスケールアウトします。

> [!NOTE]
> URI は、http:\//localhost/marathon/v2/apps/ に、スケールするアプリケーションの ID が追加されたものになります。 ここで示す Nginx サンプルを使用する場合、URI は http:\//localhost/marathon/v2/apps/nginx になります。

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

最後に、Marathon エンドポイントに対してアプリケーションを照会します。 現在、3 つの Nginx コンテナーがあることがわかります。

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>同等の PowerShell コマンド
これと同じ操作は、Windows システム上で PowerShell コマンドを使って実行できます。

エージェント名やエージェントの状態など、DC/OS クラスターに関する情報を収集するには、次のコマンドを実行します。

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Docker 形式のコンテナーは、意図するデプロイについて記述した JSON ファイルを利用して、Marathon 経由でデプロイします。 次のサンプルでは、Nginx コンテナーがデプロイされます。併せて、DC/OS エージェントのポート 80 がコンテナーのポート 80 に関連付けられます。

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Docker 形式のコンテナーをデプロイするには、アクセスできる場所に JSON ファイルを保存します。 次に、コンテナーをデプロイするために、次のコマンドを実行します。 JSON ファイル (この例では `marathon.json`) にパスを指定します。

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Marathon API を利用して、アプリケーションのデプロイをスケールアウトまたはスケールインすることもできます。 前の例では、アプリケーションの 1 つのインスタンスをデプロイしました。 それをアプリケーションの 3 つのインスタンスにスケールアウトしてみましょう。 これを行うには、次の JSON テキストを使って JSON ファイルを作成し、それをアクセス可能な場所に保存します。

```json
{ "instances": 3 }
```

次のコマンドを実行してアプリケーションをスケールアウトします。

> [!NOTE]
> URI は、http:\//localhost/marathon/v2/apps/ に、スケールするアプリケーションの ID が追加されたものになります。 ここで示す Nginx サンプルを使用する場合、URI は http:\//localhost/marathon/v2/apps/nginx になります。

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>次の手順
* [Mesos HTTP エンドポイントの詳細](https://mesos.apache.org/documentation/latest/endpoints/)
* [Marathon REST API の詳細](https://mesosphere.github.io/marathon/docs/rest-api.html)

