---
title: Azure IoT Edge モジュール用のマーケットプレースの説明 | Microsoft Docs
description: IoT Edge モジュール用のマーケットプレースの説明を作成します。
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
ms.openlocfilehash: 30f44e0e8bae4071403b5c28cd4133970fc4d468
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58010955"
---
# <a name="iot-edge-module-marketplace-tab"></a>IoT Edge モジュールの [Marketplace] タブ


**[新しいプラン]** ページの **[Marketplace]** タブでは、マーケティング、販売、および法律に関する情報と契約を見込み客に提供できるほか、マーケットプレースによって生成されるリードを管理できます。 この長いフォームは、4 つのセクションに分かれています: **[概要]**、**[Marketing Artifacts]\(マーケティングの成果物\)**、**[Lead Management]\(潜在顧客の管理\)**、**[法的情報]**。

## <a name="overview"></a>概要

このセクションでは、Azure Marketplace プランについての一般的な情報を入力します。  フィールド名に付いているアスタリスク (*) は、そのフィールドが必須であることを示します。

![IoT Edge モジュール用の [新しいプラン] フォームにある [Marketplace] タブの [概要] セクション](./media/iot-edge-module-marketplace-tab-overview.png)

次の表では、これらのフィールドの目的および内容について説明しています。

|  **フィールド**                |     **説明**                                                          |
|  ---------                |     ---------------                                                          |
| **タイトル**                 | プランのタイトル。 マーケットプレースで目立つように表示されます。  最大長は 50 文字です。 <!--ADD PICTURE IN ACTION-->|
| **まとめ**               | プランの短い要約。 最大長は 100 文字です。 <!--ADD PICTURE IN ACTION-->|
| **Long Summary (長い要約)**          | プランのより長い要約 (**[要約]** と同じでもかまいません)。  最大長は 256 文字です。 <!--ADD PICTURE IN ACTION-->|
| **説明**           | プランの説明。  最大長は 3,000 文字で、単純な HTML 書式がサポートされています。<br/> 末尾に*最小ハードウェア要件*の段落が含まれている必要があります。 次に例を示します。 <br/> <p><u>最小ハードウェア要件:</u> Linux x64 および arm32 OS、1 GB の RAM、500 MB のストレージ</p>
| **Marketing Identifier (マーケティング識別子)**  | このプランに関連付ける一意の URL。通常、組織およびソリューションの名前が含まれ、最大長は 50 文字です。  例:  <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleIoTEdgeModule`  |
| **Preview Subscription IDs (プレビュー サブスクリプション ID)** | 1 から 100 個のプレビューアーのサブスクリプション識別子を追加します。 ホワイトリストに登録されたこれらのサブスクリプションは、プランの発行後、運用が開始される前にプランにアクセスできます。 |
| **役に立つリンク**          | プランが最もよく当てはまる、複数選択が可能なビジネスおよび技術のカテゴリ。  最大で 10 個設定できます。 少なくともドキュメントへのリンクを 1 つと、[Azure IoT デバイス カタログ](https://catalog.azureiotsolutions.com/)に記載されている互換性のある IoT Edge デバイスへのリンクを 1 つ、追加するようにしてください。 |
| **Suggested Categories (推奨されるカテゴリ)** | 最大 5 個までカテゴリを選択します。 これらは製品の詳細ページに表示されます。 参照ページでは、すべての IoT Edge モジュールが、*[モノのインターネット] \> [IoT Edge モジュール]* カテゴリの下に表示されます。|

### <a name="offer-example"></a>プランの例

 次の例は、プランの **[タイトル]**、**[要約]**、**[説明]**、**[ロゴ]**、**[スクリーンショット]** の各フィールドが、さまざまなビューでどのように表示されるかを示しています。
 
#### <a name="on-the-azure-marketplace-website"></a>Azure Marketplace Web サイト:

- プランを参照する場合:

    ![マーケットプレース プランが Azure Marketplace Web サイトでどのように表示されるか - 参照](./media/iot-edge-module-ampdotcom-card.png)

- プランの詳細を確認する場合:

    ![Web サイトで製品の詳細を確認するときに、IoT Edge モジュールがどのように表示されるか](./media/iot-edge-module-ampdotcom-pdp.png)

#### <a name="on-the-azure-portal-website"></a>Azure portal Web サイト:

- プランを参照する場合:

    ![Azure portal の参照時に IoT Edge モジュールがどのように表示されるか #1](./media/iot-edge-module-portal-browse.png)

    ![Azure portal の参照時に IoT Edge モジュールがどのように表示されるか #2](./media/iot-edge-module-portal-product-picker.png)

- プランを検索する場合:

    ![Azure portal の検索時に IoT Edge モジュールがどのように表示されるか](./media/iot-edge-module-portal-search.png)

- プランの詳細を確認する場合:

    ![ポータルで製品の詳細を確認するときに、IoT Edge モジュールがどのように表示されるか](./media/iot-edge-module-portal-pdp.png)


## <a name="marketing-artifacts"></a>マーケティングの成果物

このセクションには、次のサブセクションがあります: **[ロゴ]**、**[スクリーンショット]**、**[ビデオ]**。 

>[!Note]
>必須のマーケティングの成果物はロゴだけですが、顧客へのアピールを最大にするために、すべて設定することをお勧めします。

![仮想マシン用の [新しいプラン] フォームにある [Marketplace] タブの [Marketing Artifacts]\(マーケティングの成果物\) セクション](./media/publishvm_009.png)

|  **フィールド**                |     **説明**                                                          |
|  ---------                |     ---------------                                                          |
| *ロゴ*  | ロゴが使用される方法と場所を確認するには、前の画面キャプチャを参照してください。  |
| **Small**                 | 40 x 40 ピクセルの PNG 形式                                                     |
| **Medium**                | 90 x 90 ピクセルの PNG 形式                                                     |
| **Large**                 | 115 x 115 ピクセルの PNG 形式                                                  |
| **ワイド**                  | 255 x 115 ピクセルの PNG 形式                                                   |
| **ヒーロー**                  | 815 x 290 ピクセルの PNG 形式。  オプション。ただし、ヒーロー アイコンはアップロードしたら削除できません。 |
| *スクリーンショット*  | スクリーンショットは製品の詳細ページに表示されます。 その IoT Edge モジュール自体について、またそのしくみについて視覚的に伝えるのに適した方法です。 たとえば、アーキテクチャ ダイアグラムやユース ケースの例を表示できます。 オプション。ただし、SKU あたり最大 5 つのスクリーンショット。 |
| **Name**                  | 名前またはタイトル。 最大長は 100 文字です。                             |
| **Image**                 | 画面キャプチャ画像。533 x 324 ピクセルの PNG 形式                                         |
| *ビデオ*  | ビデオは製品の詳細ページに表示されます。 その IoT Edge モジュール自体について、またそのしくみについて視覚的に伝えるのに適した方法です。 |
| **Name**                  | 名前またはタイトル。 最大長は 100 文字です。                              |
| **リンク**                  | ビデオの URL。YouTube や Vimeo でホストされます                                        |
| **サムネイル**             | 533 x 324 ピクセルの PNG 形式                                                           |


### <a name="logo-guidelines"></a>ロゴのガイドライン

<!-- It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Cloud パートナー ポータルにアップロードされるすべてのロゴは、以下のガイドラインに従っている必要があります。

*  Azure の設計には簡単なカラー パレットがあります。 ロゴのプライマリ カラーとセカンダリ カラーの数は少なくしてください。
*  Azure portal のテーマの色は白黒です。 これらの色を、ロゴの背景色として使用しないでください。 Azure portal でロゴが目立つ色を使用してください。 背景色としてはシンプルなプライマリ カラーをお勧めします。 透明な背景を使用している場合は、ロゴとテキストが白、黒、または青ではないことを確認してください。
*  ロゴではグラデーションの背景を使用しないでください。
*  ロゴにはテキストを使用しないでください (会社またはブランドの名前であっても)。 ロゴのルック アンド フィールは "フラット" にする必要があり、グラデーションは避ける必要があります。
*  ロゴを拡大しないでください。

#### <a name="hero-logo"></a>Hero ロゴ

Hero ロゴはオプションです。

>[!Important]
>ヒーロー ロゴは、アップロードした後に削除できません。

ヒーロー ロゴについては、以下のガイドラインに従ってください。 

*  黒、白、透明の背景は使用できません。
*  ロゴの背景には明るい色を使用しないでください。  発行元の表示名、プランのタイトル、プランの長い要約は、白のフォント色で表示され、背景に対して目立つ必要があります。
*  ロゴのデザイン時にはテキストの使用をできるだけ避けてください。 プランが表示されるときに、発行元の名前、プランのタイトル、プランの長い要約、および作成ボタンが、プログラムによってロゴ内に埋め込まれます。 
* ヒーロー ロゴの右側に、未使用の四角形の領域を含めてください。 この空白スペースは 415 x 100 ピクセルで、左側からのオフセットが 370 ピクセルです。  

<!-- P2: woudl be nice to also have an picture int he context of iot edge of the hero image
As an example, the following hero icon is for the Azure Container Service.

![Example hero icon for Azure Container Service](./media/publishvm_010.png)
-->


## <a name="lead-management"></a>リードの管理

このセクションでは、お客様の Azure Marketplace プランから生成された顧客リードを収集するためのオプションを設定できます。 ドロップダウン リストで、以下のストレージ オプションを選択できます。

* **なし** - 既定値。リード情報は収集されません。
* Azure テーブル - 接続文字列によって指定された Azure テーブルに書き込まれます。
* Dynamics CRM Online - URL と認証資格情報によって指定された [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) インスタンスに書き込まれます。
* HTTPS エンドポイント - 指定した HTTPS エンドポイントに JSON ペイロードとして書き込まれます。
* Marketo - サーバー ID、munchkin ID、フォーム ID によって指定された [Marketo](https://www.marketo.com/) インスタンスに書き込まれます。
* Salesforce - オブジェクト識別子によって指定された [Salesforce](https://www.salesforce.com/) データベースに書き込まれます。

プランを正常に発行した後、リードの接続が検証され、構成した宛先にテスト リードが自動的に送信されます。 

>[!Note]
>リード情報は継続的に管理する必要があり、それらの設定は顧客管理のアーキテクチャが変更されるたびに、すぐに更新する必要があります。

<!-- there is missing some marketing imagess
1. inside azure portal the wide logo/regular logo
2. inside azure portal the top curation section
3. amp.com the pricing tab
4. amp.com the tile -->

## <a name="legal"></a>法的情報

このセクションでは、各オファーに必須の 2 つの法的ドキュメントを指定できます。プライバシー ポリシーと利用規約。

|  **フィールド**                |     **説明**                                                          |
|  ---------                |     ---------------                                                          |
| **プライバシー ポリシーの URL**    | 投稿済みのプライバシー ポリシーの URL                                            |
| **使用条件**          | インラインの単純な HTML の利用規約か、投稿済みの利用規約へのリンク       |

<br/>

## <a name="next-steps"></a>次の手順

[[サポート]](./cpp-support-tab.md) タブを使用し、プランに対してテクニカル サポートとユーザー サポートのリソースを指定します。
