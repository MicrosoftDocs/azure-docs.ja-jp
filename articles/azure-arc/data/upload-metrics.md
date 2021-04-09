---
title: メトリックを Azure Monitor にアップロードする
description: Azure Arc 対応データ サービスのメトリックを Azure Monitor にアップロードする
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: d7c611f1cdb5e3294e38f87c0534003813e50388
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575693"
---
# <a name="upload-metrics-to-azure-monitor"></a>メトリックを Azure Monitor にアップロードする

定期的に、監視しているメトリックをエクスポートしてから、Azure にアップロードできます。 また、データのエクスポートとアップロードでは、データ コントローラー、SQL マネージド インスタンス、および Azure の PostgreSQL Hyperscale サーバー グループ リソースが作成および更新されます。

> [!NOTE] 
> プレビュー期間中は、Azure Arc 対応データ サービス利用のコストは発生しません。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>前提条件

続行する前に、必要なサービス プリンシパルを作成して、適切なロールに割り当てていることを確認してください。 詳細については、次の情報を参照してください。
* [サービス プリンシパルを作成する](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)。
* [サービス プリンシパルにロールを割り当てる](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-metrics"></a>メトリックのアップロード

Azure Arc データ サービスを使用すると、必要に応じてメトリックを Azure Monitor にアップロードできます。これにより、メトリックの集計と分析、アラートの生成、通知の送信、自動化されたアクションのトリガーを行えます。 

Azure Monitor にデータを送信することで、メトリック データをオフサイトで大規模に保存することも可能になるため、高度な分析のためにデータを長期間保存できるようになります。

Azure Arc データ サービスを使用するサイトが複数ある場合は、サイト全体のすべてのログとメトリックを収集する中央の場所として Azure Monitor を利用できます。

## <a name="set-final-environment-variables-and-confirm"></a>最終的な環境変数を設定して確認する

環境変数に SPN 機関の URL を設定します。

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

必要に応じて、必要なすべての環境変数が設定されていることを調べて確認します。


::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

::: zone pivot="client-operating-system-windows-command"

```console
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

## <a name="upload-metrics-to-azure-monitor"></a>メトリックを Azure Monitor にアップロードする

Azure Arc 対応 SQL マネージド インスタンスと Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのメトリックをアップロードするには、以下の CLI コマンドを実行します。

1. `azdata`を使用して、データ コントローラーにログインします。
 
1. 指定したファイルにすべてのメトリックをエクスポートします。

   ```console
   azdata arc dc export --type metrics --path metrics.json
   ```

2. メトリックを Azure Monitor にアップロードします。

   ```console
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >Azure Arc 対応データ インスタンスが最初のアップロード用に作成された後、少なくとも 30 分間待機します。
   >
   >Azure Monitor では過去 30 分間のメトリックのみが受け入れられるため、メトリックに対して `export` の後に直ちに `upload` を実行してください。 [詳細については、こちらを参照してください](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting)。


エクスポート中に "メトリックを取得できませんでした" というエラーが表示された場合は、次のコマンドを実行して、データ収集が `true` に設定されているかどうかを確認します。

```console
azdata arc dc config show
```

"security セクション" を参照してください

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

`allowNodeMetricsCollection` プロパティと `allowPodMetricsCollection` プロパティが `true` に設定されているかどうかを確認します。

## <a name="view-the-metrics-in-the-portal"></a>ポータルでメトリックを表示する

メトリックがアップロードされたら、Azure portal で表示できるようになります。
> [!NOTE]
> ポータルでメトリックを表示する前に、アップロードしたデータが処理されるまでに数分かかる場合があることに注意してください。


ポータルでメトリックを表示するには、次のリンクを使用してポータルを開きます: <https://portal.azure.com>。次に、検索バーに名前を入力し、目的のデータベース インスタンスを検索します。

CPU 使用率は [概要] ページで確認できます。または、より詳細なメトリックが必要な場合は、左側のナビゲーション ウィンドウでメトリックをクリックできます。

メトリックの名前空間として [sql server] を選択します。

視覚化するメトリックを選択します (複数選択することもできます)。

頻度を過去 30 分に変更します。

> [!NOTE]
> メトリックをアップロードできるのは過去 30 分間のみです。 Azure Monitor では、30 分より古いメトリックは拒否されます。

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

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>使用状況、メトリックのエクスポートとアップロードに関する一般的なガイダンス

Azure Arc 対応データ サービスに対する作成、読み取り、更新、削除 (CRUD) 操作は、課金および監視の目的でログに記録されます。 これらの CRUD 操作を監視し、使用量を適切に計算するバックグラウンド サービスがあります。 実際の使用状況または使用量の計算は、スケジュールに従って、バックグラウンドで実行されます。 

プレビュー期間中、この処理は夜間に行われます。 一般的なガイダンスは、使用状況を 1 日に 1 回だけアップロードすることです。 使用状況情報をエクスポートし、同じ 24 時間以内に複数回アップロードすると、リソースの使用状況ではなく Azure portal のリソース インベントリのみが更新されます。

メトリックをアップロードする場合、Azure Monitor では過去 30 分間のデータのみが受け入れられます ([詳細を参照](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting))。 メトリックをアップロードするためのガイダンスは、エクスポート ファイルを作成した直後にメトリックをアップロードして、Azure portal でデータセット全体を表示できるようにすることです。 たとえば、午後 2:00 にメトリックをエクスポートし、午後 2:50 にアップロード コマンドを実行した場合などです。 Azure Monitor では過去 30 分間のデータのみが受け入れられるため、ポータルにデータが表示されない場合があります。 

## <a name="next-steps"></a>次の手順

[ログを Azure Monitor にアップロードする](upload-logs.md)

[使用状況データ、メトリック、およびログを Azure Monitor にアップロードする](upload-usage-data.md)

[課金データを Azure にアップロードして Azure portal に表示する](view-billing-data-in-azure.md)

[Azure portal で Azure Arc データ コントローラー リソースを表示する](view-data-controller-in-azure-portal.md)
