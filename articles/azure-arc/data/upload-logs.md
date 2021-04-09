---
title: ログを Azure Monitor にアップロードする
description: Azure Arc 対応データ サービスのログを Azure Monitor にアップロードする
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: f3f29ae1ab868a96e6f70ed964f79c47bc591c4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92373906"
---
# <a name="upload-logs-to-azure-monitor"></a>ログを Azure Monitor にアップロードする

定期的に、ログをエクスポートしてから、Azure にアップロードできます。 ログをエクスポートおよびアップロードすると、Azure 内でデータ コントローラー、SQL マネージド インスタンス、および PostgreSQL Hyperscale サーバー グループのリソースも作成および更新されます。

> [!NOTE] 
> プレビュー期間中は、Azure Arc 対応データ サービス利用のコストは発生しません。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>開始する前に

ログをアップロードする前に、次を実行する必要があります。 

1. [Log Analytics ワークスペースを作成する](#create-a-log-analytics-workspace)
1. [環境変数に ID と共有キーを割り当てる](#assign-id-and-shared-key-to-environment-variables)

## <a name="create-a-log-analytics-workspace"></a>ログ分析ワークスペースを作成する

Log Analytics ワークスペースを作成するには、こちらのコマンドを実行して Log Analytics ワークスペースを作成し、アクセス情報を環境変数内に設定します。

> [!NOTE]
> 既にワークスペースがある場合は、この手順をスキップしてください。

```azurecli
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>
```

出力例:

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>環境変数に ID と共有キーを割り当てる

後で使用するために、Log Analytics ワークスペースの `customerId` を環境変数として保存します。

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_ID=<customerId>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID='<customerId>'
```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_ID='<customerId>'
```
::: zone-end

このコマンドを実行すると、Log Analytics ワークスペースに接続するために必要なアクセス キーが返されます。

```azurecli
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

出力例:

```output
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

後で使用するため、プライマリ キーを環境変数に保存します。

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_SHARED_KEY=<primarySharedKey>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:WORKSPACE_SHARED_KEY='<primarySharedKey>'
```
```
::: zone-end


::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_SHARED_KEY='<primarySharedKey>'
```

::: zone-end

## <a name="set-final-environment-variables-and-confirm"></a>最終的な環境変数を設定して確認する

環境変数に SPN 機関の URL を設定します。

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end


## <a name="verify-environment-variables"></a>環境変数を確認する

必要に応じて、必要なすべての環境変数が設定されていることを調べて確認します。


::: zone pivot="client-operating-system-windows-command"

```console
echo %WORKSPACE_ID%
echo %WORKSPACE_SHARED_KEY%
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

環境変数を設定したら、ログをログ ワークスペースにアップロードできます。 

## <a name="upload-logs-to-azure-monitor"></a>ログを Azure Monitor にアップロードする

 Azure Arc 対応 SQL マネージド インスタンスと Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのログをアップロードするには、以下の CLI コマンドを実行します。

1. [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] を使用して、Azure Arc データ コントローラーにログインします。

   ```console
   azdata login
   ```

   画面の指示に従って、名前空間、管理者のユーザー名、およびパスワードを設定します。 

1. 指定したファイルにすべてのログをエクスポートします。

   ```console
   azdata arc dc export --type logs --path logs.json
   ```

2. Azure Monitor のログ分析ワークスペースにログをアップロードします。

   ```console
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>Azure portal でログを表示する

ログがアップロードされたら、ログ クエリ エクスプローラーを使用して次のようにクエリを実行できるようになります。

1. Azure portal を開いてから、上部の検索バーで名前を指定してご使用のワークスペースを検索し、それを選択します。
2. 左側のパネルで [ログ] を選択します。
3. [作業の開始] を選択します (または、Log Analytics を初めて使用する場合は、[作業の開始] ページのリンクを選択して詳細を確認します)。
4. Log Analytics を初めて使用する場合は、チュートリアルに従って Log Analytics の詳細を確認します。
5. テーブルの一覧の下部にある [カスタム ログ] を展開すると、'sql_instance_logs_CL' という名前のテーブルが表示されます。
6. テーブル名の横にある目のアイコンを選択します。
7. [クエリ エディターで表示] ボタンを選択します。
8. これでクエリ エディター内にクエリが表示されるようになり、ログ内で最も新しい 10 個のイベントが表示されます
9. ここからは、クエリ エディターやアラートの設定を使用して、ログのクエリを試してみることができます。

## <a name="automating-uploads-optional"></a>アップロードの自動化 (省略可能)

メトリックとログをスケジュールに基づいてアップロードする場合は、スクリプトを作成して、タイマーで数分ごとに実行できます。 次に、Linux シェル スクリプトを使用してアップロードを自動化する例を示します。

お好きなテキスト エディターまたはコード エディターで、ファイルに次のスクリプトを追加し、.sh (Linux/Mac)、.cmd、.bat、.ps1 などのスクリプト実行可能ファイルとして保存します。

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

スクリプト ファイルを実行可能にする

```console
chmod +x myuploadscript.sh
```

20 分ごとにスクリプトを実行します。

```console
watch -n 1200 ./myuploadscript.sh
```

Cron や Windows タスク スケジューラのようなジョブ スケジューラを使用することも、Ansible、Puppet、Chef のようなオーケストレーターを使用することもできます。

## <a name="next-steps"></a>次のステップ

[メトリックとログを Azure Monitor にアップロードする](upload-metrics.md)

[使用状況データ、メトリック、およびログを Azure Monitor にアップロードする](upload-usage-data.md)

[課金データを Azure にアップロードして Azure portal に表示する](view-billing-data-in-azure.md)

[Azure portal で Azure Arc データ コントローラー リソースを表示する](view-data-controller-in-azure-portal.md)
