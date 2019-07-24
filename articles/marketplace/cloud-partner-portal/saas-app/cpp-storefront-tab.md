---
title: Azure SaaS アプリケーション オファー ストアフロント構成 | Microsoft Docs
description: Azure Marketplace にある SaaS アプリケーション オファーのストアフロントを構成します。
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
ms.openlocfilehash: 7c4bd326c597b8e989dfc7aac5239ddb2d38376a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57884772"
---
# <a name="saas-application-storefront-details-tab"></a>SaaS アプリケーションの [ネットショップの詳細] タブ

この記事では、[ネットショップの詳細] タブを使用し、SaaS アプリについて説明し、マーケティング資産を指定する方法について説明します。 このタブには、[概要]、[Marketing Artifacts]\(マーケティングの成果物\)、[Lead Management]\(潜在顧客の管理\)、[法的情報] フォームがあります。 


## <a name="overview"></a>概要

[概要] フォームには、次の画面キャプチャのように、必須フィールドと任意フィールドが含まれています。 フィールド名に付いているアスタリスク (*) は、そのフィールドが必須であることを示します。

![ネットショップ概要フォーム](./media/saas-storefront-overview.png)

次の表では、オファーに関して指定できるネットショップの詳細についてまとめています。

|  **オファーのフィールド**   |  **説明**  |
|  ---------------   |  ---------------  |
|      オファーの概要              |        オファーの価値提案の概要です。 オファーの検索ページに表示されます。 最大で 100 文字までにする必要があります。           |
|      オファーの説明              |      アプリケーションの詳細ページに表示される説明です。 指定できる最大長は 1300 文字です。 基本的な HTML タグを使用してコンテンツの書式を設定できます。 たとえば、&lt;p&gt;、&lt;h1&gt;、&lt;h2&gt;、&lt;li&gt; です。 書式設定後の説明を確認するには、[http://htmledit.squarefree.com](https://htmledit.squarefree.com) のようなオンライン リアルタイム HTML ツールを使用できます。             |
|          業界          |        このオファーが最も適している業種を選択します。 アプリが複数の業種に関連している場合は、2 つまで選択できます。           |
|          推奨されるカテゴリ (最大 3 個)         |        このオファーが最も適しているカテゴリを選択します。 3 つまで選択できます。           |
|           アプリのバージョン         |        アプリケーションのバージョン番号を入力します。           |
|          検索キーワード (最大 3 個)          |      顧客が Marketplace ネットショップの Web サイトで、アプリケーションを検索するために使用できる最大 3 つの検索キーワードを入力します。             |

## <a name="marketing-artifacts"></a>マーケティングの成果物

マーケティングの成果物フォームを使用し、ロゴ、動画、スクリーンショット、ドキュメントなど、Azure Marketplace のマーケティング資産を識別します。

![ストアフロントのマーケティングの成果物フォーム](./media/saas-storefront-artifacts.png)

次の表はマーケティングの成果物のフィールドについてまとめたものです。

|  **オファーのフィールド**   |  **説明**  |
|  ---------------   |  ---------------  |
|                    |                   |
|                    |                   |
|                    |                   |
|          ロゴ          |        Microsoft SaaS アプリを通じて販売する場合、すべてのロゴ画像を指定してください。 掲載するだけの場合は、必要なロゴは 2 つだけです。 Cloud パートナー ポータルにアップロードされるロゴには、以下のガイドラインを使用してください。<br><ul><li>ロゴのプライマリ カラーとセカンダリ カラーの数は少なくしてください。 Azure の設計には簡単なカラー パレットがあります。 </li><li>黒または白をロゴの背景色として使用しないでください。 Azure portal のテーマ カラーは黒と白です。 Azure portal でお使いのロゴを目立たせるため、黒や白ではない色を使用します。 背景色としてはシンプルなプライマリ カラーをお勧めします。 透明な背景を使用する場合は、ロゴとテキストが黒、白、または青でないことを確認します。 </li><li>ロゴではグラデーションの背景を使用しないでください。 </li><li>ロゴにはテキストを使用しないでください (会社またはブランドの名前であっても)。 ロゴのルック アンド フィールは "フラット" にする必要があり、グラデーションは避ける必要があります。</li><li>ロゴは拡大しないでください。</li></ul>            |
|          ビデオ          |         オファーのビデオのリンクを追加できます。 顧客へのオファーと共に表示される YouTube や Vimeo のビデオへのリンクを使用できます。 また、ビデオのサムネイル画像を入力する必要があります。1280 x 720 ピクセルの png 画像を使用します。 オファーごとに最大 4 つのビデオを指定できます。          |
|          Documents          |          オファーにマーケティング ドキュメントを追加できます。 すべてのドキュメントは PDF 形式である必要があり、オファーごとに最大 3 つのドキュメントを指定できます。         |
|        Screenshots (スクリーンショット)            |         オファーのスクリーンショットを追加できます。 オファーごとに最大 5 つのスクリーンショットを追加できます。 画像の最大サイズは 1280 x 720 ピクセルです。          |
|       便利なリンク             |       アーキテクチャ ダイアグラムや、顧客が見たいと思うその他の Web サイトを参照しやすいように、オファーに関連した外部 URL を追加することができます。            |

### <a name="marketing-examples"></a>マーケティングの例

Marketplace 検索結果の例が次の画面キャプチャです。

![Marketplace の検索結果](./media/saas-marketplace-search-result.png)

検索結果のオファーのタイルを顧客がクリックすると、次の画像のようにオファーが Marketplace に表示されます。

![Marketplace の検索結果の詳細](./media/saas-marketplace-search-result-details.png)

## <a name="lead-management"></a>見込み客管理

潜在顧客の管理を構成するには、ドロップダウン リストから**潜在顧客の送信先**を選択します。 次の画面キャプチャのように、選択できる送信先が表示されます。

![ストアフロント詳細の潜在顧客の管理](./media/saas-storefront-lead-destination.png)

>[!TIP] 
>情報アイコンを選択すると、"潜在顧客を保存するシステムを選択してください。" というメッセージが表示されます。 CRM システムに接続する方法については[こちら](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)をご覧ください。

## <a name="legal"></a>法的情報

[法的情報] フォームを使用し、オファーごとに必要な法的ドキュメントを提供します。

![ストアフロント詳細の法的情報フォーム](./media/saas-storefront-lead-legal.png)

次の情報を指定します。

- [プライバシー ポリシー URL] – アプリのプライバシー ポリシーのリンクを入力します。
- [利用規約] – アプリの利用規約を入力します。 顧客は、アプリを試す前にこれらの条件を承諾する必要があります。

## <a name="next-steps"></a>次の手順

[[連絡先] タブ](./cpp-contacts-tab.md)
