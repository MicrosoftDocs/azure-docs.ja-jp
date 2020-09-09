---
title: Azure Active Directory Identity Protection 用の Microsoft Graph API
description: Microsoft Graph に対してクエリを実行して、Azure Active Directory からリスク検出とその関連情報を取得する方法を説明します
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 07/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86d88f841f76b367e83f0ae6b81e604e1b7f3e4b
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950121"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Azure Active Directory Identity Protection と Microsoft Graph の基本

Microsoft Graph は、Microsoft の統合 API エンドポイントであり、[Azure Active Directory Identity Protection](./overview-identity-protection.md) API のホームです。 危険なユーザーとサインインに関する情報を明らかにする API が 3 つあります。最初の API **riskDetection** を使用すると、Microsoft Graph に対して、ユーザーやサインインに結び付いているリスク検出とその検出に関連する情報の一覧のクエリを実行できます。 2 つ目の API である **riskyUsers** を使用すると、Microsoft Graph に対して、リスクとして検出されたユーザーの Identity Protection に関する情報のクエリを実行できます。 3 つ目の API である **signIn** を使用すると、Microsoft Graph に対して、リスク状態、詳細、およびレベルに関連する特定のプロパティを使用して、Azure AD のサインインに関する情報のクエリを実行できます。 

この記事では、Microsoft Graph への接続とこれらの API のクエリの概要について説明します。 さらに踏み込んだ概要や詳しい解説、Graph Explorer の利用については、[Microsoft Graph のサイト](https://graph.microsoft.io/)またはこれらの API に関する特定のリファレンス ドキュメントを参照してください。

* [riskDetection API](/graph/api/resources/riskdetection?view=graph-rest-v1.0)
* [riskyUsers API](/graph/api/resources/riskyuser?view=graph-rest-v1.0)
* [signIn API](/graph/api/resources/signin?view=graph-rest-v1.0)

## <a name="connect-to-microsoft-graph"></a>Microsoft Graph に接続する

Microsoft Graph を介して Identity Protection のデータにアクセスするには、次の 4 つのステップがあります。

- [ドメイン名を取得する](#retrieve-your-domain-name)
- [新しいアプリ登録を作成する](#create-a-new-app-registration)
- [API のアクセス許可を構成する](#configure-api-permissions)
- [有効な資格情報名を構成する](#configure-a-valid-credential)

### <a name="retrieve-your-domain-name"></a>ドメイン名の取得 

1. [Azure portal](https://portal.azure.com) にサインインします。  
1. **[Azure Active Directory]**  >  **[カスタム ドメイン名]** に移動します。 
1. `.onmicrosoft.com` ドメインをメモします。この情報は、後の手順で必要になります。

### <a name="create-a-new-app-registration"></a>新しいアプリ登録の作成

1. Azure portal で、 **[Azure Active Directory]**  >  **[アプリの登録]** に移動します。
1. **[新規登録]** を選択します。
1. **[作成]** ページで、以下の手順を実行します。
   1. **[名前]** ボックスにアプリケーションの名前 (例:Azure AD リスク検出 API)。
   1. **[サポートされているアカウントの種類]** で、その API を使用するアカウントの種類を選択します。
   1. **[登録]** を選択します。
1. **[アプリケーション ID]** をコピーします。

### <a name="configure-api-permissions"></a>API のアクセス許可を構成する

1. 作成した**アプリケーション**から、 **[API のアクセス許可]** を選択します。
1. **[構成されたアクセス許可]** ページで、上部ツール バーの **[アクセス許可の追加]** をクリックします。
1. **[API アクセスの追加]** ページで、 **[API を選択します]** をクリックします。
1. **[API を選択します]** ページで、 **[Microsoft Graph]** を選んで、 **[選択]** をクリックします。
1. **[API アクセス許可の要求]** ページで、以下を実行します。 
   1. **[アプリケーションのアクセス許可]** を選択します。
   1. [`IdentityRiskEvent.Read.All`] と [`IdentityRiskyUser.Read.All`] の横にあるチェックボックスをオンにします。
   1. **[アクセス許可の追加]** を選択します.
1. **[Grant admin consent for domain]\(ドメインに管理者の同意を与えます\)** を選択します。 

### <a name="configure-a-valid-credential"></a>有効な資格情報を構成する

1. 作成した**アプリケーション**から、 **[証明書とシークレット]** を選択します。
1. **[クライアント シークレット]** で、 **[新しいクライアント シークレット]** を選択します。
   1. クライアント シークレットの **[説明]** を入力し、組織のポリシーに従って有効期限を設定します。
   1. **[追加]** を選択します。

   > [!NOTE]
   > このキーを紛失した場合、このセクションに戻って新しいキーを作成する必要があります。 このキーはだれにも渡さないように注意してください。このキーがあれば、だれでもデータにアクセスすることができます。

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Microsoft Graph に対して認証を行って Identity Risk Detections API のクエリを実行する

この時点で次の情報が揃っている必要があります。

- テナントのドメインの名前
- アプリケーション (クライアント) ID 
- クライアント シークレットまたは証明書 

本人確認を行うために、post 要求を `https://login.microsoft.com` に送信します。要求本文に次のパラメーターを指定してください。

- grant_type: “**client_credentials**”
- resource: `https://graph.microsoft.com`
- client_id: \<your client ID\>
- client_secret: \<your key\>

成功すると、この要求では認証トークンが返されます。  
API を呼び出すためには、次のパラメーターを持つヘッダーを作成します。

```
`Authorization`="<token_type> <access_token>"
```

トークンの種類とアクセス トークンは、認証時に返されたトークンで確認できます。

このヘッダーは、API URL (`https://graph.microsoft.com/v1.0/identityProtection/riskDetections`) に対する要求として送信します。

成功した場合の応答は、OData JSON 形式の ID リスク検出とその関連データのコレクションです。必要に応じてこのデータを解析し、処理できます。

### <a name="sample"></a>サンプル

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

        $url = "https://graph.microsoft.com/v1.0/identityProtection/riskDetections"
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

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>すべてのオフラインリスク検出を取得する (riskDetection API)

Identity Protection サインイン リスク ポリシーを使用すると、リスクがリアルタイムで検出されたときに条件を適用できます。 オフラインで検出された場合はどうなるのでしょうか。 オフラインで発生したためにサインイン リスク ポリシーをトリガーしなかった検出を理解するには、riskDetection API に対してクエリを実行できます。

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>危険なサインイン ポリシー (riskyUsers API) によってトリガーされた MFA チャレンジに合格したすべてのユーザーを取得する

Identity Protection のリスクベースのポリシーが組織に与える影響を把握するには、危険なサインイン ポリシーによってトリガーされた MFA チャレンジに合格したすべてのユーザーのクエリを実行します。 この情報は、Identity Protection によって危険であると誤検出された可能性があるユーザーや、AI が危険と見なすアクションを実行している可能性がある正当なユーザーを把握するために役立ちます。

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

## <a name="next-steps"></a>次のステップ

以上、Microsoft Graph の API を呼び出す基本的な方法を紹介しました。  
これで、思いどおりに ID リスク検出のクエリを実行し、そのデータを活用できます。

Microsoft Graph に関する詳細情報のほか、Graph API を使ったアプリケーションの構築方法については、こちらの[ドキュメント](/graph/overview)と [Microsoft Graph](https://developer.microsoft.com/graph) のサイトをご覧ください。 

関連情報については、以下をご覧ください。

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)
- [Azure Active Directory Identity Protection で検出されるリスク検出の種類](./overview-identity-protection.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Microsoft Graph の概要](https://developer.microsoft.com/graph/docs)
- [Azure AD Identity Protection Service Root](/graph/api/resources/identityprotectionroot?view=graph-rest-1.0)