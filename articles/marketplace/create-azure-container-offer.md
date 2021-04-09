---
title: Azure Container オファーを作成する - Azure Marketplace
description: Container オファーを作成して Azure Marketplace に公開する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 06/17/2020
ms.openlocfilehash: bcb334074d842fa5c35e4619f1c5d654405388a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96459562"
---
# <a name="create-an-azure-container-offer-in-azure-marketplace"></a>Azure Marketplace で Azure Container オファーを作成する

この記事では、Azure Marketplace 向けの Container オファーを作成して公開する方法について説明します。 開始する前に、まだ[パートナー センターでコマーシャル マーケットプレース アカウントを作成](./partner-center-portal/create-account.md)していなければ、作成します。 お使いのアカウントがコマーシャル マーケットプレース プログラムに登録されていることを確認します。

## <a name="create-a-new-offer"></a>新しいオファーを作成する

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。

2. 左側のナビゲーション メニューで、 **[商業マーケットプレース]**  >  **[概要]** の順に選択します。

3. [概要] ページで、 **[+ 新しいオファー]**  >  **[Azure Container]** の順に選択します。

   ![左側のナビゲーション メニューの画像。](./partner-center-portal/media/new-offer-azure-container.png)

> [!TIP]
> オファーが公開された後、それに対してパートナー センターで行われた編集内容は、そのオファーの再公開後にのみオンライン ストアに表示されます。 変更後は必ず再公開してください。

### <a name="offer-id-and-alias"></a>オファーの ID と別名

**[オファー ID]** を入力します。 これは、アカウントのオファーごとに一意の識別子です。

- この ID は、マーケットプレース オファーの Web アドレスと Azure Resource Manager テンプレート (該当する場合) で顧客に表示されます。
- 使用できるのは小文字と数字だけです。 ハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。 たとえば、「**test-offer-1**」と入力すると、オファーの Web アドレスは `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` になります。
- **[作成]** を選択した後で、オファー ID を変更することはできません。

**[オファーのエイリアス]** を入力します。 これは、パートナー センター内でオファーに使用される名前です。

- この名前はマーケットプレースでは使用されず、顧客に表示されるオファー名やその他の値とは異なります。
- **[作成]** の選択後にこれを変更することはできません。

**[作成]** を選択してオファーを生成し、続行します。

## <a name="offer-overview"></a>オファーの概要

**[オファーの概要]** ページでは、このオファー (完了しているものと近日公開のものの両方) を公開するために必要な手順と各手順の完了に必要な時間が図で示されます。

このページには、オファーの現在の状態に基づくさまざまなリンクが示されます。 次に例を示します。

- オファーがドラフトの場合 - ドラフトのオファーを削除する
- オファーが発行中の場合 - [オファーの販売を停止する](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan)
- オファーがプレビューの場合 - [公開する](review-publish-offer.md#previewing-and-approving-your-offer)
- 公開元のサインアウトを完了していない場合 - [発行をキャンセルする](review-publish-offer.md#cancel-publishing)

## <a name="offer-setup"></a>オファーのセットアップ

オファーを設定するには、次の手順に従います。

### <a name="customer-leads--optional"></a>潜在顧客 – 省略可能

パートナー センターを使用してオファーをコマーシャル マーケットプレースに発行する場合、それを顧客関係管理 (CRM) システムに接続できます。 これにより、自社の製品に顧客が関心を示したり、製品を使用したりした場合はすぐにその顧客の連絡先情報を受信できるようになります。

1. **潜在顧客の送信先となるリードのターゲットを選択します**。 パートナー センターでは、次の CRM システムがサポートされています。

   - [Dynamics 365](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
   - [Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md)
   - [Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md)

   > [!NOTE]
   > ご利用の CRM システムが上記に記載されていない場合は、[Azure テーブル](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md)または [HTTPS エンドポイント](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md)を使用して潜在顧客のデータを格納し、次にそのデータをご利用の CRM システムにエクスポートします。

2. パートナー センターで公開する際は、オファーをリードの宛先に接続します。
3. リードの宛先への接続が正しく構成されていることを確認します。 パートナー センターで公開すると、Microsoft により接続が検証され、テスト リードが送信されます。 オファーの公開前に行うプレビュー中は、プレビュー環境でオファーを自分で購入してみることで、リードの接続をテストすることもできます。
4. リードが失われないように、リードの宛先への接続が確実に最新の状態に保たれているようにします。

その他のリード管理リソースをいくつか次に示します。

- [コマーシャル マーケットプレース オファーからの顧客リード](./partner-center-portal/commercial-marketplace-get-customer-leads.md)
- [リード管理についてよく寄せられる質問](lead-management-faq.md#common-questions-about-lead-management)
- [リード構成エラーのトラブルシューティング](lead-management-faq.md#publishing-config-errors)
- [リード管理の概要](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (ポップアップ ブロックが無効になっていることを確認してください)。

続行する前に、 **[下書きの保存]** を選択します。

### <a name="properties"></a>Properties

このページでは、マーケットプレース上でのオファーのグループ分けに使用するカテゴリと、オファーがサポートされる法的契約を定義できます。

#### <a name="category"></a>カテゴリ

オファーを適切なマーケットプレース検索領域に配置するために、カテゴリとサブカテゴリを選択します。 オファーでこれらのカテゴリがどのようにサポートされるかを、必ずオファーの説明に記述してください。 選択:

- 少なくとも 1 つ、最大で 2 つのカテゴリ。プライマリ カテゴリとセカンダリ カテゴリを含みます (省略可能)。
- プライマリ カテゴリ、セカンダリ カテゴリ、またはその両方についてそれぞれ最大 2 つのサブカテゴリ。 オファーに適用できるサブカテゴリがない場合は、 **[該当なし]** を選択します。

「[オファーの掲載のベスト プラクティス](gtm-offer-listing-best-practices.md)」でカテゴリとサブカテゴリの完全な一覧を参照してください。 コンテナーは、常に **[コンテナー]** の下、そして **[コンテナー イメージ]** カテゴリに表示されます。

#### <a name="legal"></a>法的情報

オファーの使用条件を指定する必要があります。 2 つのオプションがあります。

- Microsoft 商業マーケットプレース向け標準契約を使用する。
- 独自の使用条件を指定する。

#### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 商業マーケットプレースの標準契約

Microsoft では、商業マーケットプレースの取り引きに役立つ標準契約テンプレートをご用意しています。 [標準契約] の下でソリューションを提供することを選択できます。この場合、顧客には確認し、同意することが一度だけ求められます。 これは、独自の使用条件を作成しない場合に適しています。

標準契約の詳細については、「[Microsoft 商業マーケットプレースの標準契約](standard-contract.md)」を参照してください。 [標準契約](https://go.microsoft.com/fwlink/?linkid=2041178) PDF をダウンロードすることもできます (ポップアップ ブロッカーをオフにしてください)。

標準契約を使用するには、**[Microsoft のコマーシャル マーケットプレース向け標準契約を使用しますか?](../standard-contract.md) をオンにします

> [!NOTE]
> Microsoft 商業マーケットプレースの標準契約を使用してオファーを公開した後に、独自のカスタムの使用条件を使用することはできません。 ソリューションは、標準契約または独自の使用条件のどちらか一方の下で提供します。

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="Microsoft 商業マーケットプレース向け標準契約を使用する チェックボックスの画像。":::

##### <a name="your-own-terms-and-conditions"></a>独自の使用条件

独自のカスタムの使用条件を指定するには、 **[使用条件]** ボックスにそれを入力します。 このボックスに入力できるテキスト文字数は無制限です。 顧客は、オファーを試す前にこれらの条件に同意する必要があります。

**[下書きの保存]** を選択してから、次のセクション「オファーのリスト登録」に進みます。

## <a name="offer-listing"></a>オファーのリスト登録

このページでは、商業マーケットプレースに表示されるオファーの詳細を定義できます。 これには、オファーの名前、説明、画像などが含まれます。

> [!NOTE]
> オファーの説明が「このアプリケーションは、[英語以外の言語] でのみ利用可能です」という文言で始まっている場合、オファーの詳細は英語である必要はありません。 また、オファー コンテンツへの役に立つリンクを、オファーのリスト登録の詳細説明で使用されているものとは異なる言語で提供することもできます。

### <a name="name"></a>名前

ここで入力する名前は、オファーのタイトルとして表示されます。 このフィールドには、オファーを作成したときに **[オファーの別名]** ボックスに入力したテキストがあらかじめ入力されています。 この名前は後で変更できます。

この名前は、次のようになります。

- 商標を使用できます (商標や著作権の記号を含めても構いません)。
- 長さは 50 文字を超えてはなりません。
- 絵文字を含めることはできません。

### <a name="search-results-summary"></a>検索結果の概要

オファーの簡単な説明です。 これは最大 100 文字長とすることができ、マーケットプレースの検索結果で使用されます。

### <a name="long-summary"></a>詳細な概要

オファーのさらに詳細な説明です。 これは最大 256 文字で、マーケットプレースの検索結果で使用されます。

### <a name="description"></a>説明

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>プライバシー ポリシーのリンク

組織のプライバシー ポリシーの Web アドレスを入力します。 ご自身でオファーが確実にプライバシーに関する法律や規則に準拠するようにしていただく必要があります。 また、Web サイトに有効なプライバシー ポリシーを投稿する責任もあります。

#### <a name="useful-links"></a>便利なリンク

オファーに関する補足のオンライン ドキュメントを提供します。 最大 25 個のリンクを追加できます。 リンクを追加するには、 **[+ Add a link]\(+ リンクを追加\)** を選択し、次のフィールドに入力します。

- **[タイトル]** - オファーの詳細ページでこれが顧客に表示されます。
- **[リンク (URL)]** - 顧客がオンライン ドキュメントを閲覧するためのリンクを入力します。 リンクの先頭は http:// か https:// でなければなりません。

### <a name="contact-information"></a>連絡先情報

**サポートの連絡先** と **エンジニアリングの連絡先** の名前、メール、電話番号を入力する必要があります。 この情報は顧客には表示されませんが、Microsoft で利用可能になります。 また、クラウド ソリューション プロバイダー (CSP) パートナーに提供される場合もあります。

- サポートの連絡先 (必須):サポートに関する一般的な質問用です。
- エンジニアリングの連絡先 (必須):技術的な質問と認定に関する問題用です。
- CSP プログラムの連絡先 (省略可能):リセラーからの CSP プログラムに関する質問用です。

**[サポートの連絡先]** セクションで、 **[サポート Web サイト]** を指定します。ここで、グローバル Azure、Azure Government、またはその両方におけるオファーの提供状況に基づき、パートナーはオファーのサポートを検索できます。

**[CSP プログラムの連絡先]** セクションで、リンク (**CSP プログラムの宣材**) を入力します。このリンクでは、CSP パートナーはオファーの宣材を検索できます。

#### <a name="additional-marketplace-listing-resources"></a>マーケットプレースのリスト登録に関するその他のリソース

オファーのリスト登録の作成について詳しくは、「[オファーの掲載のベスト プラクティス](gtm-offer-listing-best-practices.md)」を参照してください

### <a name="marketplace-images"></a>マーケットプレースの画像

オファーで使用するロゴと画像を提供します。 画像はすべて PNG 形式である必要があります。 ぼやけた画像は拒否されます。

[!INCLUDE [logo tips](./includes/graphics-suggestions.md)]

>[!Note]
>ファイルのアップロードで問題が発生した場合は、パートナー センターで使用されている https://upload.xboxlive.com サービスがローカル ネットワークでブロックされていないことを確認してください。

#### <a name="store-logos"></a>ストア ロゴ

**大** サイズのロゴに PNG ファイルを指定します。 パートナー センターでは、これを使用して、**小** および **中** サイズのロゴを作成します。 必要に応じて、別の画像に置き換えることもできます。

- **大** (216 x 216 から 350 x 350 px、必須)
- **中** (90 x 90 px、省略可能)
- **小** (48 x 48 px、省略可能)

これらのロゴは、リスト登録のさまざまな場所で使用されます。

[!INCLUDE [logos-azure-marketplace-only](./includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](./includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>スクリーンショット (省略可能)

オファーがどのように動作するかを示すスクリーンショットを最大 5 つ追加します。 それぞれが 1280 x 720 ピクセルのサイズで PNG 形式である必要があります。

#### <a name="videos-optional"></a>ビデオ (省略可能)

オファーをデモンストレーションするビデオを最大 5 つ追加します。 ビデオの名前、Web アドレス、ビデオのサムネイル PNG 画像 (1280 x 720 ピクセルのサイズ) を入力します。

#### <a name="offer-examples"></a>プランのサンプル

次の例は、オファーのリスト登録のフィールドが、オファーのさまざまな場所でどのように表示されるかを示しています。

これは、Azure Marketplace の **[オファーのリスト登録]** ページを示しています。

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Azure Marketplace の [オファーのリスト登録] ページの画像。" :::

これは、Azure Marketplace の検索結果を示しています。

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="Azure Marketplace の検索結果の画像。":::

これは、Azure portal の **[オファーのリスト登録]** ページを示しています。

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Azure portal の [オファーのリスト登録] ページの画像。":::

これは、Azure portal の検索結果を示しています。

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="Azure portal の検索結果の画像。":::

## <a name="preview"></a>プレビュー

[プレビュー] タブでは、一般公開する前にオファーを検証するため、限定の **プレビュー対象ユーザー** を選択できます。

> [!IMPORTANT]
> **[プレビュー]** でオファーを表示したら、 **[一般公開する]** を選択し、オファーを一般公開する必要があります。

Azure サブスクリプション ID GUID を使用してプレビュー対象ユーザーを指定し、省略可能な説明をそれぞれに付けます。 これらのフィールドのいずれも顧客は表示できません。

> [!NOTE]
> Azure サブスクリプション ID は、Azure portal の [サブスクリプション] ページにあります。

Azure サブスクリプション ID は少なくとも 1 つ追加します。個別に追加するか (最大 10 個)、CSV ファイルをアップロードします (最大 100 個)。 これらのサブスクリプション ID を追加することで、オファーを一般公開する前にプレビューできるユーザーを定義します。 オファーが既に一般公開されている場合、オファーの変更や更新をテストするためにプレビュー対象ユーザーを選択できます。

続行する前に、 **[下書きの保存]** を選択します。

## <a name="plan-overview"></a>プランの概要

このタブでは、同じオファー内でさまざまなプラン オプションを指定できます。 プラン (以前は SKU と呼ばれていました) は、利用できるクラウド (グローバル クラウド、Government クラウドなど) とプランで参照される画像に関して異なる場合があります。 商業マーケットプレースにオファーを登録するには、プランを少なくとも 1 つ設定する必要があります。

オファーごとに最大 100 のプランを作成できます。そのうち最大 45 をプライベートにすることができます。 プライベート プランの詳細については、「[Microsoft 商業マーケットプレースでのプライベート オファー](private-offers.md)」を参照してください。

プランの作成後、 **[プランの概要]** タブが表示されます。

- プラン名
- 価格モデル
- Azure リージョン (グローバルまたは Government)
- 現在の公開状態
- 利用できるアクション

[プランの概要] で使用可能なアクションは、プランの現在の状態によって異なります。 具体的な内容を次に示します。

- **ドラフトを削除する** - プランの状態がドラフトの場合。
- **販売プランを停止する** - プランの状態が一般公開の場合。

### <a name="create-new-plan"></a>新しいプランを作成する

**[新しいプランの作成]** を選択します。 **[新しいプラン]** ダイアログ ボックスが表示されます。

**[プラン ID]** ボックスでこのオファーのプランごとに一意のプラン識別子を作成します。 この ID は、製品の Web アドレスで顧客に表示されます。 小文字、数字、ダッシュ、アンダースコアのみを使用し、最大文字数は 50 です。

> [!NOTE]
> **[作成]** を選択した後で、プラン ID を変更することはできません。

**[プラン名]** ボックスに、このプランの名前を入力します。 この名前は、顧客がオファー内のどのプランを選択するかを決めるときに表示されます。 このオファーのプランごとに一意の名前を作成します。 たとえば、**Windows Server 2016** と **Windows Server 2019** のプランで **Windows Server** のオファー名を使用できます。

### <a name="plan-setup"></a>プランのセットアップ

このタブでは、プランが利用できるクラウドを選択できます。 このタブでの設定により、他のタブに表示されるフィールドが変わります。

#### <a name="azure-regions"></a>Azure Azure リージョン

Azure Container オファーのすべてのプランは、自動的に **[Azure グローバル]** で利用できるようになります。  コマーシャル マーケットプレースを使用する、すべてのグローバル Azure リージョンの顧客がプランを使用できます。 詳細については、「[利用可能な地域と通貨サポート](marketplace-geo-availability-currencies.md)」を参照してください。

[[Azure Government]](../azure-government/documentation-government-welcome.md) オプションを選択すると、ソリューションがここに表示されます。 これはアメリカ合衆国の連邦政府顧客、州政府顧客、地方自治体顧客、部族政府顧客と、そのような顧客にサービスを提供する資格があるパートナーのための政府機関コミュニティ クラウドであり、アクセスが制御されています。 公開元はこのクラウド コミュニティのコンプライアンス制御、セキュリティ対策、ベスト プラクティスに責任を負います。 Azure Government では、物理的に離れた場所にあるデータ センターとネットワークが使用されます (場所は米国のみ)。 Azure Government に[公開](../azure-government/documentation-government-manage-marketplace-partners.md)する前に、その領域内でソリューションをテストし、確認します。これは結果が異なる場合があるためです。 ソリューションを作成し、テストするには、[Microsoft Azure 政府機関向け試用版](https://azure.microsoft.com/global-infrastructure/government/request/)に対する試用版アカウントの申請を行います。

> [!NOTE]
> プランが公開され、特定のリージョンで利用できるようになった後に、そのリージョンを削除することはできません。

#### <a name="azure-government-certifications"></a>Azure Government の認定資格

このオプションは、 **[Azure リージョン]** で **[Azure Government]** を選択した場合にのみ表示されます。

Azure Government サービスでは、特定の政府の規制および要件の対象となるデータが処理されます。 FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4、CJIS などです。

これらのプログラムの認定資格を示すため、それらを説明するリンクを最大 100 個指定できます。 プログラムのリスト登録に直接リンクするか、独自の Web サイトにリンクできます。 これらのリンクは、Azure Government の顧客にのみ表示されます。

### <a name="plan-listing"></a>プランのリスト登録

このタブには、現在のオファー内の異なるプラン別に特定の情報が表示されます。

### <a name="plan-name"></a>プラン名

ここは、プランの作成時に付けた名前が事前入力されます。 この名前は、必要に応じて変更できます。 最大 50 文字にすることができます。 この名前は Azure Marketplace と Azure portal でこのプランのタイトルとして表示されます。 プランを使用する準備ができた後、既定のモジュール名として使用されます。

### <a name="plan-summary"></a>プラン概要

(オファーではなく) ソフトウェア プランの簡単な概要です。 この概要は Azure Marketplace の検索結果に表示されます。最大 100 文字を入力できます。

### <a name="plan-description"></a>プラン説明

このソフトウェア プラン独特の要素やオファーに含まれる他のプランとの違いを説明します。 オファーについては説明しないでください。プランの説明だけです。 この説明は Azure Marketplace と Azure portal の **[オファーのリスト登録]** ページに表示されます。 プラン概要に入力したものと同じ内容にすることができます。最大 2,000 文字を入力できます。

これらのフィールドに入力した後、 **[保存]** を選択します。

#### <a name="plan-examples"></a>プランの例

次の例は、プランのリスト登録フィールドが、さまざまなビューでどのように表示されるかを示しています。

以下はプラン詳細を閲覧するときの Azure Marketplace のフィールドです。

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="Azure Marketplace でプラン詳細を閲覧するときに表示されるフィールドの画像。":::

以下は Azure portal のプラン詳細です。

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="Azure portal のプラン詳細の画像。":::

### <a name="plan-availability"></a>プランの可用性

公開したオファーを非表示にし、マーケットプレースで顧客がそれを検索、閲覧、購入できないようにする場合、 **[可用性]** タブで **[プランの非表示]** チェックボックスを選択します。

このフィールドは、次の場合に使用します。

- オファーが別のアプリケーションを経由して参照されるときに間接的に使用されることを意図している。
- オファーは個別に購入できない。
- プランは最初のテストに使用されたが、無関係となった。
- プランは一時的または季節的なオファーに使用されたが、提供されなくなった。

## <a name="technical-configuration"></a>技術的な構成

コンテナー イメージは、プライベートの [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) でホストされる必要があります。 **[技術的な構成]** タブで、Azure Container Registry 内のコンテナー イメージ リポジトリの参照情報を指定します。

オファーの公開後、特定のパブリック コンテナー レジストリでコンテナー イメージが Azure Marketplace にコピーされます。 コンテナー イメージの使用要求はすべて、Azure Marketplace パブリック コンテナー レジストリからサービスが提供されます (プライベートのものからではありません)。 詳細については、[Azure Container の技術資産の準備](create-azure-container-technical-assets.md)に関する記事を参照してください。

### <a name="image-repository-details"></a>イメージ リポジトリの詳細

**[イメージ リポジトリの詳細]** タブに次の情報を入力します。

**Azure サブスクリプション ID** - 使用状況が報告され、コンテナー イメージが含まれる Azure Container Registry に対してサービスが課金されるサブスクリプション ID を入力します。 この ID は Azure portal の[[サブスクリプション] ページ](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)にあります。

**Azure リソース グループ名** - Azure Container Registry とコンテナー イメージが含まれる [リソース グループ](../azure-resource-manager/management/manage-resource-groups-portal.md)の名前を指定します。 リソース グループにはサブスクリプション ID (上記) でアクセスできる必要があります。 この名前は Azure portal の [[リソース グループ]](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) ページにあります。

**Azure コンテナー レジストリ名** - コンテナー イメージが含まれる [Azure Container Registry](../container-registry/container-registry-intro.md) の名前を指定します。 このコンテナー レジストリは、前に指定した Azure リソース グループに存在する必要があります。 完全なログイン サーバー名ではなく、レジストリ名のみを含めます。 名前からは必ず **azurecr.io** を除外してください。 レジストリ名は、Azure portal の[[コンテナー レジストリ] ページ](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries)にあります。

**Azure Container Registry の管理者ユーザー名** – コンテナー イメージが含まれる Azure Container Registry にリンクされている [管理者ユーザー名](../container-registry/container-registry-authentication.md#admin-account)) を指定します。 このユーザー名とパスワードは、自社からレジストリにアクセスするために必要です。 管理者ユーザー名とパスワードを取得するには、Azure コマンド ライン インターフェイス (CLI) で **admin-enabled** プロパティを **[True]** に設定します。 必要に応じて、Azure portal で **[管理者ユーザー]** を **[有効にする]** に設定できます。

 :::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="[コンテナー レジストリの更新] ダイアログ ボックスの画像。":::

**Azure Container Registry のパスワード** - コンテナー イメージが含まれる Azure Container Registry に関連付けられている管理者ユーザー名のパスワードを指定します。 このユーザー名とパスワードは、自社からレジストリにアクセスするために必要です。 **[コンテナー レジストリ]**  >  **[アクセス キー]** に移動するか、Azure CLI で [show コマンド](/cli/azure/acr/credential#az-acr-credential-show)を使用することで、Azure portal からパスワードを取得できます。

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="[アクセス キー]メニューの画像。":::

**Azure Container Registry 内のリポジトリ名**。 イメージが含まれる Azure Container Registry リポジトリの名前を指定します。 レジストリにイメージをプッシュするときに、リポジトリの名前を含めます。 リポジトリの名前は、[[コンテナー レジストリ]](https://azure.microsoft.com/services/container-registry/) >  **[リポジトリ]** ページに移動すると見つかります。 詳細については、「[Azure Portal でコンテナー レジストリ リポジトリを表示する](../container-registry/container-registry-repositories.md)」を参照してください。

> [!NOTE]
> 設定した名前は変更できません。 アカウント内のオファー別に一意の名前を使用します。

### <a name="image-tags-for-new-versions-of-your-offer"></a>新しいバージョンのオファーのイメージ タグ

更新プログラムを公開したとき、顧客は Azure Marketplace から更新プログラムを自動的に取得できなければなりません。 彼らが更新しない場合、特定のバージョンのイメージを維持できなければなりません。 これを行うには、イメージを更新するたびに新しいイメージ タグを追加します。

### <a name="image-tag"></a>イメージ タグ

このフィールドには、サポートされているすべてのプラットフォームで最新バージョンのイメージを指す **最新** のタグを含める必要があります。 バージョン タグも含める必要があります (たとえば、xx.xx.xx から始まり、xx は数字です)。 複数のプラットフォームを対象とするには、顧客は[マニフェスト タグ](https://github.com/estesp/manifest-tool)を使用する必要があります。 マニフェスト タグで参照されるすべてのタグも、アップロードできるように追加する必要があります。

マニフェスト タグ (最新のタグを除く) はすべて X.Y **-** か X.Y.Z- で始める必要があります。X、Y、Z は整数です。 たとえば、**最新** のタグが 1.0.1-linux-x64、1.0.1-linux-arm32、1.0.1-windows-arm32 を指す場合、これら 6 つのタグをこのフィールドに追加する必要があります。 詳細については、[Azure Container の技術資産の準備](create-azure-container-technical-assets.md)に関する記事を参照してください。

> [!NOTE]
> テスト中にイメージを識別できるように、必ずテスト タグをイメージに追加してください。

## <a name="review-and-publish"></a>レビューと公開

オファーの必須セクションをすべて完了したら、レビューと公開のためにそれを送信することができます。

ポータルの右上隅で、 **[レビューと** **公開]** を選択します。

レビュー ページでは、次のことができます。

- オファーの各セクションの完了状態を確認する。 オファーの全セクションに完了の印が付くまで公開できません。
  - **開始前** - 開始されておらず、完了する必要があります。
  - **未完了** - 修正が必要なエラーがあり、追加情報を入力する必要があります。 ヘルプについては、このドキュメントの前のセクションを参照してください。
  - **完了** - エラーなしで必要なデータがすべて含まれています。 オファーを送信するには、オファーのすべてのセクションが完了している必要があります。
- オファーが確実に正しくテストされるよう、認定チームにはテストの指示を提供します。 また、オファーの理解に役立つ補足事項を提供します。

オファーを送信して公開するには、 **[公開]** を選択します。

オファーのプレビュー バージョンが確認して承認できるようになったら、その旨をお知らせするメールを Microsoft からお送りします。

オファーを一般に公開するには、パートナー センターにアクセスし、 **[一般公開する]** を選択します。

## <a name="next-step"></a>次のステップ

- [コマーシャル マーケットプレースで既存のオファーを更新する](./partner-center-portal/update-existing-offer.md)