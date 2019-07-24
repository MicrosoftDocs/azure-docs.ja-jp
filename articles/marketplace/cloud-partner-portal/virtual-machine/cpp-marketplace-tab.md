---
title: Azure 向け Cloud パートナー ポータルの仮想マシンの [Marketplace] タブ | Microsoft Docs
description: Azure Marketplace の VM プランの作成で使用される [Marketplace] タブについて説明します。
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
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
ms.openlocfilehash: b1b62c68ef4e18f4d4d36a78078ad7431717b754
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57995642"
---
# <a name="virtual-machine-marketplace-tab"></a>仮想マシンの [Marketplace] タブ

**[新しいプラン]** ページの **[Marketplace]** タブでは、マーケティング、販売、および法律に関する情報と契約を見込み客に提供できるほか、マーケットプレースによって生成されるリードを管理できます。 この長いフォームは、4 つのセクションに分かれています: **[概要]**、**[Marketing Artifacts]\(マーケティングの成果物\)**、**[Lead Management]\(潜在顧客の管理\)**、**[法的情報]**。 

## <a name="overview-section"></a>[概要] セクション
このセクションでは、Azure Marketplace プランについての一般的な情報を入力します。  フィールド名に付いているアスタリスク (*) は、そのフィールドが必須であることを示します。

![仮想マシン用の [新しいプラン] フォームにある [Marketplace] タブの [概要] セクション](./media/publishvm_008.png)

次の表では、これらのフィールドの目的および内容について説明しています。

|  **フィールド**                |     **説明**                                                          |
|  ---------                |     ---------------                                                          |
| **タイトル**                 | プランのタイトル。通常は長い正式な名前。 このタイトルは、マーケットプレースで目立つように表示されます。  最大長は 50 文字です。 |
| **まとめ**               | ソリューションの簡潔な目的または機能。  最大長は 100 文字です。 |
| **Long Summary (長い要約)**          | ソリューションの目的または機能。  最大長は 256 文字です。 |
| **説明**           | ソリューションの説明。  最大長は 3,000 文字で、単純な HTML 書式がサポートされています。 |
| **Microsoft CSP Reseller channel (Microsoft CSP リセラー チャネル)** | クラウド ソリューション プロバイダー (CSP) のパートナー チャネル オプトインが利用できるようになりました。  Microsoft CSP パートナー チャネルを通じたオファーのマーケティングの詳細については、「[Cloud Solution Providers (クラウド ソリューション プロバイダー)](../../cloud-solution-providers.md)」を参照してください。 |
| **Marketing Identifier (マーケティング識別子)**  | このプランに関連付ける一意の URL。通常、組織およびソリューションの名前が含まれ、最大長は 50 文字です。  例:  <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **Preview Subscription IDs (プレビュー サブスクリプション ID)** | 1 から 100 個のプレビューアーのサブスクリプション識別子を追加します。 ホワイトリストに登録されたこれらのサブスクリプションは、プランの発行後、運用が開始される前にプランにアクセスできます。 |
| **役に立つリンク**          | ドキュメント、リリース ノート、FAQ などに URL を追加します。 |
| **推奨されるカテゴリ (最大 5 個)** | プランが最もよく当てはまる、複数選択が可能なビジネスおよび技術のカテゴリ。  最大で 5 個設定できます。  |
|  |  |


## <a name="marketing-artifacts-section"></a>[マーケティングの成果物] セクション

この 2 番目のセクションは、次の 3 つのサブセクションに分かれています: **[ロゴ]**、**[スクリーンショット]**、**[ビデオ]**。 必須のマーケティングの成果物はロゴだけですが、顧客へのアピールを最大にするために、すべて設定することをお勧めします。

![仮想マシン用の [新しいプラン] フォームにある [Marketplace] タブの [Marketing Artifacts]\(マーケティングの成果物\) セクション](./media/publishvm_009.png)

|  **フィールド**                |     **説明**                                                          |
|  ---------                |     ---------------                                                          |
| *ロゴ*  |  |
| **Small**                 | 40 x 40 ピクセルの .ico ビットマップ                                                      |
| **Medium**                | 90 x 90 ピクセルの .ico ビットマップ                                                      |
| **Large**                 | 115 x 115 ピクセルの .ico ビットマップ                                                   |
| **ワイド**                  | 255 x 115 ピクセルの .ico ビットマップ                                                    |
| **ヒーロー**                  | 815 x 290 のビットマップ。  オプション。ただし、ヒーロー アイコンはアップロードしたら削除できません。 |
| *スクリーンショット*  | オプション。ただし、SKU あたり最大 5 つのスクリーンショット。 |
| **Name**                  | 名前またはタイトル <!-- TODO - max char length? none specified in UI -->                               |
| **Image**                 | 画面キャプチャ画像。533 x 324 ピクセル                                         |
| *ビデオ*  |  |
| **Name**                  | 名前またはタイトル  <!-- TODO - max char length? -->                              |
| **リンク**                  | ビデオの URL。YouTube や Vimeo でホストされます                                        |
| **サムネイル**             | 533 x 324 のビットマップ                                                               |
|  |  |


### <a name="logo-guidelines"></a>ロゴのガイドライン

<!-- TD: It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Cloud パートナー ポータルにアップロードされるすべてのロゴは、以下のガイドラインに従っている必要があります。

*  Azure の設計には簡単なカラー パレットがあります。 ロゴのプライマリ カラーとセカンダリ カラーの数は少なくしてください。
*  Azure ポータルのテーマの色は白黒です。 そのため、これらの色をロゴの背景色として使用しないでください。 Azure ポータルでロゴが目立つ色を背景色として使用します。 背景色としてはシンプルなプライマリ カラーをお勧めします。 透明な背景を使用している場合は、ロゴとテキストが白、黒または青ではないことを確認します。
*  ロゴではグラデーションの背景を使用しないでください。
*  ロゴにはテキストを使用しないでください (会社またはブランドの名前であっても)。 ロゴのルック アンド フィールは "フラット" にする必要があり、グラデーションは避ける必要があります。
*  ロゴを拡大しないでください。

#### <a name="hero-logo"></a>Hero ロゴ

ヒーロー ロゴはオプションです。ただし、ヒーロー アイコンはアップロードしたら削除できません。  ヒーロー ロゴ アイコンは、以下のガイドラインに従う必要があります。

*  黒、白、透明は、ヒーロー アイコンの背景では使用できません。
*  ヒーロー アイコンの背景色には明るい色を使用しないでください。  発行元の表示名、プランのタイトル、プランの長い要約は、白のフォント色で表示され、背景に対して目立つ必要があります。
*  ヒーロー ロゴのデザイン時にはテキストの使用をできるだけ避けてください。  プランが表示されるときに、発行元の名前、プランのタイトル、プランの長い要約、および作成ボタンが、プログラムによってヒーロー アイコン内に埋め込まれます。 
* ヒーロー アイコンの右側に、未使用の四角形を含めてください。サイズは 415 x 100 ピクセルで、左側からのオフセットは 370 ピクセルです。  

例として、Azure Container Service のヒーロー アイコンを次に示します。  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Azure Container Service のサンプル ヒーロー アイコン](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>マーケティング情報の例 

次の図は、マーケティング情報が Microsoft Windows Server の主要製品ページでどのように表示されるかを示しています。

![Microsoft Windows Server の製品ページの例](./media/publishvm_011.png)


## <a name="lead-management-section"></a>[リード管理] セクション
<!-- this all should be referenced in a common location for lead management, not in this file. nothing unique for a vm specifically. -->

3 つ目のセクションでは、Azure Marketplace プランから生成された顧客リードを収集することができます。 このリード情報に対して、(ドロップダウン リストで) 以下のストレージ オプションが用意されています。

* **なし** - 既定値。リード情報は収集されません。
* Azure テーブル - 接続文字列によって指定された Azure テーブルに書き込まれます。
* Dynamics CRM Online - URL と認証資格情報によって指定された [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) インスタンスに書き込まれます。
* HTTPS エンドポイント - 指定した HTTPS エンドポイントに JSON ペイロードとして書き込まれます。
* Marketo - サーバー ID、munchkin ID、フォーム ID によって指定された [Marketo](https://www.marketo.com/) インスタンスに書き込まれます。
* Salesforce - オブジェクト識別子によって指定された [Salesforce](https://www.salesforce.com/) データベースに書き込まれます。

プランを正常に発行した後は、リードの接続が検証され、構成した宛先にテスト リードが自動的に送信されます。 リード情報は継続的に管理する必要があり、それらの設定は顧客管理のアーキテクチャが変更されるたびに、すぐに更新する必要があります。

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>[法的情報] セクション

この最後のセクションでは、各オファーに必須の次の 2 つの法的ドキュメントを指定することができます: プライバシー ポリシーと利用規約。

|  **フィールド**                |     **説明**                                                          |
|  ---------                |     ---------------                                                          |
| **プライバシー ポリシーの URL**    | 投稿済みのプライバシー ポリシーの URL                                            |
| **使用条件**          | プレーンテキストまたは単純な HTML のポリシー。  <!-- TODO - max char length? -->       |
|  |  |

<br/>

次の [[サポート]](./cpp-support-tab.md) タブでは、プランに対してテクニカル サポートとユーザー サポートのリソースを指定します。

