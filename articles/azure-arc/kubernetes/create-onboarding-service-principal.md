---
title: Azure Arc 対応 Kubernetes 用のオンボード サービス プリンシパルを作成する
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: 'Azure Arc 対応オンボード サービス プリンシパルの作成 '
keywords: Kubernetes, Arc, Azure, コンテナー
ms.openlocfilehash: 610b355073473f0e492350753a523b7943666f13
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121747"
---
# <a name="create-an-onboarding-service-principal-for-azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes 用のオンボード サービス プリンシパルを作成する

## <a name="overview"></a>概要

限定された権限のロールの割り当てを持つサービス プリンシパルを使用して、Kubernetes クラスターを Azure Arc に接続できます。 この機能は、Azure Pipelines や GitHub Actions などの継続的インテグレーションおよび継続的デプロイ (CI/CD) パイプラインで役立ちます。

以下の手順を行って、Kubernetes クラスターを Azure Arc に接続するためにサービス プリンシパルを使用する方法について説明します。

## <a name="create-a-new-service-principal"></a>新しいサービス プリンシパルを作成する

Azure Active Directory テナントに固有のわかりやすい名前で、新しいサービス プリンシパルを作成します。

```console
az ad sp create-for-RBAC --skip-assignment --name "https://azure-arc-for-k8s-onboarding"
```

**出力:**

```console
{
  "appId": "22cc2695-54b9-49c1-9a73-2269592103d8",
  "displayName": "azure-arc-for-k8s-onboarding",
  "name": "https://azure-arc-for-k8s-onboarding",
  "password": "09d3a928-b223-4dfe-80e8-fed13baa3b3d",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

## <a name="assign-permissions"></a>アクセス許可の割り当て

新しく作成したサービス プリンシパルに "Kubernetes クラスター - Azure Arc のオンボード" ロールを割り当てます。 アクセス許可が制限されたこの組み込みの Azure ロールでは、プリンシパルがクラスターを Azure に登録することだけが許可されます。 この割り当て済みのロールを持つプリンシパルは、サブスクリプション内の他のクラスターまたはリソースを更新、削除、変更することはできません。

機能が制限されているため、お客様はこのプリンシパルを再利用して、複数のクラスターを簡単にオンボードできます。

ロールを割り当てるときに適切な `--scope` 引数を渡すことで、アクセス許可をさらに制限できます。 これにより、管理者はクラスターの登録をサブスクリプションまたはリソース グループのスコープに制限できます。 次のシナリオは、さまざまな `--scope` パラメーターでサポートされています。

| リソース  | `scope` 引数| 結果 |
| ------------- | ------------- | ------------- |
| サブスクリプション | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | サービス プリンシパルで、そのサブスクリプションの下にある任意のリソース グループにクラスターを登録できます。 |
| リソース グループ | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | サービス プリンシパルで、リソース グループ `myGroup` に __のみ__、クラスターを登録できます。 |

```console
az role assignment create \
    --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 \      # this is the id for the built-in role
    --assignee 22cc2695-54b9-49c1-9a73-2269592103d8 \  # use the appId from the new SP
    --scope /subscriptions/<<SUBSCRIPTION_ID>>         # apply the appropriate scope
```

**出力:**

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleAssignments/fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "name": "fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "principalId": "ddb0ddb4-ba84-4cde-b936-affc272a4b90",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "scope": "/subscriptions/<<SUBSCRIPTION_ID>>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="use-service-principal-with-the-azure-cli"></a>Azure CLI でサービス プリンシパルを使用する

次のコマンドを使用して、新しく作成されたサービス プリンシパルを参照します。

```azurecli
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>次のステップ

[Azure Policy を使用して](./use-azure-policy.md)クラスター構成を管理します。
