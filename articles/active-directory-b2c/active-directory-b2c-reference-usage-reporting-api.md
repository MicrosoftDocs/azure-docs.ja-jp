---
title: "Azure AD B2C Usage Reporting API のサンプルと定義 | Microsoft Docs"
description: "B2C Tenant のユーザー、認証、および MFA に関するレポートを取得する方法とそのサンプルを紹介します。"
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2017
ms.author: joroja
translationtype: Human Translation
ms.sourcegitcommit: 4b2cbf5f62ee63bada42b2a06506b793d4349fdb
ms.openlocfilehash: 7db9a45a4c80ea8d01937837dfa7a15c171fb66b
ms.lasthandoff: 02/10/2017


---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Reporting API による Azure AD B2C の使用状況レポートへのアクセス

Azure Active Directory B2C は、ID プロバイダー全体のアプリケーション ファミリーの全エンド ユーザーに、ログインおよび MFA ベースの認証を提供します。  テナントに登録されているユーザー数、登録に使用したプロバイダー、およびタイプ別の認証の数を把握することで、次のような質問に対する回答が得られます。
* 過去 10 日間で各タイプの ID プロバイダー (Microsoft アカウント、LinkedIn など) から何人のユーザーが登録したか。
* 過去&1; か月間で正常に完了した多要素認証は何件か。
* 今月完了したログインベースの認証は何件か。 日別ではどうか。 アプリケーション別ではどうか。
* B2C テナントのアクティビティの月間予想コストはどのように見積もることができるか。

この記事は、ユーザーの数、請求可能なログインベースの認証の数、および多要素認証の数に基づく、請求アクティビティに最も密接に関連付けられたレポートに焦点を当てます。


## <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Azure AD Reporting API にアクセスするための前提条件
開始する前に、[Azure AD Reporting API にアクセスするための前提条件](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)を完了する必要があります。  アプリケーションを作成し、それに対するシークレットを取得して、それに Azure AD B2C テナントのレポートにアクセスする権限を付与します。 ここでは "*Bash スクリプト*" と "*Python スクリプト*" の例も提供します。

## <a name="powershell-script"></a>PowerShell スクリプト
このスクリプトは、**TimeStamp** パラメーターと **-ApplicationId** フィルターを使用した&4; つの使用状況レポートを実行します。

```
# This script will require the Web Application and permissions setup in Azure Active Directory

# Constants
$ClientID      = "your-client-application-id-here"  
$ClientSecret  = "your-client-application-secret-here"
$loginURL      = "https://login.windows.net"
$tenantdomain  = "your-b2c-tenant-domain.onmicrosoft.com"  
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
if ($oauth.access_token -ne $null) {
    $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    Write-host Data from the tenantUserCount report
    Write-host ====================================================
     # Returns a JSON document for the report
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the tenantUserCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?%24filter=TimeStamp+gt+2016-10-15&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCountSummary report
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=TimeStamp+gt+2016-09-20+and+TimeStamp+lt+2016-10-03&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with ApplicationId filter
    Write-host ====================================================
    # Returns a JSON document for the " " report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCountSummary
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCount?%24filter=TimeStamp+gt+2016-09-10+and+TimeStamp+lt+2016-10-04&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with ApplicationId filter
    Write-host ====================================================
       $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
     Write-host $myReport.Content

} else {
    Write-Host "ERROR: No Access Token"
    }
```


## <a name="usage-report-definitions"></a>使用状況レポートの定義
**tenantUserCount** – 過去 30 日間における 1 日ごとの ID プロバイダー別のテナント内のユーザーの数  (オプションで、TimeStamp フィルターは指定した日付から現在の日付までのユーザー数を提供します)。 レポートには次の情報があります。
 * TotalUserCount = すべてのユーザー オブジェクトの数
 * OtherUserCount = AAD ディレクトリのユーザー (B2C 以外のユーザー) の数
 * LocalUserCount = B2C テナントに対してローカルの資格情報で作成された B2C ユーザー アカウントの数

**AlternateIdUserCount** = 外部 ID プロバイダー (Facebook、Microsoft アカウント、別名 OrgId のその他の AAD テナントなど) で登録されている B2C ユーザーの数

**b2cAuthenticationCountSummary** - 過去 30 日間における毎日の請求可能な認証の数を、日別または認証フローのタイプ別に合計

**b2cAuthenticationCount** - ある期間内の認証の数をカウントします。 既定値は、過去 30 日間です   (オプション: 開始時と終了時の TimeStamp はカウントを必要とする特定の期間を定義します)。出力には StartTimeStamp (このテナントのアクティビティの最も早い日付) と EndTimeStamp (最近の更新) が含まれます。

**b2cMfaRequestCountSummary** - 毎日の多要素認証の数を、日別または MFA (SMS または音声) のタイプ別に合計


## <a name="limitations"></a>制限事項
* ユーザー カウント データは 24 時間～ 48 時間ごとに更新されます。  認証は日に数回更新されます。
* ApplicationId フィルターを使用する際、次の場合は空のレポートが応答されます。
 * アプリケーション ID がテナントに存在しません。 ID が正しいことを確認してください。
 * アプリケーション ID は存在しますが、そのレポート期間内にデータが見つかりません。 日付と時刻のパラメーターを確認してください。


## <a name="next-steps"></a>次のステップ
### <a name="estimating-your-azure-ad-monthly-bill"></a>Azure AD の毎月の請求を見積もります。
[Azure AD B2C の最新の価格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)と組み合わせることで、毎日、毎週、および毎月の Azure の消費量を見積もることができます。  テナント動作の変更は全体的なコストに影響する可能性があるため、その変更を計画する際に見積もりは特に便利です。  実際のコストは、[リンクされている Azure サブスクリプション](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing)で確認できます。

### <a name="options-for-other-output-formats"></a>その他の出力形式のオプション
```
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath b2cUserJourneySummaryEvents.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/        
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/    

