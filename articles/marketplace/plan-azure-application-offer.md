---
title: コマーシャル マーケットプレース用に Azure アプリケーション オファーを計画する
description: Microsoft パートナー センターのコマーシャル マーケットプレース ポータルを使用して、Azure Marketplace で、またはクラウド ソリューション プロバイダー (CSP) プログラムを通してリスト登録または販売を行うために、新しい Azure アプリケーション オファーを計画する方法について説明します。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: f98f128e56810e3dd710ce454c8dc34b333690d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879294"
---
# <a name="plan-an-azure-application-offer-for-the-commercial-marketplace"></a>コマーシャル マーケットプレース用に Azure アプリケーション オファーを計画する

この記事では、Azure アプリケーション オファーを Microsoft コマーシャル マーケットプレースに公開するためのさまざまなオプションと要件について説明します。

## <a name="before-you-begin"></a>開始する前に

Azure アプリケーション オファーを設計、構築、およびテストするには、Azure プラットフォームと、そのオファーの構築に使用されるテクノロジの両方の技術的な知識が必要です。 エンジニアリング チームには､Microsoft の次の技術に関する知識も必要です｡

- [Azure のサービス](https://azure.microsoft.com/services/)に関する基本知識。
- [Azure アプリケーションとそのアーキテクチャを設計する](https://azure.microsoft.com/solutions/architecture/)方法。
- [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)、[Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)、[Azure ネットワーク](https://azure.microsoft.com/services/?filter=networking#networking)に関する実用的な知識。
- [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) に関する実用的な知識。
- [JSON](https://www.json.org/) に関する実用的な知識。

### <a name="technical-documentation-and-resources"></a>技術ドキュメントとリソース

コマーシャル マーケットプレース用に Azure アプリケーション オファーを計画する場合は、次のリソースを確認してください。

- [Azure Resource Manager テンプレートについての理解](../azure-resource-manager/templates/template-syntax.md)
- クイック スタート:
    - [Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)
    - [Azure テンプレートのベスト プラクティス ガイド](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    - [アプリケーション定義の発行](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
    - [サービス カタログ アプリのデプロイ](../azure-resource-manager/managed-applications/deploy-service-catalog-quickstart.md)
- チュートリアル:
    - [定義ファイルの作成](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
- サンプル:
    - [Azure CLI](../azure-resource-manager/managed-applications/cli-samples.md)
    - [Azure PowerShell](../azure-resource-manager/managed-applications/powershell-samples.md)
    - [マネージド アプリケーション ソリューション](../azure-resource-manager/managed-applications/sample-projects.md)

「[Azure Marketplace 向けのソリューション テンプレートとマネージド アプリケーションの作成](https://channel9.msdn.com/Events/Build/2018/BRK3603)」ビデオでは、Azure アプリケーション オファーの種類について包括的に紹介しています。

- 使用できるオファーの種類
- 必要な技術資産
- Azure Resource Manager テンプレートの作成方法
- アプリ UI の開発とテスト
- アプリ オファーを発行する方法
- アプリケーション レビュー プロセス

### <a name="suggested-tools"></a>推奨ツール

Azure アプリケーションを管理するためのスクリプト環境として、次のいずれか一方または両方を選択します。

- [Azure PowerShell](/powershell/azure/)
- [Azure CLI](/cli/azure)

開発環境には次にツールを加えることを推奨します｡

- [Azure 記憶域エクスプローラー](../vs-azure-tools-storage-manage-with-storage-explorer.md)
- [Visual Studio Code](https://code.visualstudio.com/) と次の拡張機能:
    - 拡張機能: [Azure リソース マネージャー ツール](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    - 拡張機能: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    - 拡張機能: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

使用できるツールについては、[Azure 開発者ツール](https://azure.microsoft.com/tools/)に関するページをご覧ください。 Visual Studio を使用している場合は、[Visual Studio Marketplace](https://marketplace.visualstudio.com/) を参照してください。

## <a name="listing-options"></a>リスト オプション

オファーを公開すると、オファーのリスト オプションが、オファーの登録情報ページの左上隅にボタンとして表示されます。 たとえば、次のスクリーンショットでは、Azure Marketplace に、 _[今すぐ入手]_ ボタンのあるオファー登録情報ページが表示されています。 体験版の提供を選択した場合は、 _[体験版]_ ボタンも表示されます。

:::image type="content" source="media/create-new-azure-app-offer/azure-app-listing-page.png" alt-text="Azure Marketplace の登録情報ページの画像。":::

## <a name="test-drive"></a>体験版

顧客が購入前にオファーを試すことができるように、Azure アプリケーション オファーの体験版を有効にすることを選択できます。 体験版の詳細については、「[体験版とは](what-is-test-drive.md)」を参照してください。 さまざまな種類の体験版の構成については、「[体験版の技術的な構成](test-drive-technical-configuration.md)」を参照してください。

また、[体験版のベスト プラクティス](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)を参照し、[体験版の概要に関する PDF](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) をダウンロードすることもできます (ポップアップ ブロックがオフになっていることを確認してください)。

> [!NOTE]
> 流し読みする場合でも、ユーザーが注目する必要がある情報です。すべての Azure アプリケーションは Azure Resource Manager テンプレートを使用して実装されるため、Azure アプリケーションで使用可能な体験版の種類は [Azure Resource Manager ベースの体験版](azure-resource-manager-test-drive.md)のみです。

## <a name="customer-leads"></a>潜在顧客

オファーをカスタマー リレーションシップ マネジメント (CRM) システムに接続して、顧客情報を収集する必要があります。 顧客は、自分の情報を共有する許可を求められます。 これらの顧客の詳細は、オファーの名前と ID のほか、顧客がオファーを見つけたオンライン ストアと共に、お客様が構成した CRM システムに送信されます。 コマーシャル マーケットプレースでは、Azure テーブルを使用したり、Power Automate を使用して HTTPS エンドポイントを構成したりするためのオプションと共に、さまざまな CRM システムがサポートされています。

CRM の接続は、オファーの作成時または作成後にいつでも追加または変更できます。 詳細なガイダンスについては、「[コマーシャル マーケットプレース オファーからの潜在顧客](partner-center-portal/commercial-marketplace-get-customer-leads.md)」を参照してください。

## <a name="categories-and-subcategories"></a>カテゴリとサブカテゴリ

少なくとも 1 つで最大 2 つのカテゴリを選択して、オファーを適切なコマーシャル マーケットプレース検索領域にグループ化できます。 プライマリおよびセカンダリ カテゴリについてそれぞれ最大 2 つのサブカテゴリを選択できます。 カテゴリとサブカテゴリの完全な一覧については、「[オファーの掲載のベスト プラクティス](gtm-offer-listing-best-practices.md#categories)」を参照してください。

## <a name="legal-contracts"></a>法的契約

顧客の調達プロセスを簡素化し、ソフトウェア ベンダーの法務の複雑さを軽減するため、Microsoft では、コマーシャル マーケットプレースでオファーに使用できる標準契約を用意しています。 標準契約の下でソフトウェアを提供すると、顧客はそれを読んで一度承諾するだけで済み、提供元は独自の使用条件を作成する必要はありません。

標準契約の使用を選択する場合、標準契約に、ユニバーサル修正条件を追加するオプションと、最大 10 個のカスタム修正を追加するオプションがあります。 また、標準契約ではなく、独自の使用条件を使用することもできます。 これらの詳細は、 **[プロパティ]** ページで管理します。 詳細については、「[Microsoft コマーシャル マーケットプレースの標準契約](standard-contract.md)」を参照してください。

> [!NOTE]
> コマーシャル マーケットプレースの標準契約を使用してオファーを公開した後に、独自のカスタム使用条件を使用することはできません。 これは、"または" のシナリオです。 ソリューションは、標準契約または独自の使用条件のいずれかの下で提供します。 標準契約の条件を変更する必要がある場合は、Standard Contract Amendments (標準契約の修正) を使用して変更することができます。

## <a name="offer-listing-details"></a>オファー登録情報の詳細

パートナー センターで新しい Azure アプリケーション オファーを作成する場合、オファーの登録情報ページで、テキスト、画像、オプションのビデオ、その他の詳細を入力します。 次の例に示すように、これは、顧客が Azure Marketplace でオファー登録情報を見つけたときに表示される内容です。

:::image type="content" source="media/create-new-azure-app-offer/example-azure-marketplace-app.png" alt-text="Azure Marketplace でこのオファーがどのように表示されるかを示しています。":::

#### <a name="call-out-descriptions"></a>コールアウトの説明

1. ロゴ
2. Categories
3. サポートのアドレス (リンク)
4. 使用条件
5. プライバシー ポリシーのアドレス (リンク)
6. プラン名
7. まとめ
8. 説明
9. スクリーンショット/ビデオ

Azure portal でのオファー情報の表示例を次のスクリーンショットに示します。

[![Azure portal でこのオファーがどのように表示されるかを示しています。](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png)](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png#lightbox)

#### <a name="call-out-descriptions"></a>コールアウトの説明

1. タイトル
2. 説明
3. 便利なリンク
4. Screenshots (スクリーンショット)

> [!NOTE]
> オファーの説明が「このアプリケーションは、[英語以外の言語] でのみ利用可能です」という文言で始まっている場合、オファー登録情報は英語である必要はありません。

オファーをより簡単に作成できるようにするには、これらの項目の一部をあらかじめ準備しておきます。 次の項目は、特に記載がない限り必須です。

- **Name**:この名前は、コマーシャル マーケットプレースでオファー登録情報のタイトルとして表示されます。 この名前は商標登録されている場合があります。 これは、絵文字 (商標および著作権マークの場合を除く) を含むことができず、50 文字以下にする必要があります。
- **検索結果の概要**: オファーの目的または機能を、100 文字以内のプレーンテキストで改行のない単一の文として記述します。 この概要は、コマーシャル マーケットプレースの登録情報の検索結果で使用されます。
- **簡単な説明**: 最大 256 文字のプレーンテキストを指定します。 この概要は、オファーの詳細ページに表示されます。
- **説明**:この説明は、Azure Marketplace の登録情報の概要に表示されます。 価値提案、主なメリット、対象となるユーザー ベース、カテゴリまたは業界との関連性、アプリ内の購入機会、オファーが対応する顧客のニーズや問題、必要な情報開示、詳細情報へのリンクを含めることを検討してください。

    このテキスト ボックスには、説明をより魅力的にするために使用できるリッチ テキスト エディター コントロールが用意されています。 また、HTML タグを使用して説明の書式を設定することもできます。 このボックスには、HTML マークアップとスペースを含む最大 3,000 文字のテキストを入力できます。 その他のヒントについては、「[人の心をつかむアプリの説明を書く](/windows/uwp/publish/write-a-great-app-description)」および「[コマーシャル マーケットプレースのオファー説明でサポートされている HTML タグ](supported-html-tags.md)」を参照してください。

- **検索キーワード** (省略可能): オンライン ストアで顧客がオファーを見つけることができるように使用する検索キーワードを、最大 3 つ指定します。 最良の結果を得るために、これらのキーワードを説明にも使用します。 オファーの **名前** と **説明** を含める必要はありません。 そのテキストは検索に自動的に含まれます。
- **プライバシー ポリシーのリンク**: 会社のプライバシー ポリシーの URL。 有効なプライバシー ポリシーを提供する必要があり、アプリがプライバシーに関する法律および規制に準拠していることを保証する責任があります。
- **役に立つリンク** (省略可能): オファーのユーザーにさまざまなリソースへのリンクを提供できます。 たとえば、フォーラム、FAQ、リリース ノートなどがあります。
- **連絡先情報**:組織の次の連絡先を指定する必要があります。
  - **サポートの連絡先**: 顧客がチケットを開くときに使用する、Microsoft パートナーの名前、電話番号、電子メール アドレスを指定します。 サポート Web サイトの URL も含める必要があります。
  - **エンジニアリングの連絡先**: オファーに問題がある場合に直接使用する、Microsoft の名前、電話番号、電子メール アドレスを指定します。 この連絡先情報は、コマーシャル マーケットプレースには表示されません。
  - **CSP プログラムの連絡先** (省略可能): クラウド ソリューション プロバイダー (CSP) プログラムにオプトインする場合は、名前、電話番号、電子メール アドレスを指定します。これにより、これらのパートナーが質問に回答できるようになります。 マーケティング資料の URL も含めることができます。
- **メディア - ロゴ**: **大** サイズのロゴに PNG ファイルを指定します。 パートナー センターでは、これを使用して、**小** および **中** サイズのロゴを作成します。 必要に応じて、別の画像に置き換えることもできます。
  - 大 (216 x 216 から 350 x 350 px、必須)
  - 中 (90 x 90 px、省略可能)
  - 小 (48 x 48 px、省略可能)

  これらのロゴは、オンライン ストアのさまざまな場所で使用されます。
  - 小のロゴは、Azure Marketplace での検索結果に表示されます。
  - 中のロゴは、Microsoft Azure で新しいリソースを作成すると表示されます。
  - 大のロゴは、Azure Marketplace のオファーのリスト登録ページに表示されます。

  お客様のロゴについては、これらのガイドラインに従ってください。

  - Azure の設計には簡単なカラー パレットがあります。 ロゴのプライマリ カラーとセカンダリ カラーの数は制限してください。
  - ポータルのテーマの色は白黒です。 この色を、ロゴの背景色として使用しないでください。 ポータルでロゴが目立つ色を使用します。 背景色としてはシンプルなプライマリ カラーをお勧めします。
  - 透明な背景を使用している場合は、ロゴおよびテキストが白、黒、または青ではないことを確認します。
  - ロゴのルック アンド フィールはフラットにして、ロゴやバックグラウンドではグラデーションを避けてください。 ロゴにはテキストを使用しないでください (会社またはブランドの名前であっても)。 画像がぼやけていると、送信が拒否されます。
  - ロゴが伸縮されていないことを確認してください。

- **メディア - スクリーンショット** (省略可能): オファーがどのように機能するかを示すスクリーンショットを追加することをお勧めします。 オファーがどのように機能するかを示す、次の要件を満たすスクリーンショットを最大 5 つ追加できます。
  - 1280 x 720 ピクセル
  - .png ファイル
  - キャプションを含める必要があります
- **メディア – ビデオ** (省略可能): オファーをデモンストレーションする、次の要件を満たすビデオを最大 5 つ追加できます。
  - 名前
  - URL:YouTube または Vimeo のみでホストされる必要があります。
  - サムネイル: 1280 x 720 .png ファイル

> [!NOTE]
> コマーシャル マーケットプレースに公開するには、オファーが一般的な[コマーシャル マーケットプレースの認定ポリシー](/legal/marketplace/certification-policies#100-general.md)を満たしている必要があります。

## <a name="preview-audience"></a>プレビュー対象ユーザー

プレビュー対象ユーザーは、オンライン ストアで公開される前にオファーにアクセスして、公開前にエンドツーエンドの機能をテストできます。

> [!NOTE]
> プレビュー対象ユーザーはプライベート プランとは異なります。 プライベート プランは、選択された特定の対象ユーザーのみが利用できます。 これにより、カスタム プランについて、特定の顧客と協議できます。

Azure サブスクリプション ID GUID を使用してプレビュー対象ユーザーを定義し、それぞれに、省略できる説明を付けます。 いずれのフィールドも顧客は表示できません。

## <a name="technical-configuration"></a>技術的な構成

[Marketplace の測定サービス API](partner-center-portal/marketplace-metering-service-apis.md) を使用して測定イベントを生成するマネージド アプリケーションの場合は、使用状況測定イベントを生成するときにサービスで使用される ID を指定する必要があります。

[Batch 使用状況イベント](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-batch-usage-event)を使用する場合は、この構成が必要です。 [使用イベント](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-single-usage-event)を送信する場合は、[インスタンス メタデータ サービス](../active-directory/managed-identities-azure-resources/overview.md)を使用して、[JSON Web トークン (JWT) ベアラー トークン](partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token)を取得することもできます。

- **Azure Active Directory テナント ID** (必須): Azure portal 内では、Microsoft の 2 つのサービス間の接続が認証済みの通信の背後で行われることを Microsoft が検証できるように、[Azure Active Directory (AD) アプリを作成する](../active-directory/develop/howto-create-service-principal-portal.md)必要があります。 Azure Active Directory (Azure AD) アプリの[テナント ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) を見つけるには、Azure Active Directory の [[アプリの登録]](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) ブレードに移動します。 **[表示名]** 列で、アプリを選択します。 次に、 **[プロパティ]** を探してから、**ディレクトリ (テナント) ID** (たとえば `50c464d3-4930-494c-963c-1e951d15360e`) を見つけます。
- **Azure Active Directory アプリケーション ID** (必須): また、自分の [アプリケーション ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) と認証キーも必要です。 アプリケーション ID を見つけるには、Azure Active Directory の [[アプリの登録]](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) ブレードに移動します。 **[表示名]** 列でアプリを選択し、次に **アプリケーション (クライアント) ID** (たとえば `50c464d3-4930-494c-963c-1e951d15360e`) を探します。 認証キーを見つけるには、 **[設定]** に移動して **[キー]** を選択します。 説明と期間を入力する必要があります。その後、数値が提供されます。

> [!NOTE]
> Azure アプリケーション ID は公開元 ID に関連付けられ、この公開元アカウント内でのみ再利用できます。

## <a name="additional-sales-opportunities"></a>その他の営業案件

Microsoft がサポートするマーケティングおよびセールス チャネルのオプトインを選択できます。 パートナー センターでオファーを作成すると、プロセスの終盤で、次の 2 つのタブが表示されます。

- **CSP を通して再販する**: このオプションを使用して、Microsoft クラウド ソリューションプロバイダー (CSP) パートナーがバンドルされたオファーの一部としてソリューションを再販できるようにします。 詳細については、「[クラウド ソリューション プロバイダー プログラム](./cloud-solution-providers.md)」を参照してください。
- **Microsoft と共同販売する**: このオプションを使用すると、Microsoft セールス チームは、顧客のニーズを評価するときに、IP の共同販売対象ソリューションを検討できます。 評価のためにオファーを準備する方法の詳細については、「[コマーシャル マーケットプレースでの共同販売オプション](commercial-marketplace-co-sell.md)」を参照してください。 IP 共同販売の要件の詳細については、[共同販売の状態の要件](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status)に関する記事を参照してください。 Microsoft CSP パートナー チャネルを通じたプランのマーケティングの詳細については、「[クラウド ソリューション プロバイダー](cloud-solution-providers.md)」を参照してください。

詳細については、[Azure Marketplace でのビジネスの拡大](https://azuremarketplace.microsoft.com/sell)に関するページを参照してください。

## <a name="plans"></a>プラン

Azure アプリケーション オファーには、1 つ以上のプランが必要です。 ソリューションのスコープと制限、および関連する価格をプランで定義します。 オファーに対して複数のプランを作成し、さまざまな技術および価格オプションを顧客に提供することができます。

価格モデル、プライベート プランなど、プランに関する一般的なガイダンスについては、「[コマーシャル マーケットプレース オファーのプランと価格](plans-pricing.md)」を参照してください。 以下のセクションでは、Azure アプリケーション プランに固有の追加情報について説明します。

### <a name="types-of-plans"></a>プランの種類

Azure アプリケーション プランには、"_ソリューション テンプレート_" と "_マネージド アプリケーション_" の 2 種類があります。 どちらのプランの種類でも、単一の仮想マシン (VM) を超えるソリューションのデプロイと構成の自動化がサポートされています。 IaaS ソリューションなどの複雑なソリューションを提供するための VM、ネットワーク、ストレージ リソースなどの複数のリソースを提供するプロセスを自動化できます。 どちらのプランの種類でも、VM を含むがそれに限定されない、さまざまな種類の Azure リソースを使用できます。

- **ソリューション テンプレート** プランは、コマーシャル マーケットプレースでソリューションを発行するための主な手段の 1 つです。 ソリューション テンプレート プランは、コマーシャル マーケットプレースで取引可能ではありませんが、コマーシャル マーケットプレースを通じて課金される有料 VM オファーをデプロイする目的に使用できます。 顧客がソリューションを管理し、トランザクションが別のプランで課金される場合は、ソリューション テンプレート プランの種類を使用します。 ソリューション テンプレートの作成について詳しくは、「[Azure Resource Manager とは](../azure-resource-manager/management/overview.md)」を参照してください。
- **マネージド アプリケーション** プランを使用すると、顧客向けのフル マネージドのターンキー アプリケーションを簡単に構築して提供できます。 機能はソリューション テンプレート プランと同じで、いくつかの重要な違いがあります。
    - リソースはリソース グループにデプロイされ、アプリの公開元によって管理されます。 リソース グループはユーザーのサブスクリプション内に存在しますが、そのリソース グループにアクセスできるのは、発行元のテナントの ID です。 
    - 発行元は、ソリューショ ンの継続的なサポートのためのコストを指定します。トランザクションはコマーシャル マーケットプレースを通じてサポートされます。
 
    ソリューションがパートナーによって管理されることをご自身または顧客が必要としている場合、またはサブスクリプションベースのソリューションをデプロイする場合は、マネージド アプリケーション プランの種類を使用します。 マネージド アプリケーションの利点と種類について詳しくは、「[Azure マネージド アプリケーションの概要](../azure-resource-manager/managed-applications/overview.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- ソリューション テンプレートを計画するには、「[Azure アプリケーション オファーのソリューション テンプレートを計画する](plan-azure-app-solution-template.md)」を参照してください。
- Azure マネージド アプリケーションを計画するには、「[Azure アプリケーション オファーの Azure マネージド アプリケーションを計画する](plan-azure-app-managed-app.md)」を参照してください。