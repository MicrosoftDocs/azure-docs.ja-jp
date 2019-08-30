---
title: Azure Data Factory を使用してデータ レイクとデータ ウェアハウスから Azure にデータを移行する | Microsoft Docs
description: Azure Data Factory を使用してデータ レイクとデータ ウェアハウスから Azure にデータを移行します。
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 7/30/2019
ms.openlocfilehash: a61ddf57d4f01fcaf4a949add29c677aa94060a9
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827844"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Azure Data Factory を使用してデータ レイクまたはデータ ウェアハウスから Azure にデータを移行する 

データ レイクまたはエンタープライズ データ ウェアハウス (EDW) を Azure に移行するときに、データ移行を行うためのツールとして Azure Data Factory を使用できます。 データ レイクの移行とデータ ウェアハウスの移行は、次のシナリオに関連しています。 

- AWS S3、オンプレミス Hadoop ファイル システムから Azure へのビッグ データ ワークロードの移行。 
- Oracle Exadata、Netezza、Teradata、AWS Redshift から Azure への EDW 移行。 

Azure Data Factory は、データ レイク移行では数 PB のデータを、データ ウェアハウス移行では数十 TB のデータを移動できます。 

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Azure Data Factory をデータ移行に使用できる理由 

- Azure Data Factory では、パフォーマンス、回復性、スケーラビリティに優れたサーバーレス方式でデータを移動するために処理能力を容易にスケールアップし、使用した分だけの料金を支払うことができます。  
  - Azure Data Factory では、データ量とファイル数の制限はありません。
  - Azure Data Factory は、ネットワークとストレージの帯域幅を 100% 利用して、環境内で最高のデータ移動スループットを実現できます。   
  - Azure Data Factory は従量課金制の方針に従うため、料金の支払いが必要なのは Azure Data Factory を使用して Azure にデータを移行している時間に対してだけです。  
- Azure Data Factory では、スケジュールされた段階的な読み込みだけでなく、1 回限りの履歴読み込みも実行できます。 
- Azure Data Factory では、Azure IR を使用して、パブリックにアクセスできるデータ レイク/ウェアハウス エンドポイント間でデータを移動します。または、セルフホステッド IR を使用して、VNet 内またはファイアウォールの背後にあるデータ レイク/ウェアハウス エンドポイントのデータを移動します。 
- Azure Data Factory はエンタープライズ レベルのセキュリティを備えています。MSI またはサービス ID を使用してサービス間の統合をセキュリティで保護するか、Azure Key Vault を利用して資格情報を管理します。 
- Azure Data Factory は、コードを使用しない作成エクスペリエンスと、豊富な組み込みの監視ダッシュボードを提供します。  

## <a name="online-vs-offline-data-migration"></a>オンラインとオフラインのデータ移行

Azure Data Factory は、ネットワーク (インターネット、ER、または VPN) 経由でデータを転送するための一般的なオンライン データ移行ツールですが、お客様の組織から Azure Data Center にデータ転送デバイスを物理的に送付し、オフラインでデータを移行することもできます。  

オンラインとオフラインの移行方法を選択する際には、次の 3 つの重要な考慮事項があります。  

- 移行するデータのサイズ 
- ネットワーク帯域幅 
- 移行期間   

2 週間 (の移行期間) 以内にデータ移行を完了したい場合に、オンライン移行ツール (Azure Data Factory) を使用するのが適切なデータ サイズとネットワーク帯域幅の組み合わせは、次の図のカット ラインで確認できます。   

![オンラインとオフライン](media/data-migration-guidance-overview/online-offline.png)

> [!NOTE]
> オンラインの移行方法の利点は、履歴データの読み込みと段階的なフィードの両方を最初から最後まで 1 つのツールで実行できることです。  これにより、移行期間全体にわたって、既存のストアと新しいストアの間でデータの同期を維持できるため、更新されたデータを使用して新しいストア上に ETL ロジックを再構築できます。 


## <a name="next-steps"></a>次の手順

- [データを AWS S3 から Azure に移行する](data-migration-guidance-s3-azure-storage.md)