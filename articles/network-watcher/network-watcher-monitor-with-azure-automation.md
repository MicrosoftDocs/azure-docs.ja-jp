---
title: VPN ゲートウェイをトラブルシューティングおよび監視する - Azure Automation
titleSuffix: Azure Network Watcher
description: この記事では、Azure Automation と Network Watcher を使用してオンプレミスの接続を診断する方法について説明します
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: af671996722524de9af1a90ae8dfde27f814c8c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011814"
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>Network Watcher のトラブルシューティングを使用した VPN ゲートウェイの監視

ネットワーク パフォーマンスについて深い洞察を得ることは、信頼できるサービスを顧客に提供するうえで非常に重要です。 したがって、ネットワークの停止状態をすぐに検出し、適切な処置を取って停止状態を緩和することが非常に重要になります。 Azure Automation では、Runbook を使用してプログラムでタスクを実装、実行できます。 Azure Automation を使用すると、継続的でプロアクティブなネットワーク監視とアラートを実行するための完全な対処法を構築できます。

## <a name="scenario"></a>シナリオ

次の図のシナリオは多層アプリケーションに関するもので、VPN ゲートウェイとトンネルを使用して確立されたオンプレミスの接続があります。 アプリケーションがパフォーマンスを発揮するには、VPN ゲートウェイが稼働している必要があります。

VPN トンネルの接続状態を確認するスクリプトが含まれた Runbook を作成します。このとき、リソース トラブルシューティング API を使用して接続のトンネルの状態を確認します。 正常な状態でない場合、電子メール トリガーが管理者に送信されます。

![Scenario example][scenario]

このシナリオで、以下の作業を行います。

- 接続の状態のトラブルシューティングを行う `Start-AzureRmNetworkWatcherResourceTroubleshooting` コマンドレットを呼び出す Runbook を作成する
- Runbook へのスケジュールのリンク

## <a name="before-you-begin"></a>開始する前に

このシナリオを開始する前に、前提条件として、次のものが必要です。

- Azure の Azure Automation アカウント。 Automation アカウントに最新のモジュールがあり、AzureRM.Network モジュールもあることを確認します。 AzureRM.Network モジュールを Automation アカウントに追加する必要がある場合は、モジュール ギャラリーで入手できます。
- Azure Automation で構成された一連の資格情報が必要です。 詳細については、[Azure Automation のセキュリティ](../automation/automation-security-overview.md)に関するページを参照してください。
- 有効な SMTP サーバー (Microsoft 365、オンプレミスの電子メールなど) と Azure Automation で定義した資格情報。
- Azure で構成済みの仮想ネットワーク ゲートウェイ。
- ログを格納するための既存のコンテナーがある既存のストレージ アカウント。

> [!NOTE]
> 前の図で示したインフラストラクチャは説明のためのものであり、この記事の手順で作成するものではありません。

### <a name="create-the-runbook"></a>Runbook の作成

この例を構成する最初の手順は、Runbook の作成です。 ここでは実行アカウントを使用します。 実行アカウントについては、「[Azure 実行アカウントを使用した Runbook の認証](../automation/manage-runas-account.md)」を参照してください。

### <a name="step-1"></a>手順 1

[Azure Portal](https://portal.azure.com) で Azure Automation に移動して、 **[Runbook]** をクリックします。

![Automation アカウントの概要][1]

### <a name="step-2"></a>手順 2.

**[Runbook の追加]** をクリックして、Runbook の作成プロセスを開始します。

![[Runbook] ブレード][2]

### <a name="step-3"></a>手順 3.

**[簡易作成]** の下の **[新しい Runbook の作成]** をクリックして、Runbook を作成します。

![[Runbook の追加] ブレード][3]

### <a name="step-4"></a>手順 4.

この手順では Runbook に名前を付けます。この例では **Get-VPNGatewayStatus** です。 Runbook にわかりやすい名前を付けるのは重要です。そのため、標準的な PowerShell の命名規則に従った名前にすることをお勧めします。 この例の Runbook の種類は **PowerShell** です。他には、グラフィカル ワークフロー、PowerShell ワークフロー、グラフィカル PowerShell ワークフローを使用できます。

![[Runbook] ブレード][4]

### <a name="step-5"></a>手順 5.

この手順では Runbook を作成します。次のコード例には、ここで必要なすべてのコードがあります。 コード内の \<value\> が含まれる項目は、実際のサブスクリプションの値に置き換える必要があります。

次のコードを使用して、 **[保存]** をクリックします。

```powershell
# Set these variables to the proper values for your environment
$automationCredential = "<work or school account>"
$fromEmail = "<from email address>"
$toEmail = "<to email address>"
$smtpServer = "<smtp.office365.com>"
$smtpPort = 587
$runAsConnectionName = "<AzureRunAsConnection>"
$subscriptionId = "<subscription id>"
$region = "<Azure region>"
$vpnConnectionName = "<vpn connection name>"
$vpnConnectionResourceGroup = "<resource group name>"
$storageAccountName = "<storage account name>"
$storageAccountResourceGroup = "<resource group name>"
$storageAccountContainer = "<container name>"

# Get credentials for work or school account
$cred = Get-AutomationPSCredential -Name $automationCredential

# Get the connection "AzureRunAsConnection "
$servicePrincipalConnection=Get-AutomationConnection -Name $runAsConnectionName

"Logging in to Azure..."
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $servicePrincipalConnection.TenantId `
    -ApplicationId $servicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
"Setting context to a specific subscription"
Set-AzureRmContext -SubscriptionId $subscriptionId

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $region }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $vpnConnectionName -ResourceGroupName $vpnConnectionResourceGroup
$sa = Get-AzureRmStorageAccount -Name $storageAccountName -ResourceGroupName $storageAccountResourceGroup 
$storagePath = "$($sa.PrimaryEndpoints.Blob)$($storageAccountContainer)"
$result = Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath $storagePath

if($result.code -ne "Healthy")
    {
        $body = "Connection for $($connection.name) is: $($result.code) `n$($result.results[0].summary) `nView the logs at $($storagePath) to learn more."
        Write-Output $body
        $subject = "$($connection.name) Status"
        Send-MailMessage `
        -To $toEmail `
        -Subject $subject `
        -Body $body `
        -UseSsl `
        -Port $smtpPort `
        -SmtpServer $smtpServer `
        -From $fromEmail `
        -BodyAsHtml `
        -Credential $cred
    }
else
    {
    Write-Output ("Connection Status is: $($result.code)")
    }
```

### <a name="step-6"></a>手順 6.

Runbook を保存したら、スケジュールをそれにリンクして Runbook の開始を自動化する必要があります。 プロセスを開始するには、 **[スケジュール]** をクリックします。

![手順 6.][6]

## <a name="link-a-schedule-to-the-runbook"></a>Runbook へのスケジュールのリンク

新しいスケジュールを作成する必要があります。 **[スケジュールを Runbook にリンクします]** をクリックします。

![手順 7.][7]

### <a name="step-1"></a>手順 1

**[スケジュール]** ブレードで、 **[新しいスケジュールを作成します]** をクリックします。

![手順 8.][8]

### <a name="step-2"></a>手順 2.

**[新しいスケジュール]** ブレードで、スケジュールの情報を入力します。 設定できる値は次の一覧のようになります。

- **[名前]** - スケジュールのフレンドリ名です。
- **[説明]** - スケジュールの説明。
- **[開始]** - この値は、スケジュールがトリガーされる時間を構成する、日付、時刻、タイム ゾーンを組み合わせたものです。
- **[繰り返し]** - この値でスケジュールの繰り返しを指定します。  有効な値は、 **[1 回のみ]** と **[定期的]** のいずれかです。
- **[繰り返し間隔]** - 時間、日、週、または月で指定するスケジュールの繰り返し間隔。
- **[有効期限の設定]** - この値で、スケジュールに有効期限を設けるどうかを指定します。 **[はい]** と **[いいえ]** のいずれかに設定できます。 [はい] を選んだ場合、有効な日付と時刻を入力する必要があります。

> [!NOTE]
> 毎時を超える頻度で Runbook を実行する必要がある場合、異なる間隔 (毎時の 15 分、30 分、45 分後) で複数のスケジュールを作成する必要があります。

![手順 9.][9]

### <a name="step-3"></a>手順 3.

[保存] をクリックして、スケジュールを Runbook に保存します。

![手順 10.][10]

## <a name="next-steps"></a>次のステップ

Network Watcher トラブルシューティングを Azure Automation と統合する方法を確認しました。次は、[アラートでトリガーされるパケット キャプチャを Azure Network Watcher で作成する方法](network-watcher-alert-triggered-packet-capture.md)に関するページを参照して、VM アラートでパケット キャプチャをトリガーする方法について学習します。

<!-- images -->
[scenario]: ./media/network-watcher-monitor-with-azure-automation/scenario.png
[1]: ./media/network-watcher-monitor-with-azure-automation/figure1.png
[2]: ./media/network-watcher-monitor-with-azure-automation/figure2.png
[3]: ./media/network-watcher-monitor-with-azure-automation/figure3.png
[4]: ./media/network-watcher-monitor-with-azure-automation/figure4.png
[5]: ./media/network-watcher-monitor-with-azure-automation/figure5.png
[6]: ./media/network-watcher-monitor-with-azure-automation/figure6.png
[7]: ./media/network-watcher-monitor-with-azure-automation/figure7.png
[8]: ./media/network-watcher-monitor-with-azure-automation/figure8.png
[9]: ./media/network-watcher-monitor-with-azure-automation/figure9.png
[10]: ./media/network-watcher-monitor-with-azure-automation/figure10.png