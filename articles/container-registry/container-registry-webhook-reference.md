---
title: Azure Container Registry webhook スキーマ リファレンス
description: Azure Container Registry の webhook 要求 JSON ペイロードのリファレンスです。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 03/05/2019
ms.author: danlep
ms.openlocfilehash: fcdee2be92f2a3052e2ebbfaab3a2f9cb96e0125
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311599"
---
# <a name="azure-container-registry-webhook-reference"></a>Azure Container Registry webhook リファレンス

コンテナー レジストリに対して特定のアクションが実行されたときにイベントを生成する、コンテナー レジストリの [webhook を構成する](container-registry-webhook.md)ことができます。 たとえば、コンテナー イメージまたは Helm chart がレジストリにプッシュされたとき、または削除されたときにトリガーされる Webhook を有効にします。 webhook がトリガーされると、Azure Container Registry は、イベントに関する情報を含む HTTP または HTTPS 要求を、ユーザーが指定したエンドポイントに対して発行します。 エンドポイントは webhook を処理し、適切に対応できます。

以下のセクションでは、サポートされるイベントによって生成される webhook 要求のスキーマについて詳しく説明します。 イベントに関するセクションには、そのイベントの種類のペイロード スキーマ、要求のペイロードの例、および webhook をトリガーするコマンドの例が含まれます。

Azure Container Registry webhook の構成方法については、「[Azure Container Registry webhook の使用](container-registry-webhook.md)」をご覧ください。

## <a name="webhook-requests"></a>webhook の要求

### <a name="http-request"></a>HTTP 要求

トリガーされた webhook は、ユーザーが webhook を構成するときに指定した URL エンドポイントに対して、HTTP `POST` 要求を行います。

### <a name="http-headers"></a>HTTP ヘッダー

ユーザーが webhook の `Content-Type` カスタム ヘッダーを指定していない場合、webhook 要求には `application/json` の `Content-Type` が含まれます。

ユーザーが webhook に指定したカスタム ヘッダー以外に、他のヘッダーが要求に追加されることはありません。

## <a name="push-event"></a>push イベント

コンテナー イメージがリポジトリにプッシュされるとトリガーされる webhook です。

### <a name="push-event-payload"></a>push イベントのペイロード

|要素|Type|説明|
|-------------|----------|-----------|
|`id`|string|webhook イベントの ID。|
|`timestamp`|DateTime|webhook イベントがトリガーされた日時。|
|`action`|string|webhook イベントをトリガーしたアクション。|
|[target](#target)|複合型|webhook イベントをトリガーしたイベントのターゲット。|
|[request](#request)|複合型|webhook イベントを生成した要求。|

### <a name="target"></a>target

|要素|Type|説明|
|------------------|----------|-----------|
|`mediaType`|string|参照されているオブジェクトの MIME の種類。|
|`size`|Int32|コンテンツのバイト数。 length フィールドと同じです。|
|`digest`|string|コンテンツのダイジェスト。Registry V2 HTTP API 仕様で定義されています。|
|`length`|Int32|コンテンツのバイト数。 size フィールドと同じです。|
|`repository`|string|リポジトリの名前。|
|`tag`|string|イメージ タグの名前。|

### <a name="request"></a>request

|要素|Type|説明|
|------------------|----------|-----------|
|`id`|string|イベントを開始した要求の ID。|
|`host`|string|レジストリ インスタンスの外部からアクセス可能なホスト名。受信した要求の HTTP ホスト ヘッダーで指定されています。|
|`method`|string|イベントを生成した要求メソッド。|
|`useragent`|string|要求のユーザー エージェント ヘッダー。|

### <a name="payload-example-image-push-event"></a>ペイロードの例: イメージの push イベント

```JSON
{
  "id": "cb8c3971-9adc-488b-xxxx-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-xxxx-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

イメージの **push** イベント Webhook をトリガーする [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) コマンドの例:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>chart の push イベント

Helm chart がリポジトリにプッシュされるとトリガーされる Webhook。

### <a name="chart-push-event-payload"></a>chart の push イベントのペイロード

|要素|Type|説明|
|-------------|----------|-----------|
|`id`|string|webhook イベントの ID。|
|`timestamp`|DateTime|webhook イベントがトリガーされた日時。|
|`action`|string|webhook イベントをトリガーしたアクション。|
|[target](#helm_target)|複合型|webhook イベントをトリガーしたイベントのターゲット。|

### <a name="helm_target"></a>target

|要素|Type|説明|
|------------------|----------|-----------|
|`mediaType`|string|参照されているオブジェクトの MIME の種類。|
|`size`|Int32|コンテンツのバイト数。|
|`digest`|string|コンテンツのダイジェスト。Registry V2 HTTP API 仕様で定義されています。|
|`repository`|string|リポジトリの名前。|
|`tag`|string|chart のタグ名。|
|`name`|string|chart の名前。|
|`version`|string|chart のバージョン。|

### <a name="payload-example-chart-push-event"></a>ペイロードの例: chart の push イベント

```JSON
{
  "id": "6356e9e0-627f-4fed-xxxx-d9059b5143ac",
  "timestamp": "2019-03-05T23:45:31.2614267Z",
  "action": "chart_push",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

**chart_push** イベント Webhook をトリガーする [Azure CLI](/cli/azure/acr) コマンドの例:

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>インベントリを削除する

イメージのリポジトリまたはマニフェストが削除されるとトリガーされる Webhook。 タグが削除されたときはトリガーされません。

### <a name="delete-event-payload"></a>delete イベントのペイロード

|要素|Type|説明|
|-------------|----------|-----------|
|`id`|string|webhook イベントの ID。|
|`timestamp`|DateTime|webhook イベントがトリガーされた日時。|
|`action`|string|webhook イベントをトリガーしたアクション。|
|[target](#delete_target)|複合型|webhook イベントをトリガーしたイベントのターゲット。|
|[request](#delete_request)|複合型|webhook イベントを生成した要求。|

### <a name="delete_target"></a> target

|要素|Type|説明|
|------------------|----------|-----------|
|`mediaType`|string|参照されているオブジェクトの MIME の種類。|
|`digest`|string|コンテンツのダイジェスト。Registry V2 HTTP API 仕様で定義されています。|
|`repository`|string|リポジトリの名前。|

### <a name="delete_request"></a> request

|要素|Type|説明|
|------------------|----------|-----------|
|`id`|string|イベントを開始した要求の ID。|
|`host`|string|レジストリ インスタンスの外部からアクセス可能なホスト名。受信した要求の HTTP ホスト ヘッダーで指定されています。|
|`method`|string|イベントを生成した要求メソッド。|
|`useragent`|string|要求のユーザー エージェント ヘッダー。|

### <a name="payload-example-image-delete-event"></a>ペイロードの例: イメージの delete イベント

```JSON
{
    "id": "afc359ce-df7f-4e32-xxxx-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-xxxx-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

**delete** イベント webhook をトリガーする [Azure CLI](/cli/azure/acr) コマンドの例:

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>chart の delete イベント

Helm chart またはリポジトリが削除されるとトリガーされる Webhook。 

### <a name="chart-delete-event-payload"></a>chart の delete イベントのペイロード

|要素|Type|説明|
|-------------|----------|-----------|
|`id`|string|webhook イベントの ID。|
|`timestamp`|DateTime|webhook イベントがトリガーされた日時。|
|`action`|string|webhook イベントをトリガーしたアクション。|
|[target](#chart_delete_target)|複合型|webhook イベントをトリガーしたイベントのターゲット。|

### <a name="chart_delete_target"></a> target

|要素|Type|説明|
|------------------|----------|-----------|
|`mediaType`|string|参照されているオブジェクトの MIME の種類。|
|`size`|Int32|コンテンツのバイト数。|
|`digest`|string|コンテンツのダイジェスト。Registry V2 HTTP API 仕様で定義されています。|
|`repository`|string|リポジトリの名前。|
|`tag`|string|chart のタグ名。|
|`name`|string|chart の名前。|
|`version`|string|chart のバージョン。|

### <a name="payload-example-chart-delete-event"></a>ペイロードの例: chart の delete イベント

```JSON
{
  "id": "338a3ef7-ad68-4128-xxxx-fdd3af8e8f67",
  "timestamp": "2019-03-06T00:10:48.1270754Z",
  "action": "chart_delete",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

**chart_delete** イベント Webhook をトリガーする [Azure CLI](/cli/azure/acr) コマンドの例:

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>次の手順

[Azure Container Registry webhook の使用](container-registry-webhook.md)