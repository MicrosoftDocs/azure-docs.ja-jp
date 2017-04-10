---
title: "Azure Active Directory Identity Protection と Microsoft Graph の基本 | Microsoft Docs"
description: "Microsoft Graph に対するクエリを通じて、Azure Active Directory から一連のリスク イベントとその関連情報を取得する方法を説明します。"
services: active-directory
keywords: "azure active directory identity protection, リスク イベント, 脆弱性, セキュリティ ポリシー, Microsoft Graph"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 77031b3bbe2372bf2cac34bac45368ac40911641
ms.openlocfilehash: 9c7c10031c068eeb02b4468ec48bf60aece2f12e


---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Azure Active Directory Identity Protection と Microsoft Graph の基本
Microsoft Graph は、Microsoft のさまざまな API を統合したエンドポイントであり、[Azure Active Directory Identity Protection](active-directory-identityprotection.md) の API の母体となっています。 そこに初めて導入された API が **identityRiskEvents** です。Microsoft Graph に対して一連の[リスク イベント](active-directory-identityprotection-risk-events-types.md)とその関連情報を照会することができます。 この記事では、この API クエリの基礎について説明します。 さらに踏み込んだ概要や詳しい解説、Graph Explorer の利用については、[Microsoft Graph のサイト](https://graph.microsoft.io/)を参照してください。


Microsoft Graph を介して Identity Protection のデータにアクセスするには、次の 3 つのステップがあります。

1. アプリケーションとクライアント シークレットを追加します。 
2. このシークレットとその他いくつかの情報を使って Microsoft Graph に本人性を証明し、認証トークンを取得します。 
3. このトークンを使って API エンドポイントに要求を行い、Identity Protection データを取得します。

以降の手順を開始する前に次の情報が必要となります。

* Azure AD にアプリケーションを作成するための管理者特権
* テナントのドメインの名前 (例: contoso.onmicrosoft.com)

## <a name="add-an-application-with-a-client-secret"></a>アプリケーションとクライアント シークレットを追加する
1. [サインイン](https://manage.windowsazure.com) します。 
2. 左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。 
   
    ![Creating an application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_01.png)
3. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
4. 上部のメニューで、 **[アプリケーション]**をクリックします。
   
    ![Creating an application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_02.png)
5. ページの下部にある **[追加]** をクリックします。
   
    ![Creating an application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_03.png)
6. **[実行する作業を選択してください]** ダイアログで、**[組織で開発中のアプリケーションを追加]** をクリックします。
   
    ![Creating an application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_04.png)
7. **[アプリケーション情報の指定]** ダイアログで、次の手順を実行します。
   
    ![Creating an application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_05.png)
   
    a. **[名前]** ボックスにアプリケーションの名前 (例: AADIP Risk Event API Application) を入力します。
   
    b. **[種類]** として **[Web アプリケーションや Web API]** を選択します。
   
    c. **[次へ]**をクリックします。
8. **[アプリケーションのプロパティ]** ダイアログで、次の手順を実行します。
   
    ![Creating an application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_06.png)
   
    a. **[サインオン URL]** ボックスに、「`http://localhost`」と入力します。
   
    b. **[アプリケーション ID/URI]** ボックスに、「`http://localhost`」と入力します。
   
    c. ページの下部にある **[完了]**」を参照してください。

続けてアプリケーションの構成を行います。

![Creating an application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_07.png)

## <a name="grant-your-application-permission-to-use-the-api"></a>API を使用するためのアクセス許可をアプリケーションに付与します。
1. 対象アプリケーションのページで、上部のメニューにある **[構成]**をクリックします。 
   
    ![Creating an application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_08.png)
2. **[その他のアプリケーションに対するアクセス許可]** セクションで、**[アプリケーションの追加]** をクリックします。
   
    ![Creating an application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_09.png)
3. **[他のアプリケーションに対するアクセス許可]** ダイアログで、次の手順を実行します。
   
    ![Creating an application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_10.png)
   
    a. **[Microsoft Graph]** を選択します。
   
    b. ページの下部にある **[完了]**」を参照してください。
4. **[アプリケーションのアクセス許可: 0]** をクリックし、**[Read all identity risk event information (すべての ID リスク イベント情報の読み取り)]** を選択します。
   
    ![Creating an application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_11.png)
5. ページの下部にある **[保存]** をクリックします。
   
    ![Creating an application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)

## <a name="get-an-access-key"></a>アクセス キーを取得する
1. 対象アプリケーションのページの **[キー]** セクションで、期間として 1 年を選択します。
   
    ![Creating an application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_13.png)
2. ページの下部にある **[保存]** をクリックします。
   
    ![Creating an application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)
3. [キー] セクションで、新しく作成したキーの値をコピーし、安全な場所に貼り付けます。
   
    ![Creating an application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_14.png)
   
   > [!NOTE]
   > このキーを紛失した場合、このセクションに戻って新しいキーを作成する必要があります。 このキーはだれにも渡さないように注意してください。このキーがあれば、だれでもデータにアクセスすることができます。
   > 
   > 
4. **[プロパティ]** セクションで **[クライアント ID]** をコピーし、安全な場所に貼り付けます。 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Microsoft Graph に本人であることを証明して Identity Risk Events API を照会する
この時点で次の情報が揃っている必要があります。

* 上記でコピーしたクライアント ID
* 上記でコピーしたキー
* テナントのドメインの名前

本人確認を行うために、post 要求を `https://login.microsoft.com` に送信します。要求本文に次のパラメーターを指定してください。

* grant_type: “**client_credentials**”
* resource: “**https://graph.microsoft.com**”
* client_id: <your client ID>
* client_secret: <your key>

> [!NOTE]
> **client_id** パラメーターと **client_secret** パラメーターにはそれぞれ値を指定する必要があります。
> 
> 

認証に成功すると、認証トークンが返されます。  
API を呼び出すためには、次のパラメーターを持つヘッダーを作成します。

    `Authorization`=”<token_type> <access_token>"


トークンの種類とアクセス トークンは、認証時に返されたトークンで確認できます。

このヘッダーは、API URL (`https://graph.microsoft.com/beta/identityRiskEvents`) に対する要求として送信します。

成功した場合の応答には、一連の ID リスク イベントとその関連データが OData JSON 形式で格納されます。必要に応じてこのデータを解析し、処理してください。

以下に示したコードは、Powershell を使用して認証と API 呼び出しを行う例です。  
該当するクライアント ID とキー、テナントのドメインを追加してください。

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


## <a name="next-steps"></a>次のステップ
以上、Microsoft Graph の API を呼び出す基本的な方法を紹介しました。  
これを応用すれば、思いどおりに ID リスク イベントを照会し、そのデータを活用することができます。

Microsoft Graph に関する詳細情報のほか、Graph API を使ったアプリケーションの構築方法については、こちらの[ドキュメント](https://graph.microsoft.io/docs)と [Microsoft Graph](https://graph.microsoft.io/) のサイトをご覧ください。 また、[Azure AD Identity Protection API](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) のページでは、Graph で利用できる Identity Protection の全 API の一覧を掲載しています。ぜひお気に入りに登録してご利用ください。 API を介した Identity Protection の操作方法が新たに追加された場合、このページに反映していく予定です。

## <a name="additional-resources"></a>その他のリソース
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)
* [Azure Active Directory Identity Protection で検出されるリスク イベントの種類](active-directory-identityprotection-risk-events-types.md)
* [Microsoft Graph](https://graph.microsoft.io/)
* [Microsoft Graph の概要](https://graph.microsoft.io/docs)
* [Azure AD Identity Protection Service Root](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)




<!--HONumber=Nov16_HO3-->


