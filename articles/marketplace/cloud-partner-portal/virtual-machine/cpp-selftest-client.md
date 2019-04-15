---
title: 仮想マシンを事前に検証するための自己テスト クライアント - Azure Marketplace | Microsoft Docs
description: Azure Marketplace の仮想マシン イメージを事前に検証するための自己テスト クライアントを作成する方法。
services: Azure, Marketplace, Cloud Partner Portal, Virtual Machine
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: pbutlerm
ms.openlocfilehash: ae01b0fb088035240e670c16d4d457d8abda1bfa
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2019
ms.locfileid: "58848933"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-virtual-machine-image"></a>Azure 仮想マシン イメージを事前に検証するための自己テスト クライアントを作成する

この記事は、自己テスト API を使用するクライアント サービスを作成する場合のガイドとして使用してください。 自己テスト API を使用して仮想マシン (VM) を事前検証することで、Azure Marketplace の最新の公開要件を確実に満たすことができます。 このクライアント サービスを使用すると、マイクロソフト認定資格を受けるためにオファーを送信する前に VM をテストできます。

## <a name="development-and-testing-overview"></a>開発とテストの概要

自己テスト プロセスの一環として、Azure Marketplace に接続して Azure サブスクリプションで実行されている VM を検証するローカル クライアントを作成します。 VM では、Windows または Linux オペレーティング システムが実行されている可能性があります。

ローカル クライアントで、自己テスト API を使用して認証し、接続情報を送信し、テスト結果を受信するスクリプトを実行します。

自己テスト クライアントを作成する手順の概要は次のとおりです。

1. アプリケーションの Azure Active Directory (AD) テナントを選択します。
2. クライアント アプリを登録します。
3. クライアント Azure AD アプリのトークンを作成します。
4. そのトークンを自己テスト API に渡します。

クライアントを作成したら、VM に対してそれをテストすることができます。

### <a name="self-test-client-authorization"></a>自己テスト クライアントの承認

次の図は、クライアントの資格情報 (共有シークレットまたは証明書) を使用したサービス間の呼び出しの場合に承認がどのように機能するかを示しています。

![クライアントの承認プロセス](./media/stclient-dev-process.png)

## <a name="the-self-test-client-api"></a>自己テスト クライアント API

自己テスト API には、POST メソッドのみをサポートする 1 つのエンドポイントが含まれています。  その構造を次に示します。

```
Uri:             https://isvapp.azurewebsites.net/selftest-vm
Method:          Post
Request Header:  Content-Type: “application/json”
Authorization:   “Bearer xxxx-xxxx-xxxx-xxxxx”
Request body:    The Request body parameters should use the following JSON format:
                 {
                   "DNSName":"XXXX.westus.cloudapp.azure.com",
                   "User":"XXX",
                   "Password":"XXX@1234567",
                   "OS":"XXX",
                   "PortNo":"22",
                   "CompanyName":"ABCD",
                 }
```

次の表で、API のフィールドについて説明します。


|      フィールド         |    説明    |
|  ---------------   |  ---------------  |
|  Authorization     |  "Bearer xxxx-xxxx-xxxx-xxxxx" という文字列には、PowerShell を使用して作成できる Azure Active Directory (AD) クライアント トークンが含まれています。          |
|  DNSName           |  テストする VM の DNS 名    |
|  User              |  VM にサインインするためのユーザー名         |
|  パスワード          |  VM にサインインするためのパスワード          |
|  OS                |  VM のオペレーティング システム: `Linux` または `Windows` のどちらか          |
|  PortNo            |  VM に接続するための空きポート番号。 このポート番号は通常、Linux の場合は `22`、Windows の場合は `5986` です。          |
|  |  |

## <a name="consuming-the-api"></a>API の利用

PowerShell または cURL を使用して自己テスト API を利用できます。

### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>Linux OS 上で PowerShell を使用して API を利用する

PowerShell で API を呼び出すには、次の手順に従います。

1. `Invoke-WebRequest` コマンドを使用して API を呼び出します。
2. 次のコード例とスクリーン キャプチャに示すように、メソッドは Post、コンテンツの種類は JSON です。
3. 本文パラメーターを JSON 形式で指定します。

次のコード例は、API に対する PowerShell 呼び出しを示しています。

```powershell
$accesstoken = “Get token for your Client AAD App”
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Linux"
    "PortNo" = "22"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```
次のスクリーン キャプチャは、PowerShell で API を呼び出す場合の例を示しています。

![Linux OS 用の PowerShell を使用して API を呼び出す](./media/stclient-call-api-ps-linuxvm.png)

前の例を使用し、JSON を取得して解析し、以下の詳細を取得することができます。

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

`$res.Content` が表示されている次のスクリーン キャプチャで、JSON 形式のテスト結果の詳細がわかります。

![Linux に対する PowerShell 呼び出しの JSON 結果](./media/stclient-pslinux-rescontent-json.png)

次の画面キャプチャは、オンラインの JSON ビューアー ([Code Beautify](https://codebeautify.org/jsonviewer) や [JSON Viewer](https://jsonformatter.org/json-viewer) など) に表示された JSON テスト結果の例を示しています。

![Linux VM に対する PowerShell 呼び出しの JSON 結果](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>Windows OS 上で PowerShell を使用して API を利用する

PowerShell で API を呼び出すには、次の手順に従います。

1. `Invoke-WebRequest` コマンドを使用して API を呼び出します。
2. 次のコード例とスクリーン キャプチャに示すように、メソッドは Post、コンテンツの種類は JSON です。
3. Body パラメーターを JSON 形式で作成します。

次のコード例は、API に対する PowerShell 呼び出しを示しています。

```powershell
$accesstoken = “Get token for your Client AAD App”
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Windows"
    "PortNo" = "5986"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

次のスクリーン キャプチャは、PowerShell で API を呼び出す場合の例を示しています。

![Windows VM 用の PowerShell を使用して API を呼び出す](./media/stclient-call-api-ps-windowsvm.png)

前の例を使用し、JSON を取得して解析し、以下の詳細を取得することができます。

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

`$res.Content` が表示されている次のスクリーン キャプチャで、JSON 形式のテスト結果の詳細がわかります。

![Windows に対する PowerShell 呼び出しの JSON 結果](./media/stclient-pswindows-rescontent-json.png)

次のスクリーン キャプチャは、オンラインの JSON ビューアーに表示されたテスト結果を示しています
(たとえば、[Code Beautify](https://codebeautify.org/jsonviewer)、[JSON Viewer](https://jsonformatter.org/json-viewer))。

![Windows VM に対する PowerShell 呼び出しの JSON 結果](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>Linux OS 上で cURL を使用して API を利用する

cURL を使用して API を呼び出すには、次の手順に従います。

1. API を呼び出すには curl コマンドを使用します。
2. 次のコード スニペットに示すように、メソッドは Post、コンテンツの種類は JSON です。

```
CURL POST -H "Content-Type:application/json"
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX”
https://isvapp.azurewebsites.net/selftest-vm
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'
```

次のスクリーンは、curl を使用して API を呼び出す例を示しています。

![curl コマンドを使用して API を呼び出す](./media/stclient-consume-api-curl.png)

次のスクリーン キャプチャは、curl 呼び出しの JSON 結果を示しています。

![curl 呼び出しの JSON 結果](./media/stclient-consume-api-curl-json.png)


## <a name="choose-the-azure-ad-tenant-for-the-app"></a>アプリの Azure AD テナントを選択する

次の手順を使用して、アプリケーションを作成する Azure AD テナントを選択します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. 上部のメニュー バーにある自分のアカウントを選択し、[ディレクトリ] 一覧から、アプリケーションを登録する Active Directory テナントを選択します。 または、**[ディレクトリ + サブスクリプション]** アイコンを選択してグローバル サブスクリプション フィルターを表示します。 次のスクリーン キャプチャは、このフィルターの例を示しています。

   ![サブスクリプション フィルターを選択する](./media/stclient-subscription-filter.png)

3. 左側のナビゲーション バーで、**[すべてのサービス]** を選択してから **[Azure Active Directory]** を選択します。

   次の手順では、テナント名 (またはディレクトリ名) やテナント ID (またはディレクトリ ID) が必要になる場合があります。

   **テナント情報を取得するには:**

   **Azure Active Directory の [概要]** で "プロパティ" を検索し、**[プロパティ]** を選択します。 例として次のスクリーン キャプチャを使用します。

   - **名前** - テナント名またはディレクトリ名
   - **ディレクトリ ID** - テナント ID またはディレクトリ ID。またはスクロール バーを使用して [プロパティ] を見つけます。

   ![Azure Active Directory の [プロパティ] ページ](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>クライアント アプリを登録する

次の手順を使用して、クライアント アプリを登録します。

1. 左側のナビゲーション バーで、**[すべてのサービス]** を選択してから **[アプリの登録]** を選択します。
2. **[アプリの登録]** で **[+ 新しいアプリケーションの登録]** を選択します。
3. **[作成]** の以下のフィールドに必要な情報を入力します。

   - **名前** - アプリのフレンドリ名を入力します。 たとえば、"SelfTestClient" です。
   - **アプリケーションの種類** - **[Web App/API]\(Web アプリ/API\)** を選択します
   - **サインオン URL** - 「https:\//isvapp.azurewebsites.net/selftest-vm」と入力します

4. **作成**を選択します。
5. **[アプリの登録]** または **[登録済みのアプリ]** の下に **[アプリケーション ID]** をコピーします。

   ![アプリケーション ID を取得する](./media/stclient-app-id.png)

6. 登録済みのアプリのツール バーで、**[設定]** を選択します。
7. **[必要なアクセス許可]** を選択して、アプリケーションのアクセス許可を構成します。
8. **[必要なアクセス許可]** で **[+ 追加]** を選択します。
9. **[API アクセスの追加]** ページで **[API を選択します]** を選択します。
10. **[API を選択します]** で「Windows Azure classic deployment model (Windows Azure クラシック デプロイ モデル)」と入力して API を検索します。
11. 検索結果から **[Windows Azure classic deployment model]\(Windows Azure クラシック デプロイ モデル\)** を選択し、**[選択]** をクリックします。

    ![アプリのマルチテナントを構成する](./media/stclient-select-api.png)

12. **[API アクセスの追加]** で、**[アクセス許可を選択します]** を選択します。
13. **[Access "Windows Azure Service Management API"]\("Windows Azure Service Management API" へのアクセス\)** を選択します。

    ![アプリの API アクセスを有効にする](./media/stclient-enable-api-access.png)

14. **[選択]** をクリックします。
15. **[完了]** を選択します。
16. **[設定]** で **[プロパティ]** を選択します。
17. **[プロパティ]** で **[マルチテナント]** まで下にスクロールします。 **[はい]** を選択します。

    ![アプリのマルチテナントを構成する](./media/stclient-yes-multitenant.png)

18. **[保存]** を選択します。
19. **[設定]** で **[キー]** を選択します。
20. [キー] の **[説明]** テキストボックスを選択してシークレット キーを作成します。 次のフィールドを構成します。

    - キー名を入力します。 たとえば、"selftestclient" です。
    - **[有効期限]** ドロップダウン リストで、[1 年] を選択します。
    - **[保存]** を選択してキーを生成します。
    - **[値]** でキーをコピーします。

      >[!Important]
      >**[キー]** フォームを終了すると、キー値を確認できなくなります。

    ![キー値フォーム](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>クライアント アプリのトークンを作成する

OAuth REST API を使用してトークンを作成および取得するには、次のプログラムのいずれかを使用できます。

- postman
- Linux の cURL
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>Postman を使用してトークンを作成および取得するには

 承認されたアプリケーションのいずれかのトークンを Auth0 に要求するには、次の形式のペイロードを使用して [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) エンドポイントに対して POST 操作を実行します。

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```

要求本文で次のパラメーターを渡します。

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

要求ヘッダーで次のパラメーターを渡します。

```
Content-Type: application/x-www-form-urlencoded
```

次のスクリーン キャプチャは、Postman を使用してトークンを取得する例を示しています。

![Postman を使用してトークンを取得する](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>Linux で cURL を使用してトークンを作成および取得するには

承認されたアプリケーションのいずれかのトークンを Auth0 に要求するには、次の形式のペイロードを使用して [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) エンドポイントに対して POST 操作を実行します。

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

次のスクリーン キャプチャは、curl コマンドを使用してトークンを取得する例を示しています。

![curl コマンドを使用してトークンを取得する](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>C&#35; を使用してトークンを作成および取得するには

承認されたアプリケーションのいずれかのトークンを Auth0 に要求するには、次の形式のペイロードを使用して https:\//soamtenant.auth0.com/oauth/token エンドポイントに対して POST 操作を実行します。

```csharp
string clientId = "Your Application Id";
string clientSecret = "Your Application Secret";
string audience = "https://management.core.windows.net";
string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, "https://login.microsoftonline.com/common/oauth2/token");
string grantType = "client_credentials";
var client = new RestClient(authority);
var request = new RestRequest(Method.POST);
       request.AddHeader("content-type", "application/x-www-form- urlencoded");

string requestBody = "grant_type=" + grantType + "&" + "client_id=" +   clientId + "&" + "client_secret=" + clientSecret + "&" + "resource=" +  audience;

request.AddParameter("application/x-www-form-urlencoded", requestBody,  ParameterType.RequestBody);

IRestResponse response = client.Execute(request);
var content = response.Content;
var token = JObject.Parse(content)["access_token"];
```

### <a name="to-create-and-get-a-token-using-powershell"></a>PowerShell を使用してトークンを作成および取得するには

承認されたアプリケーションのいずれかのトークンを Auth0 に要求するには、次の形式のペイロードを使用して https:\//soamtenant.auth0.com/oauth/token エンドポイントに対して POST 操作を実行します。

```powershell
$clientId = "Application Id of AD Client APP";
$clientSecret = "Secret Key of AD Client APP “
$audience = "https://management.core.windows.net";
$authority = "https://login.microsoftonline.com/common/oauth2/token"
$grantType = "client_credentials";

$requestBody = "grant_type=" + $grantType + "&" + "client_id=" +  $clientId   + "&" + "client_secret=" + $clientSecret + "&" + "resource=" + $audience;

$headers = New-Object  "System.Collections.Generic.Dictionary[[String],[String]]"
             $headers.Add("ContentType", "application/x-www-form-urlencoded")
resp = Invoke-WebRequest -Method Post -Uri $authority -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $requestBody

$token = $resp.Content | ConvertFrom-Json
$token.AccessToken
```

## <a name="pass-the-client-app-token-to-the-api"></a>クライアント アプリ トークンを API に渡す

承認ヘッダーに次のコードを使用して、トークンを自己テスト API に渡します。

```powershell
$redirectUri = ‘https://isvapp.azurewebsites.net/selftest-vm’
$accesstoken = ‘place your token here’

$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body =
@{
      'DNSName'="XXXX.cloudapp.azure.com";
      'User'="XXX";
      'Password'="XXXX@12345";
      'OS'="Linux";
      'PortNo'="22"
  } | ConvertTo-Json

$result=Invoke-WebRequest -Method Post -Uri $redirectUri -Headers $headers -ContentType 'application/json' -Body $Body
$result
Write-Output 'Test Results:'
$result.Content
```

## <a name="test-your-self-test-client"></a>自己テスト クライアントをテストする

クライアントをテストするには、次の手順に従います。

1. テストする VM をデプロイします。
2. 承認にクライアント アプリ トークンを使用して自己テスト API を呼び出します。
3. JSON 形式のテスト結果を取得します。

### <a name="test-result-examples"></a>テスト結果の例

次のスニペットは、JSON 形式のテスト結果を示しています。

**Windows VM のテスト結果:**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Microsoft Windows Server 2016 Datacenter",
  "OSVersion": "10.0.14393",
  "CreatedDate": "06/01/2018 07:48:04",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "Tes tID": "1.1.4",
      "TestCaseName": "OS Architecture",
      "Description": "Azure supports 64bit Operating System Only.",
      "Result": "Passed",
      "ActualValue": "64 Bit",
      "Re quiredValue": "OS Should be 64 bit"
    },
    {
      "TestID": "1.1.5",
      "TestCaseName": "User account dependency",
      "Description": "Application execution should not have de pendency on administrator account.",
      "Result": "Passed",
      "ActualValue": "isv",
      "RequiredValue": "Logged in user should not be an administrator"
    },
    {
      "TestID": " 1.1.6",
      "TestCaseName": "Failover Cluster",
      "Description": "Windows Server Failover Clustering feature is not yet supported, Application should not have dependency on this feature.",
      "Result": "Passed",
      "ActualValue": "Disabled",
      "RequiredValue": "Failover Clustering feature is disabled"
    },
```

**Linux VM のテスト結果:**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Ubuntu  16.04",
  "OSVersion": "16.04",
  "CreatedDate": "06/01/2018 07:04:24",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "TestID": "48",
      "TestCaseName": "Bash Hi story",
      "Description": "Bash history files should be cleared before creating the VM image.",
      "Result": "Passed",
      "ActualValue": "No file Exist",
      "RequiredVal ue": "1024"
    },
    {
      "TestID": "39",
      "TestCaseName": "Linux Agent Version",
      "Description": "Azure Linux Agent 2.2.10 and above should be installed. ",
      "Result": "Pas sed",
      "ActualValue": "2.2.20",
      "RequiredValue": "2.2.10"
    },
    {
      "TestID": "40",
      "TestCaseName": "Required Kernel Parameters",
      "Description": "Verifies the following  kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300",
      "Result": "Passed",
      "ActualValue": "Matched Parameter: console=ttyS0,earlypri ntk=ttyS0,rootdelay=300",
      "RequiredValue": "console=ttyS0#earlyprintk=ttyS0#rootdelay=300"
    },
```

## <a name="next-steps"></a>次の手順

Azure 仮想マシンを正常にテストしたら、[オファーを発行](./cpp-publish-offer.md)できます。
