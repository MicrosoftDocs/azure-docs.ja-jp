---
title: Azure アプリケーション オファーの [Marketplace] タブ | Microsoft Docs
description: '[Marketplace] タブを使用し、Azure アプリケーション オファーのマーケティング資産を識別します。'
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
ms.openlocfilehash: 1203f33458c83bbde7a263022b78563bad183143
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56870739"
---
# <a name="azure-application-marketplace-tab"></a>Azure アプリケーションの [Marketplace] タブ

[Marketplace] タブを使用して Azure アプリケーションについて説明し、マーケティング資産を提供します。 このタブには、[概要]、[Marketing Artifacts]\(マーケティングの成果物\)、[Lead Management]\(潜在顧客の管理\)、[法的情報] フォームがあります。

## <a name="overview-form"></a>[概要] フォーム

[概要] フォームには、次の画面キャプチャのように、必須フィールドと任意フィールドが含まれています。 必須フィールドはアスタリスク (*) で示されます。

![[概要] フォーム](./media/azureapp-marketplace-overview.png)

次の表は、オファーにストアフロントを作成するための設定をまとめたものです。

|    **フィールド**       |  **説明**  |
|  ---------------   |  ---------------  |
|        タイトル            |        プランのタイトル。 マーケットプレースで目立つように表示されます。 最大長は 50 文字です。           |
|         まとめ           |        プランの短い要約。 最大長は 100 文字です。           |
|         Long Summary\(長い要約\)           |        オファーの長めの要約 (まとめと同じでもかまいません)。 最大長は 256 文字です。           |
|       説明            |         プランの説明。 最大長は 3000 文字です。 単純な HTML 書式設定を使用可能 (&lt;p&gt;、&lt;em&gt;、&lt;ul&gt;、&lt;li&gt;、&lt;ol&gt;、ヘッダー タグなど)。           |
|        Marketing Identifier\(マーケティング識別子\)            |         このプランに関連付ける一意の URL。通常、組織およびソリューションの名前が含まれ、最大長は 50 文字です。 サービスに短くてわかりやすいマーケティング識別子を選択します。 これはこのオファーの URL で使用されます。 たとえば、発行元 ID が "contoso" で、マーケティング識別子が "sampleApp" の場合、Azure Marketplace では、オファーの URL は https://azuremarketplace.microsoft.com/en-us/marketplace/apps/contoso.sampleApp になります。          |
|        Preview Subscription IDs (プレビュー サブスクリプション ID)            |         プレビューアーのサブスクリプション識別子を追加します (1 - 100 個)。 ホワイトリストに登録されたこれらのサブスクリプションには、オファーの発行後、運用が開始される前にオファーにアクセスする許可が与えられます。          |
|         役に立つリンク           |        サポート、ドキュメント、フォーラムなど、オファーの利用者向けにさまざまリソースのリンクを提供できます。ドキュメントにはリンクを少なくとも 1 つ追加してください。            |
|         推奨されるカテゴリ (最大 5 個)           |         最大 5 個までカテゴリを選択します。 選択したカテゴリは、Azure Marketplace と Azure portal で利用可能な製品カテゴリにオファーをマッピングするために使用されます。 閲覧ページと製品の詳細ページに表示されます。          |

## <a name="marketing-artifacts"></a>マーケティングの成果物

[マーケティングの成果物] には、次の画面キャプチャのように、必須フィールドと任意フィールドが含まれています。 必須フィールドはアスタリスク (*) で示されます。

![マーケティングの成果物フォーム](./media/azureapp-marketplace-artifacts.png)

次の表はマーケティングの成果物についてまとめたものです。

|    **フィールド**       |  **説明**  |
|  ---------------   |  ---------------  |
|       Small             |        PNG 形式の 40 x 40 ピクセル           |
|       Medium             |        PNG 形式の 90 x 90 ピクセル           |
|       Large             |        PNG 形式の 115 x 115 ピクセル         |
|       Wide             |        PNG 形式の 255 x 115 ピクセル           |
|       ヒーロー             |        PNG 形式の 815 x 290 ピクセル 省略可能。 **注:** Hero アイコンはアップロード後に削除できません。           |
|       スクリーンショット (最大 5 つ)             |        スクリーンショットは製品の詳細ページに表示されます。 アプリ自体について、またそのしくみについて視覚的に伝えるのに適した方法です。 たとえば、アーキテクチャ ダイアグラムやユース ケースの例を表示できます。 スクリーンショットは任意です。SKU あたり 5 枚に制限されています。 スクリーンショットを追加するには:<ul><li>**[+ スクリーンショットを追加]** を選択し、[スクリーンショット] ウィンドウを開きます。</li><li>**[名前]** - 名前/タイトルを入力します (最大長は 100 文字)。</li><li>**[アップロード]** - 画像をアップロードします。 PNG 形式にする必要があります。サイズは 533 x 324 ピクセルです。</li></ul>           |
|        ビデオの追加            |        ビデオは製品の詳細ページに表示されます。 アプリケーション自体について、またそのしくみについて視覚的に伝えるのに適した方法です。 ビデオを追加するには: <ul><li>**[+ ビデオの追加]** を選択し、[ビデオ] ウィンドウを開きます。</li><li>**[名前]** - 名前/タイトルを入力します (最大長は 100 文字)。</li><li>**[リンク]** – ビデオをホストしているサイト (YouTube や Vimeo) の URL を入力します。</li><li>**[サムネイル]** – サムネイルをアップロードします。 PNG 形式にする必要があります。サイズは 533 x 324 ピクセルです。</li></ul>          |

### <a name="artifact-examples-in-azure-marketplace"></a>Azure Marketplace の成果物の例

Marketplace 検索結果の例が次の画面キャプチャです。

![Marketplace のオファー検索結果](./media/azureapp-marketplace-example-browse.png)

検索結果のオファーのタイルを顧客がクリックすると、次の画像のようにオファーが Marketplace に表示されます。

![Marketplace のオファー検索結果の詳細](./media/azureapp-marketplace-example-details.png)

### <a name="artifact-examples-in-azure-portal"></a>Azure portal の成果物の例

Azure portal には次の画面キャプチャのようにオファーが表示されます。 この例にあるアプリケーション オファーは、**[Marketplace]、[すべて]、[開発 + テスト]、[Jenkins]** の順に選択すると見つかります。 Jenkins オファーでは、ロゴ、タイトル、発行元の表示名を確認できます。

![Azure portal でオファーを閲覧する](./media/azureapp-portalbrowse-artifacts-jenkins.png)

ユーザーが Jenkins を選択すると、アプリケーションに関する詳細が次の画面キャプチャのように表示されます。

![Azure portal のオファー詳細](./media/azureapp-portal-artifacts-jenkins-details.png)

### <a name="logo-guidelines"></a>ロゴのガイドライン

Cloud パートナー ポータルにアップロードされるすべてのロゴは、以下のガイドラインに従っている必要があります。

- Azure の設計には簡単なカラー パレットがあります。 ロゴのプライマリ カラーとセカンダリ カラーの数は少なくしてください。
- Azure portal のテーマの色は白黒です。 これらの色を、ロゴの背景色として使用しないでください。 Azure portal でロゴが目立つ色を使用してください。 背景色としてはシンプルなプライマリ カラーをお勧めします。 透明な背景を使用している場合は、ロゴとテキストが白、黒、または青ではないことを確認してください。
- ロゴではグラデーションの背景を使用しないでください。
- ロゴにはテキストを使用しないでください (会社またはブランドの名前であっても)。 ロゴのルック アンド フィールは "フラット" にする必要があり、グラデーションは避ける必要があります。
- ロゴを拡大しないでください。

#### <a name="hero-logo"></a>Hero ロゴ

Hero ロゴはオプションです。

>[!IMPORTANT]
>Hero ロゴはアップロード後に削除できません。

ヒーロー ロゴについては、以下のガイドラインに従ってください。

- 黒、白、透明の背景は使用できません。
- ロゴの背景には明るい色を使用しないでください。 発行元の表示名、プランのタイトル、オファーの長い要約は、白のフォント色で表示され、背景に対して目立つ必要があります。
- ロゴのデザイン時にはテキストの使用をできるだけ避けてください。 プランが表示されるときに、発行元の名前、プランのタイトル、プランの長い要約、および作成ボタンが、プログラムによってロゴ内に埋め込まれます。
- Hero ロゴの右側に、未使用の四角形の領域を含めてください。 この空白スペースは 415 x 100 ピクセルで、左側からのオフセットが 370 ピクセルです。

## <a name="lead-management"></a>見込み客管理

[Lead Management]\(潜在顧客の管理\) フォームには、潜在顧客管理を構成するための任意フィールドがあります。 潜在顧客の管理を構成するには、ドロップダウン リストから潜在顧客の送信先を選択します。 次の画面キャプチャのように、選択できる送信先が表示されます。

![潜在顧客の管理の送信先を選択する](./media/azureapp-marketplace-leadmgmt.png)

>[!TIP]
>情報アイコンを選択すると、"潜在顧客を保存するシステムを選択してください。" というメッセージが表示されます。 CRM システムに接続する方法については[こちら](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)をご覧ください。


詳細については、[潜在顧客の構成](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)に関するページを参照してください。

## <a name="legal"></a>法的情報

[法的情報] フォームを使用し、オファーごとに必要な法的ドキュメントを提供します。

次の情報を指定します。

- **[プライバシー ポリシー URL]** – アプリのプライバシー ポリシーのリンクを入力します。
- **[利用規約]** - アプリの利用規約を入力します。 顧客は、アプリを試す前にこれらの条件を承諾する必要があります。

![[法的情報] フォーム](./media/azureapp-marketplace-legal.png)

## <a name="next-steps"></a>次の手順

[[サポート] タブ](./cpp-support-tab.md)