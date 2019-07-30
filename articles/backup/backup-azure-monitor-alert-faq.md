---
title: Azure Backup の監視アラートに関する FAQ
description: 一般的な質問への回答:Azure Backup の監視アラート
author: srinathvasireddy
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: srinathv
ms.openlocfilehash: dc68b68ffa0ae2ee4f1bdd8f2c7ac0d7ff5ebb8c
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465848"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Azure Backup の監視アラート - FAQ
この記事では、Azure の監視アラートに関する一般的な質問への回答を示します。

## <a name="configure-azure-backup-reports"></a>Azure Backup のレポートを構成する

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-storage-account"></a>レポート データがストレージ アカウントへの流入を開始したかどうかはどのように確認できますか?
構成したストレージ アカウントに移動し、コンテナーを選択します。 コンテナーに insights-logs-azurebackupreport のエントリーがある場合は、レポート データのフローが既に開始されています。

### <a name="what-is-the-frequency-of-data-push-to-a-storage-account-and-the-azure-backup-content-pack-in-power-bi"></a>Power BI でのストレージ アカウントと Azure Backup コンテンツ パックへのデータ プッシュの頻度はどれくらいですか?
  初日のユーザーの場合は、ストレージ アカウントにデータをプッシュするまでに約 24 時間かかります。 この初期のプッシュが完了した後、データは次の図に示されている頻度で更新されます。

  * **ジョブ**、**アラート**、**バックアップ項目**、**コンテナー**、**保護されているサーバー**、および**ポリシー**に関連したデータは、それがログに記録された時点で顧客のストレージ アカウントにプッシュされます。

  * **ストレージ**に関連したデータは、24 時間ごとに顧客のストレージ アカウントにプッシュされます。

       ![Azure Backup のレポートのデータ プッシュ頻度](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  * Power BI では、[1 日 1 回のスケジュールされた更新](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed)が行われます。 Power BI でコンテンツ パックのデータを手動で更新することもできます。

### <a name="how-long-can-i-retain-reports"></a>レポートはどれだけの期間保持できますか?
ストレージ アカウントを構成するとき、ストレージ アカウント内のレポート データの保有期間を選択できます。 「[レポート用のストレージ アカウントを構成する](backup-azure-configure-reports.md#configure-storage-account-for-reports)」セクションの手順 6. に従います。 また、[Excel でレポートを分析し](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/)、必要に応じてより長い保有期間にわたってレポートを保存することもできます。

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-storage-account"></a>ストレージ アカウントを構成した後は、すべてのデータがレポートに表示されますか?
 ストレージ アカウントを構成した後に生成されたすべてのデータがストレージ アカウントにプッシュされ、レポートで使用できます。 進行中のジョブはレポートにプッシュされません。 そのジョブは、完了するか、または失敗した後にレポートに送信されます。

### <a name="if-i-already-configured-the-storage-account-to-view-reports-can-i-change-the-configuration-to-use-another-storage-account"></a>レポートを表示するためのストレージ アカウントを既に構成している場合は、別のストレージ アカウントを使用するように構成を変更できますか?
はい、別のストレージ アカウントをポイントするように構成を変更できます。 Azure Backup コンテンツ パックに接続するときに、新しく構成されたストレージ アカウントを使用します。 また、別のストレージ アカウントが構成された後、新しいデータはこのストレージ アカウントに流入します。 以前の (構成を変更する前の) データは、引き続き前のストレージ アカウント内に残ります。

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>コンテナーおよびサブスクリプションにまたがるレポートを表示できますか?
はい、複数のコンテナーに対して同じストレージ アカウントを構成して、複数のコンテナーにわたるレポートを表示できます。 また、複数のサブスクリプションの複数のコンテナーに対して同じストレージ アカウントを構成することもできます。 その後、Power BI で Azure Backup コンテンツ パックに接続するときに、このストレージ アカウントを使用してレポートを表示できます。 選択されるストレージ アカウントは、Recovery Services コンテナーと同じリージョンに存在する必要があります。

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Azure バックアップ エージェント ジョブの状態がポータルに反映されるまでに、どれくらいの時間がかかりますか。
Azure portal に Azure バックアップ エージェント ジョブの状態が反映されるまでには、最大で 15 分かかります。

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>バックアップ ジョブが失敗した場合、アラートが発生するまでどのくらい時間がかかりますか。
アラートは、Azure のバックアップが失敗してから 20 分以内に発生します。

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>通知が設定されているのに、電子メールが送信されないことはありますか。
はい。 次のような状況では、通知は送信されません。

* 通知頻度が 1 時間ごとに設定されており、アラートが発生してから 1 時間以内に解決した
* ジョブが取り消された
* 元のバックアップ ジョブが進行中のために、2 番目のバックアップ ジョブが失敗した

## <a name="recovery-services-vault"></a>Recovery Services コンテナー

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Azure バックアップ エージェント ジョブの状態がポータルに反映されるまでに、どれくらいの時間がかかりますか。
Azure portal に Azure バックアップ エージェント ジョブの状態が反映されるまでには、最大で 15 分かかります。

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>バックアップ ジョブが失敗した場合、アラートが発生するまでどのくらい時間がかかりますか。
アラートは、Azure のバックアップが失敗してから 20 分以内に発生します。

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>通知が設定されているのに、電子メールが送信されないことはありますか。
はい。 次のような状況では、通知は送信されません。

* 通知頻度が 1 時間ごとに設定されており、アラートが発生してから 1 時間以内に解決した
* ジョブが取り消された
* 元のバックアップ ジョブが進行中のために、2 番目のバックアップ ジョブが失敗した

## <a name="next-steps"></a>次の手順

その他のよく寄せられる質問をお読みください。

- Azure VM バックアップについて[よく寄せられる質問](backup-azure-vm-backup-faq.md)
- Azure Backup エージェントについて[よく寄せられる質問](backup-azure-file-folder-backup-faq.md)
