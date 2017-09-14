---
title: "Azure コンテナー レジストリ Webhook | Microsoft Docs"
description: "Azure コンテナー レジストリ Webhook"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acr, azure-container-registry
keywords: "Docker, コンテナー, ACR"
ms.assetid: 
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: d0190f5725671c320d92b897f0dcef7a526a86e3
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="using-azure-container-registry-webhooks---azure-portal"></a>Azure コンテナー レジストリ Webhook - Azure Portal

Docker Hub で公開 Docker イメージを格納するように、Azure コンテナー レジストリではプライベート Docker コンテナー イメージを格納および管理します。 いずれかのレジストリ リポジトリで特定のアクションが発生した場合に、Webhooks を使用してイベントをトリガーします。 Webhooks では、レジストリ レベルでイベントに応答したり、特定のリポジトリ タグに範囲を絞ったりできます。 

背景と概念の詳細については、[概要](./container-registry-intro.md)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件 

- Azure コンテナーで管理されるレジストリ - Azure サブスクリプションで管理対象のコンテナー レジストリを作成します。 たとえば、Azure Portal または Azure CLI 2.0 を使用します。 
- Docker CLI - ローカル コンピューターを Docker ホストとして設定し、Docker CLI コマンドにアクセスするには、Docker エンジンをインストールします。 

## <a name="create-webhook-azure-portal"></a>Azure Portal での Webhook の作成

1. Azure Portal にログインし、Webhook を作成するレジストリに移動します。 

2. コンテナー ブレードで、[Webhook] タブを選択します。 

3. Webhook ブレード ツールバーから [追加] を選択します。 

4. 次の情報を利用して、*[Create Webhook]\(Webhook の作成\)* フォームを完成させます。

| 値 | Description |
|---|---|
| 名前 | Webhook に付与する名前。 小文字と数字のみを 5 ～ 50 文字まで含めることができます。 |
| サービス URI | Webhook が POST 通知を送信する URI。 |
| カスタム ヘッダー | POST 要求と共に渡すヘッダー。 "キー: 値" の形式にする必要があります。 |
| トリガー アクション | Webhook をトリガーするアクション。 現在の Webhook は、イメージに対するプッシュまたは削除アクションによってトリガーできます。 |
| 状態 | Webhook の作成後の状態。 既定で有効です。 |
| Scope | Webhook が動作するスコープです。 既定では、スコープはレジストリ内のすべてのイベントです。 "リポジトリ: タグ" の形式を使用して、スコープを 1 つのリポジトリまたはタグに指定することができます。 |

Webhook フォームの例 :

![DCOS UI](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>Azure CLI での Webhook の作成

Azure CLI を使用して Webhook を作成するには、[az acr webhook create](/cli/azure/acr/webhook#create) コマンドを使用します。

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Webhook をテストする

### <a name="azure-portal"></a>Azure ポータル

コンテナー イメージのプッシュおよび削除アクションで Webhook を使用する前に、**[Ping]** ボタンを使って Webhook をテストできます。 このボタンを使用すると、Ping が指定されたエンドポイントに対して一般的な POST 要求を送信し、応答を記録します。 これは、Webhook が適切に設定されていることを確認するうえで役立ちます。

1. テストする Webhook を選択します。 
2. 上部のツールバーで、"Ping" アクションを選択します。 
3. 要求と応答を確認します。

### <a name="azure-cli"></a>Azure CLI

Azure CLI を使用して ACR Webhook をテストするには、[az acr webhook ping](/cli/azure/acr/webhook#ping) コマンドを使用します。

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

結果を表示するには、[az acr webhook list-events](/cli/azure/acr/webhook#list-events) コマンドを使用します。 

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Webhook の削除

### <a name="azure-portal"></a>Azure ポータル

Azure Portal で Webhook を選択してから [削除] ボタンをクリックすると、各 Webhook を削除できます。

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```
