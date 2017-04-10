---
title: "Azure Active Directory サインイン アクティビティ レポート API のサンプル | Microsoft Docs"
description: "Azure Active Directory Reporting API の概要について説明します。"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: c41c1489-726b-4d3f-81d6-83beb932df9c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2016
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e6b1137c8ca33774ef9852b9441b541cf7723ebd


---
# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Azure Active Directory サインイン アクティビティ レポート API のサンプル
このトピックは Azure Active Directory Reporting API に関するトピックのコレクションの一部です。  
Azure AD レポートは、コードまたは関連ツールを使用してサインイン アクティビティ データにアクセスできるようにする API を提供します。  
このトピックでは、 **サインイン アクティビティ API**のサンプル コードを提供します。

参照:

* [監査ログ](active-directory-reporting-azure-portal.md#audit-logs)に関する記事
* [Azure Active Directory Reporting API の概要](active-directory-reporting-api-getting-started.md) 」。

質問、問題点、またはフィードバックについては、 [AAD レポート ヘルプ](mailto:aadreportinghelp@microsoft.com)にお問い合わせください。

## <a name="prerequisites"></a>前提条件
このトピックに掲載されているサンプルを使用するには、事前に、 [Azure AD レポート API にアクセスするための前提条件](active-directory-reporting-api-prerequisites.md)を完了する必要があります。  

## <a name="powershell-script"></a>PowerShell スクリプト
    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID       = "<clientId>"             # Should be a ~35 character string insert your info here
    $ClientSecret   = "<clientSecret>"         # Should be a ~44 character string insert your info here
    $loginURL       = "https://login.windows.net/"
    $tenantdomain   = "<tenantDomain>"
    $ daterange            # For example, contoso.onmicrosoft.com

    $7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
    # or, AddMinutes(-5)

    Write-Output $7daysago

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}

    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    $url = "https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&`$filter=signinDateTime ge $7daysago"

    $i=0

    Do{
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        Write-Output "Save the output to a file SigninActivities$i.json"
        Write-Output "---------------------------------------------"
        $myReport.Content | Out-File -FilePath SigninActivities$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)

    } else {

        Write-Host "ERROR: No Access Token"
    }




## <a name="executing-the-script"></a>スクリプトの実行
スクリプトの編集が完了したら、実行して、監査ログ レポートから予期したデータが返されることを確認します。

スクリプトからは、サインイン レポートの出力が JSON 形式で返されます。 また、同じ出力内容を使って `SigninActivities.json` ファイルも作成されます。 他のレポートからデータを返すようにスクリプトを変更してテストしたり、必要のない出力形式をコメント化したりできます。

## <a name="next-steps"></a>次のステップ
* このトピックに掲載されているサンプルをカスタマイズしますか。 [Azure Active Directory サインイン アクティビティ レポート API リファレンス](active-directory-reporting-api-sign-in-activity-reference.md)を確認してください。 
* Azure Active Directory Reporting API の使用に関する網羅的な概要を参照するには、 [Azure Active Directory Reporting API の概要](active-directory-reporting-api-getting-started.md)を参照してください。
* Azure Active Directory のレポート作成に関する詳細については、「 [Azure Active Directory レポート ガイド](active-directory-reporting-guide.md)」を参照してください。  




<!--HONumber=Dec16_HO5-->


