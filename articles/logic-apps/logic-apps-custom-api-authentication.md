---
title: カスタム API の呼び出しをセキュリティで保護するために認証を追加する
description: Azure Logic Apps からのカスタム API の呼び出しのセキュリティ強化のために認証を設定する方法
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/22/2017
ms.openlocfilehash: 5e0dcd478c6eb6696a0e07d35d4dccddac68ac1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95993185"
---
# <a name="increase-security-for-calls-to-custom-apis-from-azure-logic-apps"></a>Azure Logic Apps からのカスタム API の呼び出しのセキュリティの向上

API の呼び出しのセキュリティを向上させるために、Azure portal で Azure Active Directory (Azure AD) 認証を設定できます。コードを更新する必要はありません。 あるいは、API のコードで認証を要求し、強制できます。

## <a name="authentication-options-for-your-api"></a>API の認証オプション

カスタム API の呼び出しのセキュリティは、次の方法で向上させることができます。

* [コードを変更しない](#no-code):Azure portal 経由で [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) を利用し、API を保護します。そのため、コードを更新したり、API を再デプロイしたりする必要がありません。

  > [!NOTE]
  > 既定では、Azure Portal で Azure AD 認証をオンにしてもきめ細かい認証は実行されません。 たとえば、この認証は、特定のユーザーやアプリではなく、特定のテナントに API を制限します。 

* [API のコードを更新する](#update-code):コードを利用して[証明書認証](#certificate)、[基本認証](#basic)、[Azure AD 認証](#azure-ad-code)を強制することで API を保護します。

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>コードを変更せず、API の呼び出しを認証する

この方法の一般的な手順を次に示します。

1. 2 つの Azure Active Directory (Azure AD) アプリケーション ID を作成します。ロジック アプリ用に 1 つ、Web アプリ (または API アプリ) 用に 1 つです。

2. API の呼び出しを認証するには、ロジック アプリの Azure AD アプリケーション ID に関連付けられているサービス プリンシパルの資格情報 (クライアント ID とシークレット) を利用します。

3. アプリケーション ID をロジック アプリ定義に追加します。

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>パート 1: ロジック アプリの Azure AD アプリケーション ID を作成する

ロジック アプリはこのアプリケーション ID を利用し、Azure AD に対して認証します。 この ID は、ディレクトリに対して一度だけ設定します。 たとえば、ロジック アプリごとに一意の ID を作成できますが、すべてのロジック アプリに同じ ID を使用することを選択できます。 これらの ID は Azure ポータルまたは [PowerShell](#powershell) を使用して設定できます。

**Azure ポータルでロジック アプリのアプリケーション ID を作成する**

1. [Azure portal](https://portal.azure.com "https://portal.azure.com") で、 **[Azure Active Directory]** を選択します。 

2. Web アプリまたは API アプリと同じディレクトリにいることを確認します。

   > [!TIP]
   > ディレクトリを切り替えるには、プロファイルを選択し、別のディレクトリを選択します。 あるいは、 **[概要]**  >  **[ディレクトリの切り替え]** の順に選択します。

3. ディレクトリ メニューの **[管理]** で、 **[アプリの登録]**  >  **[新しいアプリケーションの登録]** の順に選択します。

   > [!TIP]
   > 既定で、アプリ登録一覧にはディレクトリのすべてのアプリ登録が表示されます。 自分のアプリ登録のみを表示するには、検索ボックスの隣にある **[マイ アプリ]** を選択します。 

   ![新しいアプリ登録を作成する](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. アプリケーション ID に名前を付け、 **[アプリケーションの種類]** を **[Web アプリ / API]** に設定し、 **[サインオン URL]** のドメインとして書式設定された一意の文字列を指定し、 **[作成]** を選択します。

   ![アプリケーション ID に名前を付け、サインオン URL を指定する](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   ロジック アプリに作成したアプリケーション ID がアプリ登録一覧に表示されます。

   ![ロジック アプリのアプリケーション ID](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. アプリ登録一覧で、新しいアプリケーション ID を選択します。 **アプリケーション ID** をコピーして保存し、パート 3 でロジック アプリの "クライアント ID" として使用します。

   ![ロジック アプリのアプリケーション ID をコピーし、保存する](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. アプリケーション ID 設定が表示されない場合、 **[設定]** または **[すべての設定]** を選択します。

7. **[API アクセス]** で **[キー]** を選択します。 **[説明]** にキーの名前を入力します。 **[有効期限]** で、キーの有効期間を選択します。

   ここで作成するキーは、アプリケーション ID の "シークレット" またはロジック アプリのパスワードとして機能します。

   ![ロジック アプリ ID のキーを作成する](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. ツールバーで、 **[保存]** を選択します。 **[値]** にキーが表示されます。 
後で使用するために **キーをコピーし、保存します**。 **[キー]** ページを閉じるとキーが非表示になるためです。

   パート 3 でロジック アプリを構成するとき、このキーを "シークレット" またはパスワードとして指定します。

   ![後のためにキーをコピーして保存する](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**PowerShell でロジック アプリのアプリケーション ID を作成する**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このタスクは Azure Resource Manager と PowerShell で実行できます。 PowerShell で、次のコマンドレットを実行します。

1. `Add-AzAccount`

1. `$SecurePassword = Read-Host -AsSecureString`

1. パスワードを入力し、Enter キーを押します。

1. `New-AzADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password $SecurePassword`

1. **テナント ID** (Azure AD テナントの GUID)、**アプリケーション ID**、使用したパスワードを必ずコピーします。

詳細については、「[リソースにアクセスするためのサービス プリンシパルを Azure PowerShell で作成する](../active-directory/develop/howto-authenticate-service-principal-powershell.md)」を参照してください。

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>パート 2: Web アプリまたは API アプリの Azure AD アプリケーション ID を作成する

Web アプリまたは API アプリが既にデプロイされている場合、Azure ポータルで認証をオンにし、アプリケーション ID を作成できます。 デプロイされていない場合、[Resource Manager テンプレートでデプロイするときに認証をオンにできます](#authen-deploy)。 

**デプロイされたアプリに対し、Azure ポータルでアプリケーション ID を作成し、認証をオンにする**

1. [Azure portal](https://portal.azure.com "https://portal.azure.com") で、Web アプリまたは API アプリを探して選択します。 

2. **[設定]** で、 **[認証/承認]** を選択します。 **[App Service 認証]** で認証を **[オン]** にします。 **[認証プロバイダー]** の下の **[Azure Active Directory]** を選択します。

   ![認証をオンにする](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. 次に、Web アプリまたは API アプリの Azure AD アプリケーション ID を作成します。 **[Azure Active Directory 設定]** ページで、 **[管理モード]** を **[簡易]** に設定します。 **[新しい AD アプリを作成する]** を選択します。 アプリケーション ID に名前を付け、 **[OK]** を選択します。 

   ![Web アプリまたは API アプリのアプリケーション ID を作成する](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. **[認証/承認]** ページで、 **[保存]** をクリックします。

次に、Web アプリまたは API アプリに関連付けられているアプリケーション ID のクライアント ID とテナント ID を見つける必要があります。 ID はパート 3 で使用します。 Azure ポータルで次の手順を続行します。

**Azure ポータルで、Web アプリまたは API アプリのアプリケーション ID のクライアント ID とテナント ID を見つける**

1. **[認証プロバイダー]** の下の **[Azure Active Directory]** を選択します。 

   !["Azure Active Directory" を選択する](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. **[Azure Active Directory 設定]** ページで、 **[管理モード]** を **[詳細]** に設定します。

3. **[クライアント ID]** をコピーし、パート 3 で使用するためにこの GUID を保存します。

   > [!TIP] 
   > **[クライアント ID]** と **[発行者の URL]** が表示されない場合、Azure ポータルを更新し、手順 1 を繰り返してください。

4. **[発行者の URL]** で、パート 3 のために GUID だけをコピーして保存します。 必要であれば、Web アプリまたは API アプリのデプロイ テンプレートでもこの GUID を使用できます。

   この GUID は特定のテナントの GUID ("テナント ID") であり、URL (`https://sts.windows.net/{GUID}`) に表示されます。

5. 変更を保存せず、 **[Azure Active Directory の設定]** ページを閉じます。

<a name="authen-deploy"></a>

**Resource Manager テンプレートでデプロイするときに認証をオンにする**

ロジック アプリのアプリ ID とは異なる Azure AD アプリケーション ID を Web アプリまたは API アプリに作成する必要があります。 アプリケーション ID を作成するには、パート 2 の Azure ポータルの手順に従います。 

パート 1 の手順も利用できますが、 **[サインオン URL]** と **[アプリ ID/URI]** には Web アプリまたは API アプリの実際の `https://{URL}` を使用してください。 これらの手順では、アプリのデプロイ テンプレートとパート 3 で使用するために、クライアント ID とテナント ID の両方を保存する必要があります。

> [!NOTE]
> Web アプリまたは API アプリの Azure AD アプリケーション ID を作成するときは、PowerShell ではなく、Azure ポータルを使用する必要があります。 PowerShell コマンドレットでは、ユーザーが Web サイトにサインインするために必要なアクセス許可が設定されません。

クライアント ID とテナント ID を取得したら、ID を Web アプリまたは API アプリのサブ リソースとしてデプロイ テンプレートに追加します。

``` json
"resources": [ 
   {
      "apiVersion": "2015-08-01",
      "name": "web",
      "type": "config",
      "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
      "properties": {
         "siteAuthEnabled": true,
         "siteAuthSettings": {
            "clientId": "<client-ID>",
            "issuer": "https://sts.windows.net/<tenant-ID>/"
         }
      }
   } 
]
```

Azure Active Directory 認証と共に、空の Web アプリやロジック アプリを自動デプロイするには、[ここで完全なテンプレートを表示する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json)か、ここで **[Azure に配置する]** をクリックします。

[![Azure へのデプロイ](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>パート 3: ロジック アプリの承認セクションを入力する

先ほどのテンプレートではこの承認セクションが既に設定されていますが、ロジック アプリを直接作成する場合、承認セクション全体を追加する必要があります。

コード ビューでロジック アプリ定義を開き、**HTTP** アクション定義に移動し、 **[認証]** セクションを見つけ、次のプロパティを追加します。

```json
{
   "tenant": "<tenant-ID>",
   "audience": "<client-ID-from-Part-2-web-app-or-API app>", 
   "clientId": "<client-ID-from-Part-1-logic-app>",
   "secret": "<key-from-Part-1-logic-app>", 
   "type": "ActiveDirectoryOAuth"
}
```

| プロパティ | 必須 | 説明 | 
| -------- | -------- | ----------- | 
| tenant | はい | Azure AD テナントの GUID | 
| audience | はい | アクセスするターゲット リソースの GUID。Web アプリまたは API アプリのアプリケーション ID からのクライアント ID です | 
| clientId | はい | アクセスを要求するクライアントの GUID。ロジック アプリのアプリケーション ID からのクライアント ID です | 
| secret | はい | アクセス トークンを要求しているクライアントのアプリケーション ID からのキーまたはパスワード | 
| type | はい | 認証の種類。 ActiveDirectoryOAuth 認証の場合、値 `ActiveDirectoryOAuth`を使用します。 | 
|||| 

次に例を示します。

``` json
{
   "actions": {
      "HTTP": {
         "inputs": {
            "method": "POST",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         }
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>コードを利用して API 呼び出しを保護する

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>証明書の認証

ロジック アプリから Web アプリまたは API アプリに入ってくる要求を検証するために、クライアント証明書を利用できます。 コードの設定方法については、「[Web アプリの TLS 相互認証を構成する方法](../app-service/app-service-web-configure-tls-mutual-auth.md)」を参照してください。

**[承認]** セクションで、次のプロパティを追加します。

```json
{
   "type": "ClientCertificate",
   "password": "<password>",
   "pfx": "<long-pfx-key>"
} 
```

| プロパティ | 必須 | 説明 |
| -------- | -------- | ----------- |
| `type` | はい | 認証の種類。 TLS/SSL クライアント証明書の場合、値として `ClientCertificate` を指定する必要があります。 |
| `password` | いいえ | クライアント証明書 (PFX ファイル) にアクセスするためのパスワード |
| `pfx` | はい | Base64 でエンコードされた、クライアント証明書のコンテンツ (PFX ファイル) |
||||

<a name="basic"></a>

#### <a name="basic-authentication"></a>[基本認証]

ロジック アプリから Web アプリまたは API アプリに入ってくる要求を検証するために、ユーザー名やパスワードなど、基本認証を利用できます。 基本認証は一般的なパターンなので、この認証は Web アプリまたは API アプリの作成にどの言語が使われていても使用できます。

**[承認]** セクションで、次のプロパティを追加します。

```json
{
   "type": "Basic",
   "username": "<username>",
   "password": "<password>"
}
```

| プロパティ | 必須 | 説明 | 
| -------- | -------- | ----------- | 
| type | はい | 使用する認証の種類。 基本認証の場合、値 `Basic` を使用する必要があります。 | 
| username | はい | 認証に使用するユーザー名 | 
| password | はい | 認証に使用するパスワード | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>コードを利用した Azure Active Directory 認証

既定では、Azure Portal で Azure AD 認証をオンにしてもきめ細かい認証は実行されません。 たとえば、この認証は、特定のユーザーやアプリではなく、特定のテナントに API を制限します。 

コードを利用し、ロジック アプリへの API アクセスを制限するには、JSON Web トークン (JWT) が含まれるヘッダーを抽出します。 呼び出し元の ID を確認し、一致しない要求を拒否します。

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/overview-authentication-authorization.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>次のステップ

* [ロジック アプリ ワークフローからカスタム API をデプロイして呼び出す](../logic-apps/logic-apps-custom-api-host-deploy-call.md)
