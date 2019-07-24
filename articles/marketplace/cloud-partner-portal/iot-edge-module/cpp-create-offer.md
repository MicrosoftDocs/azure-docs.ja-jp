---
title: Azure IoT Edge モジュールのオファーを作成する | Microsoft Docs
description: Marketplace で新規 IoT Edge モジュールを公開する方法。
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
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 69ee0c0add2895b30a60db577cc11c83a0e00bb8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57854774"
---
# <a name="create-a-new-iot-edge-module-offer-with-the-cloud-partner-portal"></a>Cloud パートナー ポータルで新しい IoT Edge モジュールのオファーを作成する

この記事では、Azure Marketplace 向けの IoT Edge モジュール オファー エントリを作成して発行する方法について説明します。 各オファーは、Azure Marketplace では専用のエンティティとして表示され、1 つ以上の SKU に関連付けられます。  IoT Edge モジュールのオファーは、以下のグループの資産およびサポート サービスで構成されます。

|  **資産グループ**   |  **説明**  |
|  ---------------   |  ---------------  |
|    SKU            |  オファーの展開可能な最小単位。 1 つのオファー (製品クラス) に、複数の SKU を関連付けることができます。 SKU を使用して、サポートされる機能および課金モデルを区別することができます。 |
|  マーケットプレース       | マーケティング、法律、およびリード管理の資産と仕様が含まれます。  <ul><li> マーケティング資産には、オファーの名前、説明、およびロゴが含まれます</li> <li> 法的資産には、プライバシー ポリシー、使用条件、その他の法的文書が含まれます</li>  <li> リード管理ポリシーにより、Azure Marketplace エンド ユーザー ポータルからリードを処理する方法を指定できます。</li> </ul> |
| サポート            | サポート連絡先およびポリシー情報が含まれます |


## <a name="new-offer-form"></a>新しいオファー フォーム 

[Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインし、左側のメニュー バーで **[+ 新しいプラン]** を選択します。 [新しいプラン] メニューで、**[IoT Edge モジュール]** を選択して **[新しいオファー]** フォームを表示し、新しい ioT Edge モジュール オファーの資産を定義するプロセスを開始します。 

![新しい IoT Edge モジュール オファーのユーザー インターフェイスの選択](./media/new-iot-edge-module-offer.png)

## <a name="next-steps"></a>次の手順

IoT Edge モジュール オファーの種類に対する **[新しいプラン]** ページで提供されている一連のタブとフォーム フィールドを使用して、新しいオファーを作成できます。 以下の各記事では、タブを使用して新しい IoT Edge モジュール オファーの資産グループとサポート サービスを定義する方法が説明されています。

- [[プランの設定] タブ](./cpp-offer-settings-tab.md)
- [[SKU] タブ](./cpp-skus-tab.md)
- [[Marketplace] タブ](./cpp-marketplace-tab.md)
- [[サポート] タブ](./cpp-support-tab.md)
