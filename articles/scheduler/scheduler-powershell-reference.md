---
title: "Scheduler PowerShell Cmdlets Reference (Scheduler PowerShell コマンドレット リファレンス)"
description: "Scheduler PowerShell Cmdlets Reference (Scheduler PowerShell コマンドレット リファレンス)"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: c40545833da86426d3e71955b8eb8627db3c1e4b
ms.openlocfilehash: 26eb1469bc508ef518103a955fa0fbc6db2be46d


---
# <a name="scheduler-powershell-cmdlets-reference"></a>Scheduler PowerShell Cmdlets Reference (Scheduler PowerShell コマンドレット リファレンス)
次のテーブルでは、Azure Scheduler の主なコマンドレットを説明し、各コマンドレットのリファレンス ページへのリンクを示します。

Azure PowerShell をインストールして、Azure サブスクリプションに関連付けるには、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」を参照してください。 

[Azure Resource Manager コマンドレット](https://msdn.microsoft.com/library/mt125356\(v=azure.200\).aspx)の詳細については、「[Azure Resource Manager での Azure PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。

| コマンドレット | コマンドレットの説明 |
| --- | --- |
| [Disable-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490133\(v=azure.200\).aspx) |ジョブ コレクションを無効にします。 |
| [Enable-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490135\(v=azure.200\).aspx) |ジョブ コレクションを有効にします。 |
| [Get-AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490125\(v=azure.200\).aspx) |Scheduler ジョブを取得します。 |
| [Get-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490132\(v=azure.200\).aspx) |ジョブ コレクションを取得します。 |
| [Get-AzureRmSchedulerJobHistory](https://msdn.microsoft.com/library/mt490126\(v=azure.200\).aspx) |ジョブ履歴を取得します。 |
| [New-AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490136\(v=azure.200\).aspx) |HTTP ジョブを作成します。 |
| [New-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490141\(v=azure.200\).aspx) |ジョブ コレクションを作成します。 |
| [New-AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490134\(v=azure.200\).aspx) |Service Bus キュー ジョブを作成します。 |
| [New-AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490142\(v=azure.200\).aspx) |Service Bus トピック ジョブを作成します。 |
| [New-AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490127\(v=azure.200\).aspx) |ストレージ キュー ジョブを作成します。 |
| [Remove-AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490140\(v=azure.200\).aspx) |Scheduler ジョブを削除します。 |
| [Remove-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490131\(v=azure.200\).aspx) |ジョブ コレクションを削除します。 |
| [Set-AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490130\(v=azure.200\).aspx) |Scheduler HTTP ジョブを変更します。 |
| [Set-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490129\(v=azure.200\).aspx) |ジョブ コレクションを変更します。 |
| [Set-AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490143\(v=azure.200\).aspx) |Service Bus キュー ジョブを変更します。 |
| [Set-AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490137\(v=azure.200\).aspx) |Service Bus トピック ジョブを変更します。 |
| [Set-AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490128\(v=azure.200\).aspx) |ストレージ キュー ジョブを変更します。 |

詳細については、次のいずれかのコマンドレットを実行してください。 

```
Get-Help <cmdlet name> -Detailed
```
```
Get-Help <cmdlet name> -Examples
```
```
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>関連項目
 [What is Scheduler? (Scheduler とは)](scheduler-intro.md)

 [Azure Scheduler の概念、用語集、エンティティ階層構造](scheduler-concepts-terms.md)

 [Azure ポータル内で Scheduler を使用した作業開始](scheduler-get-started-portal.md)

 [Azure Scheduler のプランと課金](scheduler-plans-billing.md)

 [Azure Scheduler REST API リファレンス](https://msdn.microsoft.com/library/mt629143)

 [Azure Scheduler の高可用性と信頼性](scheduler-high-availability-reliability.md)

 [Azure Scheduler の制限、既定値、エラー コード](scheduler-limits-defaults-errors.md)

 [Azure Scheduler 送信認証](scheduler-outbound-authentication.md)




<!--HONumber=Dec16_HO1-->


