---
title: Azure Active Directory Identity Protection 用の Microsoft Graph | Microsoft Docs
description: Microsoft Graph に対するクエリを通じて、Azure Active Directory から一連のリスク イベントとその関連情報を取得する方法を説明します。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f939811bec312baa1f4c37f0f915d2e881121af
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334078"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Azure Active Directory Identity Protection と Microsoft Graph の基本

Microsoft Graph は、Microsoft の統合 API エンドポイントであり、[Azure Active Directory Identity Protection](../active-directory-identityprotection.md) API のホームです。 危険なユーザーとサインインに関する情報を明らかにする API が 3 つあります。最初の API である **identityRiskEvents** を使用して、Microsoft Graph に対して一連の[リスク イベント](../reports-monitoring/concept-risk-events.md)とその関連情報のクエリを実行できます。 2 つ目の API である **riskyUsers** を使用すると、Microsoft Graph に対して、リスクとして検出されたユーザーの Identity Protection に関する情報のクエリを実行できます。 3 つ目の API である **signIn** を使用すると、Microsoft Graph に対して、リスク状態、詳細、およびレベルに関連する特定のプロパティを使用して、Azure AD のサインインに関する情報のクエリを実行できます。 この記事では、Microsoft Graph への接続とこれらの API のクエリの概要について説明します。 さらに踏み込んだ概要や詳しい解説、Graph Explorer の利用については、[Microsoft Graph のサイト](https://graph.microsoft.io/)またはこれらの API に関する特定のリファレンス ドキュメントを参照してください。

* [identityRiskEvents API](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta)
* [riskyUsers API](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)
* [signIn API](https://docs.microsoft.com/graph/api/resources/signin?view=graph-rest-beta)

## <a name="connect-to-microsoft-graph"></a>Microsoft Graph に接続する

Microsoft Graph を介して Identity Protection のデータにアクセスするには、次の 4 つのステップがあります。

1. ドメイン名を取得します。
2. 新しいアプリ登録を作成します。 
3. このシークレットとその他いくつかの情報を使って Microsoft Graph に本人性を証明し、認証トークンを取得します。 
4. このトークンを使って API エンドポイントに要求を行い、Identity Protection データを取得します。

以降の手順を開始する前に次の情報が必要となります。

* Azure AD にアプリケーションを作成するための管理者特権
* テナントのドメインの名前 (例: contoso.onmicrosoft.com)

## <a name="retrieve-your-domain-name"></a>ドメイン名の取得 

1. Azure Portal に管理者として[サインイン](https://portal.azure.com)します。 
1. 左のナビゲーション ウィンドウで、 **[Active Directory]** をクリックします。 

   ![Creating an application](./media/graph-get-started/41.png)

1. **[管理]** セクションで、 **[プロパティ]** をクリックします。

   ![Creating an application](./media/graph-get-started/42.png)

1. ドメイン名をコピーします。

## <a name="create-a-new-app-registration"></a>新しいアプリ登録の作成

1. **[Active Directory]** ページの **[管理]** セクションで、 **[アプリの登録]** をクリックします。

   ![Creating an application](./media/graph-get-started/42.png)

1. 上部のメニューで、 **[新しいアプリケーションの登録]** をクリックします。

   ![Creating an application](./media/graph-get-started/43.png)

1. **[作成]** ページで、以下の手順を実行します。

   ![Creating an application](./media/graph-get-started/44.png)

   1. **[名前]** ボックスにアプリケーションの名前 (例:AADIP Risk Event API Application) を入力します。

   1. **[種類]** として **[Web アプリケーションや Web API]** を選択します。

   1. **[サインオン URL]** ボックスに、「`http://localhost`」と入力します。

   1. **Create** をクリックしてください。
1. **[設定]** ページを開くには、アプリケーションの一覧で、新しく作成したアプリの登録をクリックします。 
1. **[アプリケーション ID]** をコピーします。

## <a name="grant-your-application-permission-to-use-the-api"></a>API を使用するためのアクセス許可をアプリケーションに付与します。

1. **[設定]** ページで、 **[必要なアクセス許可]** をクリックします。

   ![Creating an application](./media/graph-get-started/15.png)

1. **[必要なアクセス許可]** ページで、上部ツール バーの **[追加]** をクリックします。

   ![Creating an application](./media/graph-get-started/16.png)

1. **[API アクセスの追加]** ページで、 **[API を選択します]** をクリックします。

   ![Creating an application](./media/graph-get-started/17.png)

1. **[API を選択します]** ページで、 **[Microsoft Graph]** を選んで、 **[選択]** をクリックします。

   ![Creating an application](./media/graph-get-started/18.png)

1. **[API アクセスの追加]** ページで、 **[アクセス許可を選択します]** をクリックします。

   ![Creating an application](./media/graph-get-started/19.png)

1. **[アクセスの有効化]** ページで、 **[すべての ID リスク イベント情報の読み取り]** をクリックし、 **[選択]** をクリックします。

   ![Creating an application](./media/graph-get-started/20.png)

1. **[API アクセスの追加]** ページで、 **[完了]** をクリックします。

   ![Creating an application](./media/graph-get-started/21.png)

1. **[必要なアクセス許可]** ページで **[アクセス許可の付与]** をクリックし、 **[はい]** をクリックします。

   ![Creating an application](./media/graph-get-started/22.png)

## <a name="get-an-access-key"></a>アクセス キーを取得する

1. **[設定]** ページで、 **[キー]** をクリックします。

   ![Creating an application](./media/graph-get-started/23.png)

1. **[キー]** ページで、次の手順を実行します。

   ![Creating an application](./media/graph-get-started/24.png)

   1. **[キーの説明]** テキスト ボックスに、説明を入力します (例: "*AADIP リスク イベント*")。
   1. **[期間]** として、 **[1 年]** を選びます。
   1. **[Save]** をクリックします。
   1. キーの値をコピーし、安全な場所に貼り付けます。   
   
   > [!NOTE]
   > このキーを紛失した場合、このセクションに戻って新しいキーを作成する必要があります。 このキーはだれにも渡さないように注意してください。このキーがあれば、だれでもデータにアクセスすることができます。
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Microsoft Graph に本人であることを証明して Identity Risk Events API を照会する

この時点で次の情報が揃っている必要があります。

- テナントのドメインの名前
- クライアント ID 
- キー 

本人確認を行うために、post 要求を `https://login.microsoft.com` に送信します。要求本文に次のパラメーターを指定してください。

- grant_type: “**client_credentials**”
- resource: `https://graph.microsoft.com`
- client_id: \<クライアント ID\>
- client_secret: \<キー\>

認証に成功すると、認証トークンが返されます。  
API を呼び出すためには、次のパラメーターを持つヘッダーを作成します。

```
`Authorization`=”<token_type> <access_token>"
```

トークンの種類とアクセス トークンは、認証時に返されたトークンで確認できます。

このヘッダーは、API URL (`https://graph.microsoft.com/beta/identityRiskEvents`) に対する要求として送信します。

成功した場合の応答には、一連の ID リスク イベントとその関連データが OData JSON 形式で格納されます。必要に応じてこのデータを解析し、処理してください。

以下に示したコードは、PowerShell を使って認証と API 呼び出しを行う例です。  
該当するクライアント ID、秘密キー、テナントのドメインを追加してください。

```PowerShell
    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 
```

## <a name="query-the-apis"></a>API のクエリを実行する

これら 3 つの API によって、組織内の危険なユーザーとサインインに関する情報をさまざまな方法で取得できるようになります。 次に、これらの API と関連するサンプル要求の一般的なユース ケースをいくつか示します。 これらのクエリは、上記のサンプル コードを使用するか、[Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) を使用して実行できます。

### <a name="get-the-high-risk-and-medium-risk-events-identityriskevents-api"></a>高リスクと中リスクのイベントを取得する (identityRiskEvents API)

中リスクと高リスクのイベントは、Identity Protection のサインインまたはユーザーリスク ポリシーをトリガーする機能を持つ可能性があるイベントを表します。 サインインしようとしているユーザーが正当な ID 所有者ではない可能性が中度または高度であるため、このようなイベントを優先して修正する必要があります。 

```
GET https://graph.microsoft.com/beta/identityRiskEvents?`$filter=riskLevel eq 'high' or riskLevel eq 'medium'" 
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>危険なサインイン ポリシー (riskyUsers API) によってトリガーされた MFA チャレンジに合格したすべてのユーザーを取得する

Identity Protection のリスクベースのポリシーが組織に与える影響を把握するには、危険なサインイン ポリシーによってトリガーされた MFA チャレンジに合格したすべてのユーザーのクエリを実行します。 この情報は、Identity Protection によって危険であると誤検出された可能性があるユーザーや、AI が危険と見なすアクションを実行している可能性がある正当なユーザーを把握するために役立ちます。

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>特定のユーザーに対する危険なサインインをすべて取得する (signIn API)

ユーザーが侵害された可能性があると考えられる場合は、危険なサインインをすべて取得することで、危険の状態をより把握できます。 

```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```
## <a name="next-steps"></a>次の手順

以上、Microsoft Graph の API を呼び出す基本的な方法を紹介しました。  
これを応用すれば、思いどおりに ID リスク イベントを照会し、そのデータを活用することができます。

Microsoft Graph に関する詳細情報のほか、Graph API を使ったアプリケーションの構築方法については、こちらの[ドキュメント](https://docs.microsoft.com/graph/overview)と [Microsoft Graph](https://developer.microsoft.com/graph) のサイトをご覧ください。 

関連情報については、以下をご覧ください。

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
- [Azure Active Directory Identity Protection で検出されるリスク イベントの種類](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Microsoft Graph の概要](https://developer.microsoft.com/graph/docs)
- [Azure AD Identity Protection Service Root](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)
