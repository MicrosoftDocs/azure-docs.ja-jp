---
title: 商業マーケットプレースで新しい Dynamics 365 Business Central オファーを作成する
description: Microsoft パートナー センターの商業マーケットプレース ポータルを使用して、Azure Marketplace、AppSource、クラウド ソリューション プロバイダー (CSP) プログラムでリスト登録または販売を行うために新しい Dynamics 365 Business Central オファーを作成する方法。
author: JnHs
manager: evansma
ms.author: jenhayes
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e127d937dfaeb8afaad6b9a95383f0008f5ea898
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2019
ms.locfileid: "70033278"
---
# <a name="create-a-new-dynamics-365-business-central-offer"></a>新しい Dynamics 365 Business Central オファーの作成

このトピックでは、新しい Dynamics 365 Business Central オファーの作成方法について説明します。 [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) はエンタープライズ リソース プランニング (ERP) システムであり、財務、業務、サプライ チェーン、CRM、プロジェクト管理と電子商取引など、さまざまなビジネス プロセスを処理します。 Premium パッケージでは、クラシック デプロイ モデルと製造もサポートされています。 Dynamics 365 Business Central のすべてのオファーは、Microsoft の認定プロセスを通過する必要があります。

Dynamics 365 Business Central オファーの作成を開始するには、確実に、最初に[パートナー センター アカウントを作成](./create-account.md)し、 **[概要]** ページを選択した状態で[商業マーケットプレース ダッシュボード](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)を開いてください。

![パートナー センターの商業マーケットプレース ダッシュボード](./media/new-offer-overview.png)

## <a name="create-a-new-offer"></a>新しいオファーを作成する

**[+ 新しいオファー]** ボタンを選択し、 **[Dynamics 365 Business Central]** メニュー項目を選択します。 **[新しいオファー]** ダイアログ ボックスが表示されます。

### <a name="offer-id-and-alias"></a>オファーの ID と別名

- **プラン ID**: 自分のアカウントでの各オファーの一意識別子。 この ID は、マーケットプレース オファーの URL アドレスと Azure Resource Manager テンプレート (該当する場合) で顧客に表示されます。 オファー ID には、小文字の英数字 (ハイフンとアンダースコアを含みますが、空白は含まれません) を 50 文字以下で指定する必要があり、 **[作成]** を選択した後で変更することはできません。  たとえば、ここに「*test-offer-1*」と入力すると、オファーの URL は `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` になります。

- **オファーの別名**:パートナー センター内でオファーを参照するために使用される名前。 この名前はマーケットプレースでは使用されず、顧客に表示されるオファー名やその他の値とは異なります。 **[作成]** の選択後にこの値を変更することはできません。

**オファー ID** と **オファーの別名**を入力したら、 **[作成]** を選択します。 これで、オファーのさまざまな部分をすべて操作できるようになります。

## <a name="offer-setup"></a>オファーのセットアップ

**[オファーのセットアップ]** ページでは、次の情報を求められます。 これらのフィールドに入力したら、忘れずに **[保存]** を選択します。

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>潜在顧客がこの登録オファーとやり取りする方法について選択してください。

このオファーに使用するオプションを選択します。

#### <a name="get-it-now-free"></a>Get it now (今すぐ入手する) (無料)

顧客に対して、プランを無料で使用できるものとしてリスト登録します。そのために、アプリにアクセスできる有効な (*http* または *https* で始まる) URL を指定します。  次に例を示します。`https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>Free trial (無料試用版) (一覧)

顧客が試用版を入手できる有効な (*http* または *https* で始まる) URL を示すことにより、無料試用版へのリンクを使用して顧客にオファーを一覧表示します。  (例: `https://contoso.com/trial/my-app`)。 オファー登録情報の無料試用版がご利用のサービスによって作成、管理、および構成され、Microsoft によって管理されるサブスクリプションはありません。

> [!NOTE]
> 試用版リンクからアプリケーションが受信するトークンは、そのアプリのアカウント作成を自動化するためのユーザー情報を Azure Active Directory (Azure AD) を介して取得するためだけに使用できます。 このトークンを使用した認証には、Microsoft アカウントはサポートされません。

#### <a name="contact-me"></a>[Contact me (お問い合わせ)]

顧客関係管理 (CRM) システムに接続して、顧客の連絡先情報を収集します。 顧客は、自分の情報を共有する許可を求められます。 これらの顧客の詳細は、オファーの名前と ID のほか、顧客がオファーを見つけたマーケットプレース ソースと一緒に、お客様が構成した CRM システムに送信されます。 CRM の構成の詳細については、「[リード管理の接続](#connect-lead-management)」を参照してください。 

### <a name="test-drive"></a>体験版

体験版は、購入前に試用するオプションを与えることで潜在顧客へのオファーを披露し、その結果、会話が増加し、見込みの高いリードが生成される優れた方法です。 [体験版について詳しくご確認ください。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

体験版を有効にするには、 **[体験版を有効にする]** チェックボックスをオンにします。 その後、顧客が一定期間オファーを試すことができるように、「[体験版の技術的な構成](#test-drive-technical-configuration)」に従って、デモ環境を構成する必要があります。 

#### <a name="type-of-test-drive"></a>体験版の種類

次のオプションから選択します。

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : お客様のソリューションを構成しているすべての Azure リソースが含まれたデプロイ テンプレート。 このシナリオに適合するのは、Azure リソースしか使用されていない製品です。
- **[Dynamics 365 for Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : Microsoft が Business Central エンタープライズ リソース プランニング システム (財務、運用、サプライ チェーン、CRM など) 用の体験版サービスをホストおよび維持します。ここには、プロビジョニングとデプロイも含まれます。  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : Microsoft が Customer Engagement システム (販売、サービス、プロジェクト サービス、フィールド サービスなど) 用の体験版サービスをホストおよび維持します。ここには、プロビジョニングとデプロイも含まれます。  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : Microsoft が Finance and Operations エンタープライズ リソース プランニング システム (財務、運用、製造、サプライ チェーンなど) 用の体験版サービスをホストおよび維持します。ここには、プロビジョニングとデプロイも含まれます。 
- **[ロジック アプリ](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : あらゆる複雑なソリューション アーキテクチャに対応するデプロイ テンプレート。 すべてのカスタム製品には、この種類の体験版を使用する必要があります。
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : カスタムビルドされたダッシュボードへの埋め込みリンク。 インタラクティブな Power BI の視覚化のデモンストレーションを行う製品には、この種類の体験版を使用する必要があります。 ここで必要なことは、埋め込み Power BI の URL をアップロードすることだけです。

#### <a name="additional-test-drive-resources"></a>体験版に関するその他のリソース

- [体験版の技術的なベスト プラクティス](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [体験版のマーケティングのベスト プラクティス](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [1 ページにまとめた体験版の概要](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>リード管理の接続

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

詳しくは、[リード管理の概要](./commercial-marketplace-get-customer-leads.md)に関する記事をご覧ください。

次のセクションに進む前に、必ず**保存**してください。

## <a name="properties"></a>properties

**[プロパティ]** ページでは、マーケットプレース上でのオファーのグループ分けに使用するカテゴリと業界、アプリのバージョン、オファーがサポートされる法的契約を定義できます。 このフィールドに入力した後、 **[保存]** を選択します。

### <a name="category"></a>Category

オファーを適切なマーケットプレース検索区分にグループ分けするために使用されるカテゴリを、最小で 1 つ、最大で 3 つ選択します。 これらのカテゴリにオファーがどのように対応しているかを、オファーの説明に含めるようにします。 

### <a name="industry"></a>業界

必要に応じて最大 2 つの業界を選択して、マーケットプレースでオファーを分類できます。 オファーが業界に固有でない場合は、このセクションは空白のままにします。 選択した業界にオファーがどのように対応しているかをオファーの説明に含めるようにします。 

### <a name="app-version"></a>アプリのバージョン

オファーのバージョン番号を入力します。 顧客にはオファーの詳細ページにこのバージョンが表示されます。

### <a name="standard-contract"></a>標準契約

顧客の調達プロセスを簡略化し、ソフトウェア ベンダーの法的な複雑さを軽減するために、Microsoft では、マーケットプレースでの取引の促進に役立つ標準契約テンプレートを用意しています。

カスタムの使用条件を作成するのではなく、標準契約の下でソフトウェアを提供することを選択できます。顧客は、それを入念に調べて一度承認するだけで済みます。

標準契約はこちら (https://go.microsoft.com/fwlink/?linkid=2041178 ) で見つけることができます。

標準契約を使用するには、 **[標準契約を使用しますか?]** チェックボックスをオンにします。

#### <a name="terms-of-use"></a>使用条件

**[標準契約を使用しますか?]** チェックボックスをオンにしない場合は、 **[使用条件]** フィールドに独自の法律条項を入力する必要があります。 最大 10,000 文字のテキストを入力するか、使用条件にもっと長い説明が必要な場合は、追加のライセンス条項が記載された場所への URL を入力します。 顧客は、アプリを試す前にこれらの条件を承諾する必要があります。

## <a name="offer-listing"></a>オファーのリスト登録

オファー登録情報ページに、オファーが登録される言語が表示されます。 現時点では、使用可能なオプションは **[英語 (米国)]** のみです。

言語/市場ごとにマーケットプレースの詳細 (オファーの名前、説明、画像など) を定義する必要があります。 この情報を提供する言語/市場名を選択します。

> [!NOTE]
> オファー登録情報のコンテンツ (説明、ドキュメント、スクリーンショット、使用条件など) は、オファーの説明が "このアプリケーションは、[英語以外の言語] でのみ利用可能です。" という文言で始まっていれば英語である必要はありません。 また、オファー登録情報のコンテンツで使用されている言語以外の言語でコンテンツを提供するための*役に立つリンクの URL* を提供することもできます。

### <a name="name"></a>名前

ここで入力する名前は、オファー登録情報のタイトルとして顧客に表示されます。 このフィールドには、オファーの作成時に **[オファーの別名]** に入力したテキストが事前に設定されていますが、この値は変更できます。 この名前は商標の場合もあります (商標または著作権マークを含めることもできます)。 名前は 50 文字以下にする必要があります。絵文字を含めることはできません。

### <a name="short-description"></a>簡単な説明

マーケットプレースの検索結果で使用できる、お客様のオファーの簡単な説明を入力します (最大 100 文字)。

### <a name="description"></a>説明

オファーの詳しい説明 (最大 3,000 文字) を入力します。 この説明は、マーケットプレースの登録情報の概要で顧客に表示されます。 オファーの価値提案、主なメリット、カテゴリまたは業界との関連性、アプリ内の購入機会、必要な情報開示を含めます。 

説明を記述するためのヒントを次に示します。  

- 説明の先頭の数文で、オファーの価値提案を明確に説明します。 価値提案には次の項目を含めます。
  - 製品の説明
  - 製品から利益を得られるユーザーの種類
  - 製品が対応する顧客のニーズや問題
- 先頭の数文は、検索エンジンの結果に表示される可能性があることを留意してください。  
- 特徴や機能に頼って製品を販売しようとせずに、 提供する価値に焦点を当ててください。  
- できるだけ業界固有の語彙や利益に基づく表現を使用します。 
- HTML タグを使用して、説明を書式設定し、より魅力的なものにすることを検討してください。

### <a name="search-keywords"></a>キーワード検索

オファーを顧客がマーケットプレースで検索できるように、必要に応じて最大 3 つの検索キーワードを入力することができます。 最良の結果を得るには、これらのキーワードを説明にも使用するようにします。

### <a name="products-your-app-works-with"></a>アプリが対応する製品

アプリが特定の製品で動作することを顧客に知らせる場合は、ここに製品名を 3 つまで入力します。

### <a name="support-urls"></a>サポート URL

このセクションには、顧客がオファーの詳細を理解するのに役立つリンクを入力できます。

#### <a name="help-link"></a>ヘルプ リンク

顧客がオファーの詳細について確認できる URL を入力します。

#### <a name="privacy-policy-url"></a>[プライバシー ポリシーの URL]

自分の組織のプライバシー ポリシーへの URL を入力します。 プライバシーに関する法律および規制にアプリが準拠していることを保証し、有効なプライバシー ポリシーを提供する責任があります。

### <a name="contacts"></a>連絡先

このセクションでは、**サポートの連絡先**と**エンジニアリングの連絡先**の名前、メール、電話番号を入力する必要があります。 この情報は顧客には表示されませんが、Microsoft で利用できるようになり、CSP パートナーに提供される場合もあります。

**[サポートの連絡先]** セクションでは、CSP パートナーがオファーのサポートを確認できる**サポート URL** も指定する必要があります。

### <a name="supporting-documents"></a>サポート ドキュメント

ここには、関連するマーケティング ドキュメント (ホワイト ペーパー、パンフレット、チェックリスト、プレゼンテーションなど) を 1 つ以上 (3 つまで) 入力します。 これらのドキュメントは、.pdf 形式である必要があります。

### <a name="marketplace-images"></a>マーケットプレースの画像

このセクションでは、顧客にオファーを表示するときに使用されるロゴや画像を指定できます。 画像はすべて .png 形式である必要があります。

#### <a name="store-logos"></a>ストア ロゴ

オファーのロゴを、2 つのサイズで提供します。**小 (48 x 48)** と**大 (216 x 216)** です。

#### <a name="hero"></a>ヒーロー

ヒーローの画像は省略可能です。 指定する場合は、815 x 290 ピクセルのサイズにする必要があります。

#### <a name="screenshots"></a>Screenshots (スクリーンショット)

オファーがどのように動作するかを示すスクリーンショットを追加します。 少なくとも 1 つのスクリーンショットが必要です。最大 5 つまで追加できます。 スクリーンショットはすべて、1280 x 720 ピクセルにする必要があります。

#### <a name="videos"></a>ビデオ

必要に応じて、オファーをデモンストレーションするビデオを最大 4 つ追加することもできます。 これらのビデオは、YouTube または Vimeo でホストされている必要があります。 それぞれのビデオについて、ビデオの名前、URL、ビデオのサムネイル画像 (1280 x 720 ピクセル) を入力します

#### <a name="additional-marketplace-listing-resources"></a>マーケットプレースのリスト登録に関するその他のリソース

- [マーケットプレース オファーのリスト登録に関するベスト プラクティス](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="availability"></a>可用性

**[可用性]** ページには、オファーを利用できる場所と方法に関するオプションが表示されます。

### <a name="markets"></a>市場

このセクションでは、オファーを利用できる市場を指定します。 これを行うには、 **[市場の編集]** を選択します。 **[市場の選択]** ポップアップ ウィンドウが表示されます。

既定では、市場は選択されていませんが、オファーを公開するには、少なくとも 1 つの市場を選択する必要があります。 **[すべて選択]** をクリックして利用可能なすべての市場でオファーを利用できるようにするか、追加する特定の市場を選択します。 完了したら、 **[保存]** を選択します。

ここで選択した内容は、新規購入にのみ適用されます。ユーザーが既に特定の市場でアプリを入手しているときに、後でその市場が削除された場合、その市場で既にオファーを入手したユーザーは引き続きそれを使用できますが、その市場で新しいユーザーがオファーを入手することはできません。

> [!IMPORTANT]
> ここやパートナー センターにこれらの要件が記載されていない場合でも、あらゆる地域の法的要件を満たす必要があります。

すべての市場を選択した場合でも、国や地域によっては、地域の法律や規制、その他の要因により、特定のオファーが一覧表示されない場合があることに注意してください。

### <a name="preview-audience"></a>プレビュー対象ユーザー

オファーをより広範なマーケットプレース オファーに公開する前に、まず、限定された**プレビュー対象ユーザー**に対してオファーを利用できるようにする必要があります。 ここでは、**非表示キー** (小文字と数字のみを使用する任意の文字列) を入力します。 プレビュー対象ユーザーのメンバーは、この非表示キーをトークンとして使用して、マーケットプレースでオファーのプレビューを表示できます。

オファーを利用可能にしてプレビューの制限を解除する準備ができたら、**非表示キー**を削除して再度発行する必要があります。

## <a name="technical-configuration"></a>技術的な構成

**[技術的な構成]** ページでは、オファーに接続するために使用される技術的な詳細を定義します。 この接続によって、最終顧客がオファーを取得することを選択した場合、Microsoft は最終顧客向けにオファーをプロビジョニングできます。

### <a name="package-type"></a>パッケージの種類

オファーに適用するオプションを選択します。

- **アドオン**:アドオン アプリは、Dynamics 365 Business Central のエクスペリエンスと既存の機能を拡張します。 詳細については、「[アドオン アプリ](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps)」を参照してください。
- **接続**: 接続アプリは、Dynamics 365 Business Central とサードパーティのソリューションまたはサービスとの間にポイントツーポイント接続を確立する必要があるシナリオで使用できます。 詳細については、「[接続アプリ](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps)」を参照してください。

### <a name="file-upload"></a>ファイルのアップロード

上記の **[アドオン]** を選択した場合、オファーのパッケージ ファイルと、依存関係があるすべての拡張機能のパッケージ ファイルをアップロードする場所。

#### <a name="extensions-package-file"></a>拡張機能パッケージ ファイル

オファーの拡張機能パッケージ ファイル (.app) をアップロードします。

#### <a name="library-package-file"></a>ライブラリ パッケージ ファイル

マーケットプレースに公開されない別の拡張機能と共にオファーをインストールする必要がある場合に必要です。 その場合は、その .app ファイルをここにアップロードします。

#### <a name="dependency-package-file"></a>依存関係パッケージ ファイル

マーケットプレースに既に公開されている別の拡張機能と共にオファーをインストールする必要がある場合に必要です。 その場合は、その `.app` または `.zip` ファイルをここにアップロードします。

### <a name="url-to-app-installation"></a>アプリのインストールの URL

上記の **[接続]** を選択した場合、ここでアプリのインストールの URL を入力します。

## <a name="test-drive-technical-configuration"></a>体験版の技術的な構成

[[オファーのセットアップ]](#offer-setup) ページで **[体験版を有効にする]** を選択した場合は、顧客がオファーの体験版を試すことができるように、ここに詳細を入力する必要があります。

**[体験版]** ページでは、デモ (つまり "体験版") を設定して、顧客が購入をコミットする前にオファーを試せるようにすることができます。 詳細については、記事「[体験版とは](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)」を参照してください。 オファーの体験版を提供する必要がなくなった場合、「 **[オファーのセットアップ](#offer-setup)** 」ページに戻って、 **[体験版を有効にする]** をオフにしてください。

利用可能な体験版の種類は次のとおりで、それぞれに独自の技術的な構成要件があります。

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [ロジック アプリ](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (技術的な構成は不要)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure Resource Manager の体験版の技術的な構成

お客様のソリューションを構成しているすべての Azure リソースが含まれたデプロイ テンプレート。 このシナリオに適合するのは、Azure リソースしか使用されていない製品です。 [Azure Resource Manager の体験版](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)の設定について詳しくご確認ください。

- **リージョン** (必須): 現在、体験版を利用可能にできるサポート対象の Azure リージョンは 26 か所です。 通常、顧客が最も近いリージョンを選択して最高のパフォーマンスを実現できるように、顧客の数が最も多いと予測されるリージョンで体験版を利用可能にする必要があります。 選択中の各リージョンで必要なすべてのリソースのデプロイが自分のサブスクリプションで許可されていることを確認する必要があります。

- **Instances**:使用可能なインスタンスの種類 (ホットまたはコールド) と数を選択します。これには、お客様のオファーが利用可能なリージョンの数が掛けられます。

**Hot**:この種類のインスタンスは選択したリージョンごとにデプロイされ、アクセスを待機します。 顧客は、デプロイを待つことなく体験版の "*ホット*" インスタンスにすぐにアクセスできます。 トレードオフとして、これらのインスタンスは Azure サブスクリプションで常に実行しているので、大きな稼働時間コストが発生します。 使用できる "*ホット*" インスタンスがない場合、ほとんどの顧客がフル デプロイを待つのを望まず、その結果顧客の利用が減るので、少なくとも 1 つの "*ホット*" インスタンスを用意することを強くお勧めします。

**コールド**:　この種類のインスタンスは、リージョンごとにデプロイできる可能性があるインスタンスの総数を表します。 コールド インスタンスでは、顧客が体験版を要求したときのデプロイ用に体験版の完全な Resource Manager テンプレートが必要です。そのため、"*コールド*" インスタンスでは、読み込みが "*ホット*" インスタンスよりもはるかに遅くなります。 トレードオフとして、体験版の期間のみの支払いで済みます。"*ホット*" インスタンスのように Azure サブスクリプションで常に実行されていることは "*ありません*"。

- **Test drive Azure Resource Manager template (体験版の Azure Resource Manager テンプレート)** : 目的の Azure Resource Manager テンプレートが含まれている .zip をアップロードします。  Azure Resource Manager テンプレートの作成については、記事「[クイック スタート: Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)」を参照してください。

- **体験版の期間** (必須): 体験版がアクティブな状態であり続ける期間の長さを時間数で入力します。 この期間が終わると、体験版は自動的に終了します。 この期間は、整数の時間でのみ設定できます (例: "2" 時間。"1.5" は有効ではありません)。

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 の体験版の技術的な構成

Microsoft は、この種類の体験版を使用してサービスのプロビジョニングとデプロイをホストおよび維持することで、体験版の設定の複雑さを排除できます。 この種類のホストされた体験版では、体験版がターゲットとしている対象ユーザーが Business Central、Customer Engagement、Operations のいずれであっても、構成は同じです。

- **Max concurrent test drives (同時実行する体験版の最大数)** (必須):　体験版を同時に使用できる顧客の最大数を設定します。 体験版がアクティブになっている間、各同時ユーザーは Dynamics 365 のライセンスを消費します。そのため、設定された上限に対応するうえで十分な数のライセンスを使用可能にする必要があります。 推奨値は 3 ～ 5 です。

- **体験版の期間** (必須): 時間数を定義して、体験版がアクティブな状態であり続ける期間の長さを入力します。 この時間が経過すると、セッションが終了し、ライセンスが消費されなくなります。 オファーの複雑さに応じて、2 時間から 24 時間までの値にすることをお勧めします。 この期間は、整数の時間でのみ設定できます (例: "2" 時間。"1.5" は有効ではありません)。  ユーザーは、時間を使い切った後に体験版にもう一度アクセスしたい場合、新しいセッションを要求できます。

- **インスタンス URL** (必須): 顧客が体験版を開始する URL。 通常、インストールされたサンプル データを使用してアプリを実行する Dynamics 365 インスタンスの URL です (例: https://testdrive.crm.dynamics.com) )。

- **Instance Web API URL (インスタンスの Web API URL)** (必須): 自分の Microsoft 365 アカウントにログインし、 **[設定]** \&、 **[カスタマイズ]** 、\& **[開発者リソース]** \&、 **[インスタンスの Web API] ([サービスのルート URL])** の順に移動することで自分の Dynamics 365 インスタンスの Web API URL を取得し、その URL (例: https://testdrive.crm.dynamics.com/api/data/v9.0) ) をここにコピーします。

- **ロール名** (必須): カスタム Dynamics 365 体験版に定義したセキュリティ ロール名を指定します。体験版の間、この名前がユーザーに割り当てられます。(例: test-drive-role)。

### <a name="technical-configuration-for-logic-app-test-drive"></a>ロジック アプリの体験版の技術的な構成

すべてのカスタム製品には、この種類の体験版デプロイ テンプレートを使用する必要があります。これは、さまざまで複雑なソリューション アーキテクチャに対応します。 ロジック アプリの体験版の設定については、GitHub 上の [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) と [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) に関するページをご覧ください。

- **リージョン** (必須。単一選択のドロップダウン リスト): 現在、体験版を利用可能にできるサポート対象の Azure リージョンは 26 か所です。 お客様のロジック アプリのリソースは、自分が選択するリージョンにデプロイされます。 お客様のロジック アプリに、特定のリージョンに格納されるカスタム リソースがある場合は、ここで必ずそのリージョンを選択します。 最善の方法は、ポータルで自分の Azure サブスクリプションにローカルにロジック アプリを完全にデプロイし、それが正常に動作するのを確認したうえでこの選択を行うことです。

- **Max concurrent test drives (同時実行する体験版の最大数)** (必須):　体験版を同時に使用できる顧客の最大数を設定します。 これらの体験版は既にデプロイされており、顧客はデプロイを待つことなくそれらにすぐにアクセスできます。

- **体験版の期間** (必須): 体験版がアクティブな状態であり続ける期間の長さを時間数で入力します。 この期間が終わると、体験版は自動的に終了します。

- **Azure リソース グループ名** (必須): お客様のロジック アプリの体験版が保存される [Azure リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)の名前を入力します。

- **Azure logic app name (Azure ロジック アプリ名)** (必須): 体験版をユーザーに割り当てるロジック アプリの名前を入力します。 このロジック アプリは、上記の Azure リソース グループに保存される必要があります。

- **Deprovision logic app name (プロビジョニング解除ロジック アプリ名)** (必須): 顧客の完了後に体験版をプロビジョニング解除するロジック アプリの名前を入力します。 このロジック アプリは、上記の Azure リソース グループに保存される必要があります。

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI の体験版では技術的な構成は不要

インタラクティブな Power BI の視覚化を示したい製品では、埋め込みリンクを使用して、カスタムビルドされたダッシュボードを体験版として共有できます。それ以上の技術的な構成は不要です。 [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) テンプレート アプリの設定について詳しくご確認ください。

### <a name="deployment-subscription-details"></a>デプロイ サブスクリプションの詳細

体験版を自動的にデプロイするには、固有の Azure サブスクリプションを別に作成し、それを指定してください (Power BI の体験版では不要です)。

- **Azure サブスクリプション ID** (Azure Resource Manager とロジック アプリでは必須): リソースの使用状況レポート用および課金用の Azure アカウント サービスへのアクセス権を付与するサブスクリプション ID を入力します。 まだお持ちでない場合、体験版に使用するために[別個の Azure サブスクリプションの作成](https://docs.microsoft.com/azure/billing/billing-create-subscription)を検討することをお勧めします。 Azure サブスクリプション ID は、[Azure portal](https://portal.azure.com/) にログインし、左側にあるメニューの **[サブスクリプション]** タブに移動して見つけることができます。 このタブを選択すると、自分のサブスクリプション ID (例: "a83645ac-1234-5ab6-6789-1h234g764ghty") が表示されます。

- **Azure AD テナント ID** (必須): 自分の Azure Active Directory (AD) [テナント ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) を入力します。 この ID を見つけるには、[Azure portal](https://portal.azure.com/) にサインインし、左側のメニューで [Active Directory] タブを選択します。**[プロパティ] を選択した後、表示される**ディレクトリ ID** 番号を確認します (例: 50c464d3-4930-494c-963c-1e951d15360e)。 また、[https://www.whatismytenantid.com](https://www.whatismytenantid.com) で URL のドメイン名を使用して、組織のテナント ID を検索することもできます。

- **Azure AD tenant name (Azure AD テナント名)** (Dynamic 365 で必須): 自分の Azure Active Directory (AD) 名を入力します。 この名前を見つけるには、[Azure portal](https://portal.azure.com/) にサインインします。右上隅にある自分のアカウント名の下に、テナント名が表示されます。

- **Azure AD app ID (Azure AD アプリ ID)** (必須): 自分の Azure Active Directory (AD) [アプリケーション ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) を入力します。 この ID を見つけるには、[Azure portal](https://portal.azure.com/) にサインインして左側のメニューで [Active Directory] タブを選択します。 **[アプリの登録]** を選択した後、表示される**アプリケーション ID** 番号を確認します (例: 50c464d3-4930-494c-963c-1e951d15360e)。

- **Azure AD アプリ クライアントのシークレット** (必須): Azure AD アプリケーションの[クライアント シークレット](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)を入力します。 この値を探すには、[Azure portal](https://portal.azure.com/) にサインインします。 左側のメニューにある **[Azure Active Directory]** タブを選択し、 **[アプリの登録]** を選択してから、体験版アプリを選択します。 次に、 **[Certificates and secrets]\(証明書とシークレット\)** 、 **[New client secret]\(新しいクライアント シークレット\)** の順に選択し、説明を入力し、 **[Expires]\(有効期限\)** で **[Never]\(なし\)** を選択してから、 **[追加]** を選択します。 必ず値をコピーしておいてください。 (これを行う前にページから移動しないでください。移動すると、値にアクセスできなくなります。)

次のセクションに進む前に、必ず**保存**してください。

### <a name="test-drive-marketplace-listings"></a>体験版のマーケットプレースの登録情報

**[体験版]** タブの下にある **[Marketplace listing]\(マーケットプレースの登録情報\)** オプションには、体験版を利用できる言語が表示されます。 現時点では、使用可能な場所は **[英語 (米国)]** のみです。 言語名を選択して、体験版のエクスペリエンスを説明する情報を入力します。

- **説明** (必須): ユーザーがお客様のオファーを購入するかどうかを決定する助けとなるように、体験版、デモの内容、試用するユーザーの目的、試せる機能、関連情報について説明します。 このフィールドには、最大で 3,000 文字のテキストを入力できます。 

- **[アクセス情報]** (Azure Resource Manager とロジックの体験版では必須): 顧客がこの体験版にアクセスして使用するうえで知っておく必要がある情報を説明します。 お客様のオファーの使用シナリオのほか、顧客が体験版を通じて機能にアクセスするために知っておくべき正確な情報を説明します。 このフィールドには、最大で 10,000 文字のテキストを入力できます。

- **ユーザー マニュアル** (必須): 体験版エクスペリエンスの詳細なチュートリアル。 ユーザー マニュアルは、体験版のエクスペリエンスで顧客が使用できる内容を正確にカバーし、疑問がある場合に参照するものとして機能する必要があります。 ファイルは PDF 形式にして、アップロード後に名前 (最大 255 文字) を付ける必要があります。

- **ビデオ: ビデオの追加** (省略可能): ビデオは YouTube または Vimeo にアップロードすることができ、リンクとサムネイル画像 (533 x 324 ピクセル) を使用してここで参照できます。これにより顧客は、オファーの機能を正しく使用する方法やそれらのメリットが際立つシナリオを把握する方法など、体験版への理解を深めるのに役立つ一連の情報を確認できます。
  - **名前** (必須)
  - **URL (YouTube または Vimeo のみ)** (必須)
  - **サムネイル (533 x 324 ピクセル)** : 画像ファイルは PNG 形式である必要があります。

## <a name="supplemental-content"></a>補足コンテンツ

このページでは、オファーの検証に役立つ、オファーに関する追加情報を提供できます。 この情報は顧客に表示されることも、マーケットプレースに公開されることもありません。

### <a name="target-release"></a>ターゲット リリース

ソリューションが対象とする Microsoft Dynamics Business Central のリリースを示します。 **[Current]\(現行\)** 、 **[Next major]\(次回のメジャー\)** 、または **[Next minor]\(次回のマイナー\)** 。 この情報によって、弊社でソリューションを適切にテストすることができます。

### <a name="supported-editions"></a>サポートされているエディション

オファーに Premium エディションの Microsoft Dynamics 365 Business Central が必要な場合、 **[Premium]** だけを選択します。 それ以外の場合、 **[Essentials]** と **[Premium]** の両方を選択します。

### <a name="key-usage-scenario"></a>主な使用シナリオ

ドキュメント (.pdf 形式) 内に列挙されているオファーの主な使用シナリオを一覧表示する `.pdf` ファイルをアップロードする必要があります。 ここに一覧表示されているすべてのシナリオは、マーケットプレースにオファーを承認する前に、弊社の検証チームによって検証される場合があります。

### <a name="app-tests-automation"></a>アプリ テストの自動化

**アプリ テストの自動化**ファイル (.app) をここに任意でアップロードできます。

### <a name="test-accounts"></a>テスト アカウント

弊社の認定チームがオファーを適切に確認するためにテスト アカウントが必要な場合は、**テスト アカウント**情報と共に .pdf、.doc、または .docx ファイルをアップロードしてください。

## <a name="publish"></a>発行

### <a name="submit-offer-to-preview"></a>プレビューへのオファーの送信

オファーの必須セクションをすべて入力したら、ポータルの右上隅にある **[公開]** を選択します。 **[Review and publish]\(確認と公開\)** ページにリダイレクトされます。 

このオファーを公開するのが初めての場合、以下のことが可能です。

- オファーの各セクションの完了状態を確認する。
    - *[未開始]* - セクションは着手されておらず、完了する必要があることを意味します。
    - *[未完了]* - 修正が必要なエラーがセクションにあり、追加の情報を入力する必要があることを意味します。 セクションに戻って更新してください。
    - *[完了]* - セクションが完了していることを意味します。必須のデータはすべて入力済みであり、エラーはありません。 オファーを送信するには、オファーのセクションがすべて完了状態でなければなりません。
- **[認定の注意書き]** セクションで、アプリの理解に役立つ補足事項に加えて、テストの指示を認定チームに提供し、アプリが確実に正しくテストされるようにします。
- **[送信]** を選択して、公開するためにオファーを送信する。 お客様が確認して承認できるように、オファーのプレビュー バージョンが利用可能になったら、Microsoft から電子メールが 届きます。 パートナー センターに戻り、そのオファーに対して **[一般公開する]** を選択して、自分のオファーを一般に公開します (プライベート オファーの場合はプライベート対象ユーザーに公開されます)。

## <a name="next-steps"></a>次の手順

- [商業マーケットプレースで既存のオファーを更新する](./update-existing-offer.md)
