---
title: "Marathon REST API を使用した Azure DC/OS クラスターの管理 | Microsoft Docs"
description: "Marathon REST API を使用して、Azure Container Service DC/OS クラスターにコンテナーをデプロイします。"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker、コンテナー、マイクロ サービス、Mesos、Azure"
ms.assetid: c7175446-4507-4a33-a7a2-63583e5996e3
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: b2b969500d20d0c840f201ed2cf13a6f2ab38ee5
ms.openlocfilehash: 719f1ea6a6f51d4a787f0465a4bbadb1a6057a8b


---
# <a name="dcos-container-management-through-the-marathon-rest-api"></a>Marathon REST API を使用した DC/OS コンテナー管理
DC/OS はクラスター化されたワークロードをデプロイし、スケールするための環境を提供すると共に、基礎となるハードウェアを抽象化します。 DC/OS に加え、コンピューティング ワークロードのスケジュールと実行を管理するフレームワークもあります。 一般的な各種ワークロードに対応したフレームワークがあるものの、このドキュメントでは、Marathon を使ってコンテナー デプロイを作成し、スケールする方法について説明します。 

## <a name="prerequisites"></a>前提条件

これらの例を見ていく前に、Azure コンテナー サービスで構成された DC/OS クラスターが必要です。 また、このクラスターへのリモート接続も必要です。 これらの項目の詳細については、次の記事を参照してください。

* [Azure コンテナー サービス クラスターのデプロイ](container-service-deployment.md)
* [Azure コンテナー サービス クラスターに接続する](container-service-connect.md)

## <a name="access-the-dcos-apis"></a>DC/OS API にアクセスする
Azure Container Service クラスターに接続したら、http://localhost:local-port を通じて DC/OS と関連の REST API にアクセスできます。 このドキュメントの例では、ポート 80 にトンネリングしていることを前提としています。 たとえば、Marathon エンドポイントには、`http://localhost/marathon/v2/` で始まる URI でアクセスできます。 

さまざまな API の詳細については、[Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) と [Chronos API](https://mesos.github.io/chronos/docs/api.html) に関する Mesosphere ドキュメントと [Mesos Scheduler API](http://mesos.apache.org/documentation/latest/scheduler-http-api/) に関する Apache ドキュメントを参照してください。

## <a name="gather-information-from-dcos-and-marathon"></a>DC/OS と Marathon から情報を収集する
DC/OS クラスターにコンテナーをデプロイする前に、DC/OS エージェントの名前や現在の状態など、DC/OS クラスターに関する情報を収集します。 DC/OS REST API の `master/slaves` エンドポイントに情報を照会してください。 問題がなければ、DC/OS エージェントの一覧と各エージェントのプロパティが返されます。

```bash
curl http://localhost/mesos/master/slaves
```

次に、Marathon `/apps` エンドポイントを利用し、DC/OS クラスターに対する現在のアプリケーション デプロイを確認します。 これが新しいクラスターであれば、アプリ用の空の配列が表示されます。

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Docker 形式のコンテナーのデプロイ
Docker 形式のコンテナーは、意図するデプロイについて記述した JSON ファイルを利用して、Marathon 経由でデプロイします。 次のサンプルでは、Nginx コンテナーがデプロイされます。併せて、DC/OS エージェントのポート 80 がコンテナーのポート 80 に関連付けられます。 また、`acceptedResourceRoles` プロパティが `slave_public` に設定されていることに注目してください。 この設定により、パブリックに公開されたエージェント スケール セット内のエージェントにコンテナーがデプロイされます。

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
    "acceptedResourceRoles": [
    "slave_public"
  ],
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
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

`http://<containerServiceName>agents.<region>.cloudapp.azure.com` でエージェント プールの完全修飾ドメイン名に HTTP 要求を行うことによって、Nginx が実行されていることを確認できます。

## <a name="scale-your-containers"></a>コンテナーのスケール
Marathon API を利用して、アプリケーションのデプロイをスケールアウトまたはスケールインすることができます。 前の例では、アプリケーションの&1; つのインスタンスをデプロイしました。 それをアプリケーションの&3; つのインスタンスにスケールアウトしてみましょう。 これを行うには、次の JSON テキストを使って JSON ファイルを作成し、それをアクセス可能な場所に保存します。

```json
{ "instances": 3 }
```

次のコマンドを実行してアプリケーションをスケールアウトします。

> [!NOTE]
> URI は、http://localhost/marathon/v2/apps/ に、スケールするアプリケーションの ID が追加されたものになります。 ここで示す Nginx サンプルを使用する場合、URI は http://localhost/marathon/v2/apps/nginx になります。
> 
> 

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
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Docker 形式のコンテナーをデプロイするには、アクセスできる場所に JSON ファイルを保存します。 次に、コンテナーをデプロイするために、次のコマンドを実行します。 JSON ファイルの名前を指定します (この例では `marathon.json`)。

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Marathon API を利用して、アプリケーションのデプロイをスケールアウトまたはスケールインすることもできます。 前の例では、アプリケーションの&1; つのインスタンスをデプロイしました。 それをアプリケーションの&3; つのインスタンスにスケールアウトしてみましょう。 これを行うには、次の JSON テキストを使って JSON ファイルを作成し、それをアクセス可能な場所に保存します。

```json
{ "instances": 3 }
```

次のコマンドを実行してアプリケーションをスケールアウトします。

> [!NOTE]
> URI は、http://localhost/marathon/v2/apps/ に、スケールするアプリケーションの ID が追加されたものになります。 ここで示す Nginx サンプルを使用する場合、URI は http://localhost/marathon/v2/apps/nginx になります。
> 
> 

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>次のステップ
* [Mesos HTTP エンドポイントの詳細](http://mesos.apache.org/documentation/latest/endpoints/)
* [Marathon REST API の詳細](https://mesosphere.github.io/marathon/docs/rest-api.html)




<!--HONumber=Feb17_HO1-->


