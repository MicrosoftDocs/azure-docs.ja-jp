---
title: Azure Data Factory でのデータの冗長性 |Microsoft Docs
description: Azure Data Factory のメタデータの冗長化メカニズムについて説明します
author: nabhishek
ms.reviewer: abnarain
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: abnarain
ms.openlocfilehash: 9d1c22b9ac6912f99838733a4326cb4082f49a6c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100369698"
---
# <a name="azure-data-factory-data-redundancy"></a>**Azure Data Factory のデータの冗長性**

Azure Data Factory のデータには、メタデータ (パイプライン、データセット、リンクされたサービス、統合ランタイムとトリガー) と監視データ (パイプライン、トリガー、アクティビティの実行) が含まれます。 

メタデータの損失を防ぐため、すべてのリージョン (ブラジル南部と東南アジアを除く) では、Azure Data Factory のデータが[ペアのリージョン](../best-practices-availability-paired-regions.md#azure-regional-pairs)に格納およびレプリケートされています。 リージョン内のデータセンターで障害が発生した場合、Microsoft は Azure Data Factory インスタンスのリージョン内フェールオーバーを開始することがあります。 ほとんどの場合、顧客側では何もする必要はありません。 Microsoft が管理するフェールオーバーが完了すると、フェールオーバー リージョンの Azure Data Factory にアクセスできるようになります。 

ブラジル南部および東南アジアでは、データ所在地の要件により、Azure Data Factory のデータは[ローカル リージョンにのみ](../storage/common/storage-redundancy.md#locally-redundant-storage)格納されます。 東南アジアでは、すべてのデータがシンガポールに格納されます。 ブラジル南部では、すべてのデータがブラジルに格納されます。 重大な障害によってリージョンが失われた場合、Microsoft はユーザーの Azure Data Factory データを回復できなくなります。  

> [!NOTE]
> Microsoft が管理するフェールオーバーは、セルフホステッド統合ランタイム (SHIR) には適用されません。これは、このインフラストラクチャが通常はカスタマー マネージドであるためです。 Azure VM で SHIR がセットアップされている場合は、[Azure Site Recovery](../site-recovery/site-recovery-overview.md) を活用して、別のリージョンへの [Azure VM フェールオーバー](../site-recovery/azure-to-azure-architecture.md)を処理することをお勧めします。



## <a name="using-source-control-in-azure-data-factory"></a>**Azure Data Factory のソース管理を使用する**

Azure Data Factory のメタデータに対して行われた変更を追跡して監査できるようにするには、Azure Data Factory のソース管理を設定することを検討する必要があります。 これにより、パイプライン、データセット、リンクされたサービス、およびトリガーのメタデータ JSON ファイルにアクセスすることもできるようになります。 Azure Data Factory を使用すると、さまざまな Git リポジトリ (Azure DevOps と GitHub) を操作できます。 

 [Azure Data Factory のソース管理](./source-control.md)を設定する方法について確認してください。 

> [!NOTE]
> 障害が発生した場合 (リージョンが失われた場合)、新しいデータ ファクトリを手動または自動でプロビジョニングできます。 新しいデータ ファクトリを作成したら、既存の Git リポジトリからパイプライン、データセット、およびリンクされたサービスの JSON を復元できます。 



## <a name="data-stores"></a>**データ ストア**

Azure Data Factory を使用すると、オンプレミスやクラウド上にあるデータ ストア間でデータを移動できます。 データ ストアとのビジネス継続性を確保するには、各データ ストアのビジネス継続性に関する推奨事項を参照する必要があります。 

 

## <a name="see-also"></a>関連項目

- [Azure リージョン ペア](../best-practices-availability-paired-regions.md)
- [Azure におけるデータ所在地](https://azure.microsoft.com/global-infrastructure/data-residency/)