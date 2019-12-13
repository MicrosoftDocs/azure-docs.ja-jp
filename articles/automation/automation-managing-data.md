---
title: Azure Automation のデータの管理
description: この記事には、Azure Automation 環境の管理に関する複数のトピックが含まれています。  現在は、データの保持、Azure Automation のバックアップ、Azure Automation でのディザスター リカバリー が含まれています。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: da1b151a150dfbf602593451d3d68043352b73eb
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850773"
---
# <a name="managing-azure-automation-data"></a>Azure Automation のデータの管理
この記事には、Azure Automation 環境の管理に関する複数のトピックが含まれています。

## <a name="data-retention"></a>データの保持
Azure Automation でリソースを削除する場合、完全に削除される前に、監査目的で 90 日間保持されます。  この期間にリソースを表示または使用することはできません。  このポリシーは、削除済みのオートメーション アカウントに属するリソースにも適用されます。

Azure Automation は、90 日を超えるジョブを自動的に削除した後、完全に消去します。

次の表は、さまざまなリソースの保持ポリシーをまとめたものです。

| Data | ポリシー |
|:--- |:--- |
| アカウント |ユーザーによってアカウントが削除された日から 90 日後に完全に消去されます。 |
| アセット |ユーザーによって資産が削除された日から 90 日後に、または資産を保持するアカウントがユーザーによって削除された日から 90 日後に完全に消去されます。 |
| モジュール |ユーザーによってモジュールが削除された日から 90 日後に、またはモジュールを保持するアカウントがユーザーによって削除された日から 90 日後に完全に消去されます。 |
| Runbooks |ユーザーによってリソースが削除された日から 90 日後に、またはリソースを保持するアカウントがユーザーによって削除された日から 90 日後に完全に消去されます。 |
| [ジョブ] |最終更新日から 90 日後に削除された後、完全に消去されます。 これは、ジョブが完了、停止、または中断された後に実行されます。 |
| ノード構成/MOF ファイル |古いノード構成は、新しいノード構成が生成された後、90 日間で完全に削除されます。 |
| DSC ノード |Windows PowerShell で [Unregister-AzureRMAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) コマンドレットを使用するか、Azure ポータルを使用して、オートメーション アカウントから登録解除された後、90 日間で完全に削除されます。 また、ノードは、ユーザーによってノードを保持するアカウントが削除された後、90 日間で完全に削除されます。 |
| ノード レポート |そのノードの新しいレポートが生成された後、90 日間で完全に削除削除されます。 |

保持ポリシーはすべてのユーザーに適用され、現在カスタマイズすることはできません。

ただし、データを長期にわたって保持する必要がある場合は、Runbook ジョブのログを Azure Monitor ログに転送できます。  詳細については、[Azure Monitor ログへの Azure Automation ジョブ データの転送](automation-manage-send-joblogs-log-analytics.md)に関するページを参照してください。   

## <a name="backing-up-azure-automation"></a>Azure Automation のバックアップ
Microsoft Azure でオートメーション アカウントを削除すると、Runbook、モジュール、構成、設定、ジョブ、資産など、アカウント内のすべてのオブジェクトが削除されます。 アカウントを削除した後にオブジェクトを修復することはできません。  削除する前に、以下の情報を使用してオートメーション アカウントの内容をバックアップできます。 

### <a name="runbooks"></a>Runbooks
Azure Portal または Windows PowerShell の [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) コマンドレットを使用して、Runbook をスクリプト ファイルにエクスポートできます。  「 [Runbook の作成またはインポート](/previous-versions/azure/dn643637(v=azure.100))」で説明されているように、このスクリプト ファイルは別のオートメーション アカウントにインポートできます。

### <a name="integration-modules"></a>統合モジュール
Azure Automation から統合モジュールをエクスポートすることはできません。  オートメーション アカウントの外部で統合モジュールを使用できることを確認する必要があります。

### <a name="assets"></a>アセット
Azure Automation から [資産](/previous-versions/azure/dn939988(v=azure.100)) をエクスポートすることはできません。  Azure Portal を使用して、変数、資格情報、証明書、接続、およびスケジュールの詳細を記録する必要があります。  その後、別のオートメーションにインポートする Runbook で使用されているすべての資産を手動で作成する必要があります。

[Azure コマンドレット](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) を使用して暗号化されていない資産の詳細を取得し、後で参照するために保存したり、同等の資産を別のオートメーション アカウントに作成したりできます。

暗号化されている変数または資格情報のパスワード フィールドの値を、コマンドレットを使用して取得することはできません。  これらの値がわからない場合は、[Get-AutomationVariable](/previous-versions/azure/dn940012(v=azure.100)) および [Get-AutomationPSCredential](/previous-versions/azure/dn940015(v=azure.100)) アクティビティを使用して Runbook から値を取得できます。

Azure Automation から証明書をエクスポートすることはできません。  すべての証明書が Azure の外部で使用できることを確認する必要があります。

### <a name="dsc-configurations"></a>DSC の構成
Azure Portal または Windows PowerShell の [Export-AzureRmAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) コマンドレットを使用して、構成をスクリプト ファイルにエクスポートできます。 これらの構成は、別のオートメーション アカウントでインポートして使用できます。

## <a name="geo-replication-in-azure-automation"></a>Azure Automation での geo レプリケーション
Azure Automation アカウントでの標準である geo レプリケーションでは、アカウント データを別の地理的リージョンにバックアップして冗長性を確保します。 アカウントのセットアップ時にはプライマリ リージョンを選択できます。アカウントにはセカンダリ リージョンが自動的に割り当てられます。 プライマリ リージョンからコピーされた、セカンダリ データは、データの損失が発生した場合にも継続的に更新されます。  

次の表に、使用可能なプライマリ リージョンとセカンダリ リージョンのペアを示します。

| プライマリ | セカンダリ |
| --- | --- |
| 米国中南部 |米国中北部 |
| 米国東部 2 |米国中部 |
| 西ヨーロッパ |北ヨーロッパ |
| 東南アジア |東アジア |
| 東日本 |西日本 |

万一、プライマリ リージョンのデータが失われた場合、Microsoft はその復旧を試みます。 プライマリ リージョンを復元できない場合は、geo フェールオーバーが実行されます。影響を受けた顧客には、サブスクリプションを通じてこのことが通知されます。


