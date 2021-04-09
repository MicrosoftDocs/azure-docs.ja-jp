---
title: Microsoft AppSource で Dynamics 365 for Operations プランを作成する
description: Microsoft AppSource で Dynamics 365 for Operations プランを作成する方法。 AppSource で、またはクラウド ソリューション プロバイダー (CSP) プログラムを使用して、お客様のプランを一覧表示または販売します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 12/02/2020
ms.openlocfilehash: bcb7f2d008c54981223b4839f9a750e6ee9ac5cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97355149"
---
# <a name="create-a-dynamics-365-for-operations-offer"></a>Dynamics 365 for Operations オファーの作成

この記事では、新しい Dynamics 365 for Operations プランの作成方法について説明します。 [Microsoft Dynamics 365 for Finance and Operations](https://dynamics.microsoft.com/finance-and-operations) は、高度な財務、経営、製造、サプライ チェーン管理がサポートされるエンタープライズ リソース プランニング (ERP) サービスです。 Dynamics 365 for Operations のすべてのオファーは、弊社の認定プロセスを通過する必要があります。

開始する前に、まだ[パートナー センターで商業マーケットプレース アカウントを作成](create-account.md)していなければ、作成してください。 お使いのアカウントがコマーシャル マーケットプレース プログラムに登録されていることを確認します。

>[!NOTE]
> オファーが公開されると、そのオファーに対する編集は、パブリケーションのためにオファーを再送信した後、パートナー センターとオンライン ストアでのみ更新されます。

## <a name="create-a-new-offer"></a>新しいオファーを作成する

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
2. 左側のナビゲーション メニューで、 **[商業マーケットプレース]**  >  **[概要]** の順に選択します。
3. [概要] ページで、 **[+ 新しいプラン]**  >  **[Dynamics 365 for Operations]** の順に選択します。

    ![左側のナビゲーション メニューの画像。](./media/new-offer-dynamics-365-operations.png)

## <a name="new-offer"></a>新しいプラン

**[オファー ID]** を入力します。 これは、アカウントのオファーごとに一意の識別子です。

- この ID は、マーケットプレース オファーの Web アドレスと Azure Resource Manager テンプレート (該当する場合) で顧客に表示されます。
- オファー ID の長さはパブリッシャー ID の組み合わせで 40 文字以下にする必要があります。
- 使用できるのは小文字と数字だけです。 ハイフンとアンダースコアを含めることができますが、スペースは使用できません。 たとえば、パブリッシャー ID が `testpublisherid` であれば、「**test-offer-1**」と入力すると、オファーの Web アドレスは `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` になります。
- **[作成]** を選択した後にこの ID を変更することはできません。

**[オファーのエイリアス]** を入力します。 これは、パートナー センター内でオファーに使用される名前です。

- この名前はマーケットプレースでは使用されず、顧客に表示されるオファー名やその他の値とは異なります。
- **[作成]** を選択した後にこの名前を変更することはできません。

**[作成]** を選択してオファーを生成し、続行します。

## <a name="offer-setup"></a>オファーのセットアップ

### <a name="alias"></a>エイリアス

パートナー センター内でのみこのオファーを参照するために使用する、わかりやすい名前を入力します。 この名前 (オファーの作成時に入力した内容が事前に入力されている) は、マーケットプレースでは使用されず、顧客に表示されるオファー名とは異なります。 オファー名を後で更新する場合は、「[オファー登録情報](#offer-listing)」ページに移動してください。

### <a name="setup-details"></a>セットアップの詳細

**[潜在顧客がこの登録オファーを利用する方法について選択してください。]** で、このオファーに使用するオプションを選択します。

- **今すぐ入手する (無料)** – 無料のオファーの一覧を顧客に提示します。
- **無料試用版 (一覧)** – 無料試用版へのリンクが付いたオファーの一覧を顧客に提示します。 オファー登録情報の無料試用版がご利用のサービスによって作成、管理、および構成され、Microsoft によって管理されるサブスクリプションはありません。

    > [!NOTE]
    > 試用版リンクからアプリケーションが受信するトークンは、そのアプリのアカウント作成を自動化するためのユーザー情報を Azure Active Directory (Azure AD) を介して取得するためだけに使用できます。 このトークンを使用した認証には、Microsoft アカウントはサポートされません。

- **連絡してください** – 顧客関係管理 (CRM) システムに接続して、顧客の連絡先情報を収集します。 顧客は、自分の情報を共有する許可を求められます。 これらの顧客の詳細は、オファーの名前と ID のほか、顧客がオファーを見つけたマーケットプレース ソースと一緒に、お客様が構成した CRM システムに送信されます。 CRM の構成の詳細については、「[潜在顧客](#customer-leads)」を参照してください。

### <a name="test-drive"></a>体験版

体験版は、購入前に試用するオプションを提供することで潜在顧客へのオファーを披露し、その結果、コンバージョンが増加し、見込みの高いリードが生成される優れた方法です。 詳細については、まず「[体験版とは](../what-is-test-drive.md)」をご覧ください。

一定期間、体験版を有効にするには、 **[体験版を有効にする]** チェック ボックスをオンにします。 オファーから体験版を削除するには、このチェック ボックスをオフにします。

### <a name="customer-leads"></a>潜在顧客

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

詳しくは、[リード管理の概要](./commercial-marketplace-get-customer-leads.md)に関する記事をご覧ください。

続行する前に、 **[下書きの保存]** を選択します。

## <a name="properties"></a>Properties

このページでは、マーケットプレース上でのオファーのグループ分けに使用するカテゴリと業界、アプリのバージョン、オファーがサポートされる法的契約を定義できます。

### <a name="categories"></a>Categories

オファーを適切なマーケットプレース検索領域に配置するために、カテゴリとサブカテゴリを選択します。 オファーでこれらのカテゴリがどのようにサポートされるかを、必ずオファーの説明に記述してください。 選択:

- 少なくとも 1 つ、最大で 2 つのカテゴリ。プライマリ カテゴリとセカンダリ カテゴリを含みます (省略可能)。
- プライマリ カテゴリ、セカンダリ カテゴリ、またはその両方についてそれぞれ最大 2 つのサブカテゴリ。 オファーに適用できるサブカテゴリがない場合は、 **[該当なし]** を選択します。

「[オファーの掲載のベスト プラクティス](../gtm-offer-listing-best-practices.md)」でカテゴリとサブカテゴリの完全な一覧を参照してください。

### <a name="industries"></a>業界

[!INCLUDE [Industry Taxonomy](includes/industry-taxonomy.md)]

### <a name="app-version"></a>アプリのバージョン

オファーのバージョン番号を入力します。 顧客にはオファーの詳細ページにこのバージョンが表示されます。

### <a name="terms-and-conditions"></a>使用条件

ここに、独自の法的な使用条件を指定します。 使用条件を見つけることができるアドレスを指定することもできます。 顧客は、オファーを試す前にこれらの条件を承諾する必要があります。

続行する前に、 **[下書きの保存]** を選択します。

## <a name="offer-listing"></a>オファーのリスト登録

<!--This page displays the languages in which your offer will be listed. Currently, **English (United States)** is the only available option.

Define marketplace details such as offer name, description, and images for each language/market. Select the language/market name to provide this info.-->This page lets you define offer details such as offer name, description, links, and contacts.

> [!NOTE]
> 1 つの言語でのみ、オファー登録情報の詳細を入力します。 オファーの説明が「このアプリケーションは、[英語以外の言語] でのみ利用可能です」という文言で始まっている場合、英語にする必要はありません。 オファー登録情報の内容で使用されているもの以外の言語でコンテンツを提供するために、"*役に立つリンクの URL*" を指定することもできます。

Microsoft AppSource でのオファー情報の表示例を次に示します (表示されている価格は例示のみを目的としており、実際のコストを反映することを意図していません)。
<!-- update screen? -->
:::image type="content" source="media/example-azure-marketplace-d365-operations.png" alt-text="Microsoft AppSource でこのオファーがどのように表示されるかを示しています。":::

#### <a name="call-out-descriptions"></a>コールアウトの説明

1. ロゴ
2. 製品
3. Categories
4. 業界
5. サポートのアドレス (リンク)
6. 使用条件
7. プライバシー ポリシー
8. プラン名
9. 説明
10. スクリーンショット/ビデオ

### <a name="marketplace-details"></a>Marketplace の詳細

ここで入力する **名前** は、オファー登録情報のタイトルとして顧客に表示されます。 このフィールドには、オファーの作成時に **[オファーの別名]** に入力したテキストが事前に設定されていますが、この値は変更できます。 この名前は商標の場合もあります (商標または著作権マークを含めることもできます)。 名前は 50 文字以下にする必要があります。絵文字を含めることはできません。

**[検索結果の概要]** では、オファーの簡単な説明 (最大 100 文字) を入力します。 この説明は、マーケットプレースの検索結果で使用される場合があります。

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

顧客がマーケットプレースでオファーを見つけるのに役立つように、必要に応じて最大 3 つの **検索キーワード** を入力することができます。 最良の結果を得るために、これらのキーワードを説明にも使用します。

**アプリが動作する製品** を顧客に知らせる場合は、製品名を 3 つまで入力します。

### <a name="helpprivacy-urls"></a>ヘルプまたはプライバシー URL

顧客がオファーの詳細について確認できる **[アプリのヘルプ リンク]** (URL) を入力します。 ヘルプ URL をサポート URL と同じにすることはできません。

自分の組織のプライバシー ポリシーへの **[プライバシー ポリシー リンク]** (URL) を入力します。 プライバシーに関する法律および規制にアプリが準拠していることを保証し、有効なプライバシー ポリシーを提供する責任があります。

### <a name="contact-information"></a>連絡先情報

**サポートの連絡先** と **エンジニアリングの連絡先** の名前、メール、電話番号を入力します。 この情報は顧客には表示されませんが、Microsoft で利用できるようになり、CSP パートナーに提供される場合もあります。

**[サポートの連絡先]** セクションに、CSP パートナーがオファーのサポートを確認できる **サポート URL** を指定します。 サポート URL をヘルプ URL と同じにすることはできません。

### <a name="supporting-documents"></a>サポート ドキュメント

ここには、関連するマーケティング ドキュメント (PDF 形式のホワイト ペーパー、パンフレット、チェックリスト、プレゼンテーションなど) を 1 つ以上 (3 つまで) 入力します。

### <a name="marketplace-media"></a>Marketplace メディア

顧客にオファーを表示するときに使用されるロゴや画像を指定します。 画像はすべて PNG 形式である必要があります。

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>ファイルのアップロードで問題が発生した場合は、パートナー センターで使用されている https://upload.xboxlive.com サービスがローカル ネットワークでブロックされていないことを確認してください。

#### <a name="logos"></a>ロゴ

**大** サイズのロゴに PNG ファイルを指定します。 パートナー センターではこれを使用して、その他の必要なサイズを作成します。 これは、必要に応じて、後で別の画像に置き換えることができます。

これらのロゴは、リスト登録のさまざまな場所で使用されます。

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Screenshots (スクリーンショット)

オファーがどのように動作するかを示すスクリーンショットを追加します。 少なくとも 1 つのスクリーンショットが必要です。最大 5 つまで追加できます。 スクリーンショットはすべて 1280 x 720 ピクセルの PNG 形式にする必要があります。

#### <a name="videos"></a>ビデオ

必要に応じて、オファーをデモンストレーションするビデオを最大 4 つ追加することもできます。 ビデオは外部サイトでホストされる必要があります。 それぞれのビデオについて、その名前、アドレス、およびビデオのサムネイル画像 (1280 x 720 ピクセル) を入力します。

マーケットプレースの登録情報に関するその他のリソースについては、[マーケットプレース オファーの登録情報のベスト プラクティス](../gtm-offer-listing-best-practices.md)に関する記事をご覧ください。

続行する前に、 **[下書きの保存]** を選択します。

## <a name="availability"></a>可用性

このページでは、オファーを利用可能にする場所と方法を定義できます。

### <a name="markets"></a>市場

オファーを使用可能にする必要がある市場を指定するには、 **[市場の編集]** を選択して **[市場の選択]** ポップアップ ウィンドウを表示します。

少なくとも 1 つの市場を選択してください。 **[すべて選択]** を選んで使用可能なすべての市場でオファーを使用できるようにするか、必要な特定の市場のみを選択します。 完了したら、 **[保存]** をクリックします。

ここで選択した内容は、新規購入にのみ適用されます。ユーザーが既に特定の市場でアプリを入手しているときに、後でその市場が削除された場合、その市場で既にオファーを入手したユーザーは引き続きそれを使用できますが、その市場で新しい顧客がオファーを入手することはできません。

> [!IMPORTANT]
> ここやパートナー センターにこれらの要件が記載されていない場合でも、あらゆる地域の法的要件を満たす必要があります。 すべての市場を選択した場合でも、国や地域によっては、地域の法律や規制、その他の要因により、特定のオファーが一覧表示されない場合があります。

### <a name="preview-audience"></a>プレビュー対象ユーザー

オファーをより広範なマーケットプレース オファーに公開する前に、まず、限定された **プレビュー対象ユーザー** に対してオファーを利用できるようにする必要があります。 ここでは、**非表示キー** (小文字と数字のみを使用する任意の文字列) を入力します。 プレビュー対象ユーザーのメンバーは、この非表示キーをトークンとして使用して、マーケットプレースでオファーのプレビューを表示できます。

オファーを利用可能にしてプレビューの制限を解除する準備ができたら、**非表示キー** を削除して再度発行する必要があります。

続行する前に、 **[下書きの保存]** を選択します。

## <a name="technical-configuration"></a>技術的な構成

このページでは、オファーに接続するために使用される技術的な詳細を定義します。 この接続によって、最終顧客がオファーを取得することを選択した場合、Microsoft は最終顧客向けにオファーをプロビジョニングできます。

### <a name="solution-identifier"></a>ソリューション識別子

ソリューションのソリューション識別子 (GUID) を入力します。

ソリューション識別子を検索するには:

1. Microsoft Dynamics Lifecycle Services (LCS) で、 **[ソリューション管理]** を選択します。
2. ソリューションを選択し、 **[パッケージの概要]** で **ソリューション識別子** を探します。 識別子が空白の場合、 **[編集]** を選択してパッケージを再発行してから、もう一度やり直してください。

### <a name="release-version"></a>リリース バージョン

このソリューションが動作する Dynamics 365 for Finance and Operations のバージョンを選択します。

続行する前に、 **[下書きの保存]** を選択します。

<!-- ## Test drive technical configuration

This page lets you set up a demonstration ("test drive") that allows customers to try your offer before purchasing it. Learn more in [What is test drive](../what-is-test-drive.md).

To enable a test drive, select the **Enable a test drive** check box on the [Offer setup](#test-drive) tab. To remove test drive from your offer, clear this check box.

When you've finished setting up your test drive, select **Save draft** before continuing.
-->
## <a name="supplemental-content"></a>補足コンテンツ

このページでは、オファーの確認に役立つ追加情報を提供できます。 この情報は顧客に表示されることも、マーケットプレースに公開されることもありません。

### <a name="validation-assets"></a>検証アセット

このセクションでは、[カスタマイズ分析レポート (CAR)](/dynamics365/unified-operations/dev-itpro/dev-tools/customization-analysis-report) をアップロードします。 このレポートは、事前に定義された一連のベスト プラクティス ルールに基づいて、カスタマイズおよび拡張モデルを分析することによって生成されます。

このファイルは .xls または .xlsx 形式である必要があります。 複数のレポートがある場合は、すべてのレポートを含む .zip ファイルをアップロードできます。

### <a name="does-solution-include-localizations"></a>Does solution include Localization?\(ソリューションにローカライズを含めますか?\)

ソリューションでローカルの標準とポリシーの使用を有効にする場合は、 **[はい]** を選択します (国/地域ごとに必要な異なる給与支払い規則に対応している場合など)。 それ以外の場合は、 **[いいえ]** を選択します。

### <a name="does-solution-enable-translations"></a>Does solution enable translation(s)? (ソリューションで翻訳を有効にしますか?)

ソリューション内のテキストを他の言語に翻訳できる場合は、 **[はい]** を選択します。 それ以外の場合は、 **[いいえ]** を選択します。

続行する前に、 **[下書きの保存]** を選択します。

## <a name="publish"></a>発行

### <a name="submit-offer-to-preview"></a>プレビューへのオファーの送信

オファーの必須セクションをすべて入力した後、ポータルの右上隅にある **[レビューと公開]** を選択します。

このオファーを公開するのが初めての場合、以下のことが可能です。

- オファーの各セクションの完了状態を確認する。
    - **未開始** – セクションは着手されておらず、入力する必要があります。
    - **未完了** – セクションに修正が必要なエラーがあるか、詳細情報が必要です。 セクションに戻って更新してください。
    - **完了** – セクションは完了しています。必須のデータはすべて入力済みであり、エラーはありません。 オファーを送信するには、オファーのセクションがすべて完了状態でなければなりません。
- **[認定の注意書き]** セクションで、アプリの理解に役立つ補足事項に加えて、テストの指示を認定チームに提供し、アプリが確実に正しくテストされるようにします。 テスト手順と最初の発行の完了の詳細については、「[AppSource Dynamics 365 Finance and Operations の機能検証](../dynamics-365-finance-operations-functional-validation.md)」を参照してください。
- **[送信]** を選択して、公開するためにオファーを送信する。 オファーのプレビュー バージョンのレビューと承認が可能になったら、メールが送信されます。 パートナー センターに戻り、 **[一般公開する]** を選択してオファーを一般に公開します。

## <a name="next-steps"></a>次のステップ

- [商業マーケットプレースで既存のオファーを更新する](./update-existing-offer.md)