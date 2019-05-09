---
title: Azure Marketplace での仮想マシン オファーを作成する | Microsoft Docs
description: Azure Marketplace 向けの新しい仮想マシン (VM) オファーを作成するために必要な手順を示します。
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
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: a3124ca2f163291d266fba77255d2ee98d30fd23
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57888810"
---
# <a name="create-virtual-machine-offer"></a>仮想マシン オファーを作成する

このセクションでは、Azure Marketplace 向けの新しい仮想マシン (VM) オファー要求を作成するために必要な手順を示します。  各オファーは、Azure Marketplace では専用のエンティティとして表示され、1 つ以上の SKU に関連付けられます。  VM オファーは、以下のグループの資産およびサポート サービスで構成されます。 

![VM オファーの資産](./media/publishvm_002.png)

各値の説明:

|  **資産グループ**   |  **説明**  |
|  ---------------   |  ---------------  |
|    SKU            |  オファーの購入可能な最小単位です。 1 つのオファー (製品クラス) に複数の SKU を関連付けることができ、それによってサポートされる機能、VM イメージの種類、課金モデルを区別することができます。 |
|  マーケットプレース       | マーケティング、法律、およびリード管理の資産と仕様が含まれます。  <ul><li> マーケティング資産には、オファーの名前、説明、およびロゴが含まれます</li> <li> 法的資産には、プライバシー ポリシー、使用条件、その他の法的文書が含まれます</li>  <li> リード管理ポリシーにより、Azure Marketplace エンド ユーザー ポータルからリードを処理する方法を指定できます。</li> </ul> |
| サポート            | サポート連絡先およびポリシー情報が含まれます |
| 体験版         | エンド ユーザーが購入する前にオファリングをテストできるようにする資産を定義します |
|  |  |


## <a name="new-offer-form"></a>[新しいプラン] フォーム

[Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインした後、左側のメニュー バーの **[+ 新しいプラン]** 項目をクリックします。 表示されるメニューで **[仮想マシン]** をクリックして **[新しいプラン]** フォームを表示し、新しい VM オファーの資産を定義するプロセスを開始します。 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![新しい仮想マシン オファーのユーザー インターフェイスの選択](./media/publishvm_003.png)

> [!WARNING]
> **[仮想マシン]** オプションが表示されない場合、または有効にならない場合は、このオファーの種類を作成するためのアクセス許可がアカウントにありません。  開発者アカウントの登録など、このオファーの種類に対するすべての[前提条件](./cpp-prerequisites.md)を満たしていることを確認してください。


## <a name="next-steps"></a>次の手順

このセクションの以降のトピックは、**[新しいプラン]** ページ (VM オファーの種類用) のタブに対応しています。  各記事では、関連付けられたタブを使用して、新しい VM オファーの資産グループとサポート サービスを定義する方法が説明されています。

- [[プランの設定] タブ](./cpp-offer-settings-tab.md)
- [[SKU] タブ](./cpp-skus-tab.md)
- [[体験版] タブ](./cpp-test-drive-tab.md)
- [[Marketplace] タブ](./cpp-marketplace-tab.md)
- [[サポート] タブ](./cpp-support-tab.md)
