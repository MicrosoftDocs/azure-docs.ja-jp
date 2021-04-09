---
title: Microsoft とのピアリングを設定するための前提条件
titleSuffix: Azure
description: Microsoft とのピアリングを設定するための前提条件
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: bc45bc8809eabe75902b602835ea595b56ff3cf8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586842"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>Microsoft とのピアリングを設定するための前提条件

新しいピアリングを要求する前、または従来のピアリングを Azure リソースに変換する前に、以下の前提条件を満たしていることを確認してください。

## <a name="azure-related-prerequisites"></a>Azure 関連の前提条件
* **Microsoft Azure アカウント:** Microsoft Azure アカウントを持っていない場合は、[Microsoft Azure アカウント](https://azure.microsoft.com/free)を作成します。 ピアリングは Azure サブスクリプション内のリソースとしてモデル化されるため、ピアリングを設定するには、有効でアクティブな Microsoft Azure サブスクリプションが必要です。 次の点に注意してください。
    * ピアリングの設定に使用される Azure リソースの種類は常に無料の Azure 製品です。つまり、Azure アカウントの作成、サブスクリプションの作成、またはピアリングを設定するための Azure リソース **PeerAsn** および **Peering** へのアクセスには課金されません。 これは、お客様と Microsoft 間の Direct ピアリングに関するピアリングの契約と混同しないでください。この条件は、ピアリング チームとの間で明確に議論されています。 この点について不明な点がある場合は、[Microsoft ピアリング](mailto:peering@microsoft.com)にお問い合わせください。
    * 同じ Azure サブスクリプションを使用して、無料または有料の他の Azure 製品またはクラウド サービスにアクセスすることができます。 有料製品にアクセスすると、料金が発生します。
    * 新しい Azure アカウントまたはサブスクリプションを作成している場合、試用期間中に、Azure Cloud サービスの試用に利用できる無料の Azure クレジットを使用できる場合があります。 詳細については、[Microsoft Azure アカウント](https://azure.microsoft.com/free)のページを参照してください。

* **ピア ASN を関連付ける:** ピアリングを要求する前に、まず、ASN と連絡先の情報をサブスクリプションに関連付けます。 [ピア ASN を Azure サブスクリプションに関連付ける](howto-subscription-association-powershell.md)方法の手順に従ってください。

## <a name="other-prerequisites"></a>その他の前提条件
* **PeeringDB プロファイル:** ピアの [PeeringDB](https://www.peeringdb.com) には、完全な最新のプロファイルが必要です。 登録システムでは、この情報を使用して、NOC 情報、技術担当者の連絡先情報、ピアリング設備での存在など、ピアの詳細が検証されます。

## <a name="next-steps"></a>次のステップ

* [ポータルを使用して Direct ピアリングを作成または変更する](howto-direct-portal.md)
* [ポータルを使用して従来の Direct ピアリングを Azure リソースに変換する](howto-legacy-direct-portal.md)
* [ポータルを使用して Exchange ピアリングを作成または変更する](howto-exchange-portal.md)
* [ポータルを使用して従来の Exchange ピアリングを Azure リソースに変換する](howto-legacy-exchange-portal.md)