---
title: Azure Data Lake Analytics の作成とクエリ - PowerShell
description: Azure PowerShell を使って Azure Data Lake Analytics アカウントを作成し、U-SQL ジョブを送信します。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/04/2017
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 93a05231bc971737a08d74ad04150e5449dfc792
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220943"
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Azure PowerShell で Azure Data Lake Analytics の使用を開始する

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure PowerShell を使って Azure Data Lake Analytics アカウントを作成し、U-SQL ジョブを送信して実行する方法について説明します。 Data Lake Analytics の詳細については、「 [Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このチュートリアルを開始する前に、次の条件を満たしておく必要があります。

* **Azure Data Lake Analytics アカウント**。 「[Data Lake Analytics の使用を開始する](./data-lake-analytics-get-started-portal.md)」を参照してください。
* **Azure PowerShell を実行できるワークステーション**。 「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/)」を参照してください。

## <a name="log-in-to-azure"></a>Azure にログインする

このチュートリアルでは、既に Azure PowerShell を使い慣れていることを前提としています。 特に、Azure にログインする方法を理解している必要があります。 ヘルプが必要な場合は、[Azure PowerShell の概要](/powershell/azure/get-started-azureps)に関するページを参照してください。

サブスクリプション名でログインするには:

```powershell
Connect-AzAccount -SubscriptionName "ContosoSubscription"
```

サブスクリプション名の代わりに、サブスクリプション ID を使用してログインすることもできます。

```powershell
Connect-AzAccount -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

成功した場合、このコマンドの出力は、次のテキストのようになります。

```text
Environment           : AzureCloud
Account               : joe@contoso.com
TenantId              : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionId        : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionName      : ContosoSubscription
CurrentStorageAccount :
```

## <a name="preparing-for-the-tutorial"></a>チュートリアルの準備

このチュートリアルの PowerShell スニペットでは、以下の変数を使って各情報を格納します。

```powershell
$rg = "<ResourceGroupName>"
$adls = "<DataLakeStoreAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Data Lake Analytics アカウントに関する情報を取得する

```powershell
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>U-SQL ジョブの送信

U-SQL スクリプトを保持する PowerShell 変数を作成します。

```powershell
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();

"@
```

`Submit-AdlJob` コマンドレットと `-Script` パラメーターでスクリプト テキストを送信します。

```powershell
$job = Submit-AdlJob -Account $adla -Name "My Job" -Script $script
```

別の方法として、`-ScriptPath` パラメーターを使用してスクリプト ファイルを送信することもできます。

```powershell
$filename = "d:\test.usql"
$script | out-File $filename
$job = Submit-AdlJob -Account $adla -Name "My Job" -ScriptPath $filename
```

`Get-AdlJob` でジョブの状態を取得します。 

```powershell
$job = Get-AdlJob -Account $adla -JobId $job.JobId
```

ジョブが完了するまで Get-AdlJob を何度も呼び出すのではなく、`Wait-AdlJob` コマンドレットを使用します。

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

`Export-AdlStoreItem` を使用して出力ファイルをダウンロードします。

```powershell
Export-AdlStoreItem -Account $adls -Path "/data.csv" -Destination "C:\data.csv"
```

## <a name="see-also"></a>関連項目

* 他のツールを使用する同じチュートリアルを表示するには、ページの上部にあるタブ セレクターをクリックします。
* U-SQL の詳細については、「 [Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)」を参照してください。
* 管理タスクについては、「 [Azure Portal を使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md)」をご覧ください。