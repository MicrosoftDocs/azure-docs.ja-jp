---
title: "AD FS 認証を使用した基幹業務 Azure アプリの作成 | Microsoft Docs"
description: "Azure App Service を使用してオンプレミス STS で認証を行う基幹業務アプリを作成する方法について説明します。 このチュートリアルでは、オンプレミス STS としての AD FS を対象にしています。"
services: app-service\web
documentationcenter: .net
author: cephalin
manager: erikre
editor: 
ms.assetid: 0fa9f7a1-37bd-4d11-845f-aeff6fc9e4ca
ms.service: app-service-web
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 08/31/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 29b7146837f8a88baebd67fc448954f01388d67b
ms.lasthandoff: 11/17/2016


---
# <a name="create-a-line-of-business-azure-app-with-ad-fs-authentication"></a>AD FS 認証を使用した基幹業務 Azure アプリの作成
この記事では、[Azure App Service](../app-service/app-service-value-prop-what-is.md) でオンプレミス [Active Directory フェデレーション サービス](http://technet.microsoft.com/library/hh831502.aspx)を ID プロバイダーとして使用して ASP.NET MVC 基幹業務アプリケーションを作成する方法について説明します。 このシナリオは、ディレクトリ データがオンサイトに格納されることが求められる組織で Azure App Service を使用して基幹業務アプリケーションを作成する場合に適しています。

> [!NOTE]
> Azure App Service における別のエンタープライズ認証や承認オプションの概要については、「 [Azure アプリでのオンプレミス Active Directory を使用した認証](web-sites-authentication-authorization.md)」をご覧ください。
> 
> 

<a name="bkmk_build"></a>

## <a name="what-you-will-build"></a>学習内容
Azure App Service Web アプリで次の機能を持つ基本的な ASP.NET アプリケーションを作成します。

* AD FS に対してユーザーの認証を行う。
* `[Authorize]` を使用して、操作ごとにユーザーを承認する。
* Visual Studio でのデバッグと App Service Web アプリへの発行の両方に対応する静的構成を使用する (1 回構成するだけで、いつでもデバッグおよび発行できる)。  

<a name="bkmk_need"></a>

## <a name="what-you-need"></a>必要なもの
[!INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

このチュートリアルを完了するには、以下が必要です。

* オンプレミス AD FS デプロイメント (このチュートリアルで使用したテスト ラボのチュートリアルについては、「 [Test Lab: Standalone STS with AD FS in Azure VM (for test only) (Azure VM での AD FS を使用したスタンドアロン STS に関するテスト ラボのページ (テストのみ))](https://blogs.msdn.microsoft.com/cephalin/2014/12/21/test-lab-standalone-sts-with-ad-fs-in-azure-vm-for-test-only/)」をご覧ください)
* 証明書利用者信頼を AD FS 管理で作成するためのアクセス許可
* Visual Studio 2013 Update 4 以降
* [Azure SDK 2.8.1](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) 以降。

<a name="bkmk_sample"></a>

## <a name="use-sample-application-for-line-of-business-template"></a>サンプル アプリケーションを基幹業務テンプレートとして使用する
このチュートリアルでは、Azure Active Directory チームによって作成されたサンプル アプリケーション ( [WebApp-WSFederation-DotNet](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet)) を使用します。 AD FS は WS-Federation をサポートするため、これをテンプレートとして使用すると、簡単に基幹業務アプリケーションを作成できます。 このアプリケーションには、次の機能があります。

* [WS-Federation](http://msdn.microsoft.com/library/bb498017.aspx) を使用してオンプレミスの AD FS デプロイメントに対して認証を行う。
* サインインとサインアウト機能
* Windows Identity Foundation の代わりに [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) を使用する。これは ASP.NET の進化形で、WIF よりも簡単に認証と承認を設定できます。

<a name="bkmk_setup"></a>

## <a name="set-up-the-sample-application"></a>サンプル アプリケーションを設定する
1. サンプル ソリューション [WebApp-WSFederation-DotNet](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) をローカル ディレクトリに複製またはダウンロードします。
   
   > [!NOTE]
   > [README.md](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet/blob/master/README.md) の手順では、Azure Active Directory を使用してアプリケーションを設定する方法を示しています。 このチュートリアルでは AD FS を使用して設定するため、代わりに以下の手順に従ってください。
   > 
   > 
2. ソリューションを開き、**ソリューション エクスプローラー**で Controllers\AccountController.cs を開きます。
   
   このコードでは、WS-Federation を使用してユーザーを認証するために、単に認証チャレンジを発行しています。 認証はすべて App_Start\Startup.Auth.cs を使用して構成します。
3. App_Start\Startup.Auth.cs を開きます。 `ConfigureAuth` メソッドには、次の行が含まれています。
   
       app.UseWsFederationAuthentication(
           new WsFederationAuthenticationOptions
           {
               Wtrealm = realm,
               MetadataAddress = metadata                                      
           });
   
   OWIN では、このスニペットが WS-Federation 認証を構成するのに必要な最小限のコードです。 これは、Web.config のいろいろな場所に XML が挿入される WIF よりもはるかに簡単で洗練されています。 必要な情報は、証明書利用者 (RP) の識別子と AD FS サービスのメタデータ ファイルの URL のみです。 次に例を示します。
   
   * RP 識別子: `https://contoso.com/MyLOBApp`
   * メタデータのアドレス: `http://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`
4. App_Start\Startup.Auth.cs で、次の静的文字列定義を変更します。  
   
   <pre class="prettyprint">
private static string realm = ConfigurationManager.AppSettings["ida:<mark>RPIdentifier</mark>"]; <mark><del>private static string aadInstance = ConfigurationManager.AppSettings["ida:AADInstance"];</del></mark>
   <mark><del>private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];</del></mark>
   <mark><del>private static string metadata = string.Format("{0}/{1}/federationmetadata/2007-06/federationmetadata.xml", aadInstance, tenant);</del></mark>
   <mark>private static string metadata = string.Format("https://{0}/federationmetadata/2007-06/federationmetadata.xml", ConfigurationManager.AppSettings["ida:ADFS"]);</mark>
   
   <mark><del>string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant);</del></mark>
   </pre>
5. 次に、Web.config に対応する変更を加えます。 Web.config を開き、次のアプリ設定を変更します。  <pre class="prettyprint">
   &lt;appSettings&gt;
   &lt;add key="webpages:Version" value="3.0.0.0" /&gt;
   &lt;add key="webpages:Enabled" value="false" /&gt;
   &lt;add key="ClientValidationEnabled" value="true" /&gt;
   &lt;add key="UnobtrusiveJavaScriptEnabled" value="true" /&gt;
   <mark><del>&lt;add key="ida:Wtrealm" value="[Enter the App ID URI of WebApp-WSFederation-DotNet https://contoso.onmicrosoft.com/WebApp-WSFederation-DotNet]" /&gt;</del></mark>
   <mark><del>&lt;add key="ida:AADInstance" value="https://login.windows.net" /&gt;</del></mark>
   <mark><del>&lt;add key="ida:Tenant" value="[Enter tenant name, e.g. contoso.onmicrosoft.com]" /&gt;</del></mark>
   <mark>&lt;add key="ida:RPIdentifier" value="[Enter the relying party identifier as configured in AD FS, e.g. https://localhost:44320/]" /&gt;</mark>
   <mark>&lt;add key="ida:ADFS" value="[Enter the FQDN of AD FS service, e.g. adfs.contoso.com]" /&gt;</mark>
   
   &lt;/appSettings&gt;
   </pre>
   
   それぞれの環境に合わせてキーの値を入力してください。
6. アプリケーションをビルドしてエラーがないことを確認します。

これで終了です。 これで、サンプル アプリケーションで AD FS を使用する準備が整いました。 ただし、後で AD FS のこのアプリケーションを使用して RP 信頼を構成する作業が残っています。

<a name="bkmk_deploy"></a>

## <a name="deploy-the-sample-application-to-azure-app-service-web-apps"></a>サンプル アプリケーションを Azure App Service Web アプリに展開する
ここでは、デバッグ環境を維持しながら App Service Web Apps で Web アプリにアプリケーションを発行します。 AD FS での RP 信頼を設定する前にアプリケーションを発行するため、この段階では認証はまだ機能しません。 ただし、この操作を行うと、後で RP 信頼を構成するために使用する Web アプリの URL を確認できます。

1. プロジェクトを右クリックし、 **[発行]**を選択します。
   
    ![](./media/web-sites-dotnet-lob-application-adfs/01-publish-website.png)
2. **[Microsoft Azure App Service]**を選択します。
3. Azure にサインインしていない場合は、 **[サインイン]** をクリックし、Azure サブスクリプションの Microsoft アカウントを使用してサインインします。
4. サインインしたら、 **[新規]** をクリックして Web アプリを作成します。
5. すべての必須フィールドに必要事項を入力します。 後でオンプレミス データに接続するため、この Web アプリ用のデータベースは作成しません。
   
    ![](./media/web-sites-dotnet-lob-application-adfs/02-create-website.png)
6. **[作成]**をクリックします。 Web アプリが作成されると、[Web の発行] ダイアログが開きます。
7. **[宛先 URL]** で、**http** を **https** に変更します。 後で使用するために URL 全体をテキスト エディターにコピーします。 **[発行]**をクリックします。
   
    ![](./media/web-sites-dotnet-lob-application-adfs/03-destination-url.png)
8. Visual Studio で、プロジェクトの **Web.Release.config** を開きます。 次の XML を `<configuration>` タグに挿入し、発行した Web アプリの URL でキー値を置き換えます。  
   
   <pre class="prettyprint">
   &lt;appSettings&gt;
   &lt;add key="ida:RPIdentifier" value="<mark>[e.g. https://mylobapp.azurewebsites.net/]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" /&gt;
   &lt;/appSettings&gt;</pre>

この作業を完了すると、Visual Studio のデバッグ環境用と Azure に発行した Web アプリ用の 2 つの RP 識別子がプロジェクトに構成されたことになります。 この 2 つの環境のそれぞれに対して AD FS に RP 信頼を設定します。 デバッグ時は、Web.config のアプリ設定を使用することで、 **デバッグ** 構成で AD FS を使用できるようになります。 発行されると (既定では **リリース** 構成が発行されます)、変換された Web.config がアップロードされ、アプリ設定への変更が Web.Release.config に反映されます。

Azure で発行した Web アプリをデバッガーにアタッチする場合 (つまり、発行した Web アプリのコードのデバッグ シンボルをアップロードする必要があります)、Azure デバッグの Debug 構成のクローンを作成できます。このとき、Web.Release.config のアプリ設定を使用するカスタム Web.config 変換 (たとえば、Web.AzureDebug.config) が使用されます。 これにより、静的構成を異なる環境間で維持できます。

<a name="bkmk_rptrusts"></a>

## <a name="configure-relying-party-trusts-in-ad-fs-management"></a>AD FS 管理で証明書利用者信頼を構成する
AD FS を使用してサンプル アプリケーションの認証を実際に行う前に、AD FS 管理で RP 信頼を構成する必要があります。 デバッグ環境用と発行した Web アプリ用の 2 つの別個の RP 信頼を設定する必要があります。

> [!NOTE]
> 両方の環境に対して次の手順を繰り返してください。
> 
> 

1. AD FS サーバーで、AD FS の管理権限を持つ資格情報でログインします。
2. AD FS 管理を開きます。 **[AD FS]\[信頼関係]\[証明書利用者信頼]** を右クリックし、**[証明書利用者信頼の追加]** を選択します。
   
   ![](./media/web-sites-dotnet-lob-application-adfs/1-add-rptrust.png)
3. **[データ ソースの選択]** ページで、**[証明書利用者に関するデータを手動で入力する]** を選択します。 
   
   ![](./media/web-sites-dotnet-lob-application-adfs/2-enter-rp-manually.png)
4. **[表示名の指定]** ページで、アプリケーションの表示名を入力し、**[次へ]** をクリックします。
5. **[プロトコルの選択]** ページで、**[次へ]** をクリックします。
6. **[証明書の構成]** ページで、**[次へ]** をクリックします。
   
   > [!NOTE]
   > ここでは既に HTTPS を使用しているため、暗号化トークンの設定は任意です。 このページで AD FS からのトークンの暗号化を設定する場合は、トークンの暗号化解除ロジックをコードに追加する必要があります。 詳細については、 [OWIN WS-Federation ミドルウェアを手動で構成して暗号化トークンを受け入れる方法](http://chris.59north.com/post/2014/08/21/Manually-configuring-OWIN-WS-Federation-middleware-and-accepting-encrypted-tokens.aspx)に関するページをご覧ください。
   > 
   > 
7. 次の手順に進む前に、Visual Studio プロジェクトの情報を 1 つ確認する必要があります。 プロジェクトのプロパティで、アプリケーションの **SSL URL** をメモに記録します。 
   
   ![](./media/web-sites-dotnet-lob-application-adfs/3-ssl-url.png)
8. AD FS 管理に戻り、**証明書利用者信頼の追加ウィザード**の **[URL の構成]** ページで **[WS-Federation パッシブ プロトコルのサポートを有効にする]** チェック ボックスをオンにし、前の手順でメモに記録した Visual Studio プロジェクトの SSL URL を入力します。 次に、 **[次へ]**をクリックします。
   
   ![](./media/web-sites-dotnet-lob-application-adfs/4-configure-url.png)
   
   > [!NOTE]
   > この URL は、認証が成功した後にクライアントを転送する宛先を指定します。 デバッグ環境では、 <code>https://localhost:&lt;port&gt;/</code>にする必要があります。 発行した Web アプリに対しては、Web アプリの URL を指定します。
   > 
   > 
9. **[識別子の構成]** ページで、プロジェクトの SSL URL が表示されていることを確認し、**[次へ]** をクリックします。 ウィザードの最後まで **[次へ]** をクリックして、既定値を受け入れます。
   
   > [!NOTE]
   > Visual Studio プロジェクトの App_Start\Startup.Auth.cs では、フェデレーション認証時にこの識別子が <code>WsFederationAuthenticationOptions.Wtrealm</code> の値に一致します。 既定では、前の手順のアプリケーションの URL が RP 識別子として追加されます。
   > 
   > 
10. これで、AD FS でのプロジェクトの RP アプリケーションの構成が完了しました。 次に、アプリケーションで必要な要求を送信するようにこのアプリケーションを構成します。 ウィザードの最後に **[クレーム規則の編集]** ダイアログが既定で表示されるため、すぐにこの作業を開始できます。 少なくとも次のクレームを構成します (かっこ内にスキーマを示します)。
    
    * 名前 (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name) - ASP.NET で `User.Identity.Name` を設定するために使用されます。
    * ユーザー プリンシパル名 (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn) - 組織内のユーザーを一意に識別するために使用されます。
    * ロールとしてのグループ メンバーシップ (http://schemas.microsoft.com/ws/2008/06/identity/claims/role) - コントローラーや操作を承認するために `[Authorize(Roles="role1, role2,...")]` 装飾と共に使用できます。 実際には、この方法がロールの承認に関して最も効率的な方法であるとは限りません。 AD ユーザーが常に数百のセキュリティ グループに属している場合、SAML トークンの数百のロール要求が発生します。 これに代わる方法は、特定のグループ内のユーザーのメンバーシップに応じて、条件付きで 1 つのロール クレームを送信する方法です。 ただし、このチュートリアルでは、単純なやり方を使用します。
    * 名前 ID (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier) - 偽造防止検証に使用できます。 偽装防止検証と連携させる方法の詳細については、「 **Azure Active Directory 認証を使用した基幹業務 Azure アプリの作成** 」の [基幹業務の機能の追加](web-sites-dotnet-lob-application-azure-ad.md#bkmk_crud)に関するセクションをご覧ください。
    
    > [!NOTE]
    > アプリケーション用に構成する必要があるクレームの種類は、アプリケーションのニーズによって決まります。 Azure Active Directory アプリケーション (RP 信頼など) でサポートされているクレームの一覧については、「 [サポートされているトークンとクレームの種類](http://msdn.microsoft.com/library/azure/dn195587.aspx)」をご覧ください。
    > 
    > 
11. [クレーム規則の編集] ダイアログで、 **[規則の追加]**をクリックします。
12. スクリーンショットに示すように名前、UPN、ロール要求を構成し、 **[完了]**をクリックします。
    
    ![](./media/web-sites-dotnet-lob-application-adfs/5-ldap-claims.png)
    
    次に、「 [Name Identifiers in SAML assertions (SAML アサーションの名前識別子)](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx)」に記載されている手順に従って、一時的な名前 ID 要求を作成します。
13. もう一度 **[規則の追加]** をクリックします。
14. **[カスタムの規則を使用して要求を送信]** を選択し、**[次へ]** をクリックします。
15. 次の規則言語を **[カスタムの規則]** ボックスに貼り付けます。この規則に **Per Session Identifier** という名前を付けて、**[完了]** をクリックします。  
    
    <pre class="prettyprint">
    c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] &amp;&amp;
    c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant"] => add( store = "_OpaqueIdStore", types = ("<mark>http://contoso.com/internal/sessionid</mark>"), query = "{0};{1};{2};{3};{4}", param = "useEntropy", param = c1.Value, param = c1.OriginalIssuer, param = "", param = c2.Value);
    </pre>
    
    これで、次のスクリーンショットのようなカスタム規則が作成されました。
    
    ![](./media/web-sites-dotnet-lob-application-adfs/6-per-session-identifier.png)
16. もう一度 **[規則の追加]** をクリックします。
17. **[入力方向のクレームを変換する]** を選択し、**[次へ]** をクリックします。
18. (カスタム規則で作成した要求の種類を使用して) スクリーンショットのように規則を構成し、 **[完了]**をクリックします。
    
    ![](./media/web-sites-dotnet-lob-application-adfs/7-transient-name-id.png)
    
    一時的な名前 ID 要求に関する手順については、「 [Name Identifiers in SAML assertions (SAML アサーションの名前識別子)](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx)」をご覧ください。
19. **[クレーム規則の編集]** ダイアログの **[適用]** をクリックします。 その結果、次のスクリーンショットのようになります。
    
    ![](./media/web-sites-dotnet-lob-application-adfs/8-all-claim-rules.png)
    
    > [!NOTE]
    > これらの手順を、デバッグ環境と発行した Web アプリの両方に対して繰り返します。
    > 
    > 

<a name="bkmk_test"></a>

## <a name="test-federated-authentication-for-your-application"></a>アプリケーションのフェデレーション認証をテストする
この段階で、AD FS に対するアプリケーションの認証ロジックをテストできます。 ここで使用している AD FS ラボ環境には、Active Directory (AD) のテスト グループに属しているテスト ユーザーが設定されています。

![](./media/web-sites-dotnet-lob-application-adfs/10-test-user-and-group.png)

デバッガーで認証をテストする場合、必要な操作は `F5`キーを押すことだけです。 発行した Web アプリでの認証をテストするには、対象の URL に移動します。

Web アプリケーションが読み込まれたら、 **[サインイン]**をクリックします。 AD FS によって選択された認証方法によって、ログイン ダイアログまたは AD FS によって提供されるログイン ページのどちらかが表示されます。 Internet Explorer 11 を使った例を次に示します。

![](./media/web-sites-dotnet-lob-application-adfs/9-test-debugging.png)

AD FS デプロイメントの AD ドメインのユーザーとしてログインすると、ホームページの上隅に**こんにちは、<User Name> さん。** というメッセージが表示されます。 " というメッセージが表示されます。 例を次に示します。

![](./media/web-sites-dotnet-lob-application-adfs/11-test-debugging-success.png)

これまで、次の処理が行われてきました。

* アプリケーションが正常に AD FS にアクセスし、対応する RP 識別子が AD FS データベースに見つかった。
* AD FS によって正常に AD ユーザーが認証され、アプリケーションのホームページにリダイレクトされた。
* AD FS によって正常に名前クレーム (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name) がアプリケーションに送信された (これは、ユーザー名が画面の隅に表示されたことで示されます)。 

名前クレームがない場合は、 **こんにちは、 さん。**という表示になります。 Views\Shared\_LoginPartial.cshtml の内容を見ると、ユーザー名を表示するために `User.Identity.Name` が使用されていることがわかります。 前に説明したように、ASP.NET では、認証されたユーザーの名前要求が SAML トークンで取得できる場合、その名前要求がこのプロパティに設定されます。 AD FS によって送信されるすべてのクレームを確認するには、Controllers\HomeController.cs の Index 操作メソッドにブレークポイントを設定します。 ユーザーが認証された後、 `System.Security.Claims.Current.Claims` コレクションを調べます。

![](./media/web-sites-dotnet-lob-application-adfs/12-test-debugging-all-claims.png) 

<a name="bkmk_authorize"></a>

## <a name="authorize-users-for-specific-controllers-or-actions"></a>特定のコントローラーまたは操作に対してユーザーを承認する
RP 信頼構成にロール要求としてグループ メンバーシップが含まれているため、コントローラーと操作の `[Authorize(Roles="...")]` 装飾内で直接これらを使用できます。 "作成、読み取り、更新、削除" (CRUD) パターンを持つ基幹業務アプリケーションでは、特定のロールが各操作にアクセスすることを承認できます。 ここでは、既存の Home コントローラーでこの機能を試してみましょう。

1. Controllers\HomeController.cs を開きます。
2. 認証されたユーザーが持つセキュリティ グループ メンバーシップを使用して、次のコードのように `About` と `Contact` のアクション メソッドを装飾します。  
   
    <pre class="prettyprint">
    <mark>[Authorize(Roles="Test Group")]</mark>
    public ActionResult About() { ViewBag.Message = "Your application description page.";
   
        return View();
    }
   
    <mark>[Authorize(Roles="Domain Admins")]</mark>
    public ActionResult Contact()  {      ViewBag.Message = "Your contact page.";
   
        return View();
    }  </pre>
   
    この AD FS ラボ環境では **Test User** を **Test Group** に追加しているため、Test Group を使用して `About` に関する承認をテストします。 `Contact` については、**Test User** が属していない **Domain Admins** を使用して失敗のケースをテストします。
3. `F5` キーを押してデバッガーを起動してサインインし、 **[About]**をクリックします。 認証されたユーザーによるこの操作の実行が承認されている場合、 `~/About/Index` ページが正常に表示されます。
4. 次に、**[Contact (連絡先)]** をクリックします。この例の **Test User** には、この操作は承認されません。 ただし、ブラウザーは AD FS にリダイレクトされ、次のメッセージが表示されます。
   
    ![](./media/web-sites-dotnet-lob-application-adfs/13-authorize-adfs-error.png)
   
    AD FS サーバーのイベント ビューアーでこのエラーを調べると、次の例外メッセージを確認できます。  
   
    <pre class="prettyprint">
    Microsoft.IdentityServer.Web.InvalidRequestException: MSIS7042: <mark>同じクライアント ブラウザー セッションで、最後の '11' 秒間に '6' 回の要求が行われました。</mark> 詳しくは、管理者に問い合わせてください。
   at Microsoft.IdentityServer.Web.Protocols.PassiveProtocolHandler.UpdateLoopDetectionCookie(WrappedHttpListenerContext context) at Microsoft.IdentityServer.Web.Protocols.WSFederation.WSFederationProtocolHandler.SendSignInResponse(WSFederationContext context, MSISSignInResponse response) at Microsoft.IdentityServer.Web.PassiveProtocolListener.ProcessProtocolRequest(ProtocolContext protocolContext, PassiveProtocolHandler protocolHandler) at Microsoft.IdentityServer.Web.PassiveProtocolListener.OnGetContext(WrappedHttpListenerContext context)  </pre>
   
    このエラーの理由は、ユーザーのロールが承認されない場合に MVC が既定で 401 Unauthorized を返すためです。 これが、ID プロバイダー (AD FS) への再認証クレームのトリガーとなります。 ユーザーは既に認証されているため、AD FS によって同じページが表示されることになります。これが原因で別の 401 が発行され、結果的にリダイレクト ループになります。 そこで、リダイレクト ループを続ける代わりに意味のメッセージを表示する単純なロジックで `HandleUnauthorizedRequest` の メソッドをオーバーライドします。
5. プロジェクトに AuthorizeAttribute.cs という名前のファイルを作成し、次のコードを貼り付けます。
   
        using System;
        using System.Web.Mvc;
        using System.Web.Routing;
   
        namespace WebApp_WSFederation_DotNet
        {
            [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
            public class AuthorizeAttribute : System.Web.Mvc.AuthorizeAttribute
            {
                protected override void HandleUnauthorizedRequest(AuthorizationContext filterContext)
                {
                    if (filterContext.HttpContext.Request.IsAuthenticated)
                    {
                        filterContext.Result = new System.Web.Mvc.HttpStatusCodeResult((int)System.Net.HttpStatusCode.Forbidden);
                    }
                    else
                    {
                        base.HandleUnauthorizedRequest(filterContext);
                    }
                }
            }
        }
   
    このオーバーライド コードは、認証されたにもかかわらず承認されない場合に HTTP 401 (Unauthorized) ではなく HTTP 403 (Forbidden) を送信します。
6. もう一度 `F5`キーを押してデバッガーを実行します。 **[Contact]** をクリックすると、(魅力的ではありませんが) より説明的なエラー メッセージが表示されます。
   
    ![](./media/web-sites-dotnet-lob-application-adfs/14-unauthorized-forbidden.png)
7. アプリケーションを Azure App Service Web アプリにもう一度発行して、ライブ アプリケーションの動作をテストします。

<a name="bkmk_data"></a>

## <a name="connect-to-on-premises-data"></a>オンプレミス データに接続する
基幹業務アプリケーションに Azure Active Directory ではなく AD FS を実装する 1 つの理由として、組織データをオフプレミスに保持する場合のコンプライアンスの問題があります。 Web アプリのデータ層として [SQL Database](/services/sql-database/) を使用することが許可されないため、Azure の Web アプリはオンプレミス データベースにアクセスする必要があることも意味しています。

Azure App Service Web Apps では、[ハイブリッド接続](../biztalk-services/integration-hybrid-connection-overview.md)と[仮想ネットワーク](web-sites-integrate-with-vnet.md)でオンプレミス データベースへのアクセスをサポートしています。 詳細については、「 [Azure App Service Web Apps で VNET 統合とハイブリッド環境を使用する方法 (ブログの投稿)](https://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/)」をご覧ください。

<a name="bkmk_resources"></a>

## <a name="further-resources"></a>他の関連リソース
* [SSL と Authorize 属性を使用してアプリケーションを保護する](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute)
* [Azure アプリでのオンプレミス Active Directory を使用した認証](web-sites-authentication-authorization.md)
* [Azure Active Directory 認証を使用した基幹業務 Azure アプリの作成](web-sites-dotnet-lob-application-azure-ad.md)
* [Visual Studio 2013 で ASP.NET のオンプレミス組織認証オプション (ADFS) を使用する方法に関するページ](http://www.cloudidentity.com/blog/2014/02/12/use-the-on-premises-organizational-authentication-option-adfs-with-asp-net-in-visual-studio-2013/)
* [VS2013 Web プロジェクトを WIF から Katana に移植する方法に関するページ](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/)
* [Active Directory フェデレーション サービスの概要に関するページ](http://technet.microsoft.com/library/hh831502.aspx)
* [WS-Federation 1.1 仕様](http://download.boulder.ibm.com/ibmdl/pub/software/dw/specs/ws-fed/WS-Federation-V1-1B.pdf?S_TACT=105AGX04&S_CMP=LP)


