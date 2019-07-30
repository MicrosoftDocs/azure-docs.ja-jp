---
title: Azure Storage でのデータ冗長性 | Microsoft Docs
description: Microsoft Azure Storage アカウント内のデータは、持続性と高可用性を保証するため、レプリケートされます。 冗長オプションには、ローカル冗長ストレージ (LRS)、ゾーン冗長ストレージ (ZRS)、geo 冗長ストレージ (GRS)、読み取りアクセス geo 冗長ストレージ (RA-GRS) などがあります。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/10/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: b4b9e913363938f98999c6a769ba83efbd625b69
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798321"
---
# <a name="azure-storage-redundancy"></a>Azure Storage の冗長性

Microsoft Azure ストレージ アカウント内のデータは、持続性と高可用性を保証するため、常にレプリケートされています。 Azure Storage では、計画されたイベントや計画外のイベント (一時的なハードウェア障害、ネットワークの停止または停電、大規模な自然災害など) から保護するためにデータがコピーされます。 同じデータ センター内、同じリージョン内の複数のゾーン データ センター間、または地理的に分離されたリージョン間でデータをレプリケートすることもできます。

レプリケーションにより、障害が発生しても、ストレージ アカウントは[ストレージのサービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/storage/) を満たすことができます。 Azure Storage の持続性と可用性の保証については、SLA をご覧ください。

Azure Storage では、巡回冗長検査 (CRCs) を使用して、格納データの整合性を定期的に検証します。 データの破損が検出された場合は、冗長データを使用して修復されます。 また Azure Storage では、データの格納時または取得時にデータ パケットの破損を検出する目的ですべてのネットワーク トラフィックのチェックサムを計算します。

## <a name="choosing-a-redundancy-option"></a>冗長オプションの選択

ストレージ アカウントを作成するときは、次の冗長オプションのいずれかを選択できます。

* [ローカル冗長ストレージ (LRS)](storage-redundancy-lrs.md)
* [ゾーン冗長ストレージ (ZRS)](storage-redundancy-zrs.md)
* [geo 冗長ストレージ (GRS)](storage-redundancy-grs.md)
* [読み取りアクセス geo 冗長ストレージ (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage)

次の表は、特定の種類のイベント (または同様の影響を受けるイベント) に対して、各レプリケーション戦略が提供する持続性と可用性の範囲について概要を説明したものです。

| シナリオ                                                                                                 | LRS                             | ZRS                              | GRS                                  | RA-GRS                               |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| データ センター内でノードを使用できない                                                                 | はい                             | はい                              | はい                                  | はい                                  |
| データ センター全体 (ゾーンまたは非ゾーン) が使用できなくなる                                           | いいえ                              | 可能                              | はい                                  | はい                                  |
| リージョン全体の停止                                                                                     | いいえ                              | いいえ                               | 可能                                  | はい                                  |
| リージョン全体が使用できなくなった場合に (リモートの geo 冗長化されたリージョンの) データへの読み取りアクセス | いいえ                              | いいえ                               | いいえ                                   | はい                                  |
| 指定された 1 年間にわたって \_\_ オブジェクトの持続性を提供するように設計                                          | 99.999999999% (イレブン ナイン) 以上 | 99.9999999999% (トゥエルブ ナイン) 以上 | 99.99999999999999% (シックスティーン ナイン) 以上 | 99.99999999999999% (シックスティーン ナイン) 以上 |
| サポートされるストレージ アカウントの種類                                                                   | GPv2、GPv1、BLOB                | GPv2                             | GPv2、GPv1、BLOB                     | GPv2、GPv1、BLOB                     |
| 読み取り要求の可用性 SLA | 99.9% 以上 (クール アクセス層の場合、99%) | 99.9% 以上 (クール アクセス層の場合、99%) | 99.9% 以上 (クール アクセス層の場合、99%) | 99.99% 以上 (クール アクセス層の場合、99.9%) |
| 書き込み要求の可用性 SLA | 99.9% 以上 (クール アクセス層の場合、99%) | 99.9% 以上 (クール アクセス層の場合、99%) | 99.9% 以上 (クール アクセス層の場合、99%) | 99.9% 以上 (クール アクセス層の場合、99%) |

ブロック BLOB、追加 BLOB、ページ BLOB、キュー、テーブル、およびファイルを含むストレージ アカウント内のすべてのデータがレプリケートされます。 ZRS に必要なのは汎用 v2 ストレージ アカウントですが、すべての種類のストレージ アカウントがレプリケートされます。

さまざまな冗長オプションの料金情報については、[Azure Storage の価格](https://azure.microsoft.com/pricing/details/storage/)に関するページをご覧ください。 

Azure Storage の持続性と可用性の保証については、[Azure Storage の SLA](https://azure.microsoft.com/support/legal/sla/storage/) に関するページをご覧ください。

> [!NOTE]
> Azure Premium Storage でサポートされるのは、ローカル冗長ストレージ (LRS) だけです。

## <a name="changing-replication-strategy"></a>レプリケーション戦略の変更
[Azure portal](https://portal.azure.com/)、[Azure PowerShell](storage-powershell-guide-full.md)、[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)、[Azure クライアント ライブラリ](https://docs.microsoft.com/azure/index#pivot=sdkstools)のいずれかを使用して、ストレージ アカウントのレプリケーション戦略を変更できます。 使用しているストレージ アカウントのレプリケーションの種類を変更してもダウンタイムは発生しません。

   > [!NOTE]
   > 現在、Azure Portal または API を使用してアカウントを ZRS に変換することはできません。 アカウントのレプリケーションを ZRS に変換する場合は、[ゾーン冗長ストレージ (ZRS)](storage-redundancy-zrs.md) に関するページを参照してください。
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>アカウントのレプリケーション戦略を変更するためにコストはかかりますか
変換パスによって変わります。 最も冗長サービスのコストが低いものから高いサービスの順に並べると、LRS、ZRS、GRS、RA-GRS があります。 たとえば、LRS *から*何かに移行する場合、より高度な冗長レベルに移行することになるので、追加料金が発生します。 GRS または RA-GRS *に*移行する場合、(プライマリ リージョン内の) データがリモートのセカンダリ リージョンにレプリケートされているため、送信帯域幅の料金がかかります。 これは初期設定時に 1 回かかる料金です。 データがコピーされた後に、追加の変換料金はかかりません。 新しいデータや既存のデータへの更新をレプリケートする場合にのみ課金されます。 帯域幅の料金の詳細については、[Azure Storage の料金に関するページ](https://azure.microsoft.com/pricing/details/storage/blobs/)をご覧ください。

ストレージ アカウントを GRS から LRS に変換した場合、追加のコストは発生しませんが、レプリケートされたデータはセカンダリ ロケーションから削除されます。

ストレージ アカウントを RA-GRS から GRS または LRS に変換した場合、そのアカウントは変換日からさらに 30 日間、RA-GRS として課金されることにご注意ください。

## <a name="see-also"></a>関連項目

- [ローカル冗長ストレージ (LRS):Azure Storage の低コストのデータ冗長性](storage-redundancy-lrs.md)
- [ゾーン冗長ストレージ (ZRS): 高可用 Azure Storage アプリケーション](storage-redundancy-zrs.md)
- [geo 冗長ストレージ (GRS):Azure Storage のリージョン間レプリケーション](storage-redundancy-grs.md)
- [Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](storage-scalability-targets.md)
- [RA-GRS ストレージを使用した高可用性アプリケーションの設計](../storage-designing-ha-apps-with-ragrs.md)
- [Microsoft Azure Storage 冗長オプションと読み取りアクセス geo 冗長ストレージ](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [SOSP ペーパー - Azure Storage:強力な整合性を備えた高可用クラウド ストレージ サービス](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
