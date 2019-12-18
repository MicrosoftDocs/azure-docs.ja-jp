---
title: 使用状況をレポートする API のサンプルと定義
titleSuffix: Azure AD B2C
description: Azure AD B2C テナントのユーザー、認証、多要素認証に関するレポートの取得についてのガイドとサンプル。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f81acf28b502965f896cd8b38767e7c2e925156c
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949340"
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Reporting API による Azure AD B2C の使用状況レポートへのアクセス

Azure Active Directory B2C (Azure AD B2C) は、ユーザーのサインインと Azure Multi-factor Authentication に基づく認証を提供します。 アプリケーション ファミリのエンドユーザーに、複数の ID プロバイダーにわたる認証が提供されます。 テナントに登録されているユーザー数、登録に使用したプロバイダー、およびタイプ別の認証の数を把握すれば、次のような質問に答えることができます。
* 過去 10 日間で各タイプの ID プロバイダー (Microsoft や LinkedIn などのアカウント) から何人のユーザーが登録したか。
* 過去 1 か月間で多要素認証を使用して正常に完了した認証は何件か。
* 今月完了したサインインベースの認証は何件か。 日別ではどうか。 アプリケーション別ではどうか。
* Azure AD B2C テナントのアクティビティの月間予想コストはどのように見積もることができるか。

この記事は、ユーザーの数、請求可能なサインインベースの認証の数、および多要素認証の数に基づく、請求アクティビティに関連付けられたレポートに焦点を当てます。


## <a name="prerequisites"></a>前提条件
開始する前に、[Azure AD Reporting API にアクセスするための前提条件](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)の手順を完了する必要があります。 アプリケーションを作成し、それに対するシークレットを取得して、それに Azure AD B2C テナントのレポートにアクセスする権限を付与します。 ここでは "*Bash スクリプト*" と "*Python スクリプト*" の例も提供します。

## <a name="powershell-script"></a>PowerShell スクリプト
このスクリプトでは、`TimeStamp` パラメーターと `ApplicationId` フィルターを使用して、4 つの使用状況レポートの作成を実行します。

```powershell
# This script will require the Web Application and permissions setup in Azure Active Directory

# Constants
$ClientID      = "your-client-application-id-here"
$ClientSecret  = "your-client-application-secret-here"
$loginURL      = "https://login.microsoftonline.com"
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
* **tenantUserCount**: 過去 30 日間の 1 日ごとに、ID プロバイダーのタイプ別にまとめたテナント内のユーザー数 (オプションで、`TimeStamp` フィルターは指定した日付から現在の日付までのユーザー数を提供します)。 レポートには次の情報があります。
  * **TotalUserCount**: すべてのユーザー オブジェクトの数。
  * **OtherUserCount**: Azure Active Directory ユーザーの数 (Azure AD B2C のユーザーではありません)。
  * **LocalUserCount**: Azure AD B2C テナントに対してローカルの資格情報を使用して作成された Azure AD B2C ユーザー アカウントの数。

* **AlternateIdUserCount**: 外部の ID プロバイダー (Facebook、Microsoft アカウント、別の Azure Active Directory テナントなど。`OrgId` とも呼ばれます) に登録されている Azure AD B2C ユーザーの数。

* **b2cAuthenticationCountSummary**: 過去 30 日間にわたる毎日の請求可能な認証の数を、日付と認証フローのタイプ別にまとめた情報。

* **b2cAuthenticationCount**: ある期間内の認証の数。 既定値は過去 30 日間です。  (省略可能: 開始と終了の `TimeStamp` パラメーターによって特定の期間を定義します。)出力には `StartTimeStamp` (このテナントのアクティビティの最も早い日付) と `EndTimeStamp` (最新の更新) が含まれます。

* **b2cMfaRequestCountSummary**: 毎日の多要素認証の数を、日付とタイプ (SMS または音声) 別にまとめた情報。


## <a name="limitations"></a>制限事項
ユーザー カウント データは 24 時間～ 48 時間ごとに更新されます。 認証は日に数回更新されます。 `ApplicationId` フィルターの使用時には、以下のいずれかの状態のために、レポートの応答が空になる場合があります。
  * アプリケーション ID がテナントに存在しません。 ID が正しいことを確認してください。
  * アプリケーション ID は存在しますが、そのレポート期間内にデータが見つかりません。 日付や時刻のパラメーターを確認してください。


## <a name="next-steps"></a>次の手順
### <a name="monthly-bill-estimates-for-azure-ad"></a>Azure AD の月ごとの請求額の見積もり
[Azure AD B2C の最新の価格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)と組み合わせることで、毎日、毎週、および毎月の Azure の消費量を見積もることができます。  全体のコストに影響する可能性があるテナント動作の変更を計画する場合は、特に見積もりが役立ちます。 実際のコストは、[リンクされている Azure サブスクリプション](active-directory-b2c-how-to-enable-billing.md)で確認できます。

### <a name="options-for-other-output-formats"></a>その他の出力形式のオプション
次のコードでは、JSON、名前の値のリスト、および XML に出力を送信する例を示します。
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath name-your-file.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```
