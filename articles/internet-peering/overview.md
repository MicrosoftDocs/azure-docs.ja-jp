---
title: Microsoft とのピアリングの設定
titleSuffix: Azure
description: ピアリングの概要
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 8f960e58d47963cbb239d498af52e7adfc77caa9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586868"
---
# <a name="internet-peering-overview"></a>インターネット ピアリングの概要

ピアリングとは、Microsoft オンライン サービスおよび Microsoft Azure サービスとの間でインターネット トラフィックを交換するための、Microsoft のグローバル ネットワーク (AS8075) と事業者のネットワークとの間の相互接続です。 通信事業者またはサービス プロバイダーは、Microsoft の任意のエッジ ロケーションで Microsoft との接続を要求できます。 各要求は、ピアリング ポリシーに準拠していることを確認するために、Microsoft によって審査されます。 Microsoft ネットワークとのピアリングは、次の 2 つの方法で設定できます。

* **Direct ピアリング:**

    ピアリングは、Microsoft エッジの Microsoft ネットワークと事業者のネットワークとの間の物理的な直接接続を介して確立されます。 BGP セッションは、Microsoft のルーティング ポリシーに従うと共に事前に交渉された契約を使用して、これらの接続全体で構成されます。 これは PNI とも呼ばれます。

* **Exchange ピアリング:**

    これは、インターネット エクスチェンジ (IX) での標準のパブリック ピアリングを指します。 Microsoft ネットワークと事業者のネットワークとの間の物理的な接続は、IX によって運用されるスイッチ ファブリックを介して行われます。 BGP セッションは、IX によって提供される IP 空間を使用して構成されます。

## <a name="benefits-of-peering-with-microsoft"></a>Microsoft とのピアリングの利点
* Microsoft とのピアリングを使用して Microsoft のトラフィックを配信することで、転送コストを削減できます。
* Microsoft エッジ ネットワークへのネットワーク ホップと待ち時間を減らすことにより、顧客のパフォーマンスを向上させます。
* 冗長な場所で Microsoft とピアリングすることにより、事業者のネットワークまたは中継プロバイダーのネットワークの障害から顧客のトラフィックを保護します。
* ピアリング接続に関するパフォーマンス メトリックを確認し、分析情報を活用して事業者のネットワークのトラブルシューティングを行います。

## <a name="benefits-of-using-azure-to-set-up-peering"></a>Azure を使用してピアリングを設定する利点

Azure PowerShell またはポータルを使用して、Microsoft とのピアリングを要求できます。 この方法で設定されたピアリングは、Azure リソースとして管理されます。これには、次の利点があります。
* 簡略化された自動化可能な手順を使用して、Microsoft とのピアリングを設定および管理できます。
* すべてのピアリングを 1 か所ですばやく簡単に表示および管理できます。
* すべての接続の状態と帯域幅データを追跡できます。
* 同じサブスクリプションを使用して Azure Cloud Services にアクセスできます。

Microsoft とのピアリングが既に確立されている場合、これらは **レガシ ピアリング** と呼ばれます。 上記の利点を活用するために、このようなピアリングを Azure リソースとして管理することを選択できます。 新しいピアリング要求を送信する場合やレガシ ピアリングを Azure リソースに変換する場合は、以下の「**次のステップ**」セクションのリンク先の記事に従ってください。

## <a name="peering-policy"></a>ピアリング ポリシー
Microsoft は、選択的ではあるものの一般的にオープンなピアリング ポリシーを保持しています。 ピアは、パフォーマンス、能力、および相互利益がある場所に基づいて選択され、特定の技術的、商業的、および法的要件の対象となります。 詳細については、「[ピアリング ポリシー](policy.md)」を参照してください。

## <a name="faq"></a>よく寄せられる質問
ピアリングについてよく寄せられる質問については、「[インターネット ピアリング - FAQ](faqs.md)」を参照してください。

## <a name="next-steps"></a>次の手順

* Microsoft との Direct ピアリングを設定する手順の詳細については、「[Direct ピアリングのチュートリアル](walkthrough-direct-all.md)」を参照してください
* Microsoft との Exchange ピアリングを設定する手順の詳細については、「[Exchange ピアリングのチュートリアル](walkthrough-exchange-all.md)」を参照してください。
* Azure のその他の重要な[ネットワーク機能](../networking/networking-overview.md)について参照してください。