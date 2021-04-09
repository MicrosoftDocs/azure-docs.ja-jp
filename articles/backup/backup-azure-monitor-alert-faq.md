---
title: 監視アラートとレポートに関する FAQ
description: この記事では、Azure Backup の監視アラートと Azure Backup のレポートに関する一般的な質問への回答を示します。
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 4e2585009b531512e54ffa889a05ec50038dd619
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98805856"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Azure Backup の監視アラート - FAQ

この記事では Azure Backup の監視とレポートに関してよく寄せられる質問への回答を示します。

## <a name="configure-azure-backup-reports"></a>Azure Backup のレポートを構成する

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-log-analytics-la-workspace"></a>Log Analytics (LA) ワークスペースへのレポート データの流入が開始されたかどうかを確認するにはどうすればよいですか?

構成した LA ワークスペースに移動します。 **[ログ]** メニュー項目に移動し、クエリ `CoreAzureBackup | take 1` を実行します。 返されるレコードがある場合は、ワークスペースへのデータの流入が開始されたことを意味します。 最初のデータ プッシュには最大 24 時間かかる場合があります。

### <a name="what-is-the-frequency-of-data-push-to-an-la-workspace"></a>LA ワークスペースへのデータプッシュの頻度はどのくらいですか?

コンテナーの診断データは少し遅れて Log Analytics ワークスペースに送られます。 すべてのイベントは、Recovery Services コンテナーからプッシュされてから 20 分から 30 分後に Log Analytics ワークスペースに到着します。 ここでは、その遅れの詳細について説明します。

* すべてのソリューションについて、バックアップ サービスの組み込みアラートは、作成されるとすぐにプッシュされます。 そのため、通常は 20 分から 30 分後に Log Analytics ワークスペースに表示されます。
* すべてのソリューションについて、オンデマンド バックアップ ジョブと復元ジョブは、終了するとすぐにプッシュされます。
* すべてのソリューション (SQL バックアップ以外) について、スケジュール済みバックアップ ジョブは、終了するとすぐにプッシュされます。
* SQL バックアップでは、15 分ごとにログ バックアップを行うことができるため、完了したすべてのスケジュール済みバックアップ ジョブの情報は、ログも含めて 6 時間ごとにバッチ処理されてプッシュされます。
* すべてのソリューションにわたって、バックアップ項目、ポリシー、復旧ポイント、ストレージなどのその他の情報は、少なくとも 1 日 1 回プッシュされます。
* バックアップ構成を変更すると (ポリシーの変更や編集など)、関連するすべてのバックアップ情報のプッシュがトリガーされます。

### <a name="how-long-can-i-retain-reporting-data"></a>レポート データを保持できる期間はどのくらいですか?

LA ワークスペースを作成した後は、最大 2 年間データを保持することを選択できます。 既定では、LA ワークスペースにはデータが 31 日間保持されます。

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-la-workspace"></a>LA ワークスペースを構成した後は、自分のデータがすべてレポートに表示されますか?

 診断設定を構成した後に生成されたデータはすべて、LA ワークスペースにプッシュされ、レポートで使用できます。 進行中のジョブはレポートにプッシュされません。 そのジョブは、完了するか、または失敗した後にレポートに送信されます。

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>コンテナーおよびサブスクリプションにまたがるレポートを表示できますか?

はい。コンテナーおよびサブスクリプションにまたがって、さらにリージョンにまたがってレポートを表示できます。 ご利用のデータは、単一の LA ワークスペースまたは LA ワークスペースのグループに存在する場合があります。

### <a name="can-i-view-reports-across-tenants"></a>テナントにまたがってレポートを表示できますか?

お客様が、ご自分の顧客のサブスクリプションまたは LA ワークスペースへの委任アクセス権を持つ [Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) ユーザーである場合、バックアップ レポートを使用して、ご利用のすべてのテナントにわたってデータを表示することができます。

## <a name="recovery-services-vault"></a>Recovery Services コンテナー

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Azure Backup エージェント ジョブの状態がポータルに反映されるまでに、どれくらいの時間がかかりますか。

Azure portal に Azure Backup エージェント ジョブの状態が反映されるまでには、最大で 15 分かかります。

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>バックアップ ジョブが失敗した場合、アラートが発生するまでどのくらい時間がかかりますか。

アラートは、Azure Backup が失敗してから 20 分以内に発生します。

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>通知が設定されているのに、電子メールが送信されないことはありますか。

はい。 次のような状況では、通知は送信されません。

* 通知頻度が 1 時間ごとに設定されており、アラートが発生してから 1 時間以内に解決した
* ジョブが取り消された
* 元のバックアップ ジョブが進行中のために、2 番目のバックアップ ジョブが失敗した

## <a name="next-steps"></a>次のステップ

その他のよく寄せられる質問をお読みください。

* Azure VM バックアップについて[よく寄せられる質問](backup-azure-vm-backup-faq.yml)
* Azure Backup エージェントについて[よく寄せられる質問](backup-azure-file-folder-backup-faq.md)
