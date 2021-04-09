---
title: Azure Integration Runtime の IP アドレス
description: データ ストアへのネットワーク アクセスをセキュリティで保護するためにファイアウォールを適切に構成するには、どの IP アドレスからの受信トラフィックを許可する必要があるかについて説明します。
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: 7b663c8d6e5849d39bb8366c82f45e0fd66d77dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100371398"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Azure Integration Runtime の IP アドレス

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Integration Runtime によって使用される IP アドレスは、Azure Integration Runtime が配置されているリージョンによって異なります。 同じリージョン内の "*すべての*" Azure Integration Runtime は、同じ IP アドレス範囲を使用します。

> [!IMPORTANT]  
> マネージド仮想ネットワークを有効にするデータ フローと Azure Integration Runtime では、固定 IP 範囲の使用はサポートされていません。
>
> これらの IP 範囲は、データ移動、パイプライン、および外部アクティビティの実行に使用できます。 これらの IP 範囲は、Azure Integration Runtime からの受信アクセスについて、データストア/ネットワーク セキュリティ グループ (NSG)/ファイアウォールでフィルター処理するために使用できます。 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Azure Integration Runtime の IP アドレス: 特定のリージョン

リソースが配置されている特定の Azure リージョンの Azure Integration Runtime 用として一覧に示されている IP アドレスからのトラフィックを許可します。 [サービス タグの IP 範囲のダウンロード リンク](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)から、サービス タグの IP 範囲の一覧を取得できます。 たとえば、Azure リージョンが **AustraliaEast** の場合、**DataFactory.AustraliaEast** から IP 範囲の一覧を取得できます。


## <a name="known-issue-with-azure-storage"></a>Azure Storage に関する既知の問題

* Azure Storage アカウントに接続しているとき、IP ネットワーク ルールは、ストレージ アカウントと同じリージョン内の Azure Integration Runtime から送信された要求に対して効果がありません。 詳細については、[こちらの記事を参照](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)してください。 

  代わりに、[Azure Storage に接続しているときは、信頼できるサービス](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)を使用することをお勧めします。 

## <a name="next-steps"></a>次のステップ

* [Azure Data Factory におけるデータ移動のセキュリティに関する考慮事項](data-movement-security-considerations.md)