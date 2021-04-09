---
title: Azure Marketplace の Azure 仮想イメージをテストする
description: Azure Marketplace で Azure 仮想マシン オファーをテストして送信する方法について学習します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: kriti-ms
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 9ffba221625c57332cd695125651d92adc11cf60
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200375"
---
# <a name="test-a-virtual-machine-image"></a>仮想マシン イメージをテストする

このトピックでは、Azure Marketplace にデプロイする仮想マシン (VM) イメージをテストする手順について説明します。

## <a name="deploy-an-azure-vm"></a>Azure VM をデプロイする

Shared Image Gallery イメージから VM をデプロイするには、次の手順を実行します。

1. Shared Image Gallery イメージのバージョンに移動します。
1. [VM の作成] をクリックします。
1. 仮想マシン名を指定し、VM のサイズを選択します。
1. [確認および作成] をクリックします。 検証に成功したら、[作成] をクリックします

> [!NOTE]
> vhd ファイルから VM を作成する必要がある場合は、[Azure Resource Manager テンプレートを準備する](https://docs.microsoft.com/azure/marketplace/azure-vm-image-test#prepare-an-azure-resource-manager-template)か、[PowerShell を使用して Azure VM をデプロイする](https://docs.microsoft.com/azure/marketplace/azure-vm-image-test#deploy-an-azure-vm-using-powershell)手順に従います。

この記事では、商業マーケットプレースで仮想マシン (VM) イメージをテストして送信し、Azure Marketplace の最新の公開要件を満たしていることを確認する方法について説明します。

VM オファーを送信する前に、次の手順を完了します。

- 一般化されたイメージを使用して Azure VM をデプロイします。[承認済みのベースを使用した Azure 仮想マシンの作成](azure-vm-create-using-approved-base.md)に関するページまたは[独自のイメージを使用した仮想マシンの作成](azure-vm-create-using-own-image.md)に関するページを参照してください。
- 検証を実行します。

## <a name="run-validations"></a>検証を実行する

デプロイされたイメージに対して検証を実行する方法は 2 つあります。

### <a name="use-certification-test-tool-for-azure-certified"></a>Azure 認定用の認定テスト ツールを使用する

#### <a name="download-and-run-the-certification-test-tool"></a>認定テスト ツールをダウンロードして実行する

Azure 認定用の認定テスト ツールはローカルの Windows マシン上で動作しますが、そのテスト対象は Azure ベースの Windows と Linux VM です。 これにより、ユーザーの VM イメージが Microsoft Azure で使用できることと、VHD を準備するにあたってのガイダンスと要件が満たされていることが認定されます。

1. 次のリンクから、[Azure 認定用の認定テスト ツール](https://www.microsoft.com/download/details.aspx?id=44299)の最新版をダウンロードしてインストールします。
2. 認定ツールを開き、 **[Start New Test]\(新規テストの開始\)** を選択します。
3. [テスト情報] 画面で、実行する **テストの名前** を入力します。
4. VM のプラットフォームとして **[Windows Server]** または **[Linux]** のどちらかを選択します。 ここでのプラットフォームの選択は、以降のオプションに影響します。
5. 該当するデータベース サービスを VM で使用している場合、 **[Test for Azure SQL Database]\(Azure SQL Database のテスト\)** チェック ボックスをオンにします。

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>VM イメージに認定ツールを接続する

1. [SSH Authentication]\(SSH 認証\) モードを選択します:パスワード認証またはキー ファイル認証。
2. パスワード ベースの認証を使用する場合は、 **[VM の DNS 名]** 、 **[ユーザー名]** 、 **[パスワード]** の値を入力します。 既定の SSH ポート番号を変更することもできます。

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="VM のテスト情報の選択を示します。":::

3. キー ファイル ベースの認証を使用する場合は、VM の DNS 名、ユーザー名、秘密キーの場所の値を入力します。 パスフレーズを含めたり、既定の SSH ポート番号を変更したりすることもできます。
4. 完全修飾 VM DNS 名 (MyVMName.Cloudapp.net など) を入力します。
5. **[ユーザー名]** と **[パスワード]** を入力します。

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="VM ユーザー名とパスワードの選択を示します。":::

6. **[次へ]** を選択します。

#### <a name="run-a-certification-test"></a>認定テストを実行する

認定ツールで VM イメージのパラメーター値を指定したら、[接続テスト] を選択して VM への有効な接続を作成します。 接続の確認後、 **[次へ]** をクリックしてテストを開始します。 テストが完了すると、結果がテーブルに表示されます。 [Status]\(状態\) 列に、各テストについて Pass(合格)/Fail(不合格)/Warning(警告) が表示されます。 いずれかのテストが失敗した場合、イメージは認定 "されません"。 その場合は、要件と失敗のメッセージを確認して、提案された変更を行い、テストを再実行します。

自動テストが完了したら、 [Questionnaire]\(アンケート\) 画面の 2 つのタブ ( [General Assessment]\(全般的な評価\) と [Kernel Customization]\(カーネルのカスタマイズ\) ) で VM イメージに関する追加情報を入力し、 [Next]\(次へ\) を選択します。

最後の画面では、Linux VM イメージの SSH アクセスに関する情報や、例外を探している場合の不合格になった評価の説明などの詳細情報を指定できます。

最後に [Generate Report]\(レポートの生成\) を選択して、実行されたテスト ケースのテスト結果とログ ファイルを、アンケートに対する自分の回答と合わせてダウンロードします。
> [!Note]
> 一部の発行元には、VM にインストールされたファイアウォールなどのソフトウェアが含まれているために、その VM をロックする必要があるシナリオがあります。 この場合は、こちらから[認定テスト ツール](https://aka.ms/AzureCertificationTestTool)をダウンロードし、パートナーセンターの[サポート](https://aka.ms/marketplacepublishersupport)でレポートを送信してください。

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>PowerShell を使用して自己テスト API を消費する方法

### <a name="on-linux-os"></a>Linux OS の場合

PowerShell で API を呼び出します。

1. Invoke-WebRequest コマンドを使用して API を呼び出します。
2. 次のコード例とスクリーン キャプチャに示すように、メソッドは Post、コンテンツの種類は JSON です。
3. 本文パラメーターを JSON 形式で指定します。

次の例は、API への PowerShell 呼び出しを示しています。

```POWERSHELL
$accesstoken = "token"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$DNSName = "<Machine DNS Name>"
$UserName = "<User ID>"
$Password = "<Password>"
$OS = "Linux"
$PortNo = "22"
$CompanyName = "ABCD"
$AppID = "<Application ID>"
$TenantId = "<Tenant ID>"

$body = @{
   "DNSName" = $DNSName
   "UserName" = $UserName
   "Password" = $Password
   "OS" = $OS
   "PortNo" = $PortNo
   "CompanyName" = $CompanyName
   "AppID" = $AppID
   "TenantId" = $TenantId
} | ConvertTo-Json

$body

$uri = "URL"

$res = (Invoke-WebRequest -Method "Post" -Uri $uri -Body $body -ContentType "application/json" -Headers $headers).Content
```

<br>PowerShell での API の呼び出しの例を次に示します。

[![PowerShell で API を呼び出している画面の例。](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>前の例を使用し、JSON を取得して解析し、以下の詳細を取得することができます。

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>次のサンプル画面 (`$res.Content` を示します) は、JSON 形式でのテスト結果の詳細を示しています。

[![テスト結果の詳細を含む PowerShell で API を呼び出している画面の例。](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>オンラインの JSON ビューアー ([Code Beautify](https://codebeautify.org/jsonviewer) や [JSON Viewer](https://jsonformatter.org/json-viewer) など) に表示された JSON テスト結果の例を次に示します。

![オンラインの JSON ビューアーでのさらに多くのテスト結果。](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>Windows OS の場合

PowerShell で API を呼び出します。

1. Invoke-WebRequest コマンドを使用して API を呼び出します。
2. 次のコード例とサンプル画面に示すように、メソッドは Post であり、コンテンツの種類は JSON です。
3. 本文パラメーターを JSON 形式で作成します。

次のコード サンプルは、API への PowerShell 呼び出しを示しています。

```PowerShell
$accesstoken = "Get token for your Client AAD App"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$Body = @{ 
   "DNSName" = "XXXX.westus.cloudapp.azure.com"
   "UserName" = "XXX"
   "Password" = "XXX@123456"
   "OS" = "Windows"
   "PortNo" = "5986"
   "CompanyName" = "ABCD"
   "AppID" = "XXXX-XXXX-XXXX"
   "TenantId" = "XXXX-XXXX-XXXX"
} | ConvertTo-Json

$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

次のサンプル画面は、PowerShell で API を呼び出す例を示しています。

**SSH キーを使用**:

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="SSH キーを使用した PowerShell での API の呼び出し。":::

**パスワードを使用**:

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="パスワードを使用した PowerShell での API の呼び出し。":::

<br>前の例を使用し、JSON を取得して解析し、以下の詳細を取得することができます。

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>次の画面 (`$res.Content` を示します) は、JSON 形式でのテスト結果の詳細を示しています。

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="JSON 形式でのテスト結果の詳細。":::

<br>オンラインの JSON ビューアー ([Code Beautify](https://codebeautify.org/jsonviewer) や [JSON Viewer](https://jsonformatter.org/json-viewer) など) に表示されたテスト結果の例を次に示します。

![オンラインの JSON ビューアーでのテスト結果。](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>CURL を使用して Linux OS で自己テスト API を消費する方法

この例では、CURL を使用して Azure Active Directory とセルフホスト VM への POST API 呼び出しを実行します。

1. セルフホスト VM に対する認証を行うために Azure AD トークンを要求する

   CURL 要求で正しい値が置き換えられていることを確認します。

   ```JSON
   curl --location --request POST 'https://login.microsoftonline.com/{TENANT_ID}/oauth2/token' \
   --header 'Content-Type: application/x-www-form-urlencoded' \
   --data-urlencode 'grant_type=client_credentials' \
   --data-urlencode 'client_id={CLIENT_ID} ' \
   --data-urlencode 'client_secret={CLIENT_SECRET}' \
   --data-urlencode 'resource=https://management.core.windows.net'
   ```

   要求からの応答の例を次に示します。

   ```JSON
   {
       "token_type": "Bearer",
       "expires_in": "86399",
       "ext_expires_in": "86399",
       "expires_on": "1599663998",
       "not_before": "1599577298",
       "resource": "https://management.core.windows.net",
       "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJS…"
   }
   ```

2. 自己テスト VM に対する要求を送信する

   ベアラー トークンとパラメーターが正しい値に置き換えられていることを確認します。

   ```JSON
   curl --location --request POST 'https://isvapp.azurewebsites.net/selftest-vm' \
   --header 'Content-Type: application/json' \
   --header 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJS…' \
   --data-raw '{
       "DNSName": "avvm1.eastus.cloudapp.azure.com",
       "UserName": "azureuser",
       "Password": "SECURE_PASSWORD_FOR_THE_SSH_INTO_VM",
       "OS": "Linux",
       "PortNo": "22",
       "CompanyName": "COMPANY_NAME",
       "AppId": "CLIENT_ID_SAME_AS_USED_FOR_AAD_TOKEN ",
       "TenantId": "TENANT_ID_SAME_AS_USED_FOR_AAD_TOKEN"
   }'
   ```

   自己テスト VM API 呼び出しからの応答の例

   ```JSON
   {
       "TrackingId": "9bffc887-dd1d-40dd-a8a2-34cee4f4c4c3",
       "Response": "{\"SchemaVersion\":1,\"AppCertificationCategory\":\"Microsoft Single VM Certification\",\"ProviderID\":\"050DE427-2A99-46D4-817C-5354D3BF2AE8\",\"OSName\":\"Ubuntu 18.04\",\"OSDistro\":\"Ubuntu 18.04.5 LTS\",\"KernelVersion\":\"5.4.0-1023-azure\",\"KernelFullVersion\":\"Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\\n\",\"OSVersion\":\"18.04\",\"CreatedDate\":\"09/08/2020 01:13:47\",\"TestResult\":\"Pass\",\"APIVersion\":\"1.5\",\"Tests\":[{\"TestID\":\"48\",\"TestCaseName\":\"Bash History\",\"Description\":\"Bash history files should be cleared before creating the VM image.\",\"Result\":\"Passed\",\"ActualValue\":\"No file Exist\",\"RequiredValue\":\"1024\"},{\"TestID\":\"39\",\"TestCaseName\":\"Linux Agent Version\",\"Description\":\"Azure Linux Agent Version 2.2.41 and above should be installed.\",\"Result\":\"Passed\",\"ActualValue\":\"2.2.49\",\"RequiredValue\":\"2.2.41\"},{\"TestID\":\"40\",\"TestCaseName\":\"Required Kernel Parameters\",\"Description\":\"Verifies the following kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300\",\"Result\":\"Warning\",\"ActualValue\":\"Missing Parameter: rootdelay=300\\r\\nMatched Parameter: console=ttyS0,earlyprintk=ttyS0\",\"RequiredValue\":\"console=ttyS0#earlyprintk=ttyS0#rootdelay=300\"},{\"TestID\":\"41\",\"TestCaseName\":\"Swap Partition on OS Disk\",\"Description\":\"Verifies that no Swap partitions are created on the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"No. of Swap Partitions: 0\",\"RequiredValue\":\"swap\"},{\"TestID\":\"42\",\"TestCaseName\":\"Root Partition on OS Disk\",\"Description\":\"It is recommended that a single root partition is created for the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"Root Partition: 1\",\"RequiredValue\":\"1\"},{\"TestID\":\"44\",\"TestCaseName\":\"OpenSSL Version\",\"Description\":\"OpenSSL Version should be >=0.9.8.\",\"Result\":\"Passed\",\"ActualValue\":\"1.1.1\",\"RequiredValue\":\"0.9.8\"},{\"TestID\":\"45\",\"TestCaseName\":\"Python Version\",\"Description\":\"Python version 2.6+ is highly recommended. \",\"Result\":\"Passed\",\"ActualValue\":\"2.7.17\",\"RequiredValue\":\"2.6\"},{\"TestID\":\"46\",\"TestCaseName\":\"Client Alive Interval\",\"Description\":\"It is recommended to set ClientAliveInterval to 180. On the application need, it can be set between 30 to 235. \\nIf you are enabling the SSH for your end users this value must be set as explained.\",\"Result\":\"Warning\",\"ActualValue\":\"120\",\"RequiredValue\":\"ClientAliveInterval 180\"},{\"TestID\":\"49\",\"TestCaseName\":\"OS Architecture\",\"Description\":\"Only 64-bit operating system should be supported.\",\"Result\":\"Passed\",\"ActualValue\":\"x86_64\\n\",\"RequiredValue\":\"x86_64,amd64\"},{\"TestID\":\"50\",\"TestCaseName\":\"Security threats\",\"Description\":\"Identifies OS with recent high profile vulnerability that may need patching.  Ignore warning if system was patched as appropriate.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04\",\"RequiredValue\":\"OS impacted by GHOSTS\"},{\"TestID\":\"51\",\"TestCaseName\":\"Auto Update\",\"Description\":\"Identifies if Linux Agent Auto Update is enabled or not.\",\"Result\":\"Passed\",\"ActualValue\":\"# AutoUpdate.Enabled=y\\n\",\"RequiredValue\":\"Yes\"},{\"TestID\":\"52\",\"TestCaseName\":\"SACK Vulnerability patch verification\",\"Description\":\"Checks if the running Kernel Version has SACK vulnerability patch.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04.5 LTS,Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\",\"RequiredValue\":\"Yes\"}]}"
   }
   ```

## <a name="next-steps"></a>次の手順

- [パートナー センター](https://partner.microsoft.com/)にサインインします。
