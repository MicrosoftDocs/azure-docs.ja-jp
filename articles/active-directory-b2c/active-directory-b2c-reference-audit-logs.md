---
title: Azure Active Directory B2C の監査ログのサンプルと定義 | Microsoft Docs
description: Azure AD B2C 監査ログへのアクセスに関するガイドとサンプル。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 2c1bfd9e2659127ab77e9db661b54fde18a8d25c
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205364"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Azure AD B2C 監査ログへのアクセス

Azure Active Directory B2C (Azure AD B2C) は、B2C リソース、発行されたトークン、および管理者のアクセス権に関するアクティビティ情報を含む監査ログを出力します。 この記事では、監査ログから入手できる情報の概要を示し、Azure AD B2C テナントのこのデータにアクセスする方法に関する手順を説明します。

> [!IMPORTANT]
> 監査ログは 7 日間のみ保持されます。 より長い保持期間が必要な場合は、次に示すいずれかの方法を使用して、ログをダウンロードして保存するための計画を立てます。

> [!NOTE]
> **[Azure Active Directory]** または **[Azure AD B2C]** ブレードの **[ユーザー]** セクションでは、個々の Azure AD B2C アプリケーションのユーザー サインインは確認できません。 そこでサインインすると、ユーザーのアクティビティは表示されますが、ユーザーがサインインした B2C アプリケーションに関連付けることはできません。 そのためには、この記事で後ほど説明するように、監査ログを使用する必要があります。

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>監査ログの B2C カテゴリで使用できるアクティビティの概要
監査ログの **B2C** カテゴリには、以下の種類のアクティビティが含まれています。

|アクティビティの種類 |説明  |
|---------|---------|
|Authorization |B2C リソースにアクセスするユーザー (例: B2C ポリシーの一覧にアクセスする管理者) の承認に関するアクティビティ         |
|Directory |管理者が Azure Portal を使用してサインインするときに取得されるディレクトリ属性に関連するアクティビティ |
|Application | B2C アプリケーションに対する CRUD 操作 |
|キー |B2C キー コンテナーに格納されたキーに対する CRUD 操作 |
|Resource |B2C リソース (例: ポリシーや ID プロバイダー) に対する CRUD 操作
|Authentication |ユーザー資格情報とトークン発行の検証|

> [!NOTE]
> ユーザー オブジェクトの CRUD アクティビティについては、**コア ディレクトリ** カテゴリを参照してください。

## <a name="example-activity"></a>アクティビティの例
次の例は、外部 ID プロバイダーを使用してユーザーがサインインするときにキャプチャされたデータを示しています: ![監査ログ - 例](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

アクティビティの詳細パネルには、次の関連情報が含まれています。

|Section|フィールド|説明|
|-------|-----|-----------|
| アクティビティ | Name | 実行されたアクティビティ。 たとえば、"Issue an id_token to the application" です (これで実際のユーザー サインインが終了します)。 |
| 開始者 (アクター) | オブジェクト ID | ユーザーがサインインする B2C アプリケーションの**オブジェクト ID** (この識別子は Azure portal には表示されませんが、Graph API などを使用してアクセスすることができます)。 |
| 開始者 (アクター) | Spn | ユーザーがサインインする B2C アプリケーションの**アプリケーション ID**。 |
| ターゲット | オブジェクト ID | サインインするユーザーの**オブジェクト ID**。 |
| 追加情報 | TenantId | Azure AD B2C テナントの**テナント ID**。 |
| 追加情報 | PolicyId | ユーザーのサインインに使用されるユーザー フロー (ポリシー) の **ポリシー ID**。 |
| 追加情報 | ApplicationId | ユーザーがサインインする B2C アプリケーションの**アプリケーション ID**。 |

## <a name="accessing-audit-logs-through-the-azure-portal"></a>Azure Portal からの監査ログへのアクセス
1. [Azure ポータル](https://portal.azure.com)にアクセスします。 [B2C] ディレクトリ内にいることを確認します。
2. 左側のお気に入りバーで **[Azure Active Directory]** をクリックします。
    
    ![監査ログ - AAD のボタン](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. **[アクティビティ]** の下の **[監査ログ]** をクリックします

    ![監査ログ - ログの選択](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. **[カテゴリ]** ドロップダウン ボックスで、 **[B2C]** を選択します
3. **[適用]** をクリックします

    ![監査ログ - カテゴリ](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

過去 7 日間にわたってログに記録されたアクティビティの一覧が表示されます。
- **[Activity Resource Type]\(アクティビティのリソースの種類)** ドロップダウン ボックスを使用して、前述したアクティビティの種類でフィルター処理します
- **[日付の範囲]** ドロップダウン ボックスを使用して、表示されるアクティビティの日付の範囲をフィルター処理します
- 一覧の特定の行をクリックすると、右側のコンテキスト ボックスに、そのアクティビティに関連付けられている追加の属性が表示されます
- **[ダウンロード]** をクリックし、アクティビティを csv ファイルとしてダウンロードします

> [!NOTE]
> また、左側のお気に入りバーで、 **[Azure Active Directory]** ではなく **[Azure AD B2C]** に移動することで監査ログを表示することもできます。 **[アクティビティ]** で **[監査ログ]** をクリックします。ここに、同様のフィルター処理機能による同じログが表示されます。

## <a name="accessing-audit-logs-through-the-azure-ad-reporting-api"></a>Azure AD Reporting API を使用した監査ログへのアクセス
監査ログは、Azure Active Directory の他のアクティビティと同じパイプラインに発行されるため、[Azure Active Directory Reporting API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference) を使用してアクセスできます。

### <a name="prerequisites"></a>前提条件
Azure AD Reporting API に対する認証を行うには、まずアプリケーションを登録する必要があります。 必ず、[Azure AD Reporting API にアクセスするための前提条件](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)の手順に従ってください。

### <a name="accessing-the-api"></a>API へのアクセス
API で Azure AD B2C 監査ログをダウンロードする場合は、ログをフィルター処理して **B2C** カテゴリに出力することをお勧めします。 カテゴリでフィルター処理するには、Azure AD Reporting API のエンドポイントを呼び出すときに、次に示すようにクエリ文字列パラメーターを使用します。

`https://graph.windows.net/your-b2c-tentant.onmicrosoft.com/activities/audit?api-version=beta&$filter=category eq 'B2C'`

### <a name="powershell-script"></a>PowerShell スクリプト
次のスクリプトは、PowerShell を使用して、Azure AD Reporting API のクエリを実行し、結果を JSON ファイルとして保存する例を示しています。

```powershell
# This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
$loginURL       = "https://login.microsoftonline.com"
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # AAD B2C Tenant; for example, contoso.onmicrosoft.com
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
    $url = 'https://graph.windows.net/' + $tenantdomain + '/activities/audit?api-version=beta&$filter=category eq ''B2C''and activityDate gt ' + $7daysago

    # loop through each query page (1 through n)
    Do {
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
```
