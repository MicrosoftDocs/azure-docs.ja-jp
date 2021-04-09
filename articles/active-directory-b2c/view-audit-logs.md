---
title: 監査ログへのアクセスと確認
titleSuffix: Azure AD B2C
description: プログラムで、かつ Azure portal で Azure AD B2C 監査ログにアクセスする方法。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 04d5a989fcaf3cef3eea3b782842fb3255d4b317
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98661104"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Azure AD B2C 監査ログへのアクセス

Azure Active Directory B2C (Azure AD B2C) は、B2C リソース、発行されたトークン、および管理者のアクセス権に関するアクティビティ情報を含む監査ログを出力します。 この記事では、監査ログから入手できる情報の概要を示し、Azure AD B2C テナントのこのデータにアクセスする方法について説明します。

監査ログ イベントは **7 日間** のみ保持されます。 より長い保持期間が必要な場合は、次に示すいずれかの方法を使用して、ログをダウンロードして保存するための計画を立てます。

> [!NOTE]
> Azure portal の **[Azure Active Directory]** ページまたは **[Azure AD B2C]** ページの **[ユーザー]** セクションでは、個々の Azure AD B2C アプリケーションのユーザー サインインは確認できません。 そこでのサインイン イベントは、ユーザー アクティビティを示しますが、ユーザーがサインインした B2C アプリケーションに関連付けることはできません。 そのためには、この記事で後ほど説明するように、監査ログを使用する必要があります。

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>監査ログの B2C カテゴリで使用できるアクティビティの概要

監査ログの **B2C** カテゴリには、以下の種類のアクティビティが含まれています。

|アクティビティの種類 |説明  |
|---------|---------|
|承認 |B2C リソースにアクセスするユーザー (例: B2C ポリシーの一覧にアクセスする管理者) の承認に関するアクティビティ。         |
|ディレクトリ |管理者が Azure portal を使用してサインインするときに取得されるディレクトリ属性に関連するアクティビティ。 |
|Application | B2C アプリケーションでの作成、読み取り、更新、削除 (CRUD) 操作。 |
|Key |B2C キー コンテナーに格納されたキーに対する CRUD 操作。 |
|リソース |B2C リソースに対する CRUD 操作。 たとえば、ポリシーと ID プロバイダー。
|認証 |ユーザー資格情報とトークン発行の検証。|

ユーザー オブジェクトの CRUD アクティビティについては、**コア ディレクトリ** カテゴリを参照してください。

## <a name="example-activity"></a>アクティビティの例

次の Azure portal からのサンプル イメージは、外部 ID プロバイダー (この場合、Facebook) を使用してユーザーがサインインするときにキャプチャされたデータを示しています。

![Azure portal の監査ログにあるアクティビティの詳細ページの例](./media/view-audit-logs/audit-logs-example.png)

アクティビティの詳細パネルには、次の関連情報が含まれています。

|Section|フィールド|説明|
|-------|-----|-----------|
| アクティビティ | 名前 | 実行されたアクティビティ。 たとえば、実際のユーザー サインインを終了する "*アプリケーションへの id_token の発行*"。 |
| 開始者 (アクター) | ObjectId | ユーザーがサインインする B2C アプリケーションの **オブジェクト ID**。 この識別子は Azure portal には表示されませんが、Microsoft Graph API を使用してアクセスできます。 |
| 開始者 (アクター) | Spn | ユーザーがサインインする B2C アプリケーションの **アプリケーション ID**。 |
| ターゲット | ObjectId | サインインするユーザーの **オブジェクト ID**。 |
| 追加の詳細 | TenantId | Azure AD B2C テナントの **テナント ID**。 |
| 追加の詳細 | PolicyId | ユーザーのサインインに使用されるユーザー フロー (ポリシー) の **ポリシー ID**。 |
| 追加の詳細 | ApplicationId | ユーザーがサインインする B2C アプリケーションの **アプリケーション ID**。 |

## <a name="view-audit-logs-in-the-azure-portal"></a>Azure portal で監査ログを表示する

Azure portal は、Azure AD B2C テナントの監査ログ イベントへのアクセスを提供します。

1. [Azure ポータル](https://portal.azure.com)
1. Azure AD B2C テナントが含まれているディレクトリに切り替え、**Azure AD B2C** を参照します。
1. 左側の **[アクティビティ]** で、**[監査ログ]** を選択します。

過去 7 日間にわたってログに記録されたアクティビティ イベントの一覧が表示されます。

![Azure portal での 2 つのアクティビティ イベントがあるフィルターの例](./media/view-audit-logs/audit-logs-example-filter.png)

次のようないくつかのフィルター処理オプションが用意されています。

* **アクティビティ リソースの種類** - [使用できるアクティビティの概要](#overview-of-activities-available-in-the-b2c-category-of-audit-logs)に関するセクションの表で示されているアクティビティの種類でフィルター処理します。
* **日付** - 表示されているアクティビティの日付範囲をフィルター処理します。

一覧で行を選択すると、イベントのアクティビティの詳細が表示されます。

アクティビティ イベントの一覧をコンマ区切り値 (CSV) ファイルでダウンロードするには、**[ダウンロード]** を選択します。

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Azure AD Reporting API を使って監査ログを取得する

監査ログは、Azure Active Directory の他のアクティビティと同じパイプラインに発行されるため、[Azure Active Directory Reporting API](/graph/api/directoryaudit-list) を使用してアクセスできます。 詳細については、「[Azure Active Directory Reporting API の概要](../active-directory/reports-monitoring/concept-reporting-api.md)」をご覧ください。

### <a name="enable-reporting-api-access"></a>Reporting API のアクセスを有効にする

Azure AD Reporting API へのスクリプトベースまたはアプリケーションベースのアクセスを許可するには、次の API アクセス許可を持つ、Azure AD B2C テナントに登録されたアプリケーションが必要です。 B2C テナント内の既存のアプリケーションの登録でこれらのアクセス許可を有効にすることも、監査ログの自動化専用に新しく作成することもできます。

* Microsoft Graph > アプリケーションのアクセス許可 > AuditLog > AuditLog.Read.All

次の記事の手順に従って、必要なアクセス許可を使用してアプリケーションを登録します。

[Microsoft Graph を使用して Azure AD B2C を管理する](microsoft-graph-get-started.md)

適切なアクセス許可を使用してアプリケーションを登録したら、スクリプトを使用してアクティビティ イベントを取得する方法の例については、この記事の後半の「PowerShell スクリプト」セクションを参照してください。

### <a name="access-the-api"></a>API にアクセスする

API を使用して Azure AD B2C 監査ログ イベントをダウンロードするには、`B2C` カテゴリのログをフィルター処理します。 カテゴリでフィルター処理するには、Azure AD Reporting API のエンドポイントを呼び出すときに、`filter` クエリ文字列パラメーターを使用します。

```http
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>PowerShell スクリプト

次の PowerShell スクリプトは、Azure AD Reporting API に対してクエリを実行する方法の例を示しています。 API に対してクエリを実行すると、ログに記録されたイベントが標準出力に出力され、JSON 出力がファイルに書き込まれます。

このスクリプトは、[Azure Cloud Shell](overview.md) で試すことができます。 必ず、自分のアプリケーション ID、クライアント シークレット、Azure AD B2C テナントの名前で更新してください。

```powershell
# This script requires an application registration that's granted Microsoft Graph API permission
# https://docs.microsoft.com/azure/active-directory-b2c/microsoft-graph-get-started

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant domain name

$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 through n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?`$filter=loggedByService eq 'B2C' and activityDateTime gt  " + $7daysago

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

この記事で前に示したアクティビティ イベント例の JSON 表現を次に示します。

```json
{
    "id": "B2C_DQO3J_4984536",
    "category": "Authentication",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "result": "success",
    "resultReason": "N/A",
    "activityDisplayName": "Issue an id_token to the application",
    "activityDateTime": "2019-09-14T18:13:17.0618117Z",
    "loggedByService": "B2C",
    "operationType": "",
    "initiatedBy": {
        "user": null,
        "app": {
            "appId": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "servicePrincipalId": null,
            "servicePrincipalName": "00000000-0000-0000-0000-000000000000"
        }
    },
    "targetResources": [
        {
            "id": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "type": "User",
            "userPrincipalName": null,
            "groupType": null,
            "modifiedProperties": []
        }
    ],
    "additionalDetails": [
        {
            "key": "TenantId",
            "value": "test.onmicrosoft.com"
        },
        {
            "key": "PolicyId",
            "value": "B2C_1A_signup_signin"
        },
        {
            "key": "ApplicationId",
            "value": "00000000-0000-0000-0000-000000000000"
        },
        {
            "key": "Client",
            "value": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36"
        },
        {
            "key": "IdentityProviderName",
            "value": "facebook"
        },
        {
            "key": "IdentityProviderApplicationId",
            "value": "0000000000000000"
        },
        {
            "key": "ClientIpAddress",
            "value": "127.0.0.1"
        }
    ]
}
```

## <a name="next-steps"></a>次のステップ

他の管理タスクを自動化することもできます。たとえば、[Microsoft Graph を使用して Azure AD B2C ユーザーアカウントを管理します](microsoft-graph-operations.md)。