---
title: Azure NetApp Files ボリュームのリージョン間レプリケーション | Microsoft Docs
description: Azure NetApp Files のリージョン間レプリケーションの動作と、サポートされているリージョン ペア、サービスレベルの目標、データ持続性、コスト モデルについて説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: b-juche
ms.custom: references_regions
ms.openlocfilehash: fd3e8295820cd9388bd7999d3008b4c583dbf295
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220906"
---
# <a name="cross-region-replication-of-azure-netapp-files-volumes"></a>Azure NetApp Files ボリュームのリージョン間レプリケーション

Azure NetApp Files レプリケーション機能を使用すると、リージョンをまたがるボリュームのレプリケーションにおいてデータを保護できます。 あるリージョンの Azure NetApp Files ボリューム (ソース) から別のリージョンにある別の Azure NetApp Files ボリューム (コピー先) にデータを非同期的にレプリケートできます。  この機能により、リージョン全体が停止したり障害が発生した場合に、重要なアプリケーションをフェールオーバーすることができます。

> [!IMPORTANT]
> リージョン間レプリケーションの機能は、現在パブリック プレビュー段階です。 [Azure NetApp Files のリージョン間レプリケーションの順番待ち送信ページ](https://aka.ms/anfcrrpreviewsignup)から、機能にアクセスするための順番待ちリクエストを送信する必要があります。 Azure NetApp Files チームからの正式な確認メールを待ってからリージョン間レプリケーション機能を使用してください。

## <a name="supported-cross-region-replication-pairs"></a><a name="supported-region-pairs"></a>サポートされているリージョン間レプリケーション ペア

Azure NetApp Files ボリューム レプリケーションは、さまざまな [Azure リージョン ペア](/azure/best-practices-availability-paired-regions#azure-regional-pairs)と非ペアの間でサポートされています。 Azure NetApp Files ボリューム レプリケーションは現在、次のリージョン間で使用できます。  

### <a name="azure-regional-pairs"></a>Azure リージョン ペア

* 米国東部および米国西部
* 米国東部 2 および米国中部
* オーストラリア東部およびオーストラリア南東部
* カナダ中部およびカナダ東部
* インド南部およびインド中部 
* ドイツ中西部およびドイツ北部
* 東日本および西日本
* 北ヨーロッパおよび西ヨーロッパ
* 英国南部および英国西部

### <a name="azure-regional-non-pairs"></a>Azure リージョン非ペア

*   米国西部 2 および米国東部
*   米国中南部および米国中部
*   米国中南部および米国東部
*   米国中南部および米国東部 2
*   米国東部および米国東部 2
*   オーストラリア東部および東南アジア 
*   ドイツ中西部および英国南部

## <a name="service-level-objectives"></a>サービスレベルの目標

回復ポイントの目標 (RPO)、つまりデータ損失の許容最大値は、レプリケーション スケジュールの 2 倍として定義されます。  実際に観察される RPO は、データセットの合計サイズ、変更率、データ上書きの割合、転送に使用できるレプリケーション帯域幅などの要因によって異なります。   

* レプリケーション スケジュールが 10 分の場合、最大 RPO は 20 分です。  
* 時間単位のレプリケーション スケジュールでは、最大 RPO は 2 時間です。  
* 毎日のレプリケーション スケジュールでは、最大 RPO は 2 日間です。  

目標復旧時間 (RTO)、つまりビジネス アプリケーションのダウンタイムの最大許容期間は、アプリケーションを起動し、2 番目のサイトでデータへのアクセスを提供する際の要因によって決定されます。 ピアリング関係を解除してコピー先のボリュームをアクティブ化し、2 番目のサイトの読み書きデータ アクセスを提供するための RTO のストレージ部分は、1 分以内に完了すると予想されます。

## <a name="cost-model-for-cross-region-replication"></a>リージョン間レプリケーションのコスト モデル  

Azure NetApp Files のリージョン間レプリケーションでは、レプリケートしたデータ量に対してのみ料金が発生します。 設定料金や最小使用料金はありません。 レプリケーション料金は、レプリケーションの頻度と、レプリケーションの初期構成時に選択した *コピー先* のボリュームのリージョンに基づいています。 詳細については、「[Azure NetApp Files の価格](https://azure.microsoft.com/pricing/details/netapp/)」ページを参照してください。  

通常の Azure NetApp Files ストレージ容量の料金は、レプリケーション先のボリューム (*データ保護* ボリュームとも呼ばれます) に適用されます。 

### <a name="pricing-examples"></a>価格の例

1 か月に請求されるリージョン間レプリケーションの金額は、その月にリージョン間レプリケーション機能を通じてレプリケートされたデータ量に基づいています。 レプリケートされるデータ量は、GiB 単位で測定されます。 これは、ソース ボリュームからコピー先のボリュームへのすべての定期的なレプリケーション、およびコピー先のボリュームからソース ボリュームへのすべてのレプリケーションの再同期中に、2 つのリージョンにレプリケートされるデータの合計を表します。

#### <a name="example-1-month-1-baseline-replication-and-incremental-replications"></a>例 1:月 1 のベースライン レプリケーションと増分レプリケーション

次の状況を想定します。

* *ソース* ボリュームは Azure NetApp Files *Premium* サービス レベルのものです。 ボリュームのクォータ サイズは 1000 GiB で、ボリュームは月の初日の始めに 500 GiB のサイズが消費されます。 このボリュームは *米国中南部* リージョンにあります。
* *コピー先* のボリュームは Azure NetApp Files *Standard* サービス レベルのものです。 これは *米国東部 2* リージョンにあります。
* 上記の 2 つのボリューム間で、*時間単位* ベースのリージョン間レプリケーションを構成しました。 そのため、レプリケーションの料金は GiB あたり $0.12 です。
* わかりやすくするために、ソース ボリュームでは 1 時間ごとにデータが一定で 0.5 GiB 分変化するが、消費される総ボリュームのサイズは増加しない (500 GiB のまま) であると仮定します。 

初期設定の後、ベースライン レプリケーションが直ちに行われます。  

* ベースライン レプリケーション中にレプリケートされるデータ量: `500 GiB`
* ベースライン レプリケーションの料金: `500 GiB * $0.12 = $60`

ベースライン レプリケーションの後、変更されたブロックのみがレプリケートされます。 そのため、以降の増分レプリケーションでは、0.5 GiB のデータのみが 1 時間ごとにレプリケートされます。

* 30 日間ある月で増分レプリケーションによってレプリケートされたデータ量の合計: `0.5 GiB * 24 hours * 30 days = 360 GiB`
* 増分レプリケーションの料金: `360 GiB * $0.12 = $43.2`

月 1 の末日までの、リージョン間レプリケーションの料金の合計は次のようになります。  

*  月 1 のリージョン間レプリケーション料金の合計: `$60 + $43.2 = $103.2`

通常の Azure NetApp Files ストレージ容量の料金は、コピー先のボリュームに適用されます。 ただし、コピー先のボリュームでは、ソース ボリューム層とは異なる (および安価な) ストレージ層を使用できます。

#### <a name="example-2-month-2-incremental-replications-and-resync-replications"></a>例 2:月 2 の増分レプリケーションとレプリケーションの再同期  

例 1 に示すように、2 つの設定の間にソース ボリューム、コピー先のボリューム、およびレプリケーション関係があるとします。 2 か月目 (30 日間ある月) の 29 日間については、時間単位のレプリケーションが予想通りに発生しました。

* 29 日間で増分レプリケーションによってレプリケートされたデータ量の合計: `0.5 GiB * 24 hours * 29 days = 348 GiB`

月の末日に、ソース リージョンで計画外の停止が発生し、コピー先のボリュームにフェールオーバーしたとします。 2 時間後にソース リージョンが復旧され、コピー先のボリュームからソース ボリュームへのレプリケーションの再同期が実行されました。 その 2 時間以内に 0.8 GiB のデータ変更がコピー先のボリュームで発生し、ソースに再同期する必要がありました。

* 定期的なレプリケーションでレプリケートされたデータ量の合計 (最終日の 22 時間): `0.5 GiB * 22 hours = 11 GiB`
* 1 回のレプリケーションの再同期中にレプリケートされるデータ量: `0.8 GiB`

そのため、月 2 の末日までの、リージョン間レプリケーションの料金の合計は次のようになります。  

* 月 2 のリージョン間レプリケーション料金の合計: `(348 GiB + 11 GiB + 0.8 GiB) * $0.12 = $43.18`

月 2 の通常の Azure NetApp Files ストレージ容量の料金は、コピー先のボリュームに適用されます。

## <a name="next-steps"></a>次のステップ
* [リージョン間レプリケーションを使用するための要件と考慮事項](cross-region-replication-requirements-considerations.md)
* [ボリューム レプリケーションを作成する](cross-region-replication-create-peering.md)
* [レプリケーション関係の正常性状態を表示する](cross-region-replication-display-health-status.md)
* [ディザスター リカバリーの管理](cross-region-replication-manage-disaster-recovery.md)
* [ボリューム レプリケーション メトリック](azure-netapp-files-metrics.md#replication)
* [ボリューム レプリケーションまたはボリュームを削除する](cross-region-replication-delete.md)
* [リージョン間レプリケーションのトラブルシューティング](troubleshoot-cross-region-replication.md)


