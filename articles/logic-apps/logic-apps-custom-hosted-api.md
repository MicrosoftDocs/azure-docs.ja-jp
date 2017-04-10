---
title: "Azure Logic Apps でのカスタム API の呼び出し | Microsoft Docs"
description: "Azure Logic Apps を使用して、Azure App Service でホストされた独自のカスタム API を呼び出します"
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2016
ms.author: stepsic
translationtype: Human Translation
ms.sourcegitcommit: fc509ef8b30fadb6e026f346d4adbd6ef759624a
ms.openlocfilehash: 74aae9f757f56e94b583069a1fdee7efaafe467c
ms.lasthandoff: 02/16/2017


---
# <a name="call-custom-apis-hosted-on-azure-app-service-with-azure-logic-apps"></a>Azure Logic Apps を使用した、Azure App Service でホストされた独自のカスタム API の呼び出し

Azure Logic Apps には、さまざまなサービス用に 40 以上のコネクタが用意されていますが、独自のカスタム API を呼び出して独自のコードを実行することもできます。 独自のカスタム Web API をホストする最も簡単かつスケーラブルな方法の&1; つは、Azure App Service を使用することです。 この記事では、App Service の API アプリ、Web アプリ、またはモバイル アプリでホストされる任意の Web API を呼び出す方法について説明します。
[API をロジック アプリのトリガーまたはアクションとして構築する方法](../logic-apps/logic-apps-create-api-app.md)について学習してください。

## <a name="deploy-your-web-app"></a>Web アプリのデプロイ

最初に、Azure App Service で API を Web アプリとしてデプロイする必要があります。 [ASP.NET Web アプリを作成する際の基本的なデプロイ](../app-service-web/web-sites-dotnet-get-started.md)について学習してください。 ロジック アプリから任意の API を呼び出すことができますが、最適な結果を得るには、ロジック アプリ アクションと簡単に統合できるように Swagger メタデータを追加することをお勧めします。 [Swagger メタデータの追加](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui)について学習してください。

### <a name="api-settings"></a>API 設定

Logic Apps デザイナーが Swagger を解析できるように、CORS を有効にして Web アプリの API 定義プロパティを設定する必要があります。 

1.    Azure Portal で Web アプリを選択します。
2.    開かれたブレードで、**[API]** を探して **[API 定義]** を選択します。 **[API 定義の場所]** を swagger.json ファイルの URL に設定します。

    この URL は通常 https://{名前}.azurewebsites.net/swagger/docs/v1) です。

3.    Logic Apps デザイナーからの要求を許可するために、**[API]** の下の **[CORS]** を選択して "*" の CORS ポリシーを設定します。

## <a name="call-into-your-custom-api"></a>カスタム API の呼び出し

CORS と API の定義プロパティを設定している場合は、Logic Apps のポータルでカスタム API アクションをワークフローに簡単に追加できます。 Logic Apps デザイナーでは、サブスクリプションの Web サイトを参照して、Swagger URL が定義された Web サイトを一覧表示できます。 Swagger をポイントして、HTTP と Swagger アクションを使用して利用できるアクションと入力を一覧表示することもできます。 さらに、HTTP アクションを使用した要求を作成して、任意の API を呼び出すことができます。Swagger doc を持たない API や、Swagger doc を公開しない API にも対応します。

API をセキュリティで保護するには、2 つの異なる方法があります。

*    コードの変更が不要な方法。 Azure Active Directory を使用すると、コードの変更や再デプロイを必要とせずに API を保護できます。
*    基本認証、Azure Active Directory 認証、または証明書認証を API のコードで強制します。

## <a name="secure-calls-to-your-api-without-changing-code"></a>コードの変更を伴わない API 呼び出しのセキュリティ保護

このセクションでは、ロジック アプリ用と Web アプリ用の&2; つの Azure Active Directory アプリケーションを作成します。 Web アプリの呼び出しの認証は、ロジック アプリの Azure Active Directory アプリに関連付けられたサービス プリンシパル (クライアント ID とシークレット) を使用して行います。 最後に、アプリケーション ID をロジック アプリの定義に追加します。

### <a name="part-1-set-up-an-application-identity-for-your-logic-app"></a>パート 1: ロジック アプリのアプリケーション ID の設定

このアプリケーション ID は、Azure Active Directory によるロジック アプリの認証に使用されます。 この ID の設定は、ディレクトリに対して一度行うだけで済みます。 たとえば、ロジック アプリごとに一意の ID を作成することもできますが、すべてのロジック アプリに同じ ID を使用できます。 これらの ID は Azure Portal か PowerShell を使用して設定できます。

#### <a name="create-the-application-identity-in-the-azure-classic-portal"></a>Azure クラシック ポータルでのアプリケーション ID の作成

1. Azure クラシック ポータルで [Azure Active Directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) に移動します。 
2. Web アプリに使用するディレクトリを選択します。
3. **[アプリケーション]** タブを選択します。 ページの下部にあるコマンド バーで、**[追加]** を選択します。
5. アプリ ID に名前を付けて、[次へ] 矢印をクリックします。
6. **[アプリケーションのプロパティ]** で、ドメイン形式の一意の文字列を入力し、チェックマークをオンにします。
7. **[構成]** タブを選択します。 **[クライアント ID]** に移動して、ロジック アプリで使用するクライアント ID をコピーします。
8. **[キー]** の **[時期の選択]** 一覧を開いて、キーの期間を選択します。
9. ページの下部にある **[保存]** をクリックします。 場合によっては数秒待つ必要があります。
10. ロジック アプリで使用できるように、**[キー]** の下に表示されるキーを必ずコピーしておきます。

#### <a name="create-the-application-identity-using-powershell"></a>PowerShell を使用したアプリケーション ID の作成

1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. **テナント ID**、**アプリケーション ID**、使用したパスワードをコピーしてください。

### <a name="part-2-protect-your-web-app-with-an-azure-active-directory-app-identity"></a>パート 2: Azure Active Directory アプリの ID による Web アプリの保護

Web アプリが既にデプロイ済みの場合、Azure Portal で承認を有効にすることができます。 それ以外の場合、Azure Resource Manager によるデプロイの中で承認を有効にすることができます。

#### <a name="enable-authorization-in-the-azure-portal"></a>Azure Portal での承認の有効化

1. Web アプリを探して選択します。 **[設定]** で、**[認証/承認]** を選択します。
2. **[App Service 認証]** で認証を **[オン]** にして、**[保存]** を選択します。

この時点で、アプリケーションが自動的に作成されます。 このアプリケーションのクライアント ID がパート 3 で必要となるため、次の手順を行う必要があります。

1. Azure クラシック ポータルで [Azure Active Directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) に移動します。
2.    ディレクトリを選択します。
3. 検索ボックスを使ってアプリを探します。
4. 一覧からアプリを選択します。
5. **[構成]** タブを選択すると、**[クライアント ID]** が表示されます。

#### <a name="deploy-your-web-app-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用した Web アプリのデプロイ

最初に、ロジック アプリに使用するアプリケーションとは別に、Web アプリ用のアプリケーションを作成する必要があります。 まず先ほどのパート 1 の手順を実行しますが、**HomePage** と **IdentifierUris** には Web アプリの実際の https://**URL** を使用します。

> [!NOTE]
> Web アプリ用のアプリケーションを作成する際には、[Azure クラシック ポータル](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)を使用する必要があります。 PowerShell コマンドレットでは、ユーザーが Web サイトにサインインするために必要なアクセス許可が設定されません。

クライアント ID とテナント ID を取得したら、次のコードを Web アプリのサブ リソースとしてデプロイ テンプレートに追加します。

```
"resources": [
    {
        "apiVersion": "2015-08-01",
        "name": "web",
        "type": "config",
        "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
        "properties": {
            "siteAuthEnabled": true,
            "siteAuthSettings": {
              "clientId": "<<clientID>>",
              "issuer": "https://sts.windows.net/<<tenantID>>/",
            }
        }
    }
]
```

デプロイを自動的に実行して、Azure Active Directory を使用する空の Web アプリとロジック アプリを一緒にデプロイするには、次の **[Deploy to Azure]** をクリックします。

[![Azure へのデプロイ](media/logic-apps-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

完全なテンプレートについては、[ロジック アプリによる、App Service でホストされ Azure Active Directory によって保護されたカスタム API の呼び出し](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json)に関するページを参照してください。

### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>パート 3: ロジック アプリの承認セクションの入力

**HTTP** アクションの **[承認]** セクションで、次の要素を指定します。

`{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| 要素 | 説明 |
| ------- | ----------- |
| type |認証の種類。 ActiveDirectoryOAuth 認証の場合、値 `ActiveDirectoryOAuth`を使用します。 |
| テナント |AD テナントを識別するためのテナント識別子。 |
| 対象となる読者 |必須。 接続するリソース。 |
| clientID |Azure AD アプリケーションのクライアント識別子。 |
| secret |必須。 トークンを要求しているクライアントのシークレット。 |

先ほどのテンプレートではこの承認セクションが既に設定されていますが、ロジック アプリを直接作成する場合、承認セクション全体を追加する必要があります。

## <a name="secure-your-api-in-code"></a>コードでの API の保護

### <a name="certificate-authentication"></a>証明書認証

クライアント証明書を使用して、Web アプリへの受信要求を検証できます。 コードの設定方法については、「[Web アプリの TLS 相互認証を構成する方法](../app-service-web/app-service-web-configure-tls-mutual-auth.md)」を参照してください。

**[承認]** セクションで次の要素を指定する必要があります。 

`{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`

| 要素 | 説明 |
| ------- | ----------- |
| type |必須。 認証の種類。 SSL クライアント証明書の場合、値として `ClientCertificate` を指定する必要があります。 |
| pfx |必須。 Base64 でエンコードされた PFX ファイルのコンテンツ。 |
| パスワード |必須。 PFX ファイルにアクセスするためのパスワード。 |

### <a name="basic-authentication"></a>基本認証

受信要求を検証する場合は、基本認証 (ユーザー名とパスワードなど) を使用できます。 基本認証は一般的なパターンなので、この認証はアプリの作成にどの言語が使われていても使用できます。

**[承認]** セクションで次の要素を指定する必要があります。

`{"type": "basic","username": "test","password": "test"}`

| 要素 | 説明 |
| --- | --- |
| type |必須。 認証の種類。 基本認証の場合、値 `Basic`を使用する必要があります。 |
| ユーザー名 |必須。 認証するユーザー名。 |
| パスワード |必須。 認証するパスワード。 |

### <a name="handle-azure-active-directory-authentication-in-code"></a>コードでの Azure Active Directory 認証の処理

既定では、Azure Portal で有効にする Azure Active Directory 認証では、きめ細かい承認は実行されません。 たとえば、この認証では API が特定のユーザーまたはアプリにロックされず、特定のテナントのみにロックされます。

たとえばコードで API をロジック アプリに制限するには、JWT を含むヘッダーを抽出します。 呼び出し元の ID を確認し、一致しない要求を拒否します。

さらに、この認証全体を独自のコードに実装し、Azure Portal の機能を使用しない場合は、「[Azure アプリでのオンプレミスの Active Directory を使用した認証](../app-service-web/web-sites-authentication-authorization.md)」を参照してください。
ロジック アプリのアプリケーション ID を作成して API の呼び出しに使用する場合、これまでの手順に従う必要があります。
