---
title: 使用状況データ、メトリック、およびログを Azure Monitor にアップロードする
description: リソース インベントリ、使用状況データ、メトリック、およびログを Azure Monitor にアップロードする
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: a522a650413be056ff64d26e90b6c15cf88d9a7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101643492"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>使用状況データ、メトリック、およびログを Azure Monitor にアップロードする

課金目的の使用状況情報、監視メトリック、およびログを定期的にエクスポートし、Azure にアップロードできます。 これらの 3 種類のデータのエクスポートとアップロードでは、データ コントローラー、SQL マネージド インスタンス、および Azure の PostgreSQL Hyperscale サーバー グループ リソースも作成および更新されます。

> [!NOTE] 
> プレビュー期間中は、Azure Arc 対応データ サービス利用のコストは発生しません。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

使用状況データ、メトリック、またはログをアップロードする前に、次のことを行う必要があります。

* ツールをインストールする 
* [`Microsoft.AzureArcData` リソース プロバイダーを登録する](#register-the-resource-provider) 
* [サービス プリンシパルを作成する](#create-service-principal)

## <a name="install-tools"></a>ツールをインストールする

必要なツールは次のとおりです。 
* Azure CLI (az) 
* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 

[ツールのインストール](./install-client-tools.md)に関する記事を参照してください。

## <a name="register-the-resource-provider"></a>リソース プロバイダーの登録

Azure にメトリックまたはユーザー データをアップロードする前に、Azure サブスクリプションに `Microsoft.AzureArcData` リソース プロバイダーが登録されていることを確認する必要があります。

リソース プロバイダーを確認するには、次のコマンドを実行します。

```azurecli
az provider show -n Microsoft.AzureArcData -o table
```

リソース プロバイダーがサブスクリプションに現在登録されていない場合は、登録できます。 登録するには、次のコマンドを実行します。  コマンドが完了するまでに 1 分から 2 分かかる場合があります。

```azurecli
az provider register -n Microsoft.AzureArcData --wait
```

## <a name="create-service-principal"></a>サービス プリンシパルの作成

使用状況とメトリックのデータをアップロードするには、サービス プリンシパルが使用されます。

次のコマンドを実行して、メトリック アップロードのサービス プリンシパルを作成します。

> [!NOTE]
> サービス プリンシパルを作成するには、[Azure で一定のアクセス許可](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)を持っている必要があります。

サービス プリンシパルを作成するには、次の例を更新します。 `<ServicePrincipalName>`、`SubscriptionId`、`resourcegroup` を実際の値で置き換えてコマンドを実行します。

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName> --role Contributor --scopes /subscriptions/{SubscriptionId}/resourceGroups/{resourcegroup}
```

以前にサービス プリンシパルを作成しており、単に最新の資格情報の取得が必要な場合は、次のコマンドを実行して資格情報をリセットします。

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

たとえば、`azure-arc-metrics` という名前のサービス プリンシパルを作成するには、次のコマンドを実行します。

```azurecli
az ad sp create-for-rbac --name azure-arc-metrics --role Contributor --scopes /subscriptions/a345c178a-845a-6a5g-56a9-ff1b456123z2/resourceGroups/myresourcegroup
```

出力例:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

後で使用するために、`appId`、`password`、`tenant` の値を環境変数に保存します。 

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_CLIENT_ID=<appId>
SET SPN_CLIENT_SECRET=<password>
SET SPN_TENANT_ID=<tenant>
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_CLIENT_ID='<appId>'
export SPN_CLIENT_SECRET='<password>'
export SPN_TENANT_ID='<tenant>'
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_CLIENT_ID="<appId>"
$Env:SPN_CLIENT_SECRET="<password>"
$Env:SPN_TENANT_ID="<tenant>"
```

::: zone-end

サービス プリンシパルを作成した後、サービス プリンシパルを適切なロールに割り当てます。 

## <a name="assign-roles-to-the-service-principal"></a>サービス プリンシパルにロールを割り当てる

こちらのコマンドを実行して、サービス プリンシパルを、データベース インスタンスのリソースが配置されているサブスクリプションの `Monitoring Metrics Publisher` ロールに割り当てます。

::: zone pivot="client-operating-system-windows-command"

> [!NOTE]
> Windows 環境から実行する場合は、ロール名に二重引用符を使用する必要があります。

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/{SubscriptionID}/resourceGroups/{resourcegroup}

```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/{SubscriptionID}/resourceGroups/{resourcegroup}
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/{SubscriptionID}/resourceGroups/{resourcegroup}
```

::: zone-end

出力例:

```output
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

サービス プリンシパルが適切なロールに割り当てられたら、メトリックまたはユーザー データのアップロードに進むことができます。 

## <a name="upload-logs-metrics-or-user-data"></a>ログ、メトリック、またはユーザー データをアップロードする

ログ、メトリック、またはユーザー データをアップロードするための具体的な手順は、アップロードする情報の種類によって異なります。 

[ログを Azure Monitor にアップロードする](upload-logs.md)

[メトリックを Azure Monitor にアップロードする](upload-metrics.md)

[使用状況データを Azure Monitor にアップロードする](upload-usage-data.md)

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>使用状況、メトリックのエクスポートとアップロードに関する一般的なガイダンス

Azure Arc 対応データ サービスに対する作成、読み取り、更新、削除 (CRUD) 操作は、課金および監視の目的でログに記録されます。 これらの CRUD 操作を監視し、使用量を適切に計算するバックグラウンド サービスがあります。 実際の使用状況または使用量の計算は、スケジュールに従って、バックグラウンドで実行されます。 

プレビュー期間中、この処理は夜間に行われます。 一般的なガイダンスは、使用状況を 1 日に 1 回だけアップロードすることです。 使用状況情報をエクスポートし、同じ 24 時間以内に複数回アップロードすると、リソースの使用状況ではなく Azure portal のリソース インベントリのみが更新されます。

メトリックをアップロードする場合、Azure Monitor では過去 30 分間のデータのみが受け入れられます ([詳細を参照](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting))。 メトリックをアップロードするためのガイダンスは、エクスポート ファイルを作成した直後にメトリックをアップロードして、Azure portal でデータセット全体を表示できるようにすることです。 たとえば、午後 2:00 にメトリックをエクスポートし、午後 2:50 にアップロード コマンドを実行した場合などです。 Azure Monitor では過去 30 分間のデータのみが受け入れられるため、ポータルにデータが表示されない場合があります。 

## <a name="next-steps"></a>次の手順

[サービス プリンシパルについて](/powershell/azure/azurerm/create-azure-service-principal-azureps#what-is-a-service-principal)

[課金データを Azure にアップロードして Azure portal に表示する](view-billing-data-in-azure.md)

[Azure portal で Azure Arc データ コントローラー リソースを表示する](view-data-controller-in-azure-portal.md)
