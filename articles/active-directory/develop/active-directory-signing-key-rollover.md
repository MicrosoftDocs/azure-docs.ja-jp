---
title: Microsoft ID プラットフォームでの署名キーのロールオーバー
description: この記事では、Azure Active Directory の署名キーのロール オーバーのベスト プラクティスについて説明します
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 8/11/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.openlocfilehash: ce4917f968ef1664a1d41f4eaff162df116bda4f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035086"
---
# <a name="signing-key-rollover-in-the-microsoft-identity-platform"></a>Microsoft ID プラットフォームでの署名キーのロールオーバー
この記事では、セキュリティ トークンに署名するために Microsoft ID プラットフォームで使用される公開キーに関して、知っておく必要があることについて説明します。 これらのキーは定期的にロールオーバーされ、緊急時にはすぐにロールオーバーされる可能性があることにご注意ください。 Microsoft ID プラットフォームを使用するすべてのアプリケーションは、キーのロールオーバー プロセスをプログラムで処理できる必要があります。 ここではキーのしくみについて説明すると共に、アプリケーションへのロールオーバーの影響を評価する方法について説明します。また、必要に応じてキーのロールオーバーに対処できるよう、アプリケーションを更新したり、定期的な手動ロールオーバー プロセスを確立したりする方法について説明しています。

## <a name="overview-of-signing-keys-in-the-microsoft-identity-platform"></a>Microsoft ID プラットフォームでの署名キーの概要
Microsoft ID プラットフォームは、業界標準に基づいて構築された公開キー暗号化を使って、それ自体とそれを使用するアプリケーションの間での信頼を確立します。 具体的には、次のように機能します。Microsoft ID プラットフォームでは、公開キーと秘密キーのペアで構成される署名キーが使用されます。 認証に Microsoft ID プラットフォームを使用するアプリケーションにユーザーがサインインすると、Microsoft ID プラットフォームによって、そのユーザーに関する情報を含むセキュリティ トークンが作成されます。 このトークンは、Microsoft ID プラットフォームによって秘密キーを使って署名されてから、アプリケーションに返送されます。 トークンが有効であり、Microsoft ID プラットフォームからのものであることを確認するには、アプリケーションで、テナントの [OpenID Connect Discovery ドキュメント](https://openid.net/specs/openid-connect-discovery-1_0.html)または SAML/WS-Fed の[フェデレーション メタデータ ドキュメント](../azuread-dev/azure-ad-federation-metadata.md)に含まれる、Microsoft ID プラットフォームによって公開された公開キーを使って、トークンの署名を検証する必要があります。

セキュリティ上の理由から、Microsoft ID プラットフォームの署名キーは定期的にロールオーバーされ、緊急時には即座にロールオーバーされる場合があります。 これらのキー ロール間に設定または保証された時間はありません。Microsoft ID プラットフォームと統合されているすべてのアプリケーションは、発生頻度に関係なくキーのロールオーバー イベントをいつでも処理できる必要があります。 このロジックを備えていないアプリケーションが期限切れのキーを使ってトークンの署名の検証を試みると、サインイン要求が失敗します。  更新プログラムを 24 時間ごとにチェックすることをお勧めします。そのためには、不明なキー識別子でトークンが検出された場合に、主要なドキュメントが直ちに更新されるように調整します (多くても 5 分に 1 回)。 

OpenID Connect Discovery ドキュメントとフェデレーション メタデータ ドキュメントには、利用できる有効なキーが常に複数存在します。 1 つのキーがすぐにロールされて別のキーに置き換えられる可能性があるので、アプリケーションは、このドキュメントで指定されているどのキーでも使用できるようになっている必要があります。  存在するキーの数は、Microsoft ID プラットフォームの内部アーキテクチャに基づいて、新しいプラットフォーム、新しいクラウド、または新しい認証プロトコルのサポートに伴って時間の経過と共に変わる場合があります。 JSON 応答でのキーの順序とそれらが公開された順序はどちらも、アプリにとって意味があってはなりません。 

1 つの署名キーだけをサポートするアプリケーション、または署名キーを手動で更新する必要があるアプリケーションは、本質的に安全性と信頼性が低くなります。  これらは、[標準ライブラリ](reference-v2-libraries.md)を使用し、他のベスト プラクティスでも常に最新の署名キーを使用するように更新される必要があります。 

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>アプリケーションに影響が波及するかどうかの評価とその対処法
キー ロールオーバーへのアプリケーション側の対応は、アプリケーションの種類や使用されている ID プロトコル、ライブラリなどさまざまな要因によって異なります。 以下の各セクションでは、ごく一般的なアプリケーションにおけるキー ロールオーバーへの影響の有無を評価し、キーの自動ロールオーバーまたは手動更新に対応するようにアプリケーションを更新するうえでの指針を取り上げています。

* [リソースにアクセスするネイティブ クライアント アプリケーション](#nativeclient)
* [リソースにアクセスする Web アプリケーション/API](#webclient)
* [Azure App Service を使用して構築された、リソースを保護する Web アプリケーション/API](#appservices)
* [.NET OWIN OpenID Connect、WS-Fed、WindowsAzureActiveDirectoryBearerAuthentication のいずれかのミドルウェアを使用し、リソースを保護する Web アプリケーション/API](#owin)
* [.NET Core OpenID Connect と JwtBearerAuthentication のいずれかのミドルウェアを使用し、リソースを保護する Web アプリケーション/API](#owincore)
* [Node.js passport-azure-ad モジュールを使用し、リソースを保護する Web アプリケーション/API](#passport)
* [Visual Studio 2015 以降を使用して作成された、リソースを保護する Web アプリケーションや Web API](#vs2015)
* [Visual Studio 2013 を使用して作成された、リソースを保護する Web アプリケーション](#vs2013)
* Visual Studio 2013 を使用して作成された、リソースを保護する Web API
* [Visual Studio 2012 を使用して作成された、リソースを保護する Web アプリケーション](#vs2012)
* [Visual Studio 2010/2008 または Windows Identity Foundation を使用して作成された、リソースを保護する Web アプリケーション](#vs2010)
* [その他のライブラリが使用されているか、サポートされているプロトコルが手動で実装された、リソースを保護する Web アプリケーション/API](#other)

このガイダンスは、次のアプリケーションには適用 **されません** 。

* Azure AD アプリケーション ギャラリーから追加したアプリケーションについては (カスタム アプリケーションも含め)、キーの署名に関して別個のガイダンスがあります。 [詳細については、こちらをご覧ください。](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* アプリケーション プロキシ経由で発行されたオンプレミスのアプリケーションでは、キーの署名について配慮する必要はありません。

### <a name="native-client-applications-accessing-resources"></a><a name="nativeclient"></a>リソースにアクセスするネイティブ クライアント アプリケーション
リソース (つまり、 Microsoft Graph、KeyVault、Outlook API、その他の Microsoft API) にアクセスするだけのアプリケーションは、通常、トークンを取得してリソース所有者に渡すだけです。 アプリケーションでリソースを保護しない場合は、トークンを調べることはないため、トークンへの適切な署名は必要はありません。

デスクトップかモバイルかを問わず、ネイティブ クライアント アプリケーションはこのカテゴリに分類され、ロールオーバーの影響を受けません。

### <a name="web-applications--apis-accessing-resources"></a><a name="webclient"></a>リソースにアクセスする Web アプリケーション/API
リソース (つまり、 Microsoft Graph、KeyVault、Outlook API、その他の Microsoft API) にアクセスするだけのアプリケーションは、通常、トークンを取得してリソース所有者に渡すだけです。 アプリケーションでリソースを保護しない場合は、トークンを調べることはないため、トークンへの適切な署名は必要はありません。

トークンを要求するためのアプリ専用のフロー (クライアント資格情報/クライアント証明書) を使用する Web アプリケーションと Web API はこのカテゴリに分類され、ロールオーバーの影響を受けません。

### <a name="web-applications--apis-protecting-resources-and-built-using-azure-app-services"></a><a name="appservices"></a>Azure App Service を使用して構築された、リソースを保護する Web アプリケーション/API
Azure App Service の認証/承認 (EasyAuth) 機能には、キーのロールオーバーを自動的に処理するうえで必要なロジックがあります。

### <a name="web-applications--apis-protecting-resources-using-net-owin-openid-connect-ws-fed-or-windowsazureactivedirectorybearerauthentication-middleware"></a><a name="owin"></a>.NET OWIN OpenID Connect、WS-Fed、WindowsAzureActiveDirectoryBearerAuthentication のいずれかのミドルウェアを使用し、リソースを保護する Web アプリケーション/API
アプリケーションで .NET OWIN OpenID Connect、WS-Fed、WindowsAzureActiveDirectoryBearerAuthentication のいずれかのミドルウェアを使用している場合、キーのロールオーバーに自動的に対処するうえで必要なロジックがあらかじめ用意されています。

それらがアプリケーションで使用されているかどうかは、アプリケーションの Startup.cs または Startup.Auth.cs ファイルで次のスニペットを探すことで確認できます。

```csharp
app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
        // ...
    });
```

```csharp
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
        // ...
    });
```

```csharp
app.UseWindowsAzureActiveDirectoryBearerAuthentication(
    new WindowsAzureActiveDirectoryBearerAuthenticationOptions
    {
        // ...
    });
```

### <a name="web-applications--apis-protecting-resources-using-net-core-openid-connect-or--jwtbearerauthentication-middleware"></a><a name="owincore"></a>.NET Core OpenID Connect と JwtBearerAuthentication のいずれかのミドルウェアを使用し、リソースを保護する Web アプリケーション/API
アプリケーションで .NET Core OWIN OpenID Connect と JwtBearerAuthentication のいずれかのミドルウェアを使用している場合、キーのロールオーバーに自動的に対処するうえで必要なロジックがあらかじめ用意されています。

それらがアプリケーションで使用されているかどうかは、アプリケーションの Startup.cs または Startup.Auth.cs から次のスニペットを探すことで確認できます。

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
     });
```

### <a name="web-applications--apis-protecting-resources-using-nodejs-passport-azure-ad-module"></a><a name="passport"></a>Node.js passport-azure-ad モジュールを使用し、リソースを保護する Web アプリケーション/API
アプリケーションで Node.js passport-ad モジュールを使用している場合、キーのロールオーバーに自動的に対処するうえで必要なロジックがあらかじめ用意されています。

アプリケーションで passport-ad が使用されているかどうかは、アプリケーションの app.js から次のスニペットを探すことで確認できます。

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="web-applications--apis-protecting-resources-and-created-with-visual-studio-2015-or-later"></a><a name="vs2015"></a>Visual Studio 2015 以降を使用して作成された、リソースを保護する Web アプリケーションや Web API
アプリケーションが Visual Studio 2015 以降の Web アプリケーション テンプレートを使用して作成されており、 **[認証の変更]** メニューで **[職場または学校アカウント]** を選択した場合は、キーのロールオーバーに自動的に対処するうえで必要なロジックがあらかじめ用意されています。 このロジックは OpenID Connect Discovery ドキュメントからキーを取得してキャッシュし、定期的にそれらを更新するもので、OWIN OpenID Connect ミドルウェアに組み込まれています。

認証を手動でソリューションに追加した場合は、キーのロールオーバーに対応するうえで必要なロジックがアプリケーションに備わっていない可能性があります。 必要なロジックを独自に作成するか、[その他のライブラリが使用されているか、サポートされているプロトコルが手動で実装された Web アプリケーション/API](#other) に関するセクションの手順に従ってください。

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Visual Studio 2013 を使用して作成された、リソースを保護する Web アプリケーション
アプリケーションが Visual Studio 2013 の Web アプリケーション テンプレートを使用して作成されており、 **[認証の変更]** メニューで **[組織アカウント]** を選択した場合、キーのロールオーバーに自動的に対処するうえで必要なロジックがあらかじめ用意されています。 このロジックにより、プロジェクトに関連付けられた 2 つのデータベース テーブルに組織の一意識別子と署名キー情報が保存されます。 このデータベースの接続文字列は、プロジェクトの Web.config ファイル内に格納されています。

認証を手動でソリューションに追加した場合は、キーのロールオーバーに対応するうえで必要なロジックがアプリケーションに備わっていない可能性があります。 必要なロジックを独自に作成するか、 [その他のライブラリが使用されているか、サポートされているプロトコルが手動で実装された Web アプリケーション/API](#other)に関するセクションの手順に従ってください。

アプリケーションでロジックが適切に機能するかどうかは、以下の手順によって確認できます。

1. Visual Studio 2013 でソリューションを開き、右側のウィンドウで **[サーバー エクスプローラー]** タブをクリックします。
2. **[データ接続]** 、 **[既定の接続]** 、 **[テーブル]** の順に展開します。 **IssuingAuthorityKeys** テーブルを右クリックし、 **[テーブル データの表示]** をクリックします。
3. **IssuingAuthorityKeys** テーブルには少なくとも 1 つの行があり、キーの拇印の値に対応しています。 テーブル内の任意の行を削除します。
4. **Tenants** テーブルを右クリックし、 **[テーブル データの表示]** をクリックします。
5. **Tenants** テーブルには少なくとも 1 つの行があり、一意のディレクトリ テナント ID に対応しています。 テーブル内の任意の行を削除します。 **Tenants** テーブルと **IssuingAuthorityKeys** テーブルの両方で行を削除しないと、実行時にエラーが表示されます。
6. アプリケーションをビルドして実行します。 アカウントにログインすると、アプリケーションを停止できます。
7. **[サーバー エクスプローラー]** に戻り、**IssuingAuthorityKeys** テーブルと **Tenants** テーブルの値を確認します。 テーブルにはフェデレーション メタデータ ドキュメントから自動的に適切な情報が入力されています。

### <a name="web-apis-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Visual Studio 2013 を使用して作成された、リソースを保護する Web API
Visual Studio 2013 で Web API テンプレートを使用して Web API を作成し、 **[認証の変更]** メニューで **[組織アカウント]** を選択した場合、アプリケーションには既に必要なロジックが含まれています。

手動で認証を構成する場合は、以下の手順に従って、自動的にキー情報を更新するように Web API を構成する方法を確認してください。

次のコード スニペットは、フェデレーション メタデータ ドキュメントから最新のキーを取得し、 [JWT トークン ハンドラー](/previous-versions/dotnet/framework/security/json-web-token-handler) を使用してトークンを検証する方法を示しています。 このコード スニペットでは、データベース内か、構成ファイル内か、またはその他の場所に配置するかどうかにかかわらず、Microsoft ID プラットフォームからの将来のトークンを検証するキーを保持するために、独自のキャッシュ メカニズムが使用されることを前提としています。

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2012"></a><a name="vs2012"></a>Visual Studio 2012 を使用して作成された、リソースを保護する Web アプリケーション
アプリケーションが Visual Studio 2012 で作成されている場合、通常、アプリケーションは ID およびアクセス ツールを使用して構成されています。 また、通常は [発行者名レジストリの検証 (VINR)](/previous-versions/dotnet/framework/security/validating-issuer-name-registry)が使用されています。 VINR の役割は、信頼できる ID プロバイダー (Microsoft ID プラットフォーム) に関する情報、およびそれらによって発行されたトークンを検証するために使用されるキーに関する情報を維持することです。 また、VINR を使用すると、ディレクトリに関連付けられている最新のフェデレーション メタデータ ドキュメントをダウンロードし、構成が期限切れになっているかどうかを確認し、必要に応じて新しいキーでアプリケーションを更新することにより、Web.config ファイルに保存されているキー情報を自動的に更新することが容易になります。

Microsoft から提供されたコード サンプルまたはチュートリアルのいずれかを使用してアプリケーションを作成した場合、キーのロールオーバー ロジックは既にプロジェクトに含まれています。 以下に示すコードがプロジェクト内に存在していることを確認できます。 アプリケーションにこのロジックが含まれていない場合は、以下の手順を実行してロジックを追加し、正常に機能することを確認してください。

1. **[ソリューション エクスプローラー]** で、適切なプロジェクトの **System.IdentityModel** アセンブリへの参照を追加します。
2. **Global.asax.cs** ファイルを開き、次の using ディレクティブを追加します。
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. 次のメソッドを **Global.asax.cs** ファイルに追加します。
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. 次に示すように、**Global.asax.cs** 内の **Application_Start()** メソッドで **RefreshValidationSettings()** メソッドを呼び出します。
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

この手順を実行すると、アプリケーションの Web.config は、最新のキーなど、フェデレーション メタデータ ドキュメントの最新情報で更新されます。 この更新は、IIS のアプリケーション プールがリサイクルされるたびに行われます。IIS の既定では、アプリケーションは 29 時間ごとにリサイクルされます。

キーのロールオーバー ロジックが機能していることを確認するには、次の手順に従います。

1. アプリケーションで上記のコードが使用されていることを確認したら、**Web.config** ファイルを開き、 **\<issuerNameRegistry>** ブロックに移動して、以下の行を見つけます。
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. **\<add thumbprint="">** 設定で、どれか 1 文字を別の文字に置き換えて拇印の値を変更します。 **Web.config** ファイルを保存します。
3. アプリケーションをビルドし、実行します。 サインイン プロセスを完了できる場合、アプリケーションではディレクトリのフェデレーション メタデータ ドキュメントから必要な情報をダウンロードすることによってキーが正しく更新されています。 サインインで問題が発生する場合は、[Microsoft ID プラットフォームを使用した Web アプリケーションへのサインオンの追加](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect)に関する記事を読むか、次のコード サンプルをダウンロードして調べることによって、アプリケーションの変更が正しいことを確認してください: [Multi-Tenant Cloud Application for Azure Active Directory (Azure Active Directory 向けのマルチテナント クラウド アプリケーション)](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b)。

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2008-or-2010-and-windows-identity-foundation-wif-v10-for-net-35"></a><a name="vs2010"></a>Visual Studio 2008/2010 および .NET 3.5 用 Windows Identity Foundation (WIF) v1.0 で作成された、リソースを保護する Web アプリケーション
WIF v1.0 でアプリケーションを作成した場合、新しいキーを使用するようにアプリケーションの構成を自動的に更新するメカニズムは用意されていません。

* "*最も簡単な方法*" は、WIF SDK に含まれている FedUtil ツールを使用することです。このツールでは、最新のメタデータ ドキュメントを取得し、構成を更新できます。
* System 名前空間に最新バージョンの WIF が含まれている .NET 4.5 にアプリケーションを更新する。 その後、 [発行者名レジストリの検証 (VINR)](/previous-versions/dotnet/framework/security/validating-issuer-name-registry) を使用して、アプリケーションの構成の自動更新を実行できます。
* このガイダンスの末尾にある手順に従って、手動ロールオーバーを実行します。

FedUtil を使用して構成を更新する手順は、次のようになります。

1. Visual Studio 2008 または 2010 をデプロイしてあるマシンに WIF v1.0 SDK がインストールされていることを確認します。 インストールされていない場合は、[ここからダウンロード](https://www.microsoft.com/download/details.aspx?id=17331) できます。
2. Visual Studio でソリューションを開き、該当するプロジェクトを右クリックして、 **[Update federation metadata (フェデレーション メタデータの更新)]** を選択します。 このオプションが表示されない場合は、FedUtil と WIF v1.0 SDK の少なくともどちらかがインストールされていません。
3. プロンプトで **[更新]** を選択してフェデレーション メタデータの更新を開始します。 アプリケーションがホストされているサーバー環境にアクセスできる場合は、FedUtil の [メタデータの自動更新スケジューラ](/previous-versions/windows-identity-foundation/ee517272(v=msdn.10))を使用することもできます。
4. **[完了]** をクリックして更新プロセスを完了します。

### <a name="web-applications--apis-protecting-resources-using-any-other-libraries-or-manually-implementing-any-of-the-supported-protocols"></a><a name="other"></a>その他のライブラリが使用されているか、サポートされているプロトコルが手動で実装された、リソースを保護する Web アプリケーション/API
その他何らかのライブラリを使用している場合またはサポートされているプロトコルを手動で実装した場合、OpenID Connect Discovery ドキュメントまたはフェデレーション メタデータ ドキュメントから確実にキーが取得されるようそのライブラリまたは実装内容を再確認する必要があります。 この点はたとえば、自分が記述したコードやライブラリのコードから、OpenID のディスカバリー ドキュメントまたはフェデレーション メタデータ ドキュメントの呼び出しを検索することによって確認できます。

キーがどこかに格納されている場合、またはアプリケーション内でハードコーディングされている場合は、キーを手動で取得し、このガイダンスの末尾にある手順に従って手動ロールオーバーを実行することで、適宜キーを更新できます。 将来、Microsoft ID プラットフォームでロールオーバーの周期が短くなった場合や、緊急のアウトオブバンド ロールオーバーが発生した場合に、中断やオーバーヘッドを避けるため、この記事で説明されているいずれかのアプローチを使って、**自動ロールオーバーをサポートするようにお客様のアプリケーションを拡張することを強くお勧めします**。

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>アプリケーションをテストして、影響を受けるかどうかを判別する
[この GitHub リポジトリ](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>アプリケーションで自動ロールオーバーがサポートされていない場合に手動ロールオーバーを実行する方法
アプリケーションで自動ロールオーバーがサポートされて **いない** 場合は、Microsoft ID プラットフォームの署名キーを定期的に監視し、適宜手動ロールオーバーを実行するプロセスを確立する必要があります。 [こちらの GitHub リポジトリ](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) には、これを実行する方法についてのスクリプトと手順が含まれています。
