---
title: "Azure Active Directory レポートの監査 API の例 | Microsoft Docs"
description: "Azure Active Directory Reporting API の概要について説明します。"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: de8b8ec3-49b3-4aa8-93fb-e38f52c99743
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2017
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 665b613db31b15b6f6d2826a0795be6275c832ca
ms.openlocfilehash: 8216fa7ab092b2d0225d075d933fa56fbab56f40


---
# <a name="azure-active-directory-reporting-audit-api-samples"></a>Azure Active Directory レポートの監査 API の例
このトピックは Azure Active Directory Reporting API に関するトピックのコレクションの一部です。  
Azure AD レポートは、コードまたは関連ツールを使用して監査データにアクセスできるようにする API を提供します。
このトピックでは、 **監査 API**のサンプル コードを提供します。

参照:

* [監査ログ](active-directory-reporting-azure-portal.md#audit-logs) に関する記事
* [Azure Active Directory Reporting API の概要](active-directory-reporting-api-getting-started.md) 」。

質問、問題点、またはフィードバックについては、 [AAD レポート ヘルプ](mailto:aadreportinghelp@microsoft.com)にお問い合わせください。


## <a name="prerequisites"></a>前提条件
このトピックに掲載されているサンプルを使用するには、事前に、 [Azure AD レポート API にアクセスするための前提条件](active-directory-reporting-api-prerequisites.md)を完了する必要があります。  

## <a name="known-issue"></a>既知の問題
EU リージョン内にテナントがある場合、アプリの認証は行われません。 この問題が解決されるまでは、回避策として、ユーザー認証を使用して監査 API にアクセスしてください。 

## <a name="powershell-script"></a>PowerShell スクリプト
    # This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

    # Constants
    $ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
    $ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
    $loginURL       = "https://login.microsoftonline.com"     # AAD Instance; for example https://login.microsoftonline.com
    $tenantdomain   = "your-tenant-domain.onmicrosoft.com"    # AAD Tenant; for example, contoso.onmicrosoft.com
    $resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
    $7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
    Write-Output "Searching for events starting $7daysago"

    # Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    # Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
    if ($oauth.access_token -ne $null) {   
        $i=0
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
        $url = 'https://graph.windows.net/' + $tenantdomain + '/activities/audit?api-version=beta&`$filter=activityDate gt ' + $7daysago

        # loop through each query page (1 through n)
        Do{
            # display each event on the console window
            Write-Output "Fetching data using Uri: $url"
            $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
            foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
                Write-Output ($event | ConvertTo-Json)
            }

            # save the query page to an output file
            Write-Output "Save the output to a file audit$i.json"
            $myReport.Content | Out-File -FilePath audit$i.json -Force
            $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
            $i = $i+1
        } while($url -ne $null)
    } else {
        Write-Host "ERROR: No Access Token"
        }

    Write-Host "Press any key to continue ..."
    $x = $host.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown")


### <a name="executing-the-powershell-script"></a>PowerShell スクリプトの実行
スクリプトの編集が完了したら、実行して、監査ログ レポートから予期したデータが返されることを確認します。

スクリプトからは、監査レポートの出力が JSON 形式で返されます。 また、同じ出力内容を使って `audit.json` ファイルも作成されます。 他のレポートからデータを返すようにスクリプトを変更してテストしたり、必要のない出力形式をコメント化したりできます。

## <a name="bash-script"></a>Bash スクリプト
    #!/bin/bash

    # Author: Ken Hoff (kenhoff@microsoft.com)
    # Date: 2015.08.20
    # NOTE: This script requires jq (https://stedolan.github.io/jq/)

    CLIENT_ID="your-application-client-id-here"         # Should be a ~35 character string insert your info here
    CLIENT_SECRET="your-application-client-secret-here" # Should be a ~44 character string insert your info here
    LOGIN_URL="https://login.windows.net"
    TENANT_DOMAIN="your-directory-name-here.onmicrosoft.com"    # For example, contoso.onmicrosoft.com

    TOKEN_INFO=$(curl -s --data-urlencode "grant_type=client_credentials" --data-urlencode "client_id=$CLIENT_ID" --data-urlencode "client_secret=$CLIENT_SECRET" "$LOGIN_URL/$TENANT_DOMAIN/oauth2/token?api-version=1.0")

    TOKEN_TYPE=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.token_type')
    ACCESS_TOKEN=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.access_token')

    # get yesterday's date

    YESTERDAY=$(date --date='1 day ago' +'%Y-%m-%d')

    URL="https://graph.windows.net/$TENANT_DOMAIN/activities/audit?api-version=beta&$filter=activityDate%20gt%20$YESTERDAY"


    REPORT=$(curl -s --header "Authorization: $TOKEN_TYPE $ACCESS_TOKEN" $URL)

    echo $REPORT | ./jq-win64.exe -r '.value' | ./jq-win64.exe -r ".[]"

## <a name="python-script"></a>Python スクリプト
    # Author: Michael McLaughlin (michmcla@microsoft.com)
    # Date: January 20, 2016
    # This requires the Python Requests module: http://docs.python-requests.org

    import requests
    import datetime
    import sys

    client_id = 'your-application-client-id-here'
    client_secret = 'your-application-client-secret-here'
    login_url = 'https://login.windows.net/'
    tenant_domain = 'your-directory-name-here.onmicrosoft.com'

    # Get an OAuth access token
    bodyvals = {'client_id': client_id,
                'client_secret': client_secret,
                'grant_type': 'client_credentials'}

    request_url = login_url + tenant_domain + '/oauth2/token?api-version=1.0'
    token_response = requests.post(request_url, data=bodyvals)

    access_token = token_response.json().get('access_token')
    token_type = token_response.json().get('token_type')

    if access_token is None or token_type is None:
        print "ERROR: Couldn't get access token"
        sys.exit(1)

    # Use the access token to make the API request
    yesterday = datetime.date.strftime(datetime.date.today() - datetime.timedelta(days=1), '%Y-%m-%d')

    header_params = {'Authorization': token_type + ' ' + access_token}
    request_string = 'https://graph.windows.net/' + tenant_domain + 'activities/audit?api-version=beta&$filter=activityDate%20gt%20' + yesterday   
    response = requests.get(request_string, headers = header_params)

    if response.status_code is 200:
        print response.content
    else:
        print 'ERROR: API request failed'





## <a name="next-steps"></a>次のステップ
* このトピックに掲載されているサンプルをカスタマイズしますか。 [Azure Active Directory 監査 API リファレンス](active-directory-reporting-api-audit-reference.md)を確認してください。 
* Azure Active Directory Reporting API の使用に関する網羅的な概要を参照するには、 [Azure Active Directory Reporting API の概要](active-directory-reporting-api-getting-started.md)を参照してください。
* Azure Active Directory のレポート作成に関する詳細については、「 [Azure Active Directory レポート ガイド](active-directory-reporting-guide.md)」を参照してください。  




<!--HONumber=Dec16_HO5-->


