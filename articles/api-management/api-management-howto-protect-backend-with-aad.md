---
title: "Azure Active Directory と API Management で Web API バックエンドを保護する | Microsoft Docs"
description: "Azure Active Directory と API Management で Web API バックエンドを保護する方法について説明します。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: f856ff03-64a1-4548-9ec4-c0ec4cc1600f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 690f820b3beb6b7d5d68732b94470602a92e9268
ms.openlocfilehash: bf0d72f97a32771f86bdff62029fb43e279b6842
ms.lasthandoff: 02/23/2017

---
# <a name="how-to-protect-a-web-api-backend-with-azure-active-directory-and-api-management"></a>Azure Active Directory と API Management で Web API バックエンドを保護する方法
次のビデオでは、Web API バックエンドをビルドし、Azure Active Directory と API Management で OAuth 2.0 プロトコルを使用して保護する方法を示します。  この記事では、ビデオで説明する手順の概要と追加情報を紹介します。 この 24 分間のビデオでは、次の手順について説明しています。

* Web API バックエンドをビルドし、AAD で保護する - 1:30 開始
* API Management に API をインポートする - 7:10 開始
* API を呼び出す開発者ポータルを構成する - 9:09 開始
* API を呼び出すデスクトップ アプリケーションを構成する - 18:08 開始
* 要求を事前承認する JWT 検証ポリシーを構成する - 20:47 開始

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Protecting-Web-API-Backend-with-Azure-Active-Directory-and-API-Management/player]
> 
> 

## <a name="create-an-azure-ad-directory"></a>Azure AD Directory を作成する
Azure Active Directory を使用して Web API バックエンドを保護するには、AAD テナントが必要です。 このビデオでは、 **APIMDemo** という名前のテナントを使用します。 AAD テナントを作成するには、[Azure クラシック ポータル](https://manage.windowsazure.com)にサインインし、**[新規]**->、**[App Services]**->、**[Active Directory]**、->**[ディレクトリ]**、->**[カスタム作成]** の順にクリックします。 

![Azure Active Directory][api-management-create-aad-menu]

この例では、**APIMDemo** という名前のディレクトリが既定のドメイン名 **DemoAPIM.onmicrosoft.com** で作成されます。 このディレクトリはビデオ全体で使用されます。

![Azure Active Directory][api-management-create-aad]

## <a name="create-a-web-api-service-secured-by-azure-active-directory"></a>Azure Active Directory で保護された Web API サービスを作成する
この手順では、Web API バックエンドは Visual Studio 2013 を使用して作成されます。 この手順はビデオの 1:30 から開始されます。 Visual Studio で Web API バックエンド プロジェクトを作成するには、**[ファイル]**、->**[新規作成]**、->**[プロジェクト]** を選択し、**Web** テンプレートの一覧から **[ASP.NET Web アプリケーション]** を選択します。 このビデオでは、 **APIMAADDemo**という名前のプロジェクトを使用します。 **[OK]** をクリックしてプロジェクトを作成します。 

![Visual Studio][api-management-new-web-app]

**[テンプレートの選択]** の一覧で **[Web API]** をクリックし、Web API プロジェクトを作成します。 Azure Directory の認証を作成するには、 **[認証の変更]**をクリックします。

![新しいプロジェクト][api-management-new-project]

**[組織アカウント]** をクリックして、AAD テナントの**ドメイン**を指定します。 この例では、ドメインは **DemoAPIM.onmicrosoft.com**です。 ディレクトリのドメインは、ディレクトリの **[ドメイン]** タブから取得できます。

![ドメイン][api-management-aad-domains]

**[認証の変更]** ダイアログ ボックスで必要な設定を構成したら、**[OK]** をクリックします。

![[認証の変更]][api-management-change-authentication]

**[OK]** をクリックすると、Visual Studio で Azure AD ディレクトリへのアプリケーションの登録が試行され、Visual Studio にサインインするように求めるメッセージが表示されます。 ディレクトリの管理者アカウントを使用してサインインします。

![Visual Studio にサインインする][api-management-sign-in-vidual-studio]

このプロジェクトを Azure Web API として構成すには、**[クラウドでホストする]** のチェックボックスをオンにして **[OK]** をクリックします。

![新しいプロジェクト][api-management-new-project-cloud]

Azure へのサインインを求めるメッセージが表示され、Web アプリを構成できるようになります。

![構成][api-management-configure-web-app]

この例では **APIMAADDemo** という名前の新しい **[App Service プラン]** を指定します。

**[OK]** をクリックし、Web アプリを構成してプロジェクトを作成します。

## <a name="add-the-code-to-the-web-api-project"></a>Web API プロジェクトにコードを追加します。
ビデオの次の手順では、Web API プロジェクトにコードを追加します。 この手順は 4 35 から開始されます。

この例の Web API は、モデルとコントローラーを使用して基本的な電卓サービスを実装します。 サービスにモデルを追加するには、**ソリューション エクスプローラー**で **[モデル]** を右クリックし、**[追加]**、**[クラス]** の順に選択します。 クラスに「 `CalcInput` 」という名前を付け、 **[追加]**をクリックします。

`CalcInput.cs` ファイルの先頭に次の `using` ステートメントを追加します。

```c#
using Newtonsoft.Json;
```

生成されたクラスを次のコードで置き換えます。

```c#
public class CalcInput
{
    [JsonProperty(PropertyName = "a")]
    public int a;

    [JsonProperty(PropertyName = "b")]
    public int b;
}
```

**ソリューション エクスプローラー**で **[コントローラー]** を右クリックし、**[追加]**、->**[コントローラー]** の順に選択します。 **[Web API 2 コントローラー - 空]** をクリックしてから **[追加]** をクリックします。 コントローラーの名前として「**CalcController**」と入力し、**[追加]** をクリックします。

![コントローラーの追加][api-management-add-controller]

`CalcController.cs` ファイルの先頭に次の `using` ステートメントを追加します。

```c#
using System.IO;
using System.Web;
using APIMAADDemo.Models;
```

生成されたコントローラー クラスを次のコードで置き換えます。 このコードは、Basic Calculator API の操作である `Add`、`Subtract`、`Multiply`、`Divide` を実装します。

```c#
[Authorize]
public class CalcController : ApiController
{
    [Route("api/add")]
    [HttpGet]
    public HttpResponseMessage GetSum([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a + b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/sub")]
    [HttpGet]
    public HttpResponseMessage GetDiff([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a - b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/mul")]
    [HttpGet]
    public HttpResponseMessage GetProduct([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a * b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/div")]
    [HttpGet]
    public HttpResponseMessage GetDiv([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a / b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }
}
```

**F6** キーを押して、ソリューションをビルドし、検証します。

## <a name="publish-the-project-to-azure"></a>Azure にプロジェクトを発行する
この手順では、Visual Studio プロジェクトを Azure に発行します。 この手順はビデオの 5:45 から開始されます。

プロジェクトを Azure に発行するには、Visual Studio で **APIMAADDemo** プロジェクトを右クリックし、**[発行]** を選択します。 **[Web の発行]** ダイアログ ボックスの既定の設定をそのまま適用し、**[発行]** をクリックします。

![Web の発行][api-management-web-publish]

## <a name="grant-permissions-to-the-azure-ad-backend-service-application"></a>Azure AD バックエンド サービス アプリケーションにアクセス許可を付与する
Web API プロジェクトの構成と公開プロセスの一環として、バックエンド サービス用の新しいアプリケーションが Azure AD Directory に作成されます。 ビデオの 6:13 から始まるビデオの手順では、Web API バックエンドにアクセス許可が付与されます。

![アプリケーション][api-management-aad-backend-app]

必要なアクセス許可を構成するアプリケーションの名前をクリックします。 **[構成]** タブに移動し、**[他のアプリケーションに対するアクセス許可]** セクションまで下へスクロールします。 **Windows** **Azure Active Directory** の横にある **[アプリケーションのアクセス許可]** ドロップダウンをクリックし、**[ディレクトリ データの読み取り]** チェック ボックスをオンにして **[保存]** をクリックします。

![Add permissions][api-management-aad-add-permissions]

> [!NOTE]
> **Windows** **Azure Active Directory** が他のアプリケーションに対するアクセス許可の下に表示されない場合、**[アプリケーション追加]** をクリックし、その一覧から追加します。
> 
> 

Azure AD アプリケーションが API Management 開発者ポータルに構成されたら、後の手順で使用するために **App Id URI** を書き留めておきます。

![App Id URI][api-management-aad-sso-uri]

## <a name="import-the-web-api-into-api-management"></a>API Management に Web API をインポートする
API は、API パブリッシャー ポータルから構成されます。このポータルには、Azure Portal からアクセスします。 パブリッシャー ポータルにアクセスするには、API Management サービスのツール バーの **[パブリッシャー ポータル]** をクリックします。 API Management サービス インスタンスをまだ作成していない場合は、「[Azure API Management での最初の API の管理][Manage your first API]」チュートリアルの「[API Management インスタンスの作成][Create an API Management service instance]」をご覧ください。

![パブリッシャー ポータル][api-management-management-console]

操作は [API に手動で追加する](api-management-howto-add-operations.md)ことも、インポートすることもできます。 このビデオでは、操作は 6:40 から始まる Swagger 形式でインポートされます。

次の内容のファイルを `calcapi.json` という名前で作成し、コンピューターに保存します。 `host` 属性がお使いの Web API バックエンドを指定していることを確認します。 この例では、 `"host": "apimaaddemo.azurewebsites.net"` が使用されます。

```json
{
  "swagger": "2.0",
  "info": {
    "title": "Calculator",
    "description": "Arithmetics over HTTP!",
    "version": "1.0"
  },
  "host": "apimaaddemo.azurewebsites.net",
  "basePath": "/api",
  "schemes": [
    "http"
  ],
  "paths": {
    "/add?a={a}&b={b}": {
      "get": {
        "description": "Responds with a sum of two numbers.",
        "operationId": "Add two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>51</code>.",
            "required": true,
            "type": "string",
            "default": "51",
            "enum": [
              "51"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>49</code>.",
            "required": true,
            "type": "string",
            "default": "49",
            "enum": [
              "49"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/sub?a={a}&b={b}": {
      "get": {
        "description": "Responds with a difference between two numbers.",
        "operationId": "Subtract two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>100</code>.",
            "required": true,
            "type": "string",
            "default": "100",
            "enum": [
              "100"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>50</code>.",
            "required": true,
            "type": "string",
            "default": "50",
            "enum": [
              "50"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/div?a={a}&b={b}": {
      "get": {
        "description": "Responds with a quotient of two numbers.",
        "operationId": "Divide two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>100</code>.",
            "required": true,
            "type": "string",
            "default": "100",
            "enum": [
              "100"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>20</code>.",
            "required": true,
            "type": "string",
            "default": "20",
            "enum": [
              "20"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/mul?a={a}&b={b}": {
      "get": {
        "description": "Responds with a product of two numbers.",
        "operationId": "Multiply two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>20</code>.",
            "required": true,
            "type": "string",
            "default": "20",
            "enum": [
              "20"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>5</code>.",
            "required": true,
            "type": "string",
            "default": "5",
            "enum": [
              "5"
            ]
          }
        ],
        "responses": { }
      }
    }
  }
}
```

電卓 API をインポートするには、左側の **[API Management]** メニューの **[API]** をクリックし、**[API のインポート]** をクリックします。

![API ボタンのインポート][api-management-import-api]

電卓 API を構成するには、次の手順を実行します。

1. **[ファイルから]** をクリックして保存した `calculator.json` ファイルを参照し、**[Swagger]** ラジオボタンをクリックします。
2. **[Web API URL サフィックス]** テキスト ボックスに **calc** と入力します。
3. **[製品 (オプション)]** ボックスをクリックし、**[スターター]** を選択します。
4. **[保存]** をクリックして、API をインポートします。

![新しい API を追加する][api-management-import-new-api]

API がインポートされると、API の概要ページがパブリッシャー ポータルに表示されます。

## <a name="call-the-api-unsuccessfully-from-the-developer-portal"></a>開発者ポータルから API の呼び出しに失敗する場合
この時点で API は API Management にインポートされましたが、バックエンド サービスが Azure AD 認証で保護されているため、まだ開発者ポータルから正常に呼び出すことはできません。 これについては次の手順を使用して説明します。ビデオの 7:40 から始まります。

パブリッシャー ポータルの右上で **[開発者ポータル]** をクリックします。

![[開発者ポータル]][api-management-developer-portal-menu]

**[API]** をクリックし、**[Calculator]** API をクリックします。

![[開発者ポータル]][api-management-dev-portal-apis]

**[試用版]**をクリックします。

![試してみる][api-management-dev-portal-try-it]

**[送信]** をクリックすると、応答ステータスに 「**401 認証エラー**」と表示されます。

![送信][api-management-dev-portal-send-401]

バックエンド API が Azure Active Directory によって保護されているため、要求は承認されていません。 API を正常に呼び出すには、OAuth 2.0 を使用して開発者を承認するよう、開発者ポータルを構成する必要があります。 このプロセスについては以降のセクションで説明します。

## <a name="register-the-developer-portal-as-an-aad-application"></a>開発者ポータルを AAD アプリケーションとして登録する
OAuth 2.0 を使用して開発者を承認するように開発者ポータルを構成する最初の手順で、開発者ポータルを AAD アプリケーションとして登録します。 この説明については、ビデオでは 8:27 から始まります。

このビデオの最初の手順から Azure AD テナント (この例では **APIMDemo**) に移動し、**[アプリケーション]** タブに移動します。

![新規アプリケーション][api-management-aad-new-application-devportal]

**[追加]** ボタンをクリックして新しい Azure Active Directory アプリケーションを作成し、**[組織で開発中のアプリケーションを追加]** を選択します。

![新規アプリケーション][api-management-new-aad-application-menu]

**[Web アプリケーションや Web API]**を選択し、名前を入力して、次へ進む矢印をクリックします。 この例では **APIMDeveloperPortal** を使用します。

![新規アプリケーション][api-management-aad-new-application-devportal-1]

**[サインオン URL]** に API Management サービスの URL を入力し、`/signin` を追加します。 この例では、 `https://contoso5.portal.azure-api.net/signin` が使用されます。

**[アプリ URL]** に API Management サービスの URL を入力し、一意の文字をいくつか追加します。 この例では `https://contoso5.portal.azure-api.net/dp` を使用しますが、ここには任意の文字を入力できます。 必要な **[アプリケーションのプロパティ]** が構成されたら、チェック マークをクリックして、アプリケーションを作成します。

![新規アプリケーション][api-management-aad-new-application-devportal-2]

## <a name="configure-an-api-management-oauth-20-authorization-server"></a>API Management OAuth 2.0 承認サーバーを構成する
次の手順では、API Management に OAuth 2.0 承認サーバーを構成します。 この手順の説明については、ビデオの 9:43 から始まります。

左側の [API Management] メニューで **[セキュリティ]** をクリックし、**[OAuth 2.0]** をクリックしてから、**[Add authorization server (認証サーバーの追加)]** をクリックします。

![承認サーバーの追加][api-management-add-authorization-server]

**[名前]** フィールドと **[説明]** フィールドに、名前とオプションの説明を入力します。 これらのフィールドは、OAuth 2.0 承認サーバーを API Management サービス インスタンス内で識別するために使用されます。 この例では **承認サーバーのデモ** を使用します。 後で OAuth 2.0 サーバーを指定して API の認証に使用するときに、この名前を選択します。

**[Client registration page URL (クライアント登録ページ URL)]** に、`http://localhost` などのプレースホルダーの値を入力します。  **[クライアント登録ページ URL]** では、ユーザーによるアカウント管理をサポートする OAuth 2.0 プロバイダーについて、ユーザーが自身のアカウントを作成および構成するために使用できるページを指定します。 この例では、ユーザーは自身のアカウントを作成も構成もしないため、プレースホルダーを使用します。

![承認サーバーの追加][api-management-add-authorization-server-1]

次に、**[Authorization endpoint URL (認証エンドポイント URL)]** と **[Token endpoint URL (トークン エンドポイント URL)]** を指定します。

![承認サーバー][api-management-add-authorization-server-1a]

これらの値は開発者ポータル用に作成した AAD アプリケーションの **[アプリのエンドポイント (App Endpoints)]** ページから取得できます。 エンドポイントにアクセスするには、AAD アプリケーションの **[構成]** タブに移動し、**[エンドポイントの表示]** をクリックします。

![アプリケーション][api-management-aad-devportal-application]

![[エンドポイントの表示]][api-management-aad-view-endpoints]

**[OAuth 2.0 承認エンドポイント]** をコピーし、**[Authorization endpoint URL (認証エンドポイント URL)]** テキスト ボックスに貼り付けます。

![承認サーバーの追加][api-management-add-authorization-server-2]

**[OAuth 2.0 トークン エンドポイント]** をコピーし、**[Token endpoint URL (トークン エンドポイント URL)]** テキスト ボックスに貼り付けます。

![承認サーバーの追加][api-management-add-authorization-server-2a]

トークン エンドポイントに貼り付けるだけでなく、**resource** という名前の追加の本文パラメーターを追加し、値には Visual Studio プロジェクトの発行時に作成されたバックエンド サービスの AAD アプリケーションから **[アプリケーション ID/URI]** を使用します。

![App Id URI][api-management-aad-sso-uri]

次に、クライアントの資格情報を指定します。 これは、アクセスする必要があるリソースの資格情報です (この場合は開発者ポータル)。

![クライアントの資格情報][api-management-client-credentials]

**クライアント ID** を取得するには、開発者ポータル用 AAD アプリケーションの **[構成]** タブに移動し、**[クライアント ID]** をコピーします。

**クライアント シークレット**を取得するには、**[キー]** セクションで **[時間の選択]** ドロップダウンをクリックし、期間を指定します。 この例では 1 年間を使用します。

![[クライアント ID]][api-management-aad-client-id]

**[保存]** をクリックして構成を保存し、キーを表示します。 

> [!IMPORTANT]
> このキーを書き留めておきます。 Azure Active Directory の構成ウィンドウを閉じると、キーは再表示できません。
> 
> 

キーをクリップボードにコピーしてパブリッシャー ポータルに戻り、キーを **[クライアント シークレット]** テキストボックスに貼り付けて **[保存]** をクリックします。

![承認サーバーの追加][api-management-add-authorization-server-3]

クライアント資格情報の直後にあるのは、認証コード付与です。 この認証コードをコピーして、Azure AD 開発者ポータル アプリケーションの構成ページに戻り、**[応答 URL]** フィールドに認証付与を貼り付けて、**[保存]** をもう一度クリックします。

![[応答 URL]][api-management-aad-reply-url]

次の手順では、開発者ポータル AAD アプリケーションのアクセス許可を構成します。 **[アプリケーションのアクセス許可]** をクリックし、**[ディレクトリ データの読み取り]** チェックボックスをオンにします。 **[保存]** をクリックしてこの変更を保存したら、**[アプリケーションの追加]** をクリックします。

![Add permissions][api-management-add-devportal-permissions]

検索アイコンをクリックし、[次で始まる] ボックスに「**APIM**」と入力したら、**APIMAADDemo** を選択し、チェック マークをクリックして保存します。

![Add permissions][api-management-aad-add-app-permissions]

**APIMAADDemo** の **[委任されたアクセス許可]** をクリックし、**[Access APIMAADDemo (APIMAADDemo へのアクセス)]** のチェック ボックスをオンにしたら、**[保存]** をクリックします。 これにより、開発者ポータル アプリケーションからバックエンド サービスにアクセスできるようになります。

![Add permissions][api-management-aad-add-delegated-permissions]

## <a name="enable-oauth-20-user-authorization-for-the-calculator-api"></a>電卓 API 用に OAuth 2.0 のユーザー認証を有効にする
OAuth 2.0 サーバーが構成されたので、API のセキュリティ設定で指定できるようになります。 この手順の説明については、ビデオの 14:30 から始まります。

左側のメニューで **[API]** をクリックし、**[Calculator]** をクリックして設定を表示し、構成します。

![電卓 API][api-management-calc-api]

**[セキュリティ]** タブに移動し、**[OAuth 2.0]** チェック ボックスをオンにします。必要な承認サーバーを **[Authorization server (承認サーバー)]** ドロップダウンから選択し、**[保存]** をクリックします。

![電卓 API][api-management-enable-aad-calculator]

## <a name="successfully-call-the-calculator-api-from-the-developer-portal"></a>開発者ポータルから電卓 API を正常に呼び出す
OAuth 2.0 認証を API で構成したら、デベロッパー センターから操作を正常に呼び出すことができます。 この手順の説明については、ビデオの 15:00 から始まります。

開発者ポータルで電卓サービスの **2 つの整数を追加する**操作に戻り、**[試してみる]** をクリックします。 **[承認]** セクションの新しい項目は、先ほど追加した承認サーバーに対応しています。

![電卓 API][api-management-calc-authorization-server]

承認ドロップダウン リストから **[認証コード]** を選択し、使用するアカウントの資格情報を入力します。 既にアカウントでサインインしている場合、確認メッセージは表示されません。

![電卓 API][api-management-devportal-authorization-code]

**[送信]** をクリックし、**[応答のステータス]** の "**200 OK**" と、応答コンテンツの操作結果を確認します。

![電卓 API][api-management-devportal-response]

## <a name="configure-a-desktop-application-to-call-the-api"></a>API を呼び出すデスクトップ アプリケーションを構成する
ビデオの 16:30 から始まる次の手順では、API を呼び出す単純なデスクトップ アプリケーションを構成します。 最初の手順では、デスクトップ アプリケーションを Azure AD に登録し、アクセス権をディレクトリとバックエンド サービスに付与します。 18:25 からは、電卓 API で操作を呼び出すデスクトップ アプリケーションのデモを紹介します。

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>要求を事前承認する JWT 検証ポリシーを構成する
ビデオの 20:48 から始まる最後の手順では、 [JWT を検証する](https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT) ポリシーを使用して、各受信要求のアクセス トークンを検証することで要求を事前承認する方法について説明します。 JWT の検証ポリシーで要求が検証されない場合、要求は API Management によってブロックされ、バックエンドへは渡されません。

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.windows.net/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
        </claim>
    </required-claims>
</validate-jwt>
```

このポリシーの構成と使用についての別のデモは、「 [「Cloud Cover Episode 177: More API Management Features (クラウド カバー エピソード 177: その他の API Management 機能の紹介)](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) 」を 13:50 まで早送りしてご覧ください。 15:00 まで早送りし、ポリシー エディターで構成されるポリシーをご覧いただいた後、開発者ポータルから操作を呼び出す方法について、必要な認証トークンを使用した場合と使用しない場合の両方のデモを 18:50 からご覧ください。

## <a name="next-steps"></a>次のステップ
* API Management についてのその他の [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=api-management) をご覧ください。
* バックエンド サービスを保護するその他の方法については、「[相互証明書認証](api-management-howto-mutual-certificates.md)」をご覧ください。

[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[api-management-developer-portal-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-developer-portal-menu.png
[api-management-dev-portal-apis]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-apis.png
[api-management-dev-portal-try-it]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-try-it.png
[api-management-dev-portal-send-401]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-send-401.png
[api-management-aad-new-application-devportal]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal.png
[api-management-aad-new-application-devportal-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-1.png
[api-management-aad-new-application-devportal-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-2.png
[api-management-aad-devportal-application]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-devportal-application.png
[api-management-add-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server.png
[api-management-aad-sso-uri]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-sso-uri.png
[api-management-aad-view-endpoints]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-view-endpoints.png
[api-management-aad-client-id]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-client-id.png
[api-management-add-authorization-server-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1.png
[api-management-add-authorization-server-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2.png
[api-management-add-authorization-server-2a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2a.png
[api-management-add-authorization-server-3]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-3.png
[api-management-aad-reply-url]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-reply-url.png
[api-management-add-devportal-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-devportal-permissions.png
[api-management-aad-add-app-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-app-permissions.png
[api-management-aad-add-delegated-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-delegated-permissions.png
[api-management-calc-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-api.png
[api-management-enable-aad-calculator]: ./media/api-management-howto-protect-backend-with-aad/api-management-enable-aad-calculator.png
[api-management-devportal-authorization-code]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-authorization-code.png
[api-management-devportal-response]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-response.png
[api-management-calc-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-authorization-server.png
[api-management-add-authorization-server-1a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1a.png
[api-management-client-credentials]: ./media/api-management-howto-protect-backend-with-aad/api-management-client-credentials.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-aad-application-menu.png

[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Manage your first API]: api-management-get-started.md

