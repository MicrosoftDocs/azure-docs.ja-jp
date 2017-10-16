---
title: "Azure Container Instances のトラブルシューティング"
description: "Azure Container Instances に関する問題のトラブルシューティングを行う方法について説明します"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: ff6da0ce95d0405714602c3872da34a2bff344d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>Azure Container Instances でのデプロイに関する問題をトラブルシューティングする

この記事では、Azure Container Instances にコンテナーをデプロイする際の問題をトラブルシューティングする方法を示します。 また、発生する可能性があるいくつかの一般的な問題についても説明します。

## <a name="getting-diagnostic-events"></a>診断イベントの取得

アプリケーション コードからコンテナー内のログを表示するには、[az container logs](/cli/azure/container#logs) コマンドを使用できます。 ただし、コンテナーが正常にデプロイされていない場合は、Azure Container Instances のリソース プロバイダーによって提供される診断情報を見直す必要があります。 コンテナーのイベントを表示するには、次のコマンドを実行します。

```azurecli-interactive
az container show -n mycontainername -g myresourcegroup
```

出力には、コンテナーの主要プロパティとデプロイ イベントが含まれます。

```bash
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "microsoft/aci-helloworld",
      ...

      "events": [
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:52+00:00",
        "lastTimestamp": "2017-08-03T22:12:52+00:00",
        "message": "Pulling: pulling image \"microsoft/aci-helloworld\"",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Pulled: Successfully pulled image \"microsoft/aci-helloworld\"",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Created: Created container with id 61602059d6c31529c27609ef4ec0c858b0a96150177fa045cf944d7cf8fbab69",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Started: Started container with id 61602059d6c31529c27609ef4ec0c858b0a96150177fa045cf944d7cf8fbab69",
        "type": "Normal"
      }
    ],
    "name": "helloworld",
      "ports": [
        {
          "port": 80
        }
      ],
    ...
  ]
}
```

## <a name="common-deployment-issues"></a>一般的なデプロイ問題

デプロイで発生するほとんどのエラーの主な原因となる少数の一般的な問題があります。

### <a name="unable-to-pull-image"></a>イメージをプルできない

Azure Container Instances は、最初にイメージをプルすることができなかった場合、最終的に失敗するまで一定期間再試行します。 イメージをプルできない場合は、次のようなイベントが表示されます。

```bash
"events": [
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:31+00:00",
    "lastTimestamp": "2017-08-03T22:19:31+00:00",
    "message": "Pulling: pulling image \"microsoft/aci-hellowrld\"",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:32+00:00",
    "lastTimestamp": "2017-08-03T22:19:32+00:00",
    "message": "Failed: Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image microsoft/aci-hellowrld:latest not found",
    "type": "Warning"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:33+00:00",
    "lastTimestamp": "2017-08-03T22:19:33+00:00",
    "message": "BackOff: Back-off pulling image \"microsoft/aci-hellowrld\"",
    "type": "Normal"
  }
]
```

この問題を解決するには、コンテナーを削除し、デプロイをやり直します。このとき、細心の注意を払って、イメージ名を正しく入力します。

### <a name="container-continually-exits-and-restarts"></a>コンテナーが絶えず終了して再起動する

現時点では、Azure Container Instances は、実行時間の長いサービスのみをサポートします。 コンテナーの実行が完了して終了した場合、コンテナーは自動的に再起動してもう一度実行されます。 これが発生した場合は、次のようなイベントが表示されます。 コンテナーが正常に起動した後、すぐに再起動していることに注意してください。 Container Instances API には、特定のコンテナーが何回再起動されているかを示す `retryCount` プロパティが含まれています。

```bash
"events": [
  {
    "count": 5,
    "firstTimestamp": "2017-08-03T22:21:55+00:00",
    "lastTimestamp": "2017-08-03T22:23:22+00:00",
    "message": "Pulling: pulling image \"alpine\"",
    "type": "Normal"
  },
  {
    "count": 5,
    "firstTimestamp": "2017-08-03T22:21:57+00:00",
    "lastTimestamp": "2017-08-03T22:23:23+00:00",
    "message": "Pulled: Successfully pulled image \"alpine\"",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:57+00:00",
    "lastTimestamp": "2017-08-03T22:21:57+00:00",
    "message": "Created: Created container with id ad2bf9bc51761c5f935260b4bab53b164d52d9cbc045b16afcb26fb4d14d0a70",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:57+00:00",
    "lastTimestamp": "2017-08-03T22:21:57+00:00",
    "message": "Started: Started container with id ad2bf9bc51761c5f935260b4bab53b164d52d9cbc045b16afcb26fb4d14d0a70",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:58+00:00",
    "lastTimestamp": "2017-08-03T22:21:58+00:00",
    "message": "Created: Created container with id 7687b9bd15dc01731fa66fc45f6f0241495600602dd03841e559453245e7f70b",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:58+00:00",
    "lastTimestamp": "2017-08-03T22:21:58+00:00",
    "message": "Started: Started container with id 7687b9bd15dc01731fa66fc45f6f0241495600602dd03841e559453245e7f70b",
    "type": "Normal"
  },
  {
    "count": 13,
    "firstTimestamp": "2017-08-03T22:21:59+00:00",
    "lastTimestamp": "2017-08-03T22:24:36+00:00",
    "message": "BackOff: Back-off restarting failed container",
    "type": "Warning"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:22:13+00:00",
    "lastTimestamp": "2017-08-03T22:22:13+00:00",
    "message": "Created: Created container with id 72e347e891290e238135e4a6b3078748ca25a1275dbbff30d8d214f026d89220",
    "type": "Normal"
  },
  ...
```

> [!NOTE]
> Linux ディストリビューションのほとんどのコンテナー イメージは、既定のコマンドとして、bash などのシェルを設定します。 独自のシェルは実行時間の長いサービスではないため、これらのコンテナーはすぐに終了し、再起動ループ状態になります。

### <a name="container-takes-a-long-time-to-start"></a>コンテナーの起動に時間がかかる

コンテナーが起動するまで時間がかかるが、最終的に起動する場合は、コンテナー イメージのサイズを調べることから始めてください。 Azure Container Instances は、要求に応じてコンテナー イメージをプルするため、起動時間はそのサイズと直接的に関係しています。

コンテナー イメージのサイズは、Docker CLI を使用して表示できます。

```bash
docker images
```

出力:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
microsoft/aci-helloworld               latest              7f78509b568e        13 days ago         68.1MB
```

イメージのサイズを小さくしておくための鍵は、最終イメージに実行時に不要なものが含まれないようにすることです。 これを行う 1 つの方法は、[マルチステージ ビルド](https://docs.docker.com/engine/userguide/eng-image/multistage-build/)を使用することです。 マルチステージ ビルドを使用すると、最終イメージにはアプリケーションに必要な成果物のみが含まれ、ビルド時に必要であった余分なコンテンツは含まれないようにすることを簡単に実行できます。

コンテナーの起動時に発生するイメージ プルの影響を軽減する他の方法は、Azure Container Instances を使用する予定のリージョンと同じリージョン内の Azure Container Registry を使用してコンテナー イメージをホストすることです。 これにより、コンテナー イメージを伝送する必要があるネットワーク パスが短縮され、ダウンロード時間が大幅に短くなります。

### <a name="resource-not-available-error"></a>リソース使用不可エラー

Azure ではリージョンによってリソースの読み込みに変化があるため、コンテナー インスタンスをデプロイしようとすると、以下のエラーが表示される場合があります。

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

このエラーは、デプロイを試行しているリージョンで高負荷になっているため、コンテナーに指定されたリソースが、その時点では割り当てできないことを示しています。 以下に示す 1 つ以上の軽減策の手順を使用して、問題を解決してください。

* コンテナーのデプロイ設定が、[Azure Container Instances のリージョンでの利用可否](container-instances-region-availability.md)に関する記事で定義されているパラメーター内に収まっていることを確認する
* コンテナーに低い CPU およびメモリ設定を指定する。
* 別の Azure リージョンにデプロイする
* 後でデプロイする
