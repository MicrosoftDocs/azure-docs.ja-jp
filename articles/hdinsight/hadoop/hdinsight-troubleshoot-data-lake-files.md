---
title: Azure HDInsight で Data Lake ストレージ ファイルにアクセスできない
description: Azure HDInsight で Data Lake ストレージ ファイルにアクセスできない
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/13/2019
ms.openlocfilehash: f4c5a23b604334952730fcc4cf1fcb3fcbed6237
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944397"
---
# <a name="unable-to-access-data-lake-storage-files-in-azure-hdinsight"></a>Azure HDInsight で Data Lake ストレージ ファイルにアクセスできない

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue-acl-verification-failed"></a>問題点:ACL の検証の失敗

次のようなエラー メッセージが表示されます。

```
LISTSTATUS failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.).
```

### <a name="cause"></a>原因

ユーザーによってファイル/フォルダーのサービス プリンシパル (SP) のアクセス許可が取り消されている可能性があります。

### <a name="resolution"></a>解像度

1. SP に、パスに沿って走査するための 'x' アクセス許可があることを確認します。 詳細については、「 [アクセス許可](https://hdinsight.github.io/ClusterCRUD/ADLS/adls-create-permission-setup.html)」を参照してください。 Data Lake ストレージ アカウント内のファイルまたはフォルダーへのアクセスを確認するための `dfs` コマンドの例:

    ```
    hdfs dfs -ls /<path to check access>
    ```

1. 実行される読み取り/書き込み操作に基づいて、パスにアクセスするために必要なアクセス許可を設定します。 さまざまなファイル システム操作に必要なアクセス許可については、こちらを参照してください。

---

## <a name="issue-service-principal-certificate-expiry"></a>問題点:サービス プリンシパルの証明書の有効期限切れ

次のようなエラー メッセージが表示されます。

```
Token Refresh failed - Received invalid http response: 500
```

### <a name="cause"></a>原因

サービス プリンシパルにアクセスするために提供された証明書の有効期限が切れている可能性があります。

1. ヘッドノードに SSH 接続します。 次の `dfs` コマンドを使用して、ストレージ アカウントへのアクセスを確認します。

    ```
    hdfs dfs -ls /
    ```

1. エラー メッセージが次の出力のようになっていることを確認します。

    ```
    {"stderr": "-ls: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken}}...
    ```

1. `core-site.xml property` - `fs.azure.datalake.token.provider.service.urls` からいずれかの URL を取得します。

1. 次の curl コマンドを実行して、OAuth トークンを取得します。

    ```
    curl gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken
    ```

1. 有効なサービス プリンシパルの出力は次のようになります。

    ```
    {"AccessToken":"MIIGHQYJKoZIhvcNAQcDoIIGDjCCBgoCAQA…….","ExpiresOn":1500447750098}
    ```

1. サービス プリンシパルの証明書の有効期限が切れている場合、出力は次のようになります。

    ```
    Exception in OAuthTokenController.GetOAuthToken: 'System.InvalidOperationException: Error while getting the OAuth token from AAD for AppPrincipalId 23abe517-2ffd-4124-aa2d-7c224672cae2, ResourceUri https://management.core.windows.net/, AADTenantId https://login.windows.net/80abc8bf-86f1-41af-91ab-2d7cd011db47, ClientCertificateThumbprint C49C25705D60569884EDC91986CEF8A01A495783 ---> Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException: AADSTS70002: Error validating credentials. AADSTS50012: Client assertion contains an invalid signature. **[Reason - The key used is expired.**, Thumbprint of key used by client: 'C49C25705D60569884EDC91986CEF8A01A495783', Found key 'Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1', Configured keys: [Key0:Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1;]]
    Trace ID: e4d34f1c-a584-47f5-884e-1235026d5000
    Correlation ID: a44d870e-6f23-405a-8b23-9b44aebfa4bb
    Timestamp: 2017-10-06 20:44:56Z ---> System.Net.WebException: The remote server returned an error: (401) Unauthorized.
    at System.Net.HttpWebRequest.GetResponse()
    at Microsoft.IdentityModel.Clients.ActiveDirectory.HttpWebRequestWrapper.<GetResponseSyncOrAsync>d__2.MoveNext()
    ```

1. その他の Azure Active Directory に関連するエラー/証明書に関連するエラーは、ゲートウェイの URL を ping して OAuth トークンを取得することによって認識できます。

1. HDI クラスターから ADLS にアクセスしようとしたときに、次のエラーが発生した場合は、 前述の手順に従って、証明書の有効期限が切れているかどうかを確認します。

    ```
    Error: java.lang.IllegalArgumentException: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://clustername.hmssomerandomstringc.cx.internal.cloudapp.net:909/api/oauthtoken}
    ```

### <a name="resolution"></a>解像度

次の PowerShell スクリプトを使用して、新しい証明書を作成するか、既存の証明書を割り当てます。

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RGNAME'
$subscriptionId = 'SUBSCRIPTIONID'
$appId = 'APPLICATIONID'
$generateSelfSignedCert = $false
$addNewCertKeyCredential = $true
$certFilePath = 'NEW_CERT_PFX_LOCAL_PATH'
$certPassword = Read-Host "Enter Certificate Password"

if($generateSelfSignedCert)
{
    Write-Host "Generating new SelfSigned certificate"
    
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
else
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}


Login-AzureRmAccount

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"

    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    
    New-AzureRmADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore

    Write-Host "Waiting for 30 seconds for the permissions to get propagated"

    Start-Sleep -s 30
}

Select-AzureRmSubscription -SubscriptionId $subscriptionId

Write-Host "Updating the certificate on HDInsight cluster."

Invoke-AzureRmResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId.ToString(); Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force

```

既存の証明書を割り当てるには、証明書を作成して、.pfx ファイルとパスワードを用意します。 その証明書を、AppId ready を使用して、クラスターを作成するのに使用されたサービス プリンシパルに関連付けます。

パラメーターを実際の値に置き換えた後、PowerShell コマンドを実行します。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]