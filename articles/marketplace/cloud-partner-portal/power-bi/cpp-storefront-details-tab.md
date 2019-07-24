---
title: Power BI アプリ オファーの [ネットショップの詳細] - Azure Marketplace | Microsoft Docs
description: Microsoft AppSource Marketplace 用に Power BI アプリ オファーの [ネットショップの詳細] のフィールドを構成します。
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
ms.date: 01/30/2019
ms.author: pbutlerm
ms.openlocfilehash: 2c99b20e554d92dbe63594d63525dd8e1d765423
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806812"
---
# <a name="power-bi-app-storefront-details-tab"></a>Power BI アプリの [ネットショップの詳細] タブ

見込み顧客にマーケティング、販売、および法律に関する情報を提供するには、**[新しいオファー]** ページの **[ネットショップの詳細]** タブを使用します。 このタブで、Azure Marketplace が生成するリードの管理も設定できます。 この長いフォームは、次の 6 つのセクションに分かれています:**[Offer Details]\(オファーの詳細\)**、**[Listing Details]\(一覧の詳細\)**、**[Marketing Artifacts]\(マーケティングの成果物\)**、**[Legal]\(法的情報\)**、**[Customer Support]\(カスタマー サポート\)**、**[Lead Management]\(リード管理\)**。  フィールド ラベルの末尾のアスタリスク (*) は、そのフィールドが必須であることを意味しています。


## <a name="offer-details-section"></a>[オファーの詳細] セクション

**[オファーの詳細]** セクションで、AppSource オファーに関する全般的な情報を入力します。

![[ネットショップの詳細] タブの [オファーの詳細] セクション](./media/offer-details-section.png)

このセクションのフィールドを完成する際、次の表を参照してください。

|   フィールド               |   説明                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------|
| **[Offer summary]\(オファーの概要\)**     | アプリの簡単な目的 (最大 100 文字)。                             |
| **プランの説明** | アプリの説明 (最大 3,000 文字)。 このフィールドは単純な HTML 書式設定をサポートしています。 |
|                       |                                                                                         |


## <a name="listing-details-section"></a>[一覧の詳細] セクション

**[一覧の詳細]** セクションは、一般に使用される業種、アプリのカテゴリ、互換性のある製品、関連付けられた検索語句など、アプリのコンテキストに関する情報を収集します。

![[ネットショップの詳細] タブの [一覧の詳細] セクション](./media/listing-details-section.png)

このセクションのフィールドを完成する際、次の表を参照してください。
 
|   フィールド                                  |   説明                                                        |
| --------------                           | ---------------------                                                |
| **業界**                           | アプリが最も適している業種を選択します。 アプリが複数の業種に関連しているなら、このフィールドを空白のままにします。      |
| **Categories (カテゴリ)**                           | アプリに関連するカテゴリを最大 3 つ選択します。     |
| **Help link for your app (アプリのヘルプ リンク)**               | アプリのオンライン ヘルプを提供するページの URL を指定します。           |
| **Products your app works with (Max 3) (アプリが連携する製品 (最大 3 個))** | テキスト フィールドを作成するには、**[新規]** プラス記号を選択します。 このフィールドに、アプリが対応する製品の名前を入力します。 最大 3 つの製品をリストします。       |
| **Search keywords (Max 3) (検索キーワード (最大 3 個))**              | AppSource でアプリケーションを検索するのにユーザーが使用する可能性のあるキーワードを、最大 3 つ入力します。 たとえば、アプリケーションの名前が My Emailing app であるなら、**emails**、**mailing**、**mail app** のようにキーワードを指定できます。 |
|  |  |


## <a name="marketing-artifacts-section"></a>[マーケティングの成果物] セクション

**[マーケティングの成果物]** セクションで、AppSource に表示するブランドとマーケティング関連の資料をアップロードします。  このセクションは、次の 4 つのサブセクションに分かれています。**[Logos]\(ロゴ\)**、**[Videos]\(ビデオ\)**、**[Documents]\(ドキュメント\)**、**[Screenshots]\(スクリーンショット\)**。 必須のマーケティング成果物はロゴとスクリーンショットのみです。 ただし、顧客に最もアピールできるように、ビデオとドキュメントも追加することをお勧めします。

![[ネットショップの詳細] タブの [マーケティングの成果物] セクション](./media/marketing-artifacts-section.png)

このセクションのフィールドを完成する際、次の表を参照してください。
 
|    フィールド                             |    説明                                                    |
|   -----------                        |    -------------                                                  |
| *ロゴ*                              |                                                                   |
| **Offer logo (png format, 48x48) (オファーのロゴ (png 形式、48 x 48))**   | アプリの概要やアプリの検索結果に表示されるロゴをアップロードします。 AppSource では、解像度が 48 x 48 ピクセルの PNG 形式のみサポートします。  |
| **Offer logo (png format, 216x216) (オファーのロゴ (png 形式、216 x 216))** | アプリの詳細ページに表示するロゴをアップロードします。  AppSource では、解像度が 216 x 216 ピクセルの PNG 形式のみサポートします。  |
| *ビデオ*                             |                                                                   |
| **Name**                             | アプリの名前かタイトルを入力します。                                          |
| **URL**                              | YouTube や Vimeo でホストされているビデオの URL を入力します。                              |
| **サムネイル**                        | アプリのサムネイル画像を追加します。  AppSource では、解像度が 1280 x 720 ピクセルの PNG 形式のみサポートします。   |
| *ドキュメント*                          | AppSource の **[詳細情報]** の見出しの下に表示されるドキュメントを、最大 3 つ追加します。  |
| **Name**                             | サポート ドキュメントの名前またはタイトルを入力します。                              |
| **ファイル**                             | PDF ファイルをアップロードします。                             |
| *スクリーンショット*                        | スクリーンショットを最大 5 つ追加します。                        |
| **Name**                             | スクリーンショットの名前またはタイトルを入力します。                                       |
| **Image**                            | PNG スクリーンショットをアップロードします。 解像度は、1280 x 720 ピクセルである必要があります。  | 
|   |   |

[Microsoft Cloud パートナー ポータル](https://cloudpartner.azure.com)にアップロードするロゴに、次のような物は*使用できません*。

- グラデーションを使用したロゴ。 ロゴの外観はフラットにする必要があります。
- 会社名やブランド名などのテキストを含むロゴ。 
- 拡大したように見えるロゴ。

## <a name="legal-section"></a>[法的情報] セクション

**[法的情報]** セクションで、各オファーに必須の 2 つの法的ドキュメント (プライバシー ポリシーと使用条件) を指定します。

![[ネットショップの詳細] タブの [法的情報] セクション](./media/legal-section.png)

このセクションのフィールドを完成する際、次の表を参照してください。

|   フィールド                |   説明                           |
|------------------------|--------------------------------------   |
| **プライバシー ポリシーの URL** | 投稿したプライバシー ポリシーの URL       |
| **使用条件**       | プレーンテキストまたは単純な HTML として書式設定された利用規約     |
|  |  |


## <a name="customer-support-section"></a>[カスタマー サポート] セクション

**[カスタマー サポート]** セクションで、オンライン カスタマー サポート ページの**サポート URL** を指定します。  このページには、電話、メール、ライブ チャットなど、複数の連絡先オプションを指定する必要があります。 


## <a name="lead-management-section"></a>[リード管理] セクション

**[リード管理]** セクションで、AppSource オファーが生み出すリードを収集するようにシステムを設定します。 リードのストレージ オプションを選択する際、次の表を参照してください。

|    フィールド               |   リードの宛先                               |
|------------------------|--------------------------------------            |
|  **なし**              | リードを収集しません。 これが既定のオプションです。  |
| **Azure Blob (deprecated) (Azure Blob (非推奨))** | コンテナー名と接続文字列を使用して [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) を指定します。  この選択肢は推奨されていません。 代わりに、**Azure テーブル**を使用してください。  |
| **Azure テーブル**        | 接続文字列を使用して [Azure Table Storage](https://docs.microsoft.com/azure/cosmos-db/table-storage-overview) を指定します。  |
| **Dynamics CRM Online** | URL と認証資格情報を使用して [Dynamics 365](https://dynamics.microsoft.com/) を指定します。 |
| **HTTPS エンドポイント**     | JSON ペイロードを使用して HTTPS エンドポイントを指定します。   |
| **Marketo**            | サーバー ID、munchkin ID、フォーム ID を使用して [Marketo](https://www.marketo.com/) インスタンスを指定します。   |
| **Salesforce**         | オブジェクト識別子を使用して [Salesforce](https://www.salesforce.com/) を指定します。 |
|  |  |

オファーを発行した後は、リードの接続が検証され、指定した格納先にテスト リードが自動的に送信されます。 現在の顧客管理アーキテクチャを反映するには、リード情報を継続的に管理し、設定をすぐに更新する必要があります。


## <a name="next-steps"></a>次の手順

[[連絡先]](./cpp-contacts-tab.md) タブで、オファーに対するテクニカル サポートとユーザー サポートのリソースを指定します。
