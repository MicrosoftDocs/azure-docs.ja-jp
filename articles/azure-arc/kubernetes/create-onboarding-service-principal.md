---
title: Azure Arc 対応オンボード サービス プリンシパルの作成 (プレビュー)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: 'Azure Arc 対応オンボード サービス プリンシパルを作成します '
keywords: Kubernetes, Arc, Azure, コンテナー
ms.openlocfilehash: 02689dba32c8cc91e4a4a4de4dee98bc990b4dd6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050072"
---
# <a name="create-an-azure-arc-enabled-onboarding-service-principal-preview"></a>Azure Arc 対応オンボード サービス プリンシパルの作成 (プレビュー)

## <a name="overview"></a>概要

Kubernetes クラスターを Azure Arc にオンボードするために、権限が制限されたロールが割り当てられたサービス プリンシパルを使用できます。これは、Azure Pipelines や GitHub Actions などの継続的インテグレーションおよび継続的デプロイ (CI/CD) パイプラインで役立ちます。

以下の手順では、Kubernetes クラスターを Azure Arc にオンボードするためにサービス プリンシパルを使用する方法について説明します。

## <a name="create-a-new-service-principal"></a>新しいサービス プリンシパルを作成する

わかりやすい名前で新しいサービス プリンシパルを作成します。 この名前は、Azure Active Directory テナントで一意である必要があります。

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

新しいサービス プリンシパルを作成したら、新しく作成したプリンシパルに "Kubernetes クラスター - Azure Arc のオンボード" ロールを割り当てます。 これは、アクセス許可が制限された組み込みの Azure ロールであり、プリンシパルはクラスターを Azure に登録することだけが許可されます。 プリンシパルは、サブスクリプション内の他のクラスターまたはリソースを更新、削除、変更することはできません。

機能が制限されているため、お客様はこのプリンシパルを再利用して、複数のクラスターを簡単にオンボードできます。

ロールを割り当てるときに適切な `--scope` 引数を渡すことで、アクセス許可をさらに制限できます。 これにより、お客様はクラスターの登録を制限できます。 次のシナリオは、さまざまな `--scope` パラメーターでサポートされています。

| リソース  | `scope` 引数| 結果 |
| ------------- | ------------- | ------------- |
| サブスクリプション | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | サービス プリンシパルは、指定されたサブスクリプションの既存のリソース グループに任意のクラスターを登録できます。 |
| リソース グループ | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | サービス プリンシパルは、リソース グループ `myGroup` に__のみ__、クラスターを登録できます。 |

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

新しく作成したサービス プリンシパルを参照します。

```console
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>次のステップ

* [Azure Policy を使用してクラスター構成を管理する](./use-azure-policy.md)
