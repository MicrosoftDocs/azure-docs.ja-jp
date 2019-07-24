---
title: Azure CDN DDoS Protection の機能 | Microsoft Docs
description: Microsoft からの Azure CDN が追加コストなしの基本的な DDoS Protection によって保護されています
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: magattus
ms.openlocfilehash: 3aefe2ff3891857ec3588e4b48f53876c5c90aa2
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58516979"
---
# <a name="azure-cdn-ddos-protection"></a>Azure CDN DDoS Protection

コンテンツ配信ネットワークは、設計により DDoS protection を提供します。 帯域幅消費型攻撃を吸収するグローバルな容量に加え、Azure CDN には、以下に概説する、追加コスト不要の追加の DDoS 保護が用意されています。

## <a name="azure-cdn-from-microsoft"></a>Microsoft Azure CDN

Microsoft の Azure CDN は、[Azure Basic DDoS](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview) で保護されています。 これは既定で、追加コストなしに Microsoft の Azure CDN プラットフォームに統合されています。 Microsoft の Azure CDN のグローバルに展開されるネットワークのフル スケールと容量が、常時有効なトラフィック監視とリアルタイムのリスク軽減によって、一般的なネットワーク層攻撃からの保護を提供します。 また、Basic DDoS の保護機能は、特に一般的で頻繁に発生するレイヤー 7 への DNS クエリ フラッド攻撃や、CDN エンドポイントを標的とするレイヤー 3 および 4 への帯域幅消費型攻撃も防御します。 このサービスには、マイクロソフトのエンタープライズ サービスとコンシューマー サービスを大規模な攻撃から保護してきた実績があります。

## <a name="azure-cdn-from-verizon"></a>Azure CDN from Verizon

Azure CDN from Verizon は、Verzion の独自の DDoS 対応策プラットフォームによって保護されます。 これは既定で、追加コストなしに Azure CDN from Verizon に統合されています。 特に一般的で頻繁に発生するレイヤー 7 への DNS クエリ フラッド攻撃や、CDN エンドポイントを標的とするレイヤー 3 および 4 への帯域幅消費型攻撃に対する基本的な保護機能も提供します。

## <a name="azure-cdn-from-akamai"></a>Azure CDN from Akamai

Azure CDN from Akamai は、Akamai の独自の DDoS 対応策プラットフォームによって保護されます。 これは既定で、追加コストなしに Azure CDN from Akamai に統合されています。 特に一般的で頻繁に発生するレイヤー 7 への DNS クエリ フラッド攻撃や、CDN エンドポイントを標的とするレイヤー 3 および 4 への帯域幅消費型攻撃に対する基本的な保護機能も提供します。

## <a name="next-steps"></a>次の手順

[Azure DDoS](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview) の詳細を学習する。 
