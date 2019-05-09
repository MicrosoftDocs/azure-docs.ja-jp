---
title: Power BI アプリ オファーを更新する - Azure Marketplace | Microsoft Docs
description: Power BI アプリ プランが Microsoft AppSource Marketplace で発行されたら、そのプランを更新します。
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
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
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 953c53b4ef2244b3444abb745f52e125306468f2
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807118"
---
# <a name="update-a-power-bi-app-offer"></a>Power BI アプリ オファーの更新

この記事では、[Cloud パートナー ポータル](https://cloudpartner.azure.com/)で Microsoft Power BI アプリ オファーを更新し、オファーを再発行する方法について説明します。 オファーを更新する一般的な理由は次のとおりです。

- Power BI でアプリのコンテンツを更新し、新しくパッケージ化されたアプリからインストール URL を取得するため
- オファーの Azure Marketplace メタデータ (販売、マーケティング、またはサポートの情報と資産) を更新するため
 
このような変更を追跡するには、ポータルの **[比較]** 機能と **[履歴]** 機能を使用します。

## <a name="common-update-operations"></a>共通の更新操作

Power BI アプリ オファーの一部の属性は、そのオファーが AppSource に公開された後に変更することはできません。 変更が許可されていない属性として、**オファー ID** や**パブリッシャー ID** などがあります。 ただし、引き続きさまざまな特性を変更できます。 一般的な変更を以下に示します。

### <a name="update-app-content-in-power-bi"></a>Power BI でアプリ コンテンツを更新する

多くの場合、Power BI アプリは、新しいコンテンツ、セキュリティ パッチ、機能などで定期的に更新されます。 アプリが変更されたら、次の手順に従ってインストール URL を更新します。

1.  [クラウド パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。
2.  **[すべてのプラン]** から、更新するプランを見つけます。
3.  **[技術情報]** タブで、新しいインストール URL を入力します。
4.  **[発行]** を選択して、アプリの新しいバージョンを AppSource に発行するワークフローを開始します。


### <a name="update-the-offers-marketplace-metadata"></a>オファーの Marketplace メタデータを更新する

オファーの Marketplace メタデータには、会社名、ロゴ、その他の情報が含まれています。 メタデータを更新するには、次の手順に従います。

1.  [クラウド パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。
2.  **[すべてのプラン]** から、更新するプランを見つけます。
3.  **[ネットショップの詳細]** タブで、「[Power BI アプリの [ネットショップの詳細] タブ](./cpp-storefront-details-tab.md)」の指示に従ってメタデータを変更します。
4.  **[発行]** を選択し、変更を発行するワークフローを開始します。


## <a name="the-compare-feature"></a>[比較] 機能

発行済みのオファーを更新したときに、加えた変更を確認できます。 **[比較]** 機能を使用するには、次の手順に従います。

1.  編集プロセスの任意の時点で、オファーの **[比較]** ボタンを選択します。

    ![[比較] ボタン](./media/compare-feature-button.png)

2.  マーケティングの資産やメタデータのバージョンを横に並べて表示します。


## <a name="history-of-publishing-actions"></a>発行操作の履歴

オファーの発行履歴を表示するには、Cloud パートナー ポータルの左側にある **[履歴]** タブを開きます。ここには、AppSource のオファーに対するタイムスタンプ付きの操作の履歴が表示されます。

## <a name="next-steps"></a>次の手順

Cloud パートナー ポータルで、[Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md) を定期的に使用して、Marketplace の顧客と使用状況に関する有用な情報を見つけます。  
