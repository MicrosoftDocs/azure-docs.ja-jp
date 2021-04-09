---
title: Google を B2B 用 ID プロバイダーとして追加する - Azure AD
description: Google とのフェデレーションを行って、ゲスト ユーザーが独自の Gmail アカウントを使用して Azure AD アプリにサインインできるようにします。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2e9acda136654a0dd654eded7b4c17e560fc0aa
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709703"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Google を B2B ゲスト ユーザーの ID プロバイダーとして追加する

Google とのフェデレーションを設定することで、招待されたユーザーが Microsoft アカウントを作成することなく、独自の Gmail アカウントを使用して共有アプリおよびリソースにサインインできるようにできます。

アプリケーションのサインイン オプションの 1 つとして Google を追加すると、ユーザーは **[サインイン]** ページで、Google へのサインインに使用するメール アドレスを入力するか、 **[サインイン オプション]** を選択してから **[Google アカウントでサインイン]** を選択できます。 どちらの場合も、認証のために Google のサインイン ページにリダイレクトされます。

![Google ユーザーのサインイン オプション](media/google-federation/sign-in-with-google-overview.png)

> [!NOTE]
> Google フェデレーションは Gmail ユーザー専用に設計されています。 G Suite ドメインとのフェデレーションを行うには、[直接フェデレーション](direct-federation.md)を使用します。

> [!IMPORTANT]
> **2021 年 1 月 4 日以降**、Google は [WebView サインインのサポートを廃止](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)します。 Gmail で Google フェデレーションまたはセルフサービス サインアップを使用している場合は、[基幹業務ネイティブ アプリケーションの互換性をテストする](google-federation.md#deprecation-of-webview-sign-in-support)必要があります。

## <a name="what-is-the-experience-for-the-google-user"></a>Google ユーザーのエクスペリエンスの内容

Google ユーザーが招待を引き換えたときのエクスペリエンスは、Google に既にサインインしているかどうかによって異なります。

- Google にサインインしていないゲストユーザーには、そうするように求めるメッセージが表示されます。
- Google に既にサインインしているゲスト ユーザーには、使用するアカウントの選択を求めるメッセージが表示されます。 招待に使用されたアカウントを選択する必要があります。

"ヘッダーが長すぎます" というエラーが表示されたゲスト ユーザーは、Cookie をクリアするか、プライベートまたは匿名のウィンドウを開いて、もう一度サインインを試すことができます。

![Google のサインイン ページを示すスクリーンショット。](media/google-federation/google-sign-in.png)

## <a name="sign-in-endpoints"></a>サインインのエンドポイント

Google ゲスト ユーザーは、[共通のエンドポイント](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint) (つまり、テナント コンテキストを含まない一般的なアプリ URL) を使用して、マルチテナントまたは Microsoft のファーストパーティ アプリにサインインできるようになりました。 サインイン プロセス中に、ゲスト ユーザーは **[サインイン オプション]** を選択してから、 **[Sign in to an organization]\(組織にサインイン\)** を選択します。 次に、ユーザーは組織の名前を入力し、Google 資格情報を使用してサインインを続行します。

Google ゲスト ユーザーは、テナント情報を含むアプリケーション エンドポイントを使用することもできます。次に例を示します。

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

また、`https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>` のようにテナント情報を含めることによって、アプリケーションまたはリソースへの直接リンクを Google ゲスト ユーザーに提供することもできます。

## <a name="deprecation-of-webview-sign-in-support"></a>WebView サインイン サポートの廃止

2021 年 1 月 4 日以降、Google は [埋め込み WebView サインインのサポートを廃止](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)します。 [Gmail で Google フェデレーションまたはセルフサービス サインアップ](identity-providers.md)を使用している場合は、基幹業務ネイティブ アプリケーションの互換性をテストする必要があります。 認証を必要とする WebView コンテンツがアプリに含まれている場合、Google Gmail ユーザーは認証を受けることができません。 Gmail ユーザーに影響を与える既知のシナリオを次に示します。

- 以前のバージョンの Windows で埋め込み WebView または WebAccountManager (WAM) を使用する Windows アプリ。
- 認証のために埋め込みブラウザー フレームワークを使用する、自分が開発したその他のネイティブ アプリ。

この変更は以下のものには影響しません。

- 最新バージョンの Windows で埋め込み WebView または WebAccountManager (WAM) を使用する Windows アプリ
- Microsoft iOS アプリ
- G Suite ID (たとえば、G Suite との SAML ベースの[直接フェデレーション](direct-federation.md)を使用している場合)

Microsoft はさまざまなプラットフォームおよびシナリオのテストを継続しており、それに応じてこの記事を更新する予定です。
### <a name="to-test-your-apps-for-compatibility"></a>アプリの互換性をテストするには

1. [Google のガイダンス](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)に従って、ご利用のアプリが影響を受けるかどうかを判断します。
2. Fiddler または別のテスト ツールを使用して、サインイン時にヘッダーを挿入し、Google 外部 ID を使用してサインインをテストします。

   1. 要求が accounts.google.com に送信されるときに、HTTP 要求ヘッダーに Google-Accounts-Check-OAuth-Login:true を追加します。
   1. accounts.google.com サインイン ページで Gmail アドレスを入力して、アプリへのサインインを試行します。
   1. サインインに失敗し、[This browser or app may not be secure]\(このブラウザーまたはアプリはセキュリティで保護されていない可能性があります\) などのエラーが表示された場合、ご利用の Google 外部 ID でのサインインはブロックされます。

3. 次のいずれかを実行して、問題を解決します。

   - 以前のバージョンの Windows で Windows アプリが埋め込み WebView または WebAccountManager (WAM) を使用している場合は、最新バージョンの Windows に更新してください。
   - サインインにシステム ブラウザーを使用するようにアプリを変更します。 詳細については、MSAL.NET のドキュメントの「[埋め込み Web UI とシステム Web UI の比較](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui)」を参照してください。  


## <a name="step-1-configure-a-google-developer-project"></a>手順 1:Google 開発者プロジェクトを構成する
最初に、Google Developers Console で新しいプロジェクトを作成して、Azure Active Directory (Azure AD) に後で追加できるクライアント ID とクライアント シークレットを取得します。 
1. https://console.developers.google.com で Google API に移動し、Google アカウントでサインインします。 共有のチーム Google アカウントを使用することをお勧めします。
2. サービスの使用条件への同意を求めるメッセージが表示されたらそのようにします。
3. 新しいプロジェクトを作成する: ページの左上隅にあるプロジェクトの一覧を選択し、 **[プロジェクトの選択]** ページで **[新しいプロジェクト]** を選択します。
4. **[新しいプロジェクト]** ページで、プロジェクトに名前 (「**Azure AD B2B**」など) を付け、 **[作成]** を選択します。 
   
   ![[新しいプロジェクト] ページを示すスクリーンショット。](media/google-federation/google-new-project.png)

4. **[API とサービス]** ページで、新しいプロジェクトの下にある **[View]\(表示\)** を選択します。

5. API カードで **[Go to APIs overview]\(API の概要に移動\)** を選択します。 **[OAuth 同意画面]** を選択します。

6. **[External]\(外部\)** を選択し、 **[Create]\(作成\)** を選択します。 

7. **[OAuth 同意画面]** で、 **[Application name]\(アプリケーション名\)** を入力します。

   ![Google の [OAuth 同意画面] を示すスクリーンショット。](media/google-federation/google-oauth-consent-screen.png)

8. **[Authorized domains]\(承認済みドメイン\)** セクションまでスクロールし、「**microsoftonline.com**」と入力します。

   ![[Authorized domains]\(承認済みドメイン\) セクションを示すスクリーンショット。](media/google-federation/google-oauth-authorized-domains.PNG)

9. **[保存]** を選択します。

10. **[Credentials]\(資格情報\)** を選択します。 **[Create credentials]\(認証情報の作成\)** メニューで、 **[OAuth client ID]\(OAuth クライアント ID\)** を選択します。

    ![Google API の資格情報作成メニューを示すスクリーンショット。](media/google-federation/google-api-credentials.png)

11. **[アプリケーションの種類]** で **[Web アプリケーション]** を選択します。 アプリケーションの適切な名前を指定します (**Azure AD B2B** など)。 **[Authorized redirect URIs]\(承認されたリダイレクト URI\)** に、次の URI を入力します。
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>(`<tenant ID>` はご利用のテナントの ID です)
   
    > [!NOTE]
    > テナント ID を確認するには、[Azure portal](https://portal.azure.com) に移動します。 **[Azure Active Directory]** で **[プロパティ]** を選択し、 **[テナント ID]** をコピーします。

    ![[Authorized redirect URIs]\(承認されたリダイレクト URI\) セクションを示すスクリーンショット。](media/google-federation/google-create-oauth-client-id.png)

12. **［作成］** を選択します クライアント ID とクライアント シークレットをコピーします。 これらは、Azure portal で ID プロバイダーを追加するときに使用します。

    ![OAuth クライアント ID とクライアント シークレットを示すスクリーンショット。](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>手順 2:Azure AD で Google フェデレーションを構成する 
次に、Google クライアント ID とクライアント シークレットを設定します。 Azure portal または PowerShell を使用して行うことができます。 自分自身を招待することで、Google フェデレーションの構成をテストしてください。 Gmail アドレスを使用し、招待を自分の招待された Google アカウントと引き換えてみます。 

**Azure portal で Google フェデレーションを構成するには** 
1. [Azure ポータル](https://portal.azure.com)にアクセスします。 左側のペインで、 **[Azure Active Directory]** を選択します。 
2. **[外部 ID]** を選択します。
3. **[すべての ID プロバイダー]** を選択し、 **[Google]** ボタンを選択します。
4. 前に取得したクライアント ID とクライアント シークレットを入力します。 **[保存]** を選択します。 

   ![Google ID プロバイダーの追加ページを示すスクリーンショット。](media/google-federation/google-identity-provider.png)

**PowerShell を使用して Google フェデレーションを構成するには**
1. 最新バージョンの Azure AD PowerShell for Graph モジュールをインストールします ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. 次のコマンドを実行します。`Connect-AzureAD`
3. サインイン プロンプトで、マネージド グローバル管理者アカウントを使用してサインインします。  
4. 次のコマンドを実行します。 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > クライアント ID とクライアント シークレットは、「手順 1: Google 開発者プロジェクトを構成する」で作成したアプリのものを使用します。 詳細については、「[New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview&preserve-view=true)」を参照してください。 
 
## <a name="how-do-i-remove-google-federation"></a>Google フェデレーションを削除する方法
Google フェデレーション セットアップは削除できます。 そのようにした場合、既に招待を引き換え済みの Google ゲスト ユーザーは、サインインできなくなります。 しかし、ディレクトリから削除し、再招待することで、リソースへのアクセス権をもう一度付与することができます。 
 
**Azure AD ポータルで Google フェデレーションを削除するには**
1. [Azure ポータル](https://portal.azure.com)にアクセスします。 左側のペインで、 **[Azure Active Directory]** を選択します。 
2. **[外部 ID]** を選択します。
3. **[すべての ID プロバイダー]** を選択します。
4. **[Google]** の行で、省略記号ボタン **[...]** を選択して、 **[削除]** を選択します。 
   
   ![ソーシャル ID プロバイダーの [削除] ボタンを示すスクリーンショット。](media/google-federation/google-social-identity-providers.png)

1. **[はい]** を選択して、削除を確認します。 

**PowerShell を使用して Google フェデレーションを削除するには** 
1. 最新バージョンの Azure AD PowerShell for Graph モジュールをインストールします ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. `Connect-AzureAD` を実行します。  
4. サインイン プロンプトで、マネージド グローバル管理者アカウントを使用してサインインします。  
5. 次のコマンドを入力します。

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > 詳細については、「[Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview&preserve-view=true)」を参照してください。
