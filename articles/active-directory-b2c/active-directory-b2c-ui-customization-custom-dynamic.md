---
title: カスタム ポリシーを使用して Azure Active Directory B2C ユーザー インターフェイス (UI) を動的にカスタマイズする | Microsoft Docs
description: 実行時に動的に変化する HTML5/CSS コンテンツで複数のブランド エクスペリエンスをサポートします。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2017
ms.author: marsma
ms.subservice: b2c
ms.openlocfilehash: e1abdfa8bc47f42f7373760370588c0bc41fc1dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66507779"
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Azure Active Directory B2C:カスタム ポリシーを使用して動的コンテンツを含む UI を構成する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) カスタム ポリシーを使用して、クエリ文字列のパラメーターを送信することができます。 パラメーターを HTML エンドポイントに渡すことで、ページのコンテンツを動的に変更することができます。 たとえば、Web またはモバイル アプリケーションから渡すパラメーターに基づいて、Azure AD B2C サインアップまたはサインイン ページの背景イメージを変更できます。 

## <a name="prerequisites"></a>前提条件
この記事では、カスタム ポリシーを使用して、*動的コンテンツ*を含む Azure AD B2C ユーザー インターフェイスをカスタマイズする方法に重点を置いて説明します。 作業を開始する場合は、「[Azure Active Directory B2C: カスタム ポリシーでの UI カスタマイズの構成](active-directory-b2c-ui-customization-custom.md)」を参照してください。 

>[!NOTE]
>Azure AD B2C 記事「[Azure Active Directory B2C: カスタム ポリシーでの UI カスタマイズの構成](active-directory-b2c-ui-customization-custom.md)」では、以下の基本について説明しています。
> * ページのユーザー インターフェイス (UI) カスタマイズ機能
> * サンプル コンテンツを使用してページの UI カスタマイズ機能をテストするための重要なツール。
> * ページの種類ごとの UI のコア要素。
> * この機能を適用する場合のベスト プラクティス。

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>HTML5/CSS テンプレートへのリンクをユーザー体験に追加する

カスタム ポリシーでは、コンテンツ定義で、指定した UI の手順に使用される HTML5 ページの URI を定義します (サインインまたはサインアップ ページなど)。 基本ポリシーでは、(CSS で) HTML5 ファイルの URI を指定することで、既定の外観を定義します。 拡張機能ポリシーでは、HTML5 ファイルの LoadUri をオーバーライドすることで、外観を変更できます。 コンテンツ定義には、必要に応じて HTML5/CSS ファイルを作成することで定義される、外部コンテンツへの URL が含まれます。 

`ContentDefinitions` セクションには一連の `ContentDefinition` XML 要素が含まれます。 `ContentDefinition` 要素の ID 属性は、コンテンツ定義に関連するページの種類を指定します。 つまり、要素では、カスタム HTML5/CSS テンプレートが適用されるコンテキストを定義します。 次の表で、IEF エンジンで認識されたコンテンツ定義 ID のセットと、それに関連するページの種類について説明します。

| コンテンツ定義 ID | 既定の HTML5 テンプレート| 説明 | 
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **エラー ページ**。 このページは、例外またはエラーが発生した場合に表示されます。 |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **ID プロバイダーの選択ページ**。 このページには、サインイン時にユーザーが選択できる ID プロバイダーが一覧表示されます。 ID プロバイダーは、通常、エンタープライズ ID プロバイダー、ソーシャル ID プロバイダー (Facebook や Google+ など)、ローカル アカウントのいずれかです。 |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **サインアップのための ID プロバイダーの選択**。 このページには、サインアップ時にユーザーが選択できる ID プロバイダーが一覧表示されます。 ID プロバイダーは、エンタープライズ ID プロバイダー、ソーシャル ID プロバイダー (Facebook や Google+ など)、ローカル アカウントのいずれかです。 |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **パスワードを忘れた場合**。 このページには、ユーザーがパスワードのリセットを開始するために入力する必要があるフォームが含まれます。  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **ローカル アカウントのサインイン ページ**。 このページには、メール アドレスまたはユーザー名に基づいたローカル アカウントでサインインするためのフォームが含まれます。 このフォームには、テキスト入力ボックスとパスワード入力ボックスを含めることができます。 |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **ローカル アカウントのサインアップ ページ**。 このページには、メール アドレスまたはユーザー名に基づいたローカル アカウントでサインアップするためのフォームが含まれます。 このフォームには、テキスト入力ボックス、パスワード入力ボックス、ラジオ ボタン、単一選択ドロップダウン ボックス、複数選択チェック ボックスなど、さまざまな入力コントロールを含めることができます。 |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **多要素認証ページ**。 このページでは、ユーザーがサインアップまたはサインインするときに、(文字または音声を使用して) 電話番号を確認することができます。 |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **ソーシャル アカウントのサインアップ ページ**。 このページには、ソーシャル ID プロバイダーから提供された既存のアカウントを使用してサインアップするときに、ユーザーが入力する必要のあるフォームが含まれます。 このページは、パスワード入力フィールドを除いて、上記のソーシャル アカウントのサインアップ ページに似ています。 |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **プロファイルの更新ページ**。 このページには、ユーザーがプロファイルを更新する場合にアクセスできるフォームが含まれます。 このページは、パスワード入力フィールドを除いて、ソーシャル アカウントのサインアップ ページに似ています。 |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **統合されたサインアップ ページまたはサインイン ページ**。 このページでは、ユーザーのサインアップおよびサインイン プロセスを処理します。 ユーザーは、エンタープライズ ID プロバイダー、ソーシャル ID プロバイダー (Facebook や Google+ など)、ローカル アカウントのいずれかを使用できます。  |

## <a name="serving-dynamic-content"></a>動的コンテンツを配信する
「[Azure Active Directory B2C: カスタム ポリシーでの UI カスタマイズの構成](active-directory-b2c-ui-customization-custom.md)」では、HTML5 ファイルを Azure Blob Storage にアップロードします。 こうした HTML5 ファイルは静的で、各要求で同じ HTML コンテンツをレンダリングします。 

この記事では、クエリ文字列パラメーターを受け入れて、適宜応答できる ASP.Net Web アプリを使用します。 

このチュートリアルでは次を行います。
* HTML5 テンプレートをホストする ASP.NET Core Web アプリケーションを作成します。 
* カスタム HTML5 テンプレート _unified.cshtml_ を追加します。 
* Azure App Service に Web アプリを発行します。 
* Web アプリのクロスオリジン リソース共有 (CORS) を設定します。
* `LoadUri` 要素をオーバーライドして、HTML5 ファイルを指定します。

## <a name="step-1-create-an-aspnet-web-app"></a>手順 1:ASP.NET Web アプリを作成する

1. Visual Studio で、 **[ファイル]**  >  **[新規作成]**  >  **[プロジェクト]** の順に選択して、プロジェクトを作成します。

2. **[新しいプロジェクト]** ウィンドウで、 **[Visual C#]**  >  **[Web]**  >  **[ASP.NET Core Web アプリケーション (.NET Core)]** の順に選択します。

3. アプリケーションに (*Contoso.AADB2C.UI* などの) 名前を付けて、 **[OK]** を選択します。

    ![新しい Visual Studio プロジェクトを作成する](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. **[Web アプリケーション]** テンプレートを選択します。

5. 認証を **[認証なし]** に設定します。

    ![Web アプリケーション テンプレートを選択する](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. **[OK]** を選択してプロジェクトを作成します。

## <a name="step-2-create-mvc-view"></a>手順 2:MVC ビューを作成する
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>手順 2.1:B2C 組み込み HTML5 テンプレートをダウンロードする
カスタム HTML5 テンプレートは、Azure AD B2C 組み込み HTML5 テンプレートに基づいています。 [unified.html ファイル](https://login.microsoftonline.com/static/tenant/default/unified.cshtml)をダウンロードするか、テンプレートを[スターター パック](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip)からダウンロードできます。 この HTML5 ファイルを使用して、統合されたサインアップまたはサインイン ページを作成します。

### <a name="step-22-add-the-mvc-view"></a>手順 2.2:MVC ビューを追加する
1. Views フォルダーの下の Home フォルダーを右クリックし、 **[追加]**  >  **[新しいアイテム]** の順に選択します。

    ![MVC の新しいアイテムを追加する](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. **[新しいアイテムの追加 - Contoso.AADB2C.UI]** ウィンドウで、 **[Web]、[ASP.NET]** の順に選択します。

3. **[MVC ビュー ページ]** を選択します。

4. **[名前]** ボックスで、名前を **unified.cshtml** に変更します。

5. **[追加]** を選択します。

    ![MVC ビューを追加する](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. *unified.cshtml* ファイルがまだ開いていない場合は、ダブルクリックして開いてから、ファイルのコンテンツをクリアします。

7. このチュートリアルでは、レイアウト ページへの参照を削除します。 次のコード スニペットを _unified.cshtml_ に追加します。

    ```csharp
    @{
        Layout = null;
    }
    ```

8. Azure AD B2C 組み込み HTML5 テンプレートからダウンロードした _unified.cshtml_ ファイルを開きます。

9. 1 つのアットマーク (@) を 2 つのアットマーク (@@) に置き換えます。

10. ファイルのコンテンツをコピーして、レイアウト定義の下に貼り付けます。 コードは次のようになります。

    ![HTML5 を追加した後の unified.cshtml ファイル](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>手順 2.3:背景イメージを変更する

`ID` の値 *background_background_image* を含む `<img>` 要素を見つけて、`src` の値を **https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1** 、または任意の背景イメージに置き換えます。

![ページ背景の変更](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>手順 2.4:MVC コントローラーにビューを追加する

1. **Controllers\HomeController.cs** を開いて、次のメソッドを追加します。 

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    このコードは、メソッドが*ビュー* テンプレート ファイルを使用して、ブラウザーへの応答をレンダリングする必要があることを指定します。 *ビュー* テンプレート ファイルの名前は明示的には指定しないため、MVC では、 */Views/Home* フォルダーの _unified.cshtml_ ビュー ファイルを使用するように既定で設定されます。
    
    _unified_ メソッドを追加した後のコードは次のようになります。
    
    ![ビューをレンダリングするためにコントローラーを変更する](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. Web アプリをデバッグし、_unified_ ページにアクセスできることを確認します (例: `http://localhost:<Port number>/Home/unified`)。

### <a name="step-25-publish-to-azure"></a>手順 2.5:Azure に発行する
1. **ソリューション エクスプローラー**で **Contoso.AADB2C.UI** プロジェクトを右クリックしてから、 **[発行]** を選択します。

    ![Microsoft Azure App Service への発行](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. **[Microsoft Azure App Service]** タイルを選択してから、 **[発行]** を選択します。

    ![新しい Microsoft Azure App Service の作成](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    **[App Service の作成]** ウィンドウが開きます。 ここで、Azure で ASP.NET Web アプリを実行するために必要なすべての Azure リソースの作成を開始できます。

    > [!NOTE]
    > 発行の詳細については、「[Azure に ASP.NET Web アプリを作成する](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet)」を参照してください。

3. **[Web アプリ名]** ボックスに、一意のアプリ名 (有効な文字は a-z、A-Z、0-9、およびハイフン (-)) を入力します。 Web アプリの URL は`http://<app_name>.azurewebsites.NET`です。`<app_name>` には Web アプリの名前を指定します。 自動的に生成される名前をそのまま使用してもかまいません。この名前は一意になっています。

4. **[作成]** をクリックして、Azure リソースの作成を開始します。

    ![App Service プロパティの入力](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    作成プロセスの完了後に、ウィザードにより Azure に ASP.NET Web アプリが発行され、既定のブラウザーでアプリが起動されます。

5. _unified_ ページの URL をコピーします (例: _https://<app_name>.azurewebsites.net/home/unified_)。

## <a name="step-3-configure-cors-in-azure-app-service"></a>手順 3:Azure App Service で CORS を構成する
1. [Azure Portal](https://portal.azure.com/) で、 **[App Services]** を選択してから、API アプリの名前を選択します。

    ![Azure Portal で API アプリを選択する](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. **[設定]** セクションの **[API]** セクションで、 **[CORS]** を選択します。

    ![CORS 設定の選択](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. **[CORS]** ウィンドウの **[許可される元のドメイン]** ボックスで、次のいずれかを実行します。

    * JavaScript の呼び出し元として許可する URL を入力します。 入力する URL ではすべて小文字を使用する必要があります。
    * アスタリスク (*) を入力して、元のドメインをすべて受け入れるように指定します。

4. **[保存]** を選択します。

    ![[CORS] ウィンドウ](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    **[保存]** を選択すると、API アプリは、指定した URL からの JavaScript 呼び出しを受け入れます。 

## <a name="step-4-html5-template-validation"></a>手順 4:HTML5 テンプレートの検証
HTML5 テンプレートを使用する準備はできています。 ただし、`ContentDefinition` では使用できません。 `ContentDefinition` をカスタム ポリシーに追加する前に、以下を確認します。
* コンテンツが HTML5 に準拠しており、アクセス可能である。
* コンテンツ サーバーで CORS が有効になっている。

    >[!NOTE]
    >コンテンツをホストしているサイトで CORS が有効になっており、CORS 要求をテストできることを確認するには、[test-cors.org](https://test-cors.org/) Web サイトにアクセスします。 

* コンテンツが **HTTPS** 経由で安全に配信される。
* すべてのリンク、CSS コンテンツ、およびイメージで、`https://yourdomain/content` などの "*絶対 URL*" を使用している。

## <a name="step-5-configure-your-content-definition"></a>手順 5:コンテンツ定義を構成する
`ContentDefinition` を構成するには、次の操作を行います。
1. ポリシーの基本ファイル (*TrustFrameworkBase.xml* など) を開きます。

2. `<ContentDefinitions>` 要素を検索し、`<ContentDefinitions>` ノードのコンテンツ全体をコピーします。

3. 拡張ファイル (*TrustFrameworkExtensions.xml* など) を開いてから、`<BuildingBlocks>` 要素を検索します。 要素が存在しない場合は追加します。

4. コピーした `<ContentDefinitions>` ノードのコンテンツ全体を `<BuildingBlocks>` 要素の子として貼り付けます。 

5. コピーした XML で `Id="api.signuporsignin"` を含む `<ContentDefinition>` ノードを検索します。

6. `LoadUri` の値を _~/tenant/default/unified_ から _https://<アプリ名>.azurewebsites.net/home/unified_ に変更します。  
    カスタム ポリシーは次のようになります。
    
    ![コンテンツ定義](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>手順 6:ポリシーをテナントにアップロードする
1. [Azure Portal](https://portal.azure.com) で、[Azure AD B2C テナントのコンテキスト](active-directory-b2c-navigate-to-b2c-context.md)に切り替えてから、 **[Azure AD B2C]** を選択します。

2. **[Identity Experience Framework]** を選択します。

3. **[すべてのポリシー]** を選択します。

4. **[ポリシーのアップロード]** を選択します。

5. **[ポリシーが存在する場合は上書きする]** チェック ボックスをオンにします。

6. *TrustFrameworkExtensions.xml* ファイルをアップロードし、検証に合格することを確認します。

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>手順 7:[今すぐ実行] を使用してカスタム ポリシーをテストする
1. **[Azure AD B2C の設定]** を選択してから、 **[Identity Experience Framework]** を選択します。

    >[!NOTE]
    >[今すぐ実行] を使用するには、テナントに少なくとも 1 つのアプリケーションが事前登録されている必要があります。 アプリケーションを登録する方法については、 Azure AD B2C の[概要](active-directory-b2c-get-started.md)に関する記事または[アプリケーションの登録](active-directory-b2c-app-registration.md)に関する記事を参照してください。

2. アップロードした証明書利用者 (RP) カスタム ポリシーである **B2C_1A_signup_signin** を開いてから、 **[今すぐ実行]** を選択します。  
    以前に作成した背景でカスタム HTML5 を表示できます。

    ![サインアップまたはサインイン ポリシー](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>手順 8:動的なコンテンツの追加
_campaignId_ という名前のクエリ文字列パラメーターに基づいて背景を変更します。 パラメーターは、証明書利用者アプリケーション (Web およびモバイル アプリ) によって Azure AD B2C に送信されます。 使用しているポリシーでこのパラメーターが読み取られ、その値が HTML5 テンプレートに送信されます。 

### <a name="step-81-add-a-content-definition-parameter"></a>手順 8.1:コンテンツ定義パラメーターを追加する

次のようにして、`ContentDefinitionParameters` 要素を追加します。
1. ポリシーの *SignUpOrSignin* ファイル (*SignUpOrSignin.xml* など) を開きます。

2. `<DefaultUserJourney>` ノードの下に `UserJourneyBehaviors` ノードを追加します。  

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ContentDefinitionParameters>
          <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        </ContentDefinitionParameters>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>手順 8.2:クエリ文字列パラメーターを受け入れるようにコードを変更し、背景イメージを置き換える
campaignId パラメーターを受け入れるように HomeController `unified` メソッドを変更します。 その後、このメソッドでパラメーターの値が確認され、適宜、`ViewData["background"]` 変数が設定されます。

1. *Controllers\HomeController.cs* ファイルを開いてから、次のコード スニペットを追加して `unified` メソッドを変更します。

    ```csharp
    public IActionResult unified(string campaignId)
    {
        // If campaign ID is Hawaii, show Hawaii background
        if (campaignId != null && campaignId.ToLower() == "hawaii")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1";
        }
        // If campaign ID is Tokyo, show Tokyo background
        else if (campaignId != null && campaignId.ToLower() == "tokyo")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19666_en_1";
        }
        // Default background
        else
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/18983_en_1";
        }

        return View();
    }

    ```

2. ID `background_background_image` の `<img>` 要素を見つけて、`src` の値を `@ViewData["background"]` に置き換えます。

    ![ページ背景の変更](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>手順 8.3:変更をアップロードして、ポリシーを発行する
1. Visual Studio プロジェクトを Azure App Service に発行します。

2. *SignUpOrSignIn.xml* ポリシーを Azure AD B2C にアップロードします。

3. アップロードした RP カスタム ポリシーである **B2C_1A_signup_signin** を開いてから、 **[今すぐ実行]** を選択します。  
    以前に表示されていたものと同じ背景イメージが表示されます。

4. ブラウザーのアドレス バーから URL をコピーします。

5. _campaignId_ クエリ文字列パラメーターを URI に追加します。 たとえば、以下のイメージのように、`&campaignId=hawaii` を追加します。

    ![ページ背景の変更](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. **Enter** キーを押して、ハワイの背景イメージを表示します。

    ![ページ背景の変更](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. 値を *Tokyo* に変更して **Enter** キーを押します。  
    ブラウザーに東京の背景が表示されます。

    ![ページ背景の変更](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>手順 9:残りのユーザー体験を変更する
サインイン ページの **[今すぐサインアップ]** リンクを選択すると、ブラウザーには、定義したイメージではなく、既定の背景イメージが表示されます。 このような動作になるのは、サインアップまたはサインイン ページのみを変更したためです。 残りの自己アサート コンテンツ定義を変更するには、次のようにします。
1. "手順 2" に戻り、次の操作を行います。

    a. *selfasserted* ファイルをダウンロードします。

    b. ファイル コンテンツをコピーします。

    c. 新しいビュー *selfasserted* を作成します。

    d. *selfasserted* を **ホーム** コントローラーに追加します。

2. "手順 4" に戻り、次の操作を行います。 

    a. 拡張機能ポリシーで、`Id="api.selfasserted"`、`Id="api.localaccountsignup"`、および `Id="api.localaccountpasswordreset"` を含む `<ContentDefinition>` ノードを見つけます。

    b. `LoadUri` 属性を *selfasserted* URI に設定します。

3. "手順 8.2" に戻り、クエリ文字列パラメーターを受け入れるようにコードを変更します。ただし、ここでは *selfasserted* 関数に設定します。 

4. *TrustFrameworkExtensions.xml* ポリシーをアップロードし、検証に合格することを確認します。

5. ポリシーのテストを実行し、 **[今すぐサインアップ]** を選択して結果を確認します。

## <a name="optional-download-the-complete-policy-files-and-code"></a>(省略可能) 完全なポリシー ファイルとコードをダウンロードする
* [カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)チュートリアルの完了後に、独自のカスタム ポリシー ファイルを使用してシナリオを構築することをお勧めします。 参照用に[サンプルのポリシー ファイル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)が提供されています。
* 完全なコードは、[参照用のVisual Studio ソリューションのサンプル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)からダウンロードできます。




