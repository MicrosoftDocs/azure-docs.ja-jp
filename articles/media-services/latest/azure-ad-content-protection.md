---
title: Azure AD を使用したエンド ツー エンドのコンテンツ保護
description: この記事では、Azure Media Services と Azure Active Directory を使用してコンテンツを保護する方法について説明します。
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-javascript
ms.openlocfilehash: 006e312e67f5f4014248c44a799c2dde826801c2
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89258845"
---
# <a name="tutorial-end-to-end-content-protection-using-azure-ad"></a>チュートリアル:Azure AD を使用したエンド ツー エンドのコンテンツ保護

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

このチュートリアルおよび提供されているプレーヤー サンプルを使用すると、DRM と AES-128、ストリーミング プロトコル、コーデック、コンテナーなど、AMS でサポートされるあらゆる形式のメディア コンテンツをストリーム配信するためのエンド ツー エンドのメディア コンテンツ保護サブシステムを Azure Media Services (AMS) と Azure Active Directory (AAD) 上にセットアップすることができます。 OAuth 2 で保護されたあらゆる REST API に、認可コード フローと PKCE (Proof Key for Code Exchange) を通じて安全にアクセスできるよう、サンプルは汎用的に作成されています。 (Azure Media Services ライセンス配信サービスはその 1 つに過ぎません。)その他、Microsoft Graph API のほか、OAuth 2 認可コード フローでセキュリティ保護されたすべての独自開発 REST API で使用できます。 これは、[サンプル コード](https://github.com/Azure-Samples/media-services-content-protection-azure-ad)の手引きとなるドキュメントです。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
>
> * 認証の要件を検討する
> * アプリの動作を理解する
> * バックエンド リソース アプリを登録する
> * クライアント アプリを登録する
> * Media Services アカウントのコンテンツ キー ポリシーとストリーミング ポリシーを設定する
> * プレーヤー アプリをセットアップする

Azure Media Services サブスクリプションをお持ちでない方は、Azure の[無料試用版アカウント](https://azure.microsoft.com/free/)を作成してから Media Services アカウントを作成してください。

### <a name="duration"></a>Duration
このチュートリアルの所要時間は、前提条件となるテクノロジを用意したうえで約 2 時間です。

## <a name="prerequisites"></a>前提条件

以下に示した最新のテクノロジのバージョンと概念が使用されています。 このチュートリアルに取り組む前に、それらについての知識を身に付けておくことをお勧めします。

### <a name="prerequisite-knowledge"></a>前提条件となる知識

次の概念は必須ではありませんが、このチュートリアルに取り組む前に理解しておくことをお勧めします。

* デジタル著作権管理 (DRM)
* [Azure Media Services (AMS) v3](./media-services-overview.md)
* AMS API v3、Azure portal、[Azure Media Services Explorer (AMSE) ツール](https://github.com/Azure/Azure-Media-Services-Explorer)のいずれかを使用した AMS [コンテンツ キー ポリシー](content-key-policy-concept.md)
* [Microsoft ID プラットフォーム](../../active-directory/develop/index.yml)上の Azure AD v2 エンドポイント
* 先進的なクラウド認証 ([OAuth 2.0、OpenID Connect](../../active-directory/develop/active-directory-v2-protocols.md) など)
  * [OAuth 2.0 の認可コード フロー](../../active-directory/develop/v2-oauth2-auth-code-flow.md)と PKCE が必要な理由
  * [委任されたアクセス許可とアプリケーションのアクセス許可](../../active-directory/develop/developer-glossary.md#permissions)
* [JWT トークン](../../active-directory/develop/access-tokens.md)とその要求、署名キー ロールオーバー (サンプルに含まれています)

### <a name="prerequisite-code-and-installations"></a>前提条件のコードとインストール

* サンプル コード。 [サンプル コード](https://github.com/Azure-Samples/media-services-content-protection-azure-ad)をダウンロードします。
* Visual Studio Code のインストール。 Visual Studio Code は、こちら ([https://code.visualstudio.com/download](https://code.visualstudio.com/download)) からダウンロードしてください。
* Node.js のインストール。 Node.js は、こちら ([https://nodejs.org](https://nodejs.org)) からダウンロードしてください。このインストールには NPM が付属します。
* [Azure サブスクリプション](https://azure.microsoft.com/free/)。
* Azure Media Services (AMS) アカウント。
* @azure/msal-browser v2.0。これは、各種クライアント プラットフォームを対象とした [Microsoft Authentication Library (MSAL)](../../active-directory/develop/msal-overview.md) SDK ファミリーのメンバーの 1 つです。
* 最新バージョンの [Azure Media Player](https://github.com/Azure-Samples/azure-media-player-samples)(サンプルに含まれています)。
* クライアント側 JavaScript 経由でアクセスできる CORS でホストされたアプリケーション証明書と FairPlay DRM を含める場合は、Apple の FPS の資格情報。

> [!IMPORTANT]
> このチュートリアルでは、.NET を使用してコンテンツ キー ポリシー制限を作成しています。  .NET ではなく Node.js で Azure Media Services への接続を試してみたい場合は、「[Media Services v3 API に接続する - Node.js](configure-connect-nodejs-howto.md)」を参照してください。 キー ロールオーバーを自動的に処理する Node.js モジュールも用意されています。Node.js の [passport-ad モジュール](https://github.com/AzureAD/passport-azure-ad)を参照してください。

## <a name="consider-the-authentication-and-authorization-requirements"></a>認証と承認の要件を検討する

サブシステムの設計には、いくつかの課題があります。 複数の変動要因やクライアント アプリの制約があるほか、Azure AD キー ロールオーバーが 6 週間おきに発生します。

このチュートリアルで使用するシングルページ アプリ (SPA) では、認証要件への課題やそれに付随する制限を考慮に入れています。 イベント プロセッサで使用されるものは次のとおりです。

* Azure AD v2 エンドポイント。Azure AD 開発者プラットフォーム (v1 エンドポイント) は、Microsoft ID プラットフォーム (v2 エンドポイント) に移行されます。
* 認可コード フロー。OAuth 2 の暗黙的な許可フローは非推奨となりました。
* アプリ。次の制約があります。
    * パブリック クライアントは、クライアント シークレットを隠すことができません。  認可コード フロー単体では、クライアント シークレットを隠す必要があります。そこで認可コード フローに PKCE を組み合わせて使用します。
    * ブラウザーベースのアプリには、ブラウザーのセキュリティ サンドボックス (CORS/プレフライト制約) が適用されます。
    * 先進の JavaScript を使用するブラウザーベースのアプリには、JavaScript セキュリティ制約 (カスタム ヘッダー アクセシビリティ、correlation-id) が適用されます。

## <a name="understand-the-subsystem-design"></a>サブシステムの設計を理解する

以下の図は、サブシステムの設計を表しています。  次の 3 つのレイヤーがあります。

* バックオフィス レイヤー (黒)。コンテンツ キー ポリシーの構成や、ストリーム配信されるコンテンツの公開に使用されます。
* パブリック エンドポイント (青)。プレーヤー (ユーザー) と面するエンドポイントで、認証と認可、DRM ライセンス、暗号化コンテンツを提供します。
* プレーヤー アプリ (薄い青)。すべてのコンポーネントを統合すると共に、次のことを行います。
    * Azure AD を介したユーザー認証を行います。
    * Azure AD から access_token を取得します。
    * AMS と CDN からマニフェストと暗号化されたコンテンツを受信します。
    * Azure Media Services から DRM ライセンスを取得します。
    * コンテンツの解読、デコード、表示を行います。

![JWT トークンを解析するための画面](media/aad-ams-content-protection/subsystem.svg)

サブシステムの詳細については、「[アクセス制御を使用したマルチ DRM コンテンツ保護システムの設計](./design-multi-drm-system-with-access-control.md)」を参照してください。

## <a name="understand-the-single-page-app"></a>シングルページ アプリを理解する

プレーヤー アプリは、Visual Studio Code で開発されたシングルページ アプリケーション (SPA) です。次のテクノロジが使用されています。

* Node.js と ES 6 JavaScript
* @azure/msal-browser 2.0 ベータ版
* Azure Media Player SDK
* Azure AD v2 エンドポイントに対する OAuth 2 フロー (Microsoft ID プラットフォーム)

SPA プレーヤー アプリは、次のアクションを実行します。

* テナントのネイティブ ユーザーに対するユーザー認証、および他の AAD テナントまたは MSA アカウントからのゲスト ユーザーに対するユーザー認証。 ユーザーは、ブラウザーのポップアップまたはリダイレクト (Safari など、ポップアップを許可していないブラウザーの場合) を経由したサインインを選択できます。
* 認可コード フローと PKCE による `access_token` の取得。
* `access_token` の更新 (AAD によって発行されたトークンの有効期間は 1 時間です)。対応する `refresh_token` も取得されます。
* JWT トークン (`access_token` と `id_token` の両方) のテストと検査を目的とした解析。
* AES-128 コンテンツ キーまたは 3 つすべての DRM の DRM ライセンスの取得。
* DRM とストリーミング プロトコルとコンテナー形式のさまざまな組み合わせにおけるコンテンツのストリーム配信。 それぞれの組み合わせについて、正しい書式設定文字列が生成されます。
* 解読、デコード、表示。
* トラブルシューティングを目的とした Microsoft Graph API 呼び出し。 <!--See more details in the subsection Shortest path: testing my protected asset in my subscription with your hosted player app and underlying tenant. -->

サインイン、トークン取得、トークン更新、トークン表示の画面を次に示します。

 ![サインイン、トークン取得、トークン更新、トークン表示の画面](media/aad-ams-content-protection/token-acquisition.png)

JWT トークン (access_token または id_token) を解析するための画面。

![JWT トークンを解析するための画面](media/aad-ams-content-protection/parsing-jwt-tokens.png)

DRM (または AES) とストリーミング プロトコルとコンテナー形式のさまざまな組み合わせによって保護されたコンテンツをテストするための画面。

![JWT トークンを解析するための画面](media/aad-ams-content-protection/testing-protected-content.png)
-->

<!-- You can see a hosted version of the sample at [https://aka.ms/ott](https://aka.ms/ott)-->

## <a name="choose-an-azure-ad-tenant"></a>Azure AD テナントを選択する

> [!NOTE]
> ここからは、Azure portal にログインしていること、また、少なくとも 1 つの Azure AD テナントがあることを前提とします。

エンド ツー エンドのサンプルに使用する Azure AD テナントを選択します。 2 つのオプションがあります。

* 既存の Azure AD テナント。 どの Azure サブスクリプションにも Azure AD テナントが必ず 1 つ必要ですが、Azure AD テナントは、複数の Azure サブスクリプションで使用することができます。
* どの Azure サブスクリプションにも使用されて "*いない*" 新しい Azure AD テナント。 新しいテナントを選択する場合、単独の Azure AD テナントを使用する 1 つの Azure サブスクリプションに Media Services アカウントとサンプル プレーヤー アプリが存在する必要があります。 そうすることで、ある程度の柔軟性が生まれます。 たとえば、独自の Azure AD テナントを使用するだけでなく、顧客の Azure サブスクリプションにある顧客の Media Services アカウントを使用することもできます。

## <a name="register-the-backend-resource-app"></a>バックエンド リソース アプリを登録する

1. 選択または作成した Azure AD テナントに移動します。
1. メニューから **[Azure Active Directory]** を選択します。
1. メニューから **[アプリの登録]** を選択します。
1. **[+ 新規登録]** をクリックします。
1. アプリに *LicenseDeliveryResource2* という名前を付けます (2 は AAD v2 エンドポイントを表します)。
1. **[この組織ディレクトリのみに含まれるアカウント (<*ご利用のテナント名*> のみ - シングル テナント)]** を選択します。 複数のテナントへのアクセスを有効にしたい場合は、他のいずれかのマルチテナント オプションを選択します。
1. **[リダイレクト URI]** は省略可能です。また、後で変更することができます。
1. **[登録]** をクリックします。 [アプリの登録] ビューが表示されます。
1. メニューから **[マニフェスト]** を選択します。 [マニフェスト] ビューが表示されます。
1. `accessTokenAcceptedVersion` の値を *2* (引用符なし) に変更します。
1. `groupMembershipClaims` の値を *"SecurityGroup"* (引用符あり) に変更します。
1. **[保存]** をクリックします。
1. メニューから **[API の公開]** を選択します。 [Scope の追加] ビューが表示されます。 (アプリケーション ID の URI は Azure から提供されますが、変更したい場合は、[アプリケーション ID の URI] フィールドで編集できます。)
1. **保存して続行** をクリックします。 ビューが変更されます。 次の表の「設定」列に記載されている各設定について、[値] 列に値を入力し、 **[スコープの追加]** をクリックします。

| 設定 | 値 | 説明 |
| ------- | ----- | ----------- |
| スコープ名 | *DRM.License.Delivery* | この API に対するアクセスが要求されるとき、およびアクセス トークンでクライアント アプリケーションに対してスコープが付与されるときにスコープが表示される方法。 このアプリケーションで一意でなければなりません。 ベスト プラクティスは、"resource.operation.constraint" をパターンとして使用して名前を生成することです。 |
| 同意できるユーザー | *管理者とユーザー* | ユーザーの同意を有効にしているディレクトリでユーザーがこのスコープに同意できるかどうかを示します。 |
| 管理者の同意の表示名 | *DRM ライセンス配信* | 同意画面で管理者がこのスコープに同意するときのスコープの呼称。 |
| 管理者の同意の説明** | *DRM ライセンス配信バックエンド リソース スコープ* | テナント管理者が同意画面でスコープを拡張するときに表示される、スコープに関する詳細な説明。 |
| ユーザーの同意の表示名 | *DRM.License.Delivery* | 同意画面でユーザーがこのスコープに同意するときのスコープの呼称。 |
| ユーザーの同意の説明 | *DRM ライセンス配信バックエンド リソース スコープ* | これは、ユーザーが同意画面でスコープを拡張するときに表示される、スコープに関する詳細な説明です。 |
| State | *有効* | このスコープをクライアントが要求できるかどうかを示します。 クライアントに表示しないスコープの場合、[無効] に設定します。 削除できるのは無効なスコープのみで、スコープが無効になった後、クライアントが使用していないことを確認するため、少なくとも 1 週間待ってから削除することをお勧めします。 |

## <a name="register-the-client-app"></a>クライアント アプリを登録する

1. 選択または作成した Azure AD テナントに移動します。
1. メニューから **[Azure Active Directory]** を選択します。
1. メニューから **[アプリの登録]** を選択します。
1. **[+ 新規登録]** をクリックします。
1. クライアント アプリに名前を付けます (例: *AMS AAD Content Protection*)。
1. **[この組織ディレクトリのみに含まれるアカウント (<*ご利用のテナント名*> のみ - シングル テナント)]** を選択します。 複数のテナントへのアクセスを有効にしたい場合は、他のいずれかのマルチテナント オプションを選択します。
1. **[リダイレクト URI]** は省略可能です。また、後で変更することができます。
1. **[登録]** をクリックします。
1. メニューから **[API のアクセス許可]** を選択します。
1. **[+アクセス許可の追加]** をクリックします。 [API アクセス許可の要求] ビューが表示されます。
1. **[自分の API]** タブをクリックし、前セクションで作成した *LicenseDeliveryResource2* アプリを選択します。
1. DRM 矢印をクリックし、 *[DRM.License.Delivery]* アクセス許可をチェックします。
1. **[アクセス許可の追加]** をクリックします。 [アクセス許可の追加] ビューが閉じます。
1. メニューから **[マニフェスト]** を選択します。 [マニフェスト] ビューが表示されます。
1. 次の値のペアを `replyUrlsWithType` 属性に追加します。

   ```json
   "replyUrlsWithType": [
        {
            "url": "https://npmwebapp.azurewebsites.net/",
            "type": "SPA"
        },
        {
            "url": "http://localhost:3000/",
            "type": "SPA"
        }
    ],
   ```

    > [!NOTE]
    > この時点ではまだ、プレーヤー アプリの URL はありません。  localhost Web サーバーからアプリを実行している場合は、localhost の値ペアを使用できます。 プレーヤー アプリをデプロイした後で、そのデプロイした URL を含んだエントリをここに追加してください。  その作業を行わなかった場合、Azure AD のサインインでエラー メッセージが表示されます。

1. **[保存]** をクリックします。
1. 最後に、構成が正しいことを確認するために、 **[認証]** を選択します。  [認証] ビューが表示されます。 クライアント アプリケーションがシングル ページ アプリ (SPA) として表示され、リダイレクト URI が表示されて、付与タイプが [Authorization Code Flow with PKCE]\(認可コード フローと PKCE\) になります。

### <a name="set-up-the-media-services-account-content-key-policy-and-streaming-policies"></a>Media Services アカウントのコンテンツ キー ポリシーとストリーミング ポリシーを設定する

**このセクションは、読者が .NET 開発者であること、また、AMS v3 API の使い方を熟知していることを前提としています。**

> [!NOTE]
> 執筆時点では、OpenID-Config で非対称トークンの署名キーの使用が Azure portal ではサポートされておらず、Media Services アカウント キー ポリシーの設定に Azure portal を使用することはできません。Azure AD によって発行されるトークンは非対称キーで署名され、キーは 6 週間おきにロール オーバーされるため、この設定では、Azure AD キー ロールオーバーをサポートする必要があります。 したがって、このチュートリアルでは .NET と AMS v3 API を使用しています。

DRM と AES-128 には、[コンテンツ キー ポリシー](content-key-policy-concept.md)と[ストリーミング ポリシー](streaming-policy-concept.md)の構成が適用されます。  コンテンツ キー ポリシーの `ContentKeyPolicyRestriction` に変更を加えましょう。

以下に示したのは、コンテンツ キー ポリシー制限を作成するための .NET コードです。

```dotnetcli
ContentKeyPolicyRestriction objContentKeyPolicyRestriction;

//use Azure Active Directory OpenId discovery document, supporting key rollover
objContentKeyPolicyRestriction = new ContentKeyPolicyTokenRestriction()
    {
        OpenIdConnectDiscoveryDocument = ConfigAccessor.AppSettings["ida_AADOpenIdDiscoveryDocument"]
    };

string audience = ConfigAccessor.AppSettings["ida_audience"];
string issuer   = ConfigAccessor.AppSettings["ida_issuer"];

ContentKeyPolicyTokenRestriction objContentKeyPolicyTokenRestriction = (ContentKeyPolicyTokenRestriction)objContentKeyPolicyRestriction;
objContentKeyPolicyTokenRestriction.Audience             = audience;
objContentKeyPolicyTokenRestriction.Issuer               = issuer;
objContentKeyPolicyTokenRestriction.RestrictionTokenType = ContentKeyPolicyRestrictionTokenType.Jwt;

objContentKeyPolicyRestriction = objContentKeyPolicyTokenRestriction;

return objContentKeyPolicyRestriction;
```

上のコードの `ida_AADOpenIdDiscoveryDocument`、`ida_audience`、`ida_issuer` の各値を変更します。 Azure portal でこれらの項目の値を探すには、次の手順に従います。

1. 先ほど使用した AAD テナントを選択し、メニューで **[アプリの登録]** をクリックして、 **[エンドポイント]** リンクをクリックします。
1. **[OpenIdConnect metadata document]\(OpenIdConnect メタデータ ドキュメント\)** フィールドの値を選択してコピーし、`ida_AADOpenIdDiscoveryDocument` の値としてコードに貼り付けます。
1. `ida_audience` の値は、登録済みのアプリ *LicenseDeliveryResource2* の [アプリケーション (クライアント) ID] です。
1. `ida_issuer` の値には、`https://login.microsoftonline.com/[tenant_id]/v2.0` という URL を指定します。 [*tenant_id*] は、実際のテナント ID に置き換えてください。

## <a name="set-up-the-sample-player-app"></a>サンプル プレーヤー アプリを設定する

まだアプリを入手していない場合は、ソース リポジトリ [https://github.com/Azure-Samples/media-services-content-protection-azure-ad](https://github.com/Azure-Samples/media-services-content-protection-azure-ad) からアプリをクローンまたはダウンロードします。

プレーヤー アプリを設定するにあたっては、次の 2 つの方法があります。

* 最小限のカスタマイズ (client_id、tenant_id、ストリーミング URL など、いくつかの文字列定数の値を置き換えるのみ)。この場合は、Visual Studio Code と Node.js を使用する必要があります。
* 別の IDE と Web プラットフォーム (ASP.NET Core など) を使用する。この場合は、Web ページ ファイル、JavaScript ファイル、CSS ファイルをプロジェクトに配置できます。プレーヤー アプリ自体にはサーバー側のコードが一切使用されていません。

### <a name="option-1"></a>方法 1

1. Visual Studio Code を起動します。
1. プロジェクトを開くために、[ファイル]、[フォルダーを開く]、[参照] の順にクリックして、*package.json* ファイルの親フォルダーを選択します。
1. JavaScript ファイル *public/javascript/constants.js* を開きます。
1. `OAUTH2_CONST.CLIENT_ID` を、AAD テナントに登録したクライアント アプリの `client_id` に置き換えます。  `client_id` は、Azure portal の登録済みのアプリケーションの [概要] セクションで確認できます。 オブジェクト ID ではなく、クライアント ID であることに注意してください。
1. `OAUTH2_CONST.TENANT_ID` を、Azure AD テナントの `tenant_id` に置き換えます。 `tenant_id` は、Azure Active Directory メニューをクリックして確認できます。 tenant_id は、[概要] セクションに表示されます。
1. `OAUTH2_CONST.SCOPE` を、登録済みクライアント アプリに追加したスコープに置き換えます。 スコープは、 **[アプリの登録]** メニューから登録済みクライアント アプリに移動し、自分のクライアント アプリを選択して確認できます。
    1. クライアント アプリを選択し、 **[API のアクセス許可]** メニューをクリックして、API のアクセス許可 *[LicenseDeliveryResource2]* の *[DRM.License.Delivery]* スコープを選択します。 アクセス許可は、*api://df4ed433-dbf0-4da6-b328-e1fe05786db5/DRM.License.Delivery* のような形式になっている必要があります。 **重要**:`OAUTH2_CONST.SCOPE` の `offline_access` の前のスペースは維持してください。
1. `AMS_CONST` の 2 つの定数文字列を次のように置き換えます。 1 つは、テスト資産の保護されたストリーミング URL です。FairPlay のテスト ケースを含めたい場合、もう 1 つには、FPS アプリケーションの証明書の URL を指定します。 それ以外の場合、`AMS_CONST.APP_CERT_URL` は、そのままにしてかまいません。 その後、 **[保存]** をクリックします。

```javascript
//defaults in ams.js
class AMS_CONST {
    static get DEFAULT_URL() {
        return "https://eventgridmediaservice-usw22.streaming.media.azure.net/9591e337-ae90-420e-be30-1da36c06665b/MicrosoftElite01.ism/manifest(format=mpd-time-csf,encryption=cenc)";
    }
    //FairPlay application cert URL
    static get APP_CERT_URL() {
     return `${window.location.href}cert/FPSAC.cer`;
    }
}
```

ローカルでテストするには、次の手順に従います。

1. Visual Studio Code (VSC) で、メイン メニューから **[表示]** を選択し、 **[ターミナル]** を選択します。
1. まだ npm をインストールしていない場合は、コマンド プロンプトに「`npm install`」と入力します。
1. コマンド プロンプトに「`npm start`」と入力します。 (npm が起動しない場合は、コマンド プロンプトに「`cd npmweb`」と入力して、ディレクトリを `npmweb` に変更してください。)
1. ブラウザーを使用して `http://localhost:3000` にアクセスします。

サインイン (`access_token` の取得) 後、使用するブラウザーに応じて、テストする DRM (または AES) とストリーミング プロトコルとコンテナー形式の正しい組み合わせを選びます。 macOS 上の Safari でテストを実施する場合は、[Redirect API]\(リダイレクト API\) オプションをチェックしてください。Safari ではポップアップがサポートされません。 その他ほとんどのブラウザーでは、ポップアップとリダイレクトの両方のオプションが使用できます。

### <a name="option-2"></a>方法 2

別の IDE または Web プラットフォームや、Web サーバー (開発マシン上で実行されている IIS など) を使用する予定の場合は、ローカル Web サーバーの新しいディレクトリに以下のファイルをコピーしてください。 次のパスは、ダウンロードしたコード内において見つかる場所です。

* *views/index.ejs* (拡張子を .html に変更)
* *views/jwt.ejs* (拡張子を .html に変更)
* *views/info.ejs* (拡張子を .html に変更)
* *public/* * (以下に示した JavaScript ファイル、CSS、画像)

1. *view* フォルダー内のファイルを新しいディレクトリのルートにコピーします。
1. *public* フォルダーにある "*フォルダー*" を新しいディレクトリのルートにコピーします。
1. `.ejs` ファイルの拡張子を `.html` に変更します。 (サーバー側の変数は使用しないので、変更しても問題ありません。)
1. VSC (または他のコード エディター) で *index.html* を開き、`<script>` と `<link>` のパスを、ファイルの格納場所に合わせて変更します。  これまでの手順に従った場合、パスから `\` を削除するだけです。  たとえば、`<script type="text/javascript" src="/javascript/constants.js"></script>` を `<script type="text/javascript" src="javascript/constants.js"></script>` にします。
1. *javascript/constants.js* ファイル内の定数を「方法 1」と同じようにカスタマイズします。

## <a name="common-customer-scenarios"></a>一般的なカスタマー シナリオ

チュートリアルが完了し、実用的なサブシステムが完成したら、以下のカスタマー シナリオに合わせて変更を加えることができます。

### <a name="role-based-access-control-rbac-for-license-delivery-via-azure-ad-group-membership"></a>ロールベースのアクセス制御 (RBAC) による Azure AD グループ メンバーシップでのライセンス配信

これまでこのシステムでは、サインインできるすべてのユーザーが有効なライセンスを取得して、保護されたコンテンツを再生することができました。

しかし、認証済みユーザーの一部にのみコンテンツの視聴を許可し、それ以外のユーザーには視聴を許可したくない、と考えるカスタマーは多く存在します。たとえば、ビデオ コンテンツに関して Basic サブスクリプションと Premium サブスクリプションを提供するケースが該当します。 Basic サブスクリプションの料金しか支払っていない利用者については、Premium サブスクリプション限定のコンテンツを視聴できないようにする必要があります。 この要件を満たすために別途必要な手順は次のとおりです。

#### <a name="set-up-the-azure-ad-tenant"></a>Azure AD テナントを設定する

1. テナントに 2 つのアカウントを設定します。 *premium_user* と *basic_user* などの名前を付けてください。
1. ユーザー グループを作成し、*PremiumGroup* という名前を付けます。
1. *PremiumGroup* のメンバーとして *premium_user* を追加します。ただし、*basic_user* はこのグループに追加しません。
1. *PremiumGroup* の**オブジェクト ID** を書き留めます。

#### <a name="set-up-the-media-services-account"></a>Media Services アカウントを設定する

`ContentKeyPolicyRestriction` (前出の Media Services アカウントの設定に関するセクションを参照) に変更を加えます。*groups* という名前の要求を追加してください。`ida_EntitledGroupObjectId` の値には、*PremiumGroup* のオブジェクト ID を指定します。

```dotnetcli

var tokenClaims = new ContentKeyPolicyTokenClaim[] { new ContentKeyPolicyTokenClaim("groups", ConfigAccessor.AppSettings["ida_EntitledGroupObjectId"])
//, other claims, if any.
};

if (tokenClaims != null && tokenClaims.Length > 0)
{
     objContentKeyPolicyTokenRestriction.RequiredClaims = new List<ContentKeyPolicyTokenClaim>(tokenClaims);
}
```

*groups* 要求は、Azure AD の[制限付き要求セット](../../active-directory/develop/active-directory-claims-mapping.md#claim-sets)のメンバーです。

#### <a name="test"></a>テスト

1. *premium_user* アカウントでサインインします。 保護されたコンテンツを再生することができます。
1. *basic_user* アカウントでサインインします。 ビデオが暗号化されているにもかかわらず暗号化を解除するためのキーがないことを示すエラーが返されます。 プレーヤーの診断オーバーレイの下部にドロップダウンが表示されると共に、イベント、エラー、ダウンロードが表示される場合、エラー メッセージは、Azure AD トークン エンドポイントによって発行された JWT にグループ要求の要求値が存在しないためにライセンスの取得に失敗したことを示しています。

### <a name="supporting-multiple-media-service-accounts-across-multiple-subscriptions"></a>(複数のサブスクリプションにまたがる) 複数の Media Services アカウントをサポートする

カスタマーは、1 つまたは複数の Azure サブスクリプションで、Media Services アカウントを複数所有することができます。 たとえば、本番環境のプライマリ アカウントとしての Media Services アカウントの他に、セカンダリ (バックアップ) 用の Media Services アカウントと Dev/Test 用の Media Services アカウントを利用することができます。

そのために必要な作業は、`ContentKeyPolicyRestriction` を作成したときと同じ一連のパラメーター (Media Services アカウントの設定に関するセクションを参照) を、すべての Media Services アカウントで使用するだけです。

### <a name="supporting-a-customer-its-vendors-andor-subsidiaries-across-multiple-aad-tenants"></a>複数の AAD テナントにわたって存在するカスタマーとそのベンダー、子会社をサポートする

ソリューションのユーザーとして、カスタマーの子会社、ベンダー、パートナーが別々の AAD テナント (`mycustomer.com`、`mysubsidiary.com`、`myparther.com` など) に存在していてもかまいません。 このソリューションは特定の単一 AAD テナント (`mycustomer.com` など) 上に構築されていますが、他のテナントのユーザーにも対応できます。

このソリューションの `mycustomer.com` を使用して、`mypartner.com` のユーザーを `mycustomer.com` テナントのゲスト ユーザーとして追加します。 `mypartner.com` ユーザーがゲストアカウントをアクティブにしていることを確認してください。 ゲストアカウントは、別の AAD テナントのアカウントでも、`outlook.com` のアカウントでもかまいません。

`mypartner.com` のゲスト ユーザーは、`mycustomer.com` でアクティブにされた後も、本人が利用する元の AAD テナント (`mypartner.com`) 経由で認証されますが、`access_token` は `mycustomer.com` によって発行されます。

### <a name="supporting-a-customer-tenantsubscription-with-a-setup-in-your-subscriptiontenant"></a>自分のサブスクリプションとテナントのセットアップでカスタマーのテナントとサブスクリプションをサポートする

自分のセットアップを使用して、カスタマーの Media Services アカウントとサブスクリプション内の保護されたコンテンツをテストすることができます。 そのセットアップには、同じサブスクリプションの Azure AD テナントと Media Services アカウントを使用します。 カスタマーの Media Services アカウントは、カスタマー自身の Azure AD テナントの Azure サブスクリプション内に存在します。

1. 自分のテナントにゲストアカウントとしてカスタマーのアカウントを追加します。
1. カスタマーと連携して、保護されたコンテンツをカスタマーの Media Services アカウントに用意します。Media Services アカウントの設定に関するセクションに記載した 3 つのパラメーターを指定してください。

カスタマーは、あなたのセットアップにブラウザーでアクセスし、ゲストアカウントでサインインして、カスタマー自身の保護されたコンテンツをテストすることができます。 皆さんが自分のアカウントでサインインして、カスタマーのコンテンツをテストすることもできます。

サンプル ソリューションは、Microsoft テナントと Microsoft サブスクリプションまたはカスタム テナントと Microsoft サブスクリプションの組み合わせでセットアップすることができます。 Azure Media Services インスタンスは、別のサブスクリプションとそのテナントに属していてもかまいません。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

> [!WARNING]
> 今後このアプリケーションを使う予定がなければ、このチュートリアルで作成したリソースは削除してください。 そうしないと料金が発生します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [クイック スタート: コンテンツの暗号化](encrypt-content-quickstart.md)
