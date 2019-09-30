---
title: Azure Cosmos DB を使用した Azure Active Directory 証明書ベースの認証
description: 証明書ベースの認証で Azure Cosmos DB のキーにアクセスするように Azure AD ID を構成する方法について説明します。
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: 594367db838266a6d11f4fbf3b763b024f8e14d4
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70882853"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Azure AD ID で Azure Cosmos DB アカウントのキーにアクセスするための証明書ベースの認証

証明書ベースの認証では、Azure Active Directory (Azure AD) をクライアント証明書と共に使用してクライアント アプリケーションを認証できます。 ID が必要になるコンピューター (オンプレミスのコンピューターや Azure の仮想マシンなど) では証明書ベースの認証を実行できます。 それにより、アプリケーションは、キーを直接アプリケーション内に保持しなくても Azure Cosmos DB キーを読み取ることができます。 この記事では、サンプルの Azure AD アプリケーションを作成し、そこに証明書ベースの認証を構成し、新しいアプリケーション ID を使用して Azure にサインインした後、そのアプリケーションで Azure Cosmos アカウントのキーを取得する方法について説明します。 この記事では、Azure PowerShell を使用して ID を設定した後、Azure Cosmos アカウントのキーを認証してそれにアクセスする C# サンプル アプリを提供します。  

## <a name="prerequisites"></a>前提条件

* [最新バージョン](/powershell/azure/install-az-ps)の Azure PowerShell をインストールします。

* [Azure サブスクリプション](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)をお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。

## <a name="register-an-app-in-azure-ad"></a>Azure AD にアプリを登録する

この手順では、Azure AD アカウントでサンプル Web アプリケーションを登録します。 このアプリケーションは、後で Azure Cosmos DB アカウントのキーを読み取るために使用されます。 次の手順を使用してアプリケーションを登録します。 

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. **[Azure Active Directory]** ウィンドウを開き、[アプリの登録] ウィンドウに移動して **[新規登録]** を選択します。 

   ![Active Directory での新しいアプリケーションの登録](./media/certificate-based-authentication/new-app-registration.png)

1. **[アプリケーションを登録する]** フォームに次の詳細を入力します。  

   * **[名前]** – アプリケーションの名前を指定します。"sampleApp" などの任意の名前を指定できます。
   * **[サポートされているアカウントの種類]** – **[Accounts in this organizational directory only (Default Directory)]\(この組織のディレクトリ内のアカウントのみ (既定のディレクトリ))** を選択して、現在のディレクトリ内のリソースがこのアプリケーションにアクセスできるようにします。 
   * **[リダイレクト URL]** – アプリケーションの種類として **[Web]** を選択し、そのアプリケーションがホストされている URL を指定します。任意の URL を指定できます。 この例では、`https://sampleApp.com` などのテスト URL を指定できます。アプリが存在しなくてもかまいません。

   ![サンプル Web アプリケーションの登録](./media/certificate-based-authentication/register-sample-web-app.png)

1. フォームに入力したら **[登録]** を選択します。

1. アプリが登録されたら、 **[Application(client) ID] (アプリケーション (クライアント) ID)** と **[オブジェクト ID]** を書き留めておきます。これらの詳細は、次の手順で使用します。 

   ![アプリケーションおよびオブジェクト ID を取得する](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>AzureAD モジュールをインストールする

この手順では、Azure AD PowerShell モジュールをインストールします。 このモジュールは、前の手順で登録したアプリケーションの ID を取得し、そのアプリケーションに自己署名証明書を関連付けるために必要です。 

1. 管理者権限を使用して Windows PowerShell ISE を開きます。 まだ行っていない場合は、AZ PowerShell モジュールをインストールし、サブスクリプションに接続します。 複数のサブスクリプションを持っている場合は、次のコマンドに示すように、現在のサブスクリプションのコンテキストを設定できます。

   ```powershell

   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. [AzureAD](/powershell/module/azuread/?view=azureadps-2.0) モジュールをインストールしてインポートします。

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Azure AD にサインインする

アプリケーションを登録した Azure AD にサインインします。 Connect-AzureAD コマンドを使用してアカウントにサインインし、ポップアップ ウィンドウで Azure アカウントの資格情報を入力します。 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>自己署名証明書の作成

Windows PowerShell ISE の別のインスタンスを開き、次のコマンドを実行して自己署名証明書を作成し、その証明書に関連付けられたキーを読み取ります。

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>証明書ベースの資格情報を作成する 

次に、次のコマンドを実行してアプリケーションのオブジェクト ID を取得し、証明書ベースの資格情報を作成します。 この例では、1 年後に期限が切れるように証明書を設定します。これは、必要な任意の終了日に設定できます。

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

上のコマンドでは、次のスクリーンショットのような出力が表示されます。

![証明書ベースの資格情報の作成での出力](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>新しい ID を使用するように Azure Cosmos アカウントを構成する

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. Azure Cosmos アカウントに移動し、 **[アクセス制御 (IAM)]** ブレードを開きます。

1. **[追加]** および **[ロールの割り当ての追加]** を選択します。 次のスクリーンショットに示すように、 **[共同作成者]** ロールを使用して、前の手順で作成した sampleApp を追加します。

   ![新しい ID を使用するように Azure Cosmos アカウントを構成する](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. フォームに入力したら **[保存]** を選択します。


## <a name="access-the-keys-from-powershell"></a>PowerShell からキーにアクセスする

この手順では、作成したアプリケーションと証明書を使用して Azure にサインインし、Azure Cosmos アカウントのキーにアクセスします。 

1. 最初に、アカウントにサインインするために使用した Azure アカウントの資格情報をクリアします。 資格情報は、次のコマンドを使用してクリアできます。

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. 次に、アプリケーションの資格情報を使用して Azure portal にサインインできること、および Azure Cosmos DB キーにアクセスできることを検証します。

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Invoke-AzResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <Resource_Group_Name_of_your_Azure_Cosmos_account> -ResourceName <Your_Azure_Cosmos_Account_Name> 
   ```

前のコマンドでは、Azure Cosmos アカウントのプライマリおよびセカンダリ マスター キーが表示されます。 キー取得要求が成功し、そのイベントが "sampleApp" アプリケーションによって開始されたことを検証するには、Azure Cosmos アカウントのアクティビティ ログを表示できます。 
 
![Azure AD でのキー取得の呼び出しを検証する](./media/certificate-based-authentication/activity-log-validate-results.png)


## <a name="access-the-keys-from-a-c-application"></a>C# アプリケーションからキーにアクセスする 

このシナリオはまた、C# アプリケーションからキーにアクセスして検証することもできます。 次の C# コンソール アプリケーションは、Active Directory に登録されたアプリを使用して Azure Cosmos DB キーにアクセスできます。 このコードを実行する前に、tenantId、clientID、certName、リソース グループ名、サブスクリプション ID、Azure Cosmos アカウント名の詳細を必ず更新してください。 

```csharp
using System;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Linq;
using System.Net.Http;
using System.Security.Cryptography.X509Certificates;
using System.Threading;
using System.Threading.Tasks;
 
namespace TodoListDaemonWithCert
{
    class Program
    {
        private static string aadInstance = "https://login.windows.net/";
        private static string tenantId = "<Your_Tenant_ID>";
        private static string clientId = "<Your_Client_ID>";
        private static string certName = "<Your_Certificate_Name>";
 
        private static int errorCode = 0;
        static int Main(string[] args)
        {
            MainAync().Wait();
            Console.ReadKey();
 
            return 0;
        } 
 
        static async Task MainAync()
        {
            string authContextURL = aadInstance + tenantId;
            AuthenticationContext authContext = new AuthenticationContext(authContextURL);
            X509Certificate2 cert = ReadCertificateFromStore(certName);
 
            ClientAssertionCertificate credential = new ClientAssertionCertificate(clientId, cert);
            AuthenticationResult result = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);
            if (result == null)
            {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }
 
            string token = result.AccessToken;
            string subscriptionId = "<Your_Subscription_ID>";
            string rgName = "<ResourceGroup_of_your_Cosmos_account>";
            string accountName = "<Your_Cosmos_account_name>";
            string cosmosDBRestCall = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2015-04-08";
 
            Uri restCall = new Uri(cosmosDBRestCall);
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Remove("Authorization");
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + token);
            HttpResponseMessage response = await httpClient.PostAsync(restCall, null);
 
            Console.WriteLine("Got result {0} and keys {1}", response.StatusCode.ToString(), response.Content.ReadAsStringAsync().Result);
        }
 
        /// <summary>
        /// Reads the certificate
        /// </summary>
        private static X509Certificate2 ReadCertificateFromStore(string certName)
        {
            X509Certificate2 cert = null;
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection certCollection = store.Certificates;
 
            // Find unexpired certificates.
            X509Certificate2Collection currentCerts = certCollection.Find(X509FindType.FindByTimeValid, DateTime.Now, false);
 
            // From the collection of unexpired certificates, find the ones with the correct name.
            X509Certificate2Collection signingCert = currentCerts.Find(X509FindType.FindBySubjectName, certName, false);
 
            // Return the first certificate in the collection, has the right name and is current.
            cert = signingCert.OfType<X509Certificate2>().OrderByDescending(c => c.NotBefore).FirstOrDefault();
            store.Close();
            return cert;
        }
    }
}
```

次のスクリーンショットに示すように、このスクリプトではプライマリおよびセカンダリ マスター キーが出力されます。

![csharp アプリケーションの出力](./media/certificate-based-authentication/csharp-application-output.png)

前のセクションと同様に、キー取得要求イベントが "sampleApp" アプリケーションによって開始されたことを検証するには、Azure Cosmos アカウントのアクティビティ ログを表示できます。 


## <a name="next-steps"></a>次の手順

* [Azure Key Vault を使用して Azure Cosmos キーをセキュリティで保護する](access-secrets-from-keyvault.md)

* [Azure Cosmos DB のセキュリティ コントロール](cosmos-db-security-controls.md)
