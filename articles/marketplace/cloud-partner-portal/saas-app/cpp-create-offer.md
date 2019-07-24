---
title: Azure SaaS アプリケーション オファーを作成する | Microsoft Docs
description: Azure Marketplace にある SaaS アプリケ―ション オファーを作成する方法。
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
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: bcaf470fae34056fe932cf8b5fd3076899f895ae
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880281"
---
# <a name="create-a-new-saas-application-offer"></a>新しい SaaS アプリケ―ション オファーを作成する

この記事では、Azure Marketplace 用のSaaS アプリケ―ション (アプリ) オファー エントリを作成して発行する方法について説明します。

## <a name="offer-process"></a>オファー プロセス

次の図は、SaaS アプリ オファーを作成するためのプロセスを示しています。

![SaaS オファーの作成プロセス](./media/saas-offer-process-overview.png)

## <a name="offer-components"></a>オファーのコンポーネント

SaaS アプリ オファーは、次の表に示す 5 つのセクションで構成されます。

|  **資産グループ**   |  **説明**  |
|  ---------------   |  ---------------  |
|    プラン設定  |  SaaS アプリの一意の ID を構成するときに使用します。                 |
|  技術情報    |  アプリケーションの SaaS ソリューションの種類を構成し、接続の詳細を指定するときに使用します。                |
|  チャンネル情報      |   GTM の素材、連絡先などのチャネル情報を提供します。                |
|  体験版        |   このオプションのセクションでは、顧客がオファーを購入前にテストできるようにするサービスを定義します。                |
|  ネットショップの詳細       | マーケティング、法律、およびリード管理の資産と仕様が含まれます。  <ul><li> マーケティング資産には、オファーの名前、説明、およびロゴが含まれます</li> <li> 法的資産には、プライバシー ポリシー、使用条件、その他の法的文書が含まれます</li>  <li> リード管理ポリシーにより、Azure Marketplace エンド ユーザー ポータルからリードを処理する方法を指定できます。</li> </ul> |
| 連絡先            | サポート連絡先およびポリシー情報が含まれます |

## <a name="new-offer-form"></a>[新しいプラン] フォーム

[Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインし、左側のメニュー バーで **[+ 新しいオファー]** を選択します。 [新しいオファー] メニューで **[SaaS アプリ]** を選択して新しいオファー フォームを表示し、新しい SaaS アプリケーション オファーの資産を定義するプロセスを開始します。

![SaaS アプリの [新しいオファー] メニュー](./media/azure-new-saas-offer.png)

## <a name="next-steps"></a>次の手順

SaaS オファーの種類に対応する [新しいオファー] ページには、新しいオファーを作成するのに使用する一連のタブとフォーム フィールドが用意されています。 以下の各記事では、タブを使用して新しいオファーの資産グループとサポート サービスを定義する方法が説明されています。

- [[プランの設定] タブ](./cpp-offer-settings-tab.md)
- [[技術情報] タブ](./cpp-technical-info-tab.md)
- [[チャンネル情報] タブ](./cpp-channel-info-tab.md)
- [[体験版] タブ](./cpp-testdrive-tab.md)
- [[ネットショップの詳細] タブ](./cpp-storefront-tab.md)
- [[連絡先] タブ](./cpp-contacts-tab.md)
