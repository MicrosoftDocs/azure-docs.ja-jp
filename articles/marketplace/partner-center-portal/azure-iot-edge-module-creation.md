---
title: パートナー センターを使用して Azure Marketplace で Azure IoT Edge モジュールのオファーを作成する
description: パートナー センターを使用して、Azure Marketplace で IoT Edge モジュールのオファーを作成、構成、公開する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 08/07/2020
ms.openlocfilehash: 23af7b3f7cfd5ec0531c5cac73cf8a334ef4aa62
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99096442"
---
# <a name="create-an-iot-edge-module-offer"></a>IoT Edge モジュール オファーの作成

この記事では、Azure Marketplace 向けのモノのインターネット (IoT) Edge モジュール オファーを作成して発行する方法について説明します。 開始する前に、まだ[パートナー センターでコマーシャル マーケットプレース アカウントを作成](create-account.md)していなければ、作成します。 お使いのアカウントがコマーシャル マーケットプレース プログラムに登録されていることを確認します。

## <a name="create-a-new-offer"></a>新しいオファーを作成する

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
2. 左側のナビゲーション メニューで、 **[コマーシャル マーケットプレース]**  >  **[概要]** の順に選択します。
3. [概要] ページで、 **[+ 新しいオファー]**  >  **[IoT Edge モジュール]** の順に選択します。

    ![左側のナビゲーション メニューの画像。](./media/new-offer-iot-edge.png)

> [!IMPORTANT]
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

このページには、ユーザーの選択に基づき、このオファーで操作を実行するためのリンクが含まれています。 次に例を示します。

- オファーがドラフトの場合 - ドラフトのオファーを削除する
- オファーが発行中の場合 - [オファーの販売を停止する](update-existing-offer.md#stop-selling-an-offer-or-plan)
- オファーがプレビューの場合 - [公開する](../review-publish-offer.md#previewing-and-approving-your-offer)
- 公開元のサインアウトを完了していない場合 - [発行をキャンセルする](../review-publish-offer.md#cancel-publishing)

## <a name="offer-setup"></a>オファーのセットアップ

オファーを設定するには、次の手順に従います。

### <a name="customer-leads"></a>潜在顧客

パートナー センターを使用してオファーをマーケットプレースに公開する場合、それを顧客関係管理 (CRM) システムに任意で接続できます。 これにより、自社の製品に顧客が関心を示したり、製品を使用したりした場合はすぐにその顧客の連絡先情報を受信できるようになります。

1. 顧客リードの送信先となるリードのターゲットを選択します。 パートナー センターでは、次の CRM システムがサポートされています。

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > ご利用の CRM システムが上記に記載されていない場合は、[Azure テーブル](commercial-marketplace-lead-management-instructions-azure-table.md)または [HTTPS エンドポイント](commercial-marketplace-lead-management-instructions-https.md)を使用して潜在顧客のデータを格納し、次にそのデータをご利用の CRM システムにエクスポートします。

2. パートナー センターで公開する際は、オファーをリードの宛先に接続します。
3. リードの宛先への接続が正しく構成されていることを確認します。 パートナー センターで公開すると、Microsoft で接続を検証して、テスト リードを送信します。 オファーの公開前に行うプレビュー中は、プレビュー環境でオファーを自分で購入してみることで、リードの接続をテストすることもできます。
4. リードが失われないように、リードの宛先への接続が確実に最新の状態に保たれているようにします。

その他のリード管理リソースをいくつか次に示します。

- [コマーシャル マーケットプレース オファーからの顧客リード](commercial-marketplace-get-customer-leads.md)
- [リード管理についてよく寄せられる質問](../lead-management-faq.md#common-questions-about-lead-management)
- [リード構成エラーのトラブルシューティング](../lead-management-faq.md#publishing-config-errors)
- [リード管理の概要](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (ポップアップ ブロックが無効になっていることを確認してください)。

続行する前に、 **[下書きの保存]** を選択します。

### <a name="properties"></a>Properties

このページでは、マーケットプレース上でのオファーのグループ分けに使用するカテゴリと、オファーがサポートされる法的契約を定義できます。

#### <a name="category"></a>カテゴリ

オファーを適切なマーケットプレース検索領域に配置するために、カテゴリとサブカテゴリを選択します。 オファーでこれらのカテゴリがどのようにサポートされるかを、必ずオファーの説明に記述してください。 選択:

- 少なくとも 1 つ、最大で 2 つのカテゴリ。プライマリ カテゴリとセカンダリ カテゴリを含みます (省略可能)。
- プライマリ カテゴリ、セカンダリ カテゴリ、またはその両方についてそれぞれ最大 2 つのサブカテゴリ。 オファーに適用できるサブカテゴリがない場合は、 **[該当なし]** を選択します。

「[オファーの掲載のベスト プラクティス](../gtm-offer-listing-best-practices.md)」でカテゴリとサブカテゴリの完全な一覧を参照してください。 マーケットプレースでは、IoT Edge モジュールは常に  **[モノのインターネット]**  >  **[IoT Edge モジュール]**  カテゴリに表示されます。

#### <a name="legal"></a>法的情報

オファーの使用条件を指定する必要があります。 2 つのオプションがあります。

- Microsoft 商業マーケットプレース向け標準契約を使用する。
- 独自の使用条件を指定する。

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 商業マーケットプレースの標準契約

Microsoft では、商業マーケットプレースの取り引きに役立つ標準契約テンプレートをご用意しています。 [標準契約] の下でソリューションを提供することを選択できます。この場合、顧客には確認し、同意することが一度だけ求められます。 独自の使用条件を作らない場合、これをお勧めします。

標準契約の詳細については、「[Microsoft 商業マーケットプレースの標準契約](../standard-contract.md)」を参照してください。 [標準契約](https://go.microsoft.com/fwlink/?linkid=2041178) PDF をダウンロードすることもできます (ポップアップ ブロッカーをオフにしてください)。

標準契約を使用するには、 **[Microsoft 商業マーケットプレース向け標準契約を使用する]** チェックボックスを選択し、 **[承諾]** をクリックします。

> [!NOTE]
> Microsoft 商業マーケットプレースの標準契約を使用してオファーを公開した後に、独自のカスタムの使用条件を使用することはできません。 ソリューションは、標準契約または独自の使用条件の下で提供します。

![[Microsoft 商業マーケットプレース向け標準契約を使用する] チェックボックス使用の画像。](media//iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>独自の使用条件

独自のカスタムの使用条件を指定するには、 **[使用条件]** ボックスにそれを入力します。 このボックスに入力できるテキスト文字数は無制限です。 顧客は、オファーを試す前にこれらの条件に同意する必要があります。

**[下書きの保存]** を選択してから、次のセクション「オファーのリスト登録」に進みます。

## <a name="offer-listing"></a>オファーのリスト登録

ここでは、マーケットプレースに表示されるオファーの詳細を定義します。 これには、オファーの名前、説明、画像などが含まれます。 このオファーを構成するときには、Microsoft のポリシーに関するページに詳述されているポリシーに必ず従ってください。

> [!NOTE]
> オファーの詳細が「このアプリケーションは、[英語以外の言語] でのみ利用可能です」という文言で始まっていれば英語である必要はありません。 また、オファー コンテンツへの役に立つリンクを、オファーのリスト登録の詳細説明で使用されている言語とは異なる言語で提供することもできます。

### <a name="name"></a>名前

ここで入力する名前は、オファーのタイトルとして表示されます。 このフィールドには、オファーを作成したときに **[オファーの別名]** ボックスに入力したテキストがあらかじめ入力されています。 この名前は後で変更できます。

この名前は、次のようにすることができます。

- 商標登録されている可能性があります (商標または著作権マークを含めることもできます)。
- 文字長が 50 文字を超えてはなりません。
- 絵文字を含めることはできません。

### <a name="search-results-summary"></a>検索結果の概要

オファーの簡単な説明を入力します。 これは最大 100 文字長とすることができ、マーケットプレースの検索結果で使用されます。

### <a name="long-summary"></a>詳細な概要

オファーの詳細を入力します。 これは最大 256 文字長とすることができ、マーケットプレースの検索結果で使用されます。

### <a name="description"></a>説明

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

IoT Edge モジュールのオファーには、説明の一番下に、次のような最小ハードウェア要件の段落が含まれている必要があります。

- 最小ハードウェア要件: Linux x64 および arm32 OS、1 GB の RAM、500 MB のストレージ

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-url"></a>[プライバシー ポリシーの URL]

組織のプライバシー ポリシーの Web アドレスを入力します。 ご自身でオファーが確実にプライバシーに関する法律や規則に準拠するようにしていただく必要があります。 また、Web サイトに有効なプライバシー ポリシーを投稿する責任もあります。

#### <a name="useful-links"></a>便利なリンク

オファーに関する補足のオンライン ドキュメントを提供します。 最大 25 個のリンクを追加できます。 リンクを追加するには、 **[+ リンクの追加]** を選択し、次のフィールドに入力します。

- **[タイトル]** - オファーの詳細ページでタイトルが顧客に表示されます。
- **[リンク (URL)]** - 顧客がオンライン ドキュメントを閲覧するためのリンクを入力します。 リンクは `http://` または `https://` で始まっている必要があります。

少なくともドキュメントへのリンクを 1 つと、 [Azure IoT デバイス カタログ](https://catalog.azureiotsolutions.com/)に記載されている互換性のある IoT Edge デバイスへのリンクを 1 つ、追加するようにしてください。

### <a name="contact-information"></a>連絡先情報

**サポートの連絡先** と **エンジニアリングの連絡先** の名前、メール、電話番号を入力する必要があります。 この情報は顧客には表示されません。 これは Microsoft で利用することができ、クラウド ソリューション プロバイダー (CSP) パートナーに提供される場合があります。

- サポートの連絡先 (必須):サポートに関する一般的な質問用です。
- エンジニアリングの連絡先 (必須):技術的な質問と認定に関する問題用です。
- CSP プログラムの連絡先 (省略可能):リセラーからの CSP プログラムに関する質問用です。

**[サポートの連絡先]** セクションで、 **[サポート Web サイト]** の Web アドレスを指定します。このサイトでは、グローバル Azure、Azure Government、またはその両方におけるオファーの提供状況に基づき、パートナーはオファーのサポートを検索できます。

**[CSP プログラムの連絡先]** セクションで、リンク (**CSP プログラムの宣材**) を入力します。このリンクでは、CSP パートナーはオファーの宣材を検索できます。

#### <a name="additional-marketplace-listing-resources"></a>マーケットプレースのリスト登録に関するその他のリソース

オファーのリスト登録の作成について詳しくは、「[オファーの掲載のベスト プラクティス](../gtm-offer-listing-best-practices.md)」を参照してください。

### <a name="marketplace-images"></a>マーケットプレースの画像

オファーで使用するロゴと画像を提供します。 画像はすべて PNG 形式である必要があります。 ぼやけた画像は拒否されます。

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>ファイルのアップロードで問題が発生した場合は、パートナー センターで使用されている https://upload.xboxlive.com サービスがローカル ネットワークでブロックされていないことを確認してください。

#### <a name="store-logos"></a>ストア ロゴ

**大** サイズのロゴに PNG ファイルを指定します。 パートナー センターでは、これを使用して、**小** および **中** サイズのロゴを作成します。 必要に応じて、別の画像に置き換えることもできます。

- **大** (216 x 216 から 350 x 350 px、必須)
- **中** (90 x 90 px、省略可能)
- **小** (48 x 48 px、省略可能)

これらのロゴは、リスト登録のさまざまな場所で使用されます。

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>スクリーンショット (省略可能)

オファーがどのように動作するかを示すスクリーンショットを最大 5 つ追加します。 それぞれが 1280 x 720 ピクセルのサイズで PNG 形式である必要があります。

#### <a name="videos-optional"></a>ビデオ (省略可能)

オファーをデモンストレーションするビデオを最大 5 つ追加します。 ビデオの名前、Web アドレス、ビデオのサムネイル PNG 画像 (1280 x 720 ピクセルのサイズ) を入力します。

#### <a name="marketplace--examples"></a>Marketplace の例

Azure Marketplace でのオファー情報の表示例を次に示します。

:::image type="content" source="media/example-iot-azure-marketplace-offer.png" alt-text="Azure Marketplace でこのオファーがどのように表示されるかを示しています。":::

#### <a name="call-out-descriptions"></a>コールアウトの説明

1. 大型のロゴ
2. Categories
3. サポートのアドレス (リンク)
4. 使用条件
5. プライバシー ポリシーのアドレス (リンク)
6. 名前
7. まとめ
8. 説明
9. 便利なリンク
10. スクリーンショット、ビデオ

<br>Azure Marketplace の検索結果におけるオファー情報の表示例を次に示します。

:::image type="content" source="media/example-iot-azure-marketplace-offer-search-results.png" alt-text="Azure Marketplace の検索結果でこのオファーがどのように表示されるかを示しています。":::

#### <a name="call-out-descriptions"></a>コールアウトの説明

1. 小型のロゴ
2. プラン名
3. 検索結果の概要

<br>Azure portal でのオファー情報の表示例を次に示します。

:::image type="content" source="media/example-iot-azure-portal-offer.png" alt-text="Azure portal でこのオファーがどのように表示されるかを示しています。":::

#### <a name="call-out-descriptions"></a>コールアウトの説明

1. 名前
2. 説明
3. 便利なリンク
4. Screenshots (スクリーンショット)

<br>Azure portal の検索結果におけるオファー情報の表示例を次に示します。

:::image type="content" source="media/example-iot-azure-portal-offer-search-results.png" alt-text="Azure portal の検索結果でこのオファーがどのように表示されるかを示しています。":::

#### <a name="call-out-descriptions"></a>コールアウトの説明

1. 小型のロゴ
2. プラン名
3. 検索結果の概要

<br>**[下書きの保存]** を選択してから、次のセクション「プレビュー」に進みます。

## <a name="preview"></a>プレビュー

**[プレビュー]** タブでは、マーケットプレースの幅広い対象ユーザーに対して一般公開する前にオファーを検証するため、限定の **プレビュー対象ユーザー** を選択できます。

> [!IMPORTANT]
> プレビューでオファーを表示したら、 **[一般公開する]** を選択し、オファーを一般公開する必要があります。

Azure サブスクリプション ID GUID でプレビュー対象ユーザーを指定し、必要に応じてそれぞれの説明を指定します。 いずれのフィールドも顧客は表示できません。

> [!NOTE]
> Azure サブスクリプション ID は、Azure portal の [サブスクリプション] ページにあります。

Azure サブスクリプション ID は少なくとも 1 つ追加します。個別に追加するか (最大 10 個)、CSV ファイルをアップロードします (最大 100 個)。 これらのサブスクリプション ID を追加することで、オファーを一般公開する前にプレビューできるユーザーを定義します。 オファーが既に一般公開されている場合、オファーの変更や更新をテストするためにプレビュー対象ユーザーを定義できます。

**[下書きの保存]** を選択してから、次のセクション「プランの概要」に進みます。

## <a name="plan-overview"></a>プランの概要

このタブでは、パートナー センターの同じオファー内でさまざまなプラン オプションを指定できます。 プラン (以前は SKU と呼ばれていました) は、利用できるクラウド (グローバル クラウド、Government クラウドなど) とプランで参照される画像に関して異なる場合があります。 マーケットプレースにオファーを登録するには、プランを少なくとも 1 つ設定する必要があります。

オファーごとに最大 100 のプランを作成できます。そのうち最大 45 をプライベートにすることができます。 プライベート プランの詳細については、「[Microsoft 商業マーケットプレースでのプライベート オファー](../private-offers.md)」を参照してください。

プランの作成後、 **[プランの概要]** タブが表示されます。

- プラン名
- 価格モデル
- Azure リージョン (グローバルまたは Government)
- 現在の公開状態
- 利用できるアクション

[プランの概要] で使用可能なアクションは、プランの現在の状態によって異なります。 具体的な内容を次に示します。

- **下書きを削除する**:プランの状態が下書きの場合。
- **販売プランを停止する**:プランの状態が一般公開の場合。

### <a name="create-new-plan"></a>新しいプランを作成する

**[新しいプランの作成]** を選択します。 **[新しいプラン]** ダイアログ ボックスが表示されます。

**[プラン ID]** ボックスでこのオファーのプランごとに一意のプラン ID を作成します。 この ID は、製品の Web アドレスで顧客に表示されます。 小文字、数字、ダッシュ、アンダースコアのみを使用し、最大文字数は 50 です。

**[プラン名]** ボックスに、このプランの名前を入力します。 この名前は、顧客がオファー内のどのプランを選択するかを決めるときに表示されます。 このオファーのプランごとに一意の名前を作成します。 たとえば、**Windows Server 2016** プランと **Windows Server 2019** プランで **Windows Server** のオファー名を使用することがあります。

> [!NOTE]
> **[作成]** を選択した後で、プラン ID を変更することはできません。

**［作成］** を選択します

### <a name="plan-setup"></a>プランのセットアップ

このタブでは、プランが利用できるクラウドを構成できます。 このタブでの設定により、他のタブに表示されるフィールドが変わります。

#### <a name="azure-regions"></a>Azure Azure リージョン

IoT Edge モジュールのオファーのすべてのプランは、自動的に **[Azure グローバル]** で利用できるようになります。  マーケットプレースを使用する、すべてのグローバル Azure リージョンの顧客がプランを使用できます。 詳細については、「[利用可能な地域と通貨サポート](../marketplace-geo-availability-currencies.md)」を参照してください。

[[Azure Government]](../../azure-government/documentation-government-welcome.md) オプションを選択すると、ソリューションがここに表示されます。 これはアメリカ合衆国の連邦政府顧客、州政府顧客、地方自治体顧客、部族政府顧客と、そのような顧客にサービスを提供する資格があるパートナーのための政府機関コミュニティ クラウドであり、アクセスが制御されています。 公開元はこのクラウド コミュニティのコンプライアンス制御、セキュリティ対策、ベスト プラクティスに責任を負います。 Azure Government では、物理的に離れた場所にあるデータ センターとネットワークが使用されます (場所は米国のみ)。 Azure Government に[公開](../../azure-government/documentation-government-manage-marketplace-partners.md)する前に、その領域内でソリューションをテストし、確認します。これは結果が異なる場合があるためです。 ソリューションを計画し、テストするには、[Microsoft Azure 政府機関向け試用版](https://azure.microsoft.com/global-infrastructure/government/request/)に試用版アカウントを申請します。

> [!NOTE]
> プランが公開され、特定のリージョンで利用できるようになった後に、そのリージョンを削除することはできません。

#### <a name="azure-government-certifications"></a>Azure Government の認定資格

このオプションは、 **[Azure リージョン]** で **[Azure Government]** を選択した場合にのみ表示されます。

Azure Government サービスでは、特定の政府の規制および要件の対象となるデータが処理されます。 FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4、CJIS などです。 これらのプログラムの認定資格を認識させるため、認定資格について説明するリンクを 100 個まで提供することができます。 プログラムのリスト登録に直接リンクするか、独自の Web サイトにリンクできます。 これらのリンクは、Azure Government の顧客にのみ表示されます。

### <a name="plan-listing"></a>プランのリスト登録

このタブには、同じオファー内の異なるプラン別に特定の情報が表示されます。

### <a name="plan-name"></a>プラン名

ここには、登録時、プランに付けた名前が事前入力されます。 この名前は、必要に応じて変更できます。 最大 50 文字にすることができます。 この名前は Azure Marketplace と Azure portal でこのプランのタイトルとして表示されます。 プランを使用する準備ができた後、既定のモジュール名として使用されます。

### <a name="plan-summary"></a>プラン概要

(オファーではなく) プランについて簡単にまとめた説明です。 この概要は Azure Marketplace の検索結果に表示されます。最大 100 文字を入力できます。

### <a name="plan-description"></a>プラン説明

このプラン独特の要素やオファーに含まれる他のプラントの違いを説明します。 オファーについては説明しないでください。プランの説明だけです。 この説明は Azure Marketplace と Azure portal のオファーのリスト登録ページに表示されます。 プラン概要に入力したものと同じ内容にすることができます。最大 2,000 文字を入力できます。

以上のフィールドに入力したら、 **[下書きの保存]** を選択します。

#### <a name="plan-examples"></a>プランの例

Azure Marketplace でのプランの詳細の例を次に示します (表示されている価格は例示のみを目的としており、実際のコストを反映することを意図していません)。

:::image type="content" source="media/example-iot-azure-marketplace-plan.png" alt-text="Azure Marketplace でのプランの詳細を示しています。":::

#### <a name="call-out-descriptions"></a>コールアウトの説明

1. プラン名
2. プラン名
3. プラン説明

<br>Azure portal でのプランの詳細の例を次に示します (表示されている価格は例示のみを目的としており、実際のコストを反映することを意図していません)。

:::image type="content" source="media/example-iot-azure-marketplace-plan-details.png" alt-text="Azure portal でのプラン詳細を示しています。":::

#### <a name="call-out-descriptions"></a>コールアウトの説明

1. プラン名
2. プラン名
3. プラン説明

### <a name="availability"></a>可用性

公開したオファーを非表示にし、マーケットプレースで顧客がそれを検索、閲覧、購入できないようにする場合、[可用性] タブで **[プランの非表示]** チェックボックスを選択します。

このフィールドは通常、次のときに使用されます。

- 別のアプリケーションを経由して参照されるとき、オファーが間接的にのみ使用される。
- オファーは個別に購入できない。
- プランは最初のテストに使用されたが、無関係となった。
- プランは一時的または季節的なオファーに使用されたが、提供されなくなった。

## <a name="technical-configuration"></a>技術的な構成

**IoT Edge モジュール** プランの種類は、IoT Edge デバイスで実行されているコンテナーの特定の種類です。 **[技術的な構成]** タブでは、[Azure Container Registry](https://azure.microsoft.com/services/container-registry/) 内のコンテナー イメージ リポジトリの参照情報と、顧客にモジュールを簡単に使用してもらうための構成設定を指定できます。

オファーの公開後、特定のパブリック コンテナー レジストリで IoT Edge コンテナー イメージが Azure Marketplace にコピーされます。 Azure ユーザーがモジュールの使用を要求すると、プライベート コンテナー レジストリではなく、Azure Marketplace パブリック コンテナー レジストリからサービスが提供されます。

複数のプラットフォームを対象にしたり、タグを利用し、複数のバージョンのモジュール コンテナー イメージを提供したりできます。 タグとバージョン管理の詳細については、「[IoT Edge モジュールの技術アセットの準備](create-iot-edge-module-asset.md)」を参照してください。

### <a name="image-repository-details"></a>イメージ リポジトリの詳細

**[イメージ リポジトリの詳細]** タブに次の情報を入力します。

**[画像ソースの選択]** : **[Azure Container Registry]** オプションを選択します。

**[Azure サブスクリプション ID]** :リソースの使用状況が報告され、コンテナー イメージが含まれる Azure Container Registry に対してサービスが課金されるサブスクリプション ID を入力します。 この ID は Azure portal の[サブスクリプション ページ](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)にあります。

**[Azure リソース グループ名]** :Azure Container Registry とコンテナー イメージが含まれる [リソース グループ](../../azure-resource-manager/management/manage-resource-groups-portal.md)名を指定します。 リソース グループにはサブスクリプション ID (上記) でアクセスできます。 この名前は Azure portal の[リソース グループ](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) ページにあります。

**[Azure コンテナー レジストリ名]** :コンテナー イメージが含まれる [Azure Container Registry](../../container-registry/container-registry-intro.md) の名前を指定します。 このコンテナー レジストリは、前に指定した Azure リソース グループに存在する必要があります。 完全なログイン サーバー名ではなく、レジストリ名のみを指定します。 名前からは必ず **azurecr.io** を省略します。 レジストリ名は、Azure portal の[コンテナー レジストリ ページ](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries)にあります。

**[Azure Container Registry の管理者ユーザー名]** :コンテナー イメージが含まれる Azure Container Registry に関連付けられている [管理者ユーザー名](../../container-registry/container-registry-authentication.md#admin-account)を指定します。 このユーザー名とパスワードは、自社からレジストリにアクセスするために必要です。 管理者ユーザー名とパスワードを取得するには、Azure コマンド ライン インターフェイス (CLI) で **admin-enabled** プロパティを **[True]** に設定します。 任意で、Azure portal で **[管理者ユーザー]** を **[有効にする]** に設定できます。

:::image type="content" source="media/example-iot-update-container-registry.png" alt-text="[コンテナー レジストリの更新] ダイアログ ボックスの画像。":::

#### <a name="call-out-description"></a>コールアウトの説明

1. 管理者ユーザー

<br>**[Azure Container Registry のパスワード]** :コンテナー イメージが含まれる Azure Container Registry に関連付けられている管理者ユーザー名のパスワードを指定します。 会社でレジストリへのアクセスを確保するには、ユーザー名とパスワードが必要です。 **[コンテナー レジストリ]** 、 **[アクセス キー]** の順に移動するか、Azure CLI で [show コマンド](/cli/azure/acr/credential#az-acr-credential-show)を使用することで、Azure portal からパスワードを取得できます。

:::image type="content" source="media/example-iot-access-keys.png" alt-text="Azure portal のアクセス キー画面の画像。":::

#### <a name="call-out-descriptions"></a>コールアウトの説明

1. アクセス キー
2. ユーザー名
3. Password

**Azure Container Registry 内のリポジトリ名**。 イメージが含まれる Azure Container Registry リポジトリの名前を指定します。 レジストリにイメージをプッシュするとき、リポジトリの名前を指定します。 リポジトリの名前は、[[コンテナー レジストリ]](https://azure.microsoft.com/services/container-registry/) の **リポジトリ ページ** に移動すると見つかります。 詳細については、「[Azure Portal でコンテナー レジストリ リポジトリを表示する](../../container-registry/container-registry-repositories.md)」を参照してください。 設定した名前は変更できません。 アカウント内のオファー別に一意の名前を使用します。

> [!NOTE]
> Encrypted Azure Container Registry for Edge Module Certification はサポートしていません。 Azure Container Registry は暗号化を有効にせずに作成してください。

### <a name="image-tags-for-new-versions-of-your-offer"></a>新しいバージョンのオファーのイメージ タグ

更新プログラムを公開したとき、顧客は Azure Marketplace から更新プログラムを自動的に取得できなければなりません。 彼らが更新しない場合、特定のバージョンのイメージを維持できなければなりません。 これはイメージを更新するたびに新しいイメージ タグを追加することで行います。

**イメージ タグ**。 サポートされているすべてのプラットフォームで、最新版のイメージを指す **最新** のタグをこのフィールドに含める必要があります。 バージョン タグも含める必要があります (たとえば、xx.xx.xx から始まり、xx は数字です)。 複数のプラットフォームを対象とするには、顧客は[マニフェスト タグ](https://github.com/estesp/manifest-tool)を使用する必要があります。 マニフェスト タグで参照されるすべてのタグも、アップロードできるように追加する必要があります。 マニフェスト タグ (最新のタグを除く) はすべて X.Y- か X.Y.Z- で始める必要があります。X、Y、Z は整数です。 たとえば、最新のタグが 1.0.1-linux-x64、1.0.1-linux-arm32、1.0.1-windows-arm32 を指す場合、これら 6 つのタグをこのフィールドに追加する必要があります。 タグとバージョン管理の詳細については、「[IoT Edge モジュールの技術アセットの準備](create-iot-edge-module-asset.md)」を参照してください。

### <a name="default-deployment-settings-optional"></a>既定の展開設定 (省略可能)

IoT Edge モジュールをデプロイするための最も一般的な設定を定義します。 こうした既定設定によって IoT Edge モジュールをすぐに起動できるようにし、お客様のデプロイを最適化します。

**既定のルート**。 IoT Edge ハブは、モジュール、IoT ハブ、デバイス間の通信を管理します。 モジュールと IoT Hub の間でデータの入力と出力のルートを設定することにより、メッセージの処理や追加のコードの記述を行うための追加のサービスを必要とせず、自由な場所にメッセージを送信できます。 ルートは名前と値のペアを使用して作成されます。 最大 5 つの既定ルート名を定義できます。それぞれ最大 512 文字を使用できます。

ルート値には正しい[ルート構文](../../iot-edge/module-composition.md#declare-routes)を使用してください (通常、FROM/message/* INTO $upstream として定義されています)。 つまり、モジュールにより送信されるメッセージはすべて、IoT Hub に移動します。 モジュールを参照するには、既定のモジュール名を使用します。モジュール名は、スペースと特殊文字を除いた **オファー名** です。 未知の他のモジュールを参照するには、お客様がこの情報を更新する必要があることがわかるように <FROM_MODULE_NAME> 規約を使用します。 IoT Edge ルートの詳細については、「[ルートの宣言](../../iot-edge/module-composition.md#declare-routes)」を参照してください。

たとえば、モジュール ContosoModule が ContosoInput の入力をリッスンし、ContosoOutput の出力データをリッスンする場合、次の 2 つの既定のルートを定義するのが適切です。

- 名前 #1:ToContosoModule
- 値 #1:FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")
- 名前 #2:FromContosoModuleToCloud
- 値 #2:FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream

**既定のモジュール ツインの必要なプロパティ**。 モジュール ツインは IoT Hub の JSON ドキュメントであり、必要なプロパティなど、モジュール インスタンスの状態情報が格納されます。 モジュールの構成や状態を同期するために、必要なプロパティを報告されるプロパティと共に使用します。 ソリューション バックエンドにより必要なプロパティを設定でき、モジュールによりそれらを読み取れます。 モジュールでは、必要なプロパティに対する変更を知らせる通知を受け取ることもできます。 必要なプロパティは最大 5 つの名前と値のペアで作成され、各既定値は 512 文字未満にする必要があります。 最大 5 つの名前と値のツインの目的のプロパティを定義できます。 ツインの目的のプロパティの値は、有効な JSON であり、エスケープせず、配列を含まず、最大ネスト階層は 4 レベルでなければなりません。 既定値に必要なパラメーターでは意味をなさないシナリオでは (たとえば、顧客のサーバーの IP アドレスなど)、既定値としてパラメーターを追加できます。 ツインの必要なプロパティに関する詳細については、「[必要なプロパティの定義または更新](../../iot-edge/module-composition.md#define-or-update-desired-properties)」をご覧ください。

たとえば、モジュールがツインの目的のプロパティによって動的に構成可能なリフレッシュ レートをサポートする場合、以下の既定ツインの目的のプロパティを定義するのが適切です。

- 名前 #1:RefreshRate
- 値 #1:60

**既定の環境変数**。 環境変数は、構成プロセスを容易にする補足情報をモジュールに提供します。 環境変数は名前と値のペアを使用して作成されます。 既定の環境変数の名前と値はそれぞれ、512 文字未満にする必要があります。最大 5 つを定義できます。 既定値に必要なパラメーターでは意味をなさないとき (たとえば、顧客のサーバーの IP アドレスなど)、既定値としてパラメーターを追加できます。

たとえば、モジュールが開始される前に使用条件に同意する必要がある場合は、次の環境変数を定義できます。

- 名前 #1:ACCEPT_EULA
- 値 #1:Y

**既定のコンテナー作成オプション**。 コンテナー作成オプションでは、IoT Edge モジュールの Docker コンテナーの作成が指示されます。 IoT Edge では、Docker エンジン API のコンテナー作成オプションがサポートされています。 すべてのオプションは「[List containers](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList)」(コンテナーの一覧) をご覧ください。 作成オプション フィールドは有効な JSON、エスケープなし、512 文字未満にする必要があります。

たとえば、モジュールでポートをバインドする必要がある場合、次の作成オプションを定義します。

"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}

## <a name="review-and-publish"></a>レビューと公開

オファーの必須セクションをすべて完了したら、レビューと公開のためにそれを送信することができます。

ポータルの右上隅で、 **[レビューと公開]** を選択します。

[レビュー] ページで、公開状態を確認できます。

- オファーの各セクションの完了状態を確認する。 オファーの全セクションに完了の印が付くまで公開できません。
    - **[未開始]** - セクションは着手されておらず、完了する必要があります。
    - **未完了** - 修正が必要なエラーがセクションにあり、追加情報を入力する必要があります。 ガイダンスについては、このドキュメントの前のセクションを参照してください。
    - **完了** - セクションには必要なすべてのデータがあり、エラーはありません。 オファーを送信するには、オファーのすべてのセクションが完了している必要があります。
- オファーが確実に正しくテストされるよう、認定チームにはテストの指示を提供します。 また、オファーの理解に役立つ補足事項を提供します。

オファーを送信して公開するには、 **[公開]** を選択します。

オファーのプレビュー バージョンが確認して承認できるようになったら、その旨をお知らせするメールを Microsoft からお送りします。 オファーを一般に公開するには、パートナー センターにアクセスし、 **[一般公開する]** を選択します。

## <a name="next-steps"></a>次のステップ

- [商業マーケットプレースで既存のオファーを更新する](update-existing-offer.md)
