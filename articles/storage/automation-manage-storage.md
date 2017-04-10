---
title: "Azure Automation を使用した Azure Storage の管理"
description: "Azure Automation サービスを使用して大規模に Azure Storage を管理する方法について説明します。"
services: storage, automation
documentationcenter: 
author: jodoglevy
manager: eamono
editor: 
ms.assetid: bac41c39-1d95-46aa-a481-ec17bbb21b40
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: eamono
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3fac67ed9bbae90a59c9557e3ac933322c15f3cf


---
# <a name="managing-azure-storage-using-azure-automation"></a>Azure Automation を使用した Azure Storage の管理
このガイドでは、Azure Automation サービスと、このサービスを使用して Azure Storage BLOB、テーブル、およびキューの管理を簡略化する方法について紹介します。

## <a name="what-is-azure-automation"></a>Azure Automation とは
[Azure Automation](https://azure.microsoft.com/services/automation/) は、プロセスの自動化によってクラウド管理を簡略化するための Azure サービスです。 Azure Automation を使用して実行時間の長いタスク、手動タスク、エラーが発生しやすいタスク、頻繁に繰り返されるタスクを自動化し、信頼性と効率性を向上して組織のゴール達成までの時間を短縮することができます。

Azure Automation は、組織の拡大に伴って変化するニーズに対応可能な、信頼性と可用性の高いワークフロー実行エンジンを提供します。 Azure Automation では、サード パーティ製のシステムによって手動でプロセスを開始したり、必要なときに正確にタスクが起動されるようにスケジュールされた間隔でプロセスを開始できます。

Azure Automation でクラウド管理タスクを自動実行すれば、運用上のオーバーヘッドが削減され、IT/DevOps スタッフの負担が軽減されるため、ビジネス価値の向上にフォーカスすることができます。

## <a name="how-can-azure-automation-help-manage-azure-storage"></a>Azure Automation を Azure Storage の管理に役立てる方法
[Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)で利用可能な PowerShell コマンドレットを使用することにより、Azure Automation で Azure Storage を管理できます。 Azure Automation には、このような Storage PowerShell コマンドレットがあらかじめ用意されており、サービス内ですべての BLOB、テーブル、およびキュー管理タスクを実行することができます。 Azure Automation 内のこれらのコマンドレットと別の Azure サービスのコマンドレットを組み合わせて、Azure サービスおよびサード パーティ システム全体の複雑なタスクを自動化することもできます。

[Azure Automation Runbook ギャラリー](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) には、Azure Storage、その他の Azure サービス、サード パーティ製システムの管理を自動化するためのさまざまな製品チームおよびコミュニティの Runbook が含まれています。 ギャラリーに含まれている Runbook の例を示します。

* [Remove Azure Storage Blobs that are Certain Days Old Using Automation Service (Automation サービスを使用して、特定の日数が経った Azure Storage BLOB を削除する)](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
* [Download a Blob from Azure Storage (Azure Storage から BLOB をダウンロードする)](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
* [Backup all disks for a single Azure VM or for all VMs in a Cloud Service (単一の Azure VM またはクラウド サービス内のすべての VM のすべてのディスクをバックアップする)](https://gallery.technet.microsoft.com/scriptcenter/Backup-all-disks-for-a-ede940d5)

## <a name="next-steps"></a>次のステップ
ここまで、Azure Automation の基本と Azure Automation を使用して Azure Storage BLOB、テーブル、およびキューを管理する方法について説明しました。Azure Automation の詳細については、これらのリンクを参照してください。

Azure Automation チュートリアル「 [Azure Automation での Runbook の作成またはインポート](../automation/automation-creating-importing-runbook.md)」を参照してください。




<!--HONumber=Nov16_HO3-->


