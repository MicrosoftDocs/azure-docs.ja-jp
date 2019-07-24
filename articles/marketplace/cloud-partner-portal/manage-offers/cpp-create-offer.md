---
title: Marketplace のオファーを作成する - Azure Marketplace | Microsoft Docs
description: Cloud パートナー ポータルを使用して Azure Marketplace および AppSource Marketplace でオファーを作成する
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
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
ms.date: 01/10/2019
ms.author: pbutlerm
ms.openlocfilehash: af9b34d90098409135020fa8a45ecd0253f25b22
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454546"
---
# <a name="create-azure-marketplace-and-appsource-offers"></a>Azure Marketplace および AppSource のオファーを作成する

Cloud パートナー ポータルの重要な目的の 1 つは、パブリッシャーが Microsoft Azure Marketplace および AppSource Marketplace へのオファーを作成 (および発行) できるようにすることです。  この操作は、常に [[新しいプラン] メニュー](../portal-tour/cpp-new-offer-menu.md)から目的のオファーの種類を選択することから始まります。  その結果、そのオファーの種類に適した **[新しいプラン]** ページが表示されます。  たとえば、次の図は、Azure アプリケーションの種類の既定の **[新しいプラン]** ページを示しています。

![[新しいプラン] の既定のページ](./media/new-offer-page.png)

このページの上部に表示される横方向のメニューバーでは、2 つのタブを選択できます。 
- **[エディター]** タブ - 新しいオファー インスタンスの情報の入力と資産のアップロードを行うことができます。  このタブは既定で表示されます。
- **[状態]** タブ - 発行の状態が表示されます。また、検証とレビューの問題がある場合は一覧表示されます。 

オファーを作成するときは、**[エディター]** タブを使用してそのオファーに関する情報を入力します。 

## <a name="editing-operations"></a>編集操作

データの入力領域にある横方向のツール バーには、次のボタンが表示されます。

|   ボタン    |   目的                                                          |
|   ------    |  --------                                                          |
| **[[Save]]**    | 最近のデータ入力の変更を保存します。  ページから離れる前に変更を手動で保存する必要があります。そうしないと、変更は失われます。 | 
| **破棄** | (最後の保存以降の) 最近のデータ入力の変更を破棄します             |
| **比較** | 現在のオファーと発行済みのオファーの状態を比較します。  オファーが正常に発行された後にのみ有効になります。  |
| **[発行]** | このオファーの発行プロセスを開始します                       |
| **削除**  | 作成から発行までの間に、このオファーを削除します。 |
|   |   |


## <a name="editing-tabs"></a>編集タブ

オファーを作成するときに、**[新しいプラン]** ページの左側の縦の列にある各タブに必須データと省略可能データを入力します。  テキスト ボックス、ドロップダウン メニュー、チェック ボックスなどの標準のユーザー インターフェイス コントロールがデータ コレクションに対して表示されます。  編集タブの一部のコレクションはオファーの種類によって変わりますが、次の表に一般的なタブの一部を示します。

|      タブ名       |   目的                                                            |
|      --------       |   -------                                                            |
| **オファーの設定**  | オファーおよびパブリッシャーの ID 報を収集します。                    |
| **SKU**            | オファーの各在庫管理単位 (SKU) バージョンの技術的およびビジネス的特性を定義します |
| **体験版**      | このオプション機能をサポートする種類の場合、オファーのデモを定義します。  詳細については、「[体験版とは](../test-drive/what-is-test-drive.md)」を参照してください。  |
| **Marketplace** または**ストアフロント** | マーケットプレースにオファーを掲載するために使用されるテキスト文字列、ドキュメント、および画像を収集します |
| **サポート**         | 顧客、エンジニアリング、およびオンライン サポートの連絡先情報を収集します  |
|  |  |

似た名前のタブでも、オファーの種類によって内容が異なる場合があります。  これらのタブのオファー固有の詳細は、各オファーの種類の [プランの作成] セクション内に表示されます。


## <a name="next-steps"></a>次の手順

オファーを作成して保存した後は、発行する前後に[状態を表示](./cpp-view-status-offer.md)することができます。
