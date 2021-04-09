---
title: Azure Site Recovery 監視に関する一般的な質問
description: 組み込みの監視と Azure Monitor (Log Analytics) を使用する Azure Site Recovery 監視に関する一般的な質問への回答を得ます
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 66ce267581d4748ea51a3dcbd7caa61907115cc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "82131163"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>Site Recovery 監視に関する一般的な質問

この記事では、組み込みの Site Recovery 監視と Azure Monitor (Log Analytics) を使用する Azure [Site Recovery](site-recovery-overview.md) 監視に関する一般的な質問に回答します。

## <a name="general"></a>全般

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>ログに記録される RPO 値は、最新の利用可能な復旧ポイントとどのように異なりますか。

Site Recovery では、マルチステップの非同期プロセスを使用して、Azure にマシンをレプリケートします。

- レプリケーションの最後から 2 番目のステップで、マシンでの最新の変更とメタデータが、ログ/キャッシュ ストレージ アカウントにコピーされます。
- これらの変更と、回復可能ポイントを識別するためのタグが、ターゲット リージョンのストレージ アカウントとマネージド ディスクに書き込まれます。
- これで Site Recovery がマシンの回復可能ポイントを生成できるようになります。
- この時点で、RPO はそれまでにストレージ アカウントにアップロードされた変更に対して満たされています。 つまり、この時点でのマシンの RPO は、回復可能ポイントに対応するタイムスタンプからの経過時間と等しくなります。
- ここで Site Recovery は、ストレージ アカウントからアップロードされたデータを取得し、マシン用に作成されたレプリカ ディスクに適用します。
- その後、復旧ポイントを生成し、このポイントをフェールオーバー時に復旧で利用できるようにします。
- したがって、利用可能な最新の復旧ポイントは、既に処理されてレプリカ ディスクに適用された最新の復旧ポイントに対応するタイムスタンプを示します。


レプリケートするソース マシンまたはオンプレミスのインフラストラクチャ サーバーのシステム時刻が正しくないと、計算される RPO 値が正しくなくなります。 正確な RPO が報告されるようにするために、すべてのサーバーおよびすべてのマシン上のシステム クロックが正しいことを確認してください。



## <a name="inbuilt-site-recovery-logging"></a>組み込みの Site Recovery ログ記録


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>コンテナーのインフラストラクチャ ビューに表示される VM 数が、[レプリケートされたアイテム] に表示される合計数と異なるのはなぜですか。

コンテナーのインフラストラクチャ ビューは、レプリケーション シナリオによってスコープ設定されています。 このビューのカウントに含まれるのは、現在選択されているレプリケーション シナリオのマシンだけです。 また、Azure にレプリケートするように構成された VM だけがカウントの対象となります。 フェールオーバーされたマシンや、オンプレミス サイトにレプリケート バックされているマシンは、このビューではカウントされません。

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>[要点] のレプリケートされたアイテムの数が、ダッシュボード上のレプリケートされたアイテムの合計数と異なるのはなぜですか。

[要点] に表示される数には、初期レプリケーションが完了したマシンだけが含まれます。 レプリケートされたアイテムの合計には、初期レプリケーションが現在進行中のマシンも含めて、コンテナー内のすべてのマシンがカウントされます。

## <a name="azure-monitor-logging"></a>Azure Monitor ログ記録


### <a name="how-often-does-site-recovery-send-resource-logs-to-azure-monitor-log"></a>Site Recovery ではどのくらいの頻度でリソース ログが Azure Monitor ログに送信されますか。 

- AzureSiteRecoveryReplicationStats と AzureSiteRecoveryRecoveryPoints は、15 分ごとに送信されます。  
- AzureSiteRecoveryReplicationDataUploadRate と AzureSiteRecoveryProtectedDiskDataChurn は、5 分ごとに送信されます。 
- AzureSiteRecoveryJobs は、ジョブのトリガーと完了時に送信されます。
- AzureSiteRecoveryEvents は、イベントが生成されるたびに送信されます。 
- AzureSiteRecoveryReplicatedItems は、環境が変更されるたびに送信されます。 通常、データ更新の時間は、変更後 15 分です。 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>データはどのくらいの期間、Azure Monitor ログに保持されますか。 

既定では、リテンション期間は 31 日です。 この期間は、Log Analytics ワークスペースの **[使用量と推定コスト]** セクションで延ばすことができます。 **[データ保有期間]** をクリックし、範囲を選択します。

### <a name="whats-the-size-of-the-resource-logs"></a>リソース ログのサイズはどれくらいですか。 

通常、ログのサイズは 15 から 20 KB です。 


## <a name="next-steps"></a>次のステップ

[Site Recovery の組み込み監視](site-recovery-monitor-and-troubleshoot.md)または [Azure Monitor](monitor-log-analytics.md) を使用して監視する方法を学習してください。


