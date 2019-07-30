---
title: Azure Backup Central Reporting コンテンツ パックの更新
description: Power BI での Azure Backup コンテンツ パックに対する更新に関する情報です
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: kasinh
ms.openlocfilehash: 984f87a514aa55fc18a92c6912145bc65cf21f4c
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466598"
---
# <a name="update-the-azure-backup-central-reporting-content-pack"></a>Azure Backup Central Reporting コンテンツ パックの更新 

[Azure Backup コンテンツ パック](https://docs.microsoft.com/azure/backup/backup-azure-configure-reports#view-reports-in-power-bi)を使用すると、一元的なバックアップに関するレポートを表示できます。 コンテンツ パックは定期的に更新され、機能が追加されたり、バグが修正されたりします。 この記事では、コンテンツ パックを更新する方法を説明します。 また、更新を延期し、時間の経過と共に行われる更新を表示する方法も示します。

## <a name="get-updates-to-the-content-pack"></a>コンテンツ パックの更新を取得する

### <a name="get-the-updated-content-pack"></a>更新されたコンテンツ パックを取得する
コンテンツ パックのコピーに変更を加えていない場合は、自動的に更新されます。 コンテンツ パックが変更されると、Power BI とメール通知で通知が届きます。 ご自身の都合の良いときに、更新されたコンテンツ パックの取得を選択できます。 

### <a name="postpone-the-update"></a>更新を延期する
ベスト プラクティスは、コンテンツ パックを[カスタム ワークスペース](https://youtu.be/26zyOtyHPJM?t=1m57s)にインポートすることです。 これでレポートを編集することができます。
前述のとおり、コンテンツ パックが変更されると、Power BI に通知が表示されます。 コンテンツ パックの取得を後で行うことができます。 

## <a name="coming-soon"></a>近日対応予定
   
Azure Backup コンテンツ パックは、より多くのワークロードをサポートするために更新されます。 ワークロードには、IaaS VM バックアップおよび System Center Data Protection Manager の Azure SQL Database が含まれます。 このサポートにより、Azure のバックアップと Azure VM のバックアップの現在のサポートが追加されます。 このサポートは、すべてのバックアップ データを一か所で表示および分析できることを意味します。 組織のニーズに合うように、[レポートもカスタマイズできます](https://youtu.be/26zyOtyHPJM)。

Azure Backup コンテンツ パックに付属している事前構成済みのレポートが変更されます。 この変更により、ワークロード全体でレポートがより有意義なものになります。 今後提供される予定のレポートのセットを以下に示します。

### <a name="summary"></a>まとめ
   
![まとめ](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Summary.png)

### <a name="billing"></a>課金

![課金](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Billing.png)

### <a name="compliance"></a>コンプライアンス

![コンプライアンス](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Compliance.png)

### <a name="storage"></a>Storage

![Storage](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Storage.png)

### <a name="backup-items"></a>[バックアップ項目]
![[バックアップ項目]](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-BackupItem.png)

### <a name="alerts"></a>アラート

![アラート](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Alerts.png)

### <a name="jobs"></a>[ジョブ]

![[ジョブ]](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Jobs.png)
    

## <a name="next-steps"></a>次の手順

* [組織全体でのレポートの共有](https://youtu.be/26zyOtyHPJM)
* [Azure Backup FAQ](backup-azure-backup-faq.md)
