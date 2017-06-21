---
title: "Web アプリケーションからの Azure Key Vault の使用 | Microsoft Docs"
description: "このチュートリアルを使用すると、Web アプリケーションから Azure Key Vault を使用する方法について学習できます。"
services: key-vault
documentationcenter: 
author: adhurwit
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: adhurwit
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 2a7f2cb27cb4ed2d23fee09d53f85283a8592b3a
ms.lasthandoff: 03/09/2017


---
# <a name="use-azure-key-vault-from-a-web-application"></a>Web アプリケーションからの Azure Key Vault の使用
## <a name="introduction"></a>はじめに
このチュートリアルを使用すると、Azure の Web アプリケーションから Azure Key Vault を使用する方法について学習できます。 ここでは、Web アプリケーションで使用できるように Azure Key Vault からシークレットにアクセスするプロセスについて説明します。

**推定所要時間:** 15 分

Azure Key Vault の概要については、「 [Azure Key Vault とは](key-vault-whatis.md)

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下が必要です。

* Azure Key Vault のシークレットへの URI
* Key Vault にアクセスできる Azure Active Directory に登録された Web アプリケーションのクライアント ID とクライアント シークレット
* Web アプリケーション。 Web アプリとして Azure にデプロイされた ASP.NET MVC アプリケーションの手順について説明します。

> [!NOTE]
> このチュートリアルでは、 [Azure Key Vault の概要](key-vault-get-started.md)に関するページに記載されている手順を完了している必要があります。これにより、シークレットへの URI と、Web アプリケーションのクライアント ID およびクライアント シークレットを入手できます。
> 
> 

Key Vault にアクセスする Web アプリケーションは、Azure Active Directory に登録されていて、Key Vault へのアクセス権が付与されているアプリケーションです。 そうでない場合は、概要のチュートリアルにあるアプリケーションの登録に関するトピックに戻り、記載されている手順を繰り返します。

このチュートリアルは、Azure 上での Web アプリケーション作成の基本を理解している Web 開発者向けに設計されています。 Azure Web Apps の詳細については、 [Web Apps の概要](../app-service-web/app-service-web-overview.md)に関するページを参照してください。

## <a id="packages"></a>NuGet パッケージの追加
Web アプリケーションでインストールしておく必要のあるパッケージは 2 つあります。

* Active Directory 認証ライブラリ: Azure Active Directory と対話してユーザー ID を管理するためのメソッドが含まれています。
* Azure Key Vault ライブラリ: Azure Key Vault と対話するためのメソッドが含まれています。

どちらのパッケージも、パッケージ マネージャー コンソールで Install-Package コマンドを使用してインストールできます。

    // this is currently the latest stable version of ADAL
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

    Install-Package Microsoft.Azure.KeyVault


## <a id="webconfig"></a>web.config の変更
次のように、web.config ファイルに追加する必要のある 3 つのアプリケーション設定があります。

    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />


今後 Azure の Web アプリとしてアプリケーションをホストしない場合は、web.config に実際のクライアント ID、クライアント シークレット、およびシークレットの URI の値を追加する必要があります。 追加しない場合は、これらのダミーの値をそのままにしてください。セキュリティ レベルを上げるために、Azure ポータルで実際の値を追加する予定です。

## <a id="gettoken"></a>アクセス トークンを取得するメソッドの追加
Key Vault API を使用するには、アクセス トークンが必要です。 Key Vault クライアントによって Key Vault API の呼び出しが処理されますが、アクセス トークンを取得する関数を指定する必要があります。  

Azure Active Directory からアクセス トークンを取得するコードを次に示します。 このコードはアプリケーション内の任意の場所に配置できます。 Utils クラスまたは EncryptionHelper クラスを追加します。  

    //add these using statements
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System.Threading.Tasks;
    using System.Web.Configuration;

    //this is an optional property to hold the secret after it is retrieved
    public static string EncryptSecret { get; set; }

    //the method that will be provided to the KeyVaultClient
    public static async Task<string> GetToken(string authority, string resource, string scope)
    {
        var authContext = new AuthenticationContext(authority);
        ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                    WebConfigurationManager.AppSettings["ClientSecret"]);
        AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

        if (result == null)
            throw new InvalidOperationException("Failed to obtain the JWT token");

        return result.AccessToken;
    }

> [!NOTE]
> クライアント ID とクライアント シークレットを使用するのが、Azure AD アプリケーションを認証する最も簡単な方法です。 また、Web アプリケーションでこれらを使用すると、義務を分離して、キーの管理をさらに制御できます。 ただし、これは構成の設定にクライアント シークレットを配置することに依存しています。この配置は、構成の設定に保護するシークレットを配置するのと同じくらい危険な可能性があります。 クライアント ID とクライアント シークレットではなく、クライアント ID と証明書を使用して Azure AD のアプリケーションを認証する方法の詳細については、以下を参照してください。
> 
> 

## <a id="appstart"></a>アプリケーション起動時のシークレットの取得
ここで、Key Vault API を呼び出してシークレットを取得するコードが必要になります。 次のコードは、それが必要になる前に呼び出されれば、どこに配置してもかまいません。 ここでは、このコードを Global.asax の Application Start イベントに配置しました。これにより、コードは起動時に 1 回実行され、アプリケーションでシークレットを使用できるようになります。

    //add these using statements
    using Microsoft.Azure.KeyVault;
    using System.Web.Configuration;

    // I put my GetToken method in a Utils class. Change for wherever you placed your method.
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

    var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

    //I put a variable in a Utils class to hold the secret for general  application use.
    Utils.EncryptSecret = sec.Value;



## <a id="portalsettings"></a>Azure ポータルのアプリ設定の追加 (省略可能)
Azure の Web アプリがある場合は、Azure ポータルでアプリ設定の実際の値を追加できます。 これにより、実際の値は web.config ファイルに存在しなくなりますが、個別のアクセス制御機能があるポータルによって保護されます。 これらの値は、web.config で入力した値の代わりに使用されます。 名前が同じであるかどうかを確認してください。

![Azure ポータルに表示されるアプリケーションの設定][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>クライアント シークレットではなく、証明書を使用して認証する
Azure AD アプリケーションを認証する別の方法は、クライアント ID とクライアント シークレットではなく、クライアント ID と証明書を使用する方法です。 Azure の Web アプリで証明書を使用する手順を次に示します。

1. 証明書を取得または作成する
2. 証明書を Azure AD アプリケーションに関連付ける
3. 証明書を使用する Web アプリにコードを追加する
4. 証明書を Web アプリに追加する

**証明書を取得または作成する** ここでは、テスト証明書を作成します。 開発者コマンド プロンプトで証明書を作成する場合に使用できる、いくつかのコマンドを次に示します。 証明書ファイルの作成先となるディレクトリを変更します。  また、証明書の開始日と終了日は、現在の日付 + 1 年とします。

    makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 03/07/2017 -e 03/07/2018 -r
    pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123

終了日と .pfx のパスワードをメモしておいてください (この例では、2016 年 7 月 31 日と test123)。 この情報は以下で必要になります。

テスト証明書の作成の詳細については、 [独自のテスト証明書を作成する方法](https://msdn.microsoft.com/library/ff699202.aspx)

**証明書を Azure AD アプリケーションに関連付ける** 証明書を作成したので、それを Azure AD アプリケーションに関連付ける必要があります。 現在、Azure Portal ではこのワークフローはサポートしていません。このワークフローは PowerShell を使用して完了できます。 次のコマンドを実行して、証明書を Azure AD アプリケーションに関連付けます。

    $x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $x509.Import("C:\data\KVWebApp.cer")
    $credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())

    # If you used different dates for makecert then adjust these values
    $now = [System.DateTime]::Now
    $yearfromnow = $now.AddYears(1)

    $adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -CertValue $credValue -StartDate $now -EndDate $yearfromnow

    $sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName $sp.ServicePrincipalName -PermissionsToSecrets all -ResourceGroupName 'contosorg'

    # get the thumbprint to use in your app settings
    $x509.Thumbprint

これらのコマンドを実行すると、Azure AD でアプリケーションを確認できます。 検索するときは、[検索] ダイアログで [自分の会社が使用するアプリケーション] ではなく [自分の会社が所有するアプリケーション] を必ず選択してください。

Azure AD アプリケーションのオブジェクトと ServicePrincipal オブジェクトの詳細については、「 [アプリケーションおよびサービス プリンシパル オブジェクト](../active-directory/active-directory-application-objects.md)

**証明書を使用する Web アプリにコードを追加する** これで、証明書にアクセスするコードを Web アプリに追加して認証に使用できます。

まず、証明書にアクセスするコードがあります。

    public static class CertificateHelper
    {
        public static X509Certificate2 FindCertificateByThumbprint(string findValue)
        {
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            try
            {
                store.Open(OpenFlags.ReadOnly);
                X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                    findValue, false); // Don't validate certs, since the test root isn't installed.
                if (col == null || col.Count == 0)
                    return null;
                return col[0];
            }
            finally
            {
                store.Close();
            }
        }
    }


StoreLocation は LocalMachine ではなく CurrentUser であることに注意してください。 また、ここではテスト証明書を使用しているため、Find メソッドに対して ' false' を指定しています。

次に、CertificateHelper を使用し、認証に必要な ClientAssertionCertificate を作成するコードがあります。

    public static ClientAssertionCertificate AssertionCert { get; set; }

    public static void GetCert()
    {
        var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
        AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
    }


アクセス トークンを取得する新しいコードを次に示します。 これにより、上記の GetToken メソッドが置き換えられます。 便宜上、別の名前を指定しています。

    public static async Task<string> GetAccessToken(string authority, string resource, string scope)
    {
        var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
        var result = await context.AcquireTokenAsync(resource, AssertionCert);
        return result.AccessToken;
    }

使いやすくするために、このコードをすべて Web アプリ プロジェクトの Utils クラスに配置してあります。

最後のコード変更は、Application_Start メソッドで行います。 最初に、GetCert() メソッドを呼び出して ClientAssertionCertificate を読み込む必要があります。 次に、新しい KeyVaultClient を作成するときに指定するコールバック メソッドを変更します。 これにより、上記のコードが置き換わることに注意してください。

    Utils.GetCert();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));


**Azure ポータルで証明書を Web アプリに追加する** 証明書を Web アプリに追加する手順は、簡単な 2 段階のプロセスです。 まず Azure ポータルに移動し、Web アプリに移動します。 Web アプリの [設定] ブレードで、[カスタム ドメインおよび SSL] のエントリをクリックします。 開いたブレードで、先ほど作成した証明書 KVWebApp.pfx をアップロードし、pfx のパスワードを覚えているかどうかを確認できます。

![Azure ポータルでの Web アプリへの証明書の追加][2]

最後に、名前が WEBSITE\_LOAD\_CERTIFICATES で値が * の Web アプリに、アプリケーション設定を追加する必要があります。 これにより、すべての証明書が読み込まれます。 アップロードした証明書のみを読み込む場合は、そのサムプリントのコンマ区切りリストを入力できます。

Web アプリに証明書を追加する方法の詳細については、 [Azure Websites アプリケーションでの証明書の使用](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)

**証明書をシークレットとして Key Vault に追加する** 証明書を Web Apps サービスに直接アップロードするのではなく、Key Vault にシークレットとして保存しておき、そこからデプロイすることができます。 これは 2 段階のプロセスとなっており、「 [Deploying Azure Web App Certificate through Key Vault (Azure Web アプリの証明書を Key Vault 経由でデプロイする)](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)

## <a id="next"></a>次のステップ
プログラミング リファレンスについては、 [Azure Key Vault C# クライアント API リファレンス](https://msdn.microsoft.com/library/azure/dn903628.aspx)に関するページを参照してください。

<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png

