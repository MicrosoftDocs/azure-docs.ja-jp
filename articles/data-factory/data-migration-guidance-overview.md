---
title: データ レイクとデータ ウェアハウスから Azure にデータを移行する
description: Azure Data Factory を使用してデータ レイクとデータ ウェアハウスから Azure にデータを移行します。
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/30/2019
ms.openlocfilehash: aaf1593cc049e8b23f8ebe36fea022b3029ccd04
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930800"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Azure Data Factory を使用してデータ レイクまたはデータ ウェアハウスから Azure にデータを移行する

データ レイクまたはエンタープライズ データ ウェアハウス (EDW) を Microsoft Azure に移行する場合は、Azure Data Factory の使用を検討してください。 Azure Data Factory は、次のシナリオに適しています。

- Amazon Simple Storage Service (Amazon S3) またはオンプレミスの Hadoop 分散ファイル システム (HDFS) から Azure へのビッグ データ ワークロードの移行
- Oracle Exadata、Netezza、Teradata、Amazon Redshift から Azure への EDW 移行

Azure Data Factory では、データ レイク移行の場合はペタバイト (PB) 単位のデータ、データ ウェアハウス移行の場合は数十テラバイト (TB) 単位のデータを移動できます。

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Azure Data Factory をデータ移行に使用できる理由

- Azure Data Factory では、処理能力を簡単にスケールアップし、ハイ パフォーマンス、回復性、スケーラビリティを備えたサーバーレス方式でデータを移動できます。 また、使用した分にのみ料金がかかります。 また、以下の点にも注意してください。 
  - Azure Data Factory には、データ ボリュームまたはファイル数に制限がありません。
  - Azure Data Factory ではネットワークとストレージの帯域幅をフルに活用し、環境内で最高ボリュームのデータ移動スループットを実現できます。
  - Azure Data Factory では従量課金制が使用されるため、Azure へのデータ移行を実行するために実際に使用した時間に対してのみ料金がかかります。  
- Azure Data Factory では 1 回限りの履歴読み込みとスケジュールされた増分読み込みの両方を実行できます。
- Azure Data Factory では Azure 統合ランタイム (IR) を使用し、パブリックにアクセスできるデータ レイクとウェアハウス エンドポイント間でデータを移動します。 また、Azure Virtual Network (VNet) の内側またはファイアウォールの背後にあるデータ レイクとウェアハウス エンドポイントのデータ移動にセルフホステッド IR を使用することもできます。
- Azure Data Factory はエンタープライズクラスのセキュリティを備えています。セキュリティで保護されたサービス間の統合には Windows インストーラー (MSI) またはサービス ID を使用できます。また、資格情報の管理には Azure Key Vault を使用できます。
- Azure Data Factory は、コードを使用しない作成エクスペリエンスと、豊富な組み込みの監視ダッシュボードを提供します。  

## <a name="online-vs-offline-data-migration"></a>オンラインとオフラインのデータ移行

Azure Data Factory は、ネットワーク (インターネット、ER、または VPN) を介してデータを転送するための標準のオンライン データ移行ツールです。 一方、オフラインのデータ移行の場合、ユーザーは組織から Azure データ センターにデータ転送デバイスを物理的に発送します。  

オンラインとオフラインの移行方法のいずれかを選択する際には、次の 3 つの重要な考慮事項があります。  

- 移行するデータのサイズ
- ネットワーク帯域幅
- 移行期間

たとえば、2 週間 (*移行期間*) 以内に Azure Data Factory を使用してデータ移行を完了する予定があるとします。 次の表のピンク色と青色の分かれ目に注目してください。 各列の一番下にあるピンク色のセルは、移行期間が 2 週間に最も近く、2 週間未満であるデータ サイズ/ネットワーク帯域幅のペアを示します (青色のセルのサイズ/帯域幅のペアは、2 週間を超えるオンライン移行期間を示します)。 

![オンラインとオフライン](media/data-migration-guidance-overview/online-offline.png) この表は、データのサイズと利用可能なネットワーク帯域幅に基づいて、オンライン移行 (Azure Data Factory) で目的の移行期間を満たすことができるかどうかを判断するために役立ちます。 オンライン移行期間が 2 週間を超える場合は、オフライン移行を使用することをお勧めします。

> [!NOTE]
> オンライン移行を使用すると、1 つのツールで履歴データの読み込みと増分フィードの両方をエンドツーエンドで実現できます。  この方法では、移行期間中に、既存のストアと新しいストアの間でデータの同期を維持することができます。 つまり、新しいストア上に更新されたデータを使用して ETL ロジックを再構築できます。


## <a name="next-steps"></a>次の手順

- [データを AWS S3 から Azure に移行する](data-migration-guidance-s3-azure-storage.md)
- [オンプレミスの Hadoop クラスターから Azure へのデータの移行](data-migration-guidance-hdfs-azure-storage.md)
- [オンプレミスの Netezza サーバーから Azure へのデータの移行](data-migration-guidance-netezza-azure-sqldw.md)
