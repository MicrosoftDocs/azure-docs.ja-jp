---
title: オファーの掲載のベスト プラクティス - Microsoft 商業マーケットプレース
description: Microsoft AppSource と Azure Marketplace のオファーを市場に出すための掲載のベスト プラクティスについて説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 07/06/2020
ms.openlocfilehash: 3ea6a0035a9f9354be5c14699936c6a07dea1150
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94492091"
---
# <a name="offer-listing-best-practices"></a>オファーの掲載のベスト プラクティス

この記事では、Microsoft マーケットプレース オファーを作成し、機能させるためのアイデアを提供します。 次の表は、パートナー センターでオファー情報を完成させるためのベスト プラクティスについてまとめたものです。 オファー実行方法の分析については、パートナー センターの[マーケットプレース分析情報ダッシュボード](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/marketplaceinsights)に移動してください。 

## <a name="online-store-offer-details"></a>オンライン ストア オファーの詳細

| 設定 | ベスト プラクティス |
|:--- |:--- |  
| プラン名 | アプリの場合は、顧客がプランを見つけるのに役立つ検索キーワードを含む明確なタイトルを入力します。 <br> <br> コンサルティング サービスの場合は、[プラン名]: [期間] [オファーの種類] (例: Contoso:2-週間 実装) の形式に従います。 |
| オファーの説明 | 先頭の数文で、オファーの価値提案を明確に説明します。  検索エンジンの結果でこれらの文が使用される可能性があることに留意してください。 価値提案の中心の要素には、以下を含めるようにします。 <ul> <li>製品またはソリューションの説明。 </li> <li> 製品またはソリューションの恩恵を得られるユーザーのペルソナ。 </li> <li> 製品またはソリューションが対応する顧客のニーズや問題。 </li> </ul> <br> 可能な場合は、業界の標準的な語彙や利益に基づく表現を使用します。  特徴や機能に頼って製品を販売しようとせずに、  提供する価値に焦点を当ててください。 <br> <br> コンサルティング サービスの掲載では、提供するプロフェッショナル サービスを明確に示します。 |

> [!IMPORTANT]
> Microsoft の商標、Microsoft のソフトウェア、製品、およびサービスの名前を参照するときは、オファーの名前とオファーの説明が **[Microsoft の商標およびブランド ガイドライン](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks/usage/general.aspx)** とその他の関連する製品固有のガイドラインに従っていることを確認してください。

## <a name="online-store-listing-details"></a>オンライン ストアの掲載の詳細

さまざまなオンライン ストアのカテゴリと業界が、さまざまなオファーの種類に適用されます。

| オンライン ストア | Categories <br>(オンライン ストア別) | Categories <br>(オンライン ストア別) | 業界 <br> (AppSource の場合) |
| :------------------- |:----------------:|:------:|:-------------:|
| **オファリング タイプ**   |  **Azure Marketplace**  | **AppSource**  |
| Azure アプリ | X | |
| コンテナー | X | |
| コンサルティング サービス | | | X |
| Dynamics 365 for Customer Engagement & Power Platform | | X | X |
| Dynamics 365 Finance & Supply Chain Management | | X | X | 
| Dynamics 365 Business Central | | X | X |
| IoT Edge のモジュール | X | |
| Power BI | | X | X |
| SaaS | X | X | X |
| Azure Virtual Machine |  X |    |

### <a name="categories"></a>Categories

Microsoft AppSource と Azure Marketplace は、さまざまな種類のソリューションを提供するオンライン ストアです。 Azure Marketplace では、Azure 上または Azure 向けに構築された IT ソリューションが提供されます。  Microsoft AppSource では、産業用 SaaS アプリケーション、Dynamics 365 アドイン、Microsoft 365 アドイン、Power Platform アプリなどのビジネス ソリューションが提供されます。

カテゴリとサブカテゴリは、ソリューションの種類に基づいて各オンライン ストアにマップされています。 プランは、プランの種類、プランのトランザクション機能、カテゴリ/サブカテゴリの選択に応じて、Microsoft AppSource または Azure Marketplace に公開されます。 

ソリューションの種類に最も合ったカテゴリとサブカテゴリを選択します。 選択できるオプションは次のとおりです。

* プライマリ カテゴリとセカンダリ カテゴリを含む、最大 2 つのカテゴリ (省略可能)。
* プライマリ カテゴリ、セカンダリ カテゴリ、またはその両方についてそれぞれ最大 2 つのサブカテゴリ。 サブカテゴリを選択しない場合でも、選択したカテゴリのみで検出できます。

[!INCLUDE [categories and subcategories](./includes/categories.md)]

#### <a name="important-saas-offers-and-microsoft-365-add-ins"></a>重要: SaaS プランと Microsoft 365 アドイン

マーケットプレースの顧客がプランを表示および購入する方法にトランザクション機能がどのように影響するかについての詳細は、[コマーシャル マーケットプレースでの取引](marketplace-commercial-transaction-capabilities-and-considerations.md)を参照してください。 SaaS オファーでは、オファーのトランザクション機能だけでなく、選択するカテゴリによって、オファーが発行されるオンライン ストアが決まります。


| SaaS プラン    | SaaS プラン   | SaaS プラン  | SaaS プラン   | SaaS プラン   | SaaS プラン   | SaaS プラン    | 該当するオンライン ストア| 該当するオンライン ストア |
|:-------------:|:---:|:--------:|:---------:|:--:|:--:|:---:|:---------------------:|:-------------:|
| 従量制課金 | Microsoft 365 アドイン | [Contact me (お問い合わせ)] | トランザクション (少なくとも 1 つのプラン) | プライベート専用プラン | パブリック専用プラン | パブリック & プライベート プラン | AppSource | Azure Marketplace |
|  | X |  |  |  |  |  | X |  |
| X |  |  | X | X |  |  |  | X |
| X |  |  | X |  | X |  |  | X |
| X |  |  | X |  |  | X |  | X<sup>2</sup> |
|  |  |  | X | X |  |  |  | X |
|  |  |  | X |  | X |  | X<sup>1</sup> | X<sup>1</sup> |
|  |  |  | X |  |  | X | X<sup>1</sup> | X<sup>1、2</sup> |
|  |  | X |  |  |  |  | X<sup>1</sup> | X<sup>1</sup> | 

1. カテゴリ/サブカテゴリと業界の選択によって異なります
2. プライベート プランを含むオファーは Azure portal に発行されます


### <a name="industries"></a>業界

業界の選択は、AppSource に公開されているオファーと Azure Marketplace に公開されているコンサルティング サービスのみに適用されます。  オファーが業界固有のニーズに対応している場合は、業界や業種を選択し、オファーの説明に業界固有の機能を入力します。 最大 2 つの業界を選択し、業界あたり 2 つの業種を選択できます。

>[!Note]
>Azure Marketplace のコンサルティング サービス プランの場合、業界の業種はありません。

| **業界** |  **業種** |
| :------------------- | :----------------|
| **農業** | |
| **建築 & 建設** | |
| **自動車** | |
| **Distribution** | 卸売 <br> 小包の発送 |  
| **Education** | 高等教育 <br> 初等、中等教育/K-12 <br> 図書館と美術館 |
| **金融サービス** | 銀行と証券 <br> 保険 | 
| **政府** |  防衛とインテリジェンス <br> 民間政府 <br> 公安と司法 |
| **医療** | 医療保険 <br> 医療サービス <br> 製薬 | 
| **接客と旅行** | 旅行と輸送 <br> ホテルとレジャー <br> レストランと食品サービス | 
| **製造 & リソース** | 化学と農薬 <br> ディスクリート型製造業 <br> エネルギー | 
| **メディア & 通信** | メディアと娯楽 <br> 遠距離通信 | 
| **その他の公共セクター産業** | 林業と漁業 <br> 非営利団体 | 
| **プロフェッショナル サービス** | パートナー専門サービス業 <br> 法的情報 | 
| **不動産** | |

Microsoft AppSource のみの業界:

| **業界** |  **業種** |
| :------------------- | :----------------|
| **小売 & 一般消費財** | 小売 <br> 消費財 |

### <a name="applicable-products"></a>適用対象の製品

AppSource で選択した製品に表示されるオファーについて、アプリが動作する適切な製品を選択します。

### <a name="search-keywords"></a>キーワード検索

キーワードは、顧客が検索時にオファーを見つけるために役立ちます。 オファーの上位の検索キーワードを特定し、オファーの概要と説明、およびオファー一覧の詳細セクションのキーワード セクションに組み込みます。

## <a name="online-store-marketing-details"></a>オンライン ストアのマーケティングの詳細
| 設定 | ベスト プラクティス |
|:--- |:--- |  
| オファーのロゴ (PNG 形式、216 x 216 から 350 x 350 px): アプリの詳細ページ | デジタル メディアのロゴをデザインして最適化する:<br>オファーのアプリ詳細掲載ページに PNG 形式のロゴをアップロードします。 パートナー センターによって、必要なロゴのサイズに合わせてサイズが変更されます。 |
| オファーのロゴ (PNG 形式、48 x 48 ピクセル): 検索ページ | パートナー センターによって、アップロードした大きいロゴからこのロゴが生成されます。 これは、必要に応じて、後で別の画像に置き換えることができます。 |
| 「詳細情報」のドキュメント | "詳細情報" に販売とマーケティングに関連する資料を掲載する。次は例です。<ul><li>ホワイト ペーパー</li><li> パンフレット</li><li>チェックリスト</li><li> PowerPoint プレゼンテーション</li></ul><br>すべてのファイルは PDF 形式で保存します。 掲載の目標は、顧客に販売することではなく、顧客を教育することです。<br><br>アプリのランディング ページへのリンクをすべてのドキュメントに追加し、訪問者や試用を追跡する URL パラメーターを追加します。 |
| ビデオ:AppSource、コンサルティング サービス、および SaaS オファーのみ | 最も強力なビデオで、オファーの価値をナレーションの形式で伝える:<ul> <li> 自分の会社ではなく、顧客を物語の主人公にするようにしてください。 </li> <li> ビデオは、ターゲットとなる顧客の主な課題と目標に対応する内容にします。 </li> <li> 推奨される長さ:60-90 秒です。</li> <li> 主要な検索ワードをビデオの名前に組み込みます。 </li> <li> ハウツー、入門、お客様の声などのビデオを追加することを検討してください。 </li> </ul> |
| スクリーンショット (1280&nbsp;&times;&nbsp;720) | 最大 5 個のスクリーンショットを追加する:<br>主要な検索ワードをファイル名に組み込みます。 |

## <a name="link-to-your-offer-page-from-your-website"></a>自社の Web サイトからオファーのページにリンクする

自社サイトの AppSource または Marketplace バッジからコマーシャル マーケットプレースのリストにリンクする場合は、URL の末尾に次のクエリ パラメーターを含めることで、強力な分析やレポートに対応できます。
* **src**:トラフィックが AppSource にルーティングされる元のソースを含めます (Web サイト、LinkedIn、Facebook など)。
* **mktcmpid**:マーケティング キャンペーン ID。文字、数字、アンダースコア、およびハイフンを組み合わせて 16 文字までを入力できます (たとえば、*blogpost_12*)。

次の URL の例には、前述のクエリ パラメーターの両方が含まれています。`https://appsource.microsoft.com/product/dynamics-365/mscrm.04931187-431c-415d-8777-f7f482ba8095?src=website&mktcmpid=blogpost_12`

これらのパラメーターを AppSource の URL に追加すると、[パートナー センター](https://partner.microsoft.com/dashboard/commercial-marketplace/)の分析ダッシュボードでキャンペーンの効果を確認できます。

## <a name="next-steps"></a>次のステップ

[商用マーケットプレースのベネフィット](./gtm-your-marketplace-benefits.md)の詳細を確認します。

[パートナー センター](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)にサインインして、オファーを作成して構成します。
