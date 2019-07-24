---
title: Azure SaaS アプリケーションのオファーの設定 | Microsoft Docs
description: Azure Marketplace 上で SaaS アプリケーション オファー用の [プランの設定] を構成します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 8b8810d3dc899a87b99422c093b6901ed9683325
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53195801"
---
# <a name="saas-application-offer-settings-tab"></a>SaaS アプリケーションの [プランの設定] タブ

この記事では、オファー用の [プランの設定] を構成する方法について説明します。

**[SaaS アプリ] > [新しいプラン]** ページが開くと、**[プランの設定]** タブにフォーカスが設定されています。 

次の画面キャプチャに示すように、[プランの設定] タブを使用して **[プラン ID]** を構成します。 フィールド名に付いているアスタリスク (*) は、そのフィールドが必須であることを示します。

![[プランの設定] タブ](./media/saas-new-offer.png)

## <a name="offer-identity-settings"></a>オファー ID の設定

[プラン ID] では、次の表で説明するフィールドの情報を入力する必要があります。 


|  **フィールド名**   |  **説明**  |
|  ---------------   |  ---------------  |
|    プラン ID  |  発行元プロファイル内のオファーを表す一意識別子です。 この ID は製品の URL と課金レポートに表示されます。 小文字の英数字またはハイフン (-) のみで構成できます。 ID の末尾にはダッシュを使用できず、最大 50 文字に制限されます。 プランの運用が開始するとこのフィールドがロックされるので注意してください。 たとえば、Contoso という発行元が sample-vm というオファー ID を使用してオファーを発行した場合、Azure Marketplace に https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview として表示されます。                 |
|  パブリッシャー ID    |  発行元 ID は、Marketplace における発行元の一意識別子です。 すべてのオファリングは発行元 ID に関連付けられている必要があります。 発行元 ID は、オファーを保存した後は変更できません。                |
|  Name      |   これは、プランの表示名です。 この名前が Azure Marketplace と Azure portal に表示されます。 最大で 50 文字の長さにできます。 製品の覚えやすいブランド名を使用してください。 販売方法である場合を除き、会社名はここに含めないでください。 このオファーを貴社の Web サイトでマーケティングしている場合は、この名前が貴社の Web サイトで表示される名前とまったく同じであることを確認してください。               |

**[保存]** を選択して進捗状況を保存します。

## <a name="next-steps"></a>次の手順

[[技術情報] タブ](./cpp-technical-info-tab.md)
