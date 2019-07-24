---
title: Azure Marketplace SEO ガイド | Microsoft Docs
description: 検索エンジンの最適化 (SEO) を最大化するためのガイダンスを提供します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: pbutlerm
ms.openlocfilehash: da7b59400baa4c964dff71fa1f842fede9d68df1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787091"
---
# <a name="azure-marketplace-seo-guidance"></a>Azure Marketplace SEO ガイド

この記事は、[Azure Marketplace](https://azuremarketplace.microsoft.com) と [AppSource](https://appsource.microsoft.com) の検索機能により、オファーの探索可能性を最大化する方法を説明します。 


## <a name="general-explanation-of-algorithm"></a>アルゴリズムの一般的な説明

Microsoft マーケットプレースでは、サイトの検索機能を強化するために Azure Search が使用されます。 アルゴリズムは、用語の出現頻度/逆文書頻度 ([TF-IDF](https://en.wikipedia.org/wiki/Tf–idf)) に基づきます。 標準の [Lucene アナライザー](https://lucene.apache.org/core/)が使用されます。

一般的に、すべてのテキスト フィールド、カテゴリ、および業界が適合性の重み付けに加味されます。 他のアプリではあまり使用されないももの、お客様のアプリで頻繁に使用される特殊な用語は、検索でより高い一致スコアが付けられます。 "VM" などの用語が含まれる場合、あまり効果が上がらないかもしれませんが、"Azure search" のような用語は大きな効果を発揮します。
以下に、考慮すべき重要なフィールドを示します。

 
|  フィールド                   | 重要度 | ガイダンス                                                                                            |
|  --------------------    | ----------                   | ---------------                                                                   |
| プラン名               |  高      | 検索クエリが完全に一致する、または完全一致に近い場合に、高いランクが付けられます。                       |
| 発行元の名前           |  高      | 検索クエリが完全に一致する、または完全一致に近い場合に、高いランクが付けられます。                       |
| 簡単な説明        |  Medium    | アプリと発行元の名前を指定するとほぼ確実に高いランクが付けられる点を考慮すると、それでは最適な結果が得られない可能性があります。 このような場合、簡単な説明が重要になります。 テキストを簡潔にし、的を射たものとします。 最適な結果を得るために、キーワードや予想される検索語句を含めます。  たとえば、"This is the best Retail POS built fully on top of Dynamics 365"\(これは Dynamics 365 上に完全に構築された最適な Retail POS です\) とすると、"Retail POS (point of sale) for Dynamics 365"\(Dynamics 365 向けの Retail POS (Point of Sale)\) とした場合よりも効果が下がります。  | 
| 長い説明         |  低       | 説明では、さらに詳しい内容を示すことができます。 最も効果的な説明は、妥当な長さで、キーワードが使用されているものです。  キーワードを使用した的を射た説明は、長い、冗長なテキストよりも効果が上がります。 "IoT" などの重要な用語を説明で使用するようにしてください。  |
| 製品カテゴリ       | Medium     |  製品カテゴリは、発行元の選択と Microsoft の組み合わせによって決定されます。 ユーザーが適切なカテゴリで、アプリを簡単に見つけることができるように、これらのカテゴリを適切に選択します。 |
|  |  |  |


## <a name="other-tips"></a>その他のヒント

-   検索の提案は、ユーザー アクティビティの負荷を高めます。 アプリの名前/発行元に対する一致を優先します。 簡単な説明は、検索語句が発行元/アプリの名前に完全に一致しない場合に重要なフィールドになります。
-   ダウンロード用の文書には、検索の重み付けは含めません。
-   アプリの実際の購入と使用状況も、検索順位に影響します。 たとえば、同等の 2 つのアプリがあり、一方がはるかに多くのユーザーを持つ場合は、そちらにより高いランクが付けられます。
