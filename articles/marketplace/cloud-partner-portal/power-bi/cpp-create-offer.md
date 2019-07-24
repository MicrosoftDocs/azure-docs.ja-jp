---
title: Power BI アプリ オファーを作成する - Azure Marketplace | Microsoft Docs
description: Microsoft AppSource Marketplace 用に Power BI アプリ オファーを作成する方法。
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
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: 6a4f7daa337618278c3652fad3053c20557a9e28
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2019
ms.locfileid: "56414690"
---
# <a name="create-a-power-bi-app-offer"></a>Power BI アプリ オファーを作成する

この記事では、[AppSource](https://appsource.microsoft.com) に新しい Power BI アプリ オファーを作成するための実行手順について説明します。 各プランは、AppSource で独自のエンティティとして表示されます。 [Cloud パートナー ポータル](https://cloudpartner.azure.com/)で新しいプランを作成するときに、ご利用のプランに対して資産グループを 4 つ指定する必要があります。

資産グループについては次の表で説明します。

|   資産グループ      | 説明                                                                         |
| ----------------   | ----------------                                                                    |
| オファーの設定     | オファーのプライマリ ID と名前。                                      |
| 技術情報     | クライアントの Power BI ワークスペースにアプリをインストールするために使用するインストーラーの URL。 この URL を生成する方法の詳細については、[Power BI アプリに関するドキュメント](https://go.microsoft.com/fwlink/?linkid=2028636)を参照してください。 |
| ネットショップの詳細 | マーケティング、法律、およびリード管理の資産が含まれます。 マーケティング資産には、プランの説明およびロゴが含まれます。 法的資産には、プライバシー ポリシー、使用条件、およびその他の法的文書が含まれます。 リード管理ポリシーにより、AppSource ユーザー ポータルからリードを処理する方法を指定できます。 |
| 連絡先           | サポート連絡先およびポリシー情報が含まれます                                     |

## <a name="new-offer-form"></a>[新しいプラン] フォーム

Cloud パートナー ポータルにサインインしてから、左側のウィンドウの **[新しいプラン]** を選択します。 次に、[新しいプラン] フォームを表示し、新しいアプリ プランに資産を定義するプロセスを開始するために、**[Power BI アプリ]** を選択します。

![Power BI プランのメニュー項目](./media/new-offer-menu.png)

> [!NOTE] 
> **[Power BI アプリ]** オプションが表示されない場合、または有効にならない場合は、このプランの種類を作成するためのアクセス許可がアカウントにありません。 開発者アカウントの登録など、このオファーの種類に対するすべての[前提条件](./cpp-prerequisites.md)を満たしていることを確認してください。


## <a name="next-steps"></a>次の手順

以下の後続の記事では、Power BI アプリ オファーの種類の **[新しいプラン]** ページのタブについて説明しています。 各記事では、新しい Power BI アプリ オファーの資産グループとサポートされるサービスについて説明されています。

-  [[プランの設定] タブ](./cpp-offer-settings-tab.md)
-  [[技術情報] タブ](./cpp-technical-info-tab.md)
-  [[ネットショップの詳細] タブ](./cpp-storefront-details-tab.md)
-  [[連絡先] タブ](./cpp-contacts-tab.md)
