---
title: Azure Service Fabric クラスターに安全に接続する
description: Service Fabric クラスターへのクライアント アクセスを認証する方法、およびクライアントとクラスター間の通信をセキュリティで保護する方法について説明します。
ms.topic: conceptual
ms.date: 01/29/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 3a669f7400eb2daf2d71f6caed41560020699c58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791123"
---
# <a name="connect-to-a-secure-cluster"></a>セキュリティ保護されたクラスターに接続する

クライアントが Service Fabric クラスター ノードに接続する際には、証明書セキュリティまたは Azure Active Directory (AAD) を使用してクライアント認証が行われ、セキュリティ保護された通信が確立します。 この認証により、許可されたユーザーのみが、クラスターやデプロイ済みアプリケーションにアクセスし、管理タスクを実行できるようになります。  証明書または AAD セキュリティは、クラスターの作成時に、クラスターであらかじめ有効にしておく必要があります。  クラスターのセキュリティ シナリオの詳細については、 [クラスター セキュリティ](service-fabric-cluster-security.md)に関する記事を参照してください。 証明書によるセキュリティで保護されたクラスターに接続している場合は、クラスターに接続するコンピューターで[クライアント証明書を設定](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert)します。 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Azure Service Fabric CLI (sfctl) を使用してセキュリティで保護されたクラスターに接続する

Service Fabric CLI (sfctl) を使用してセキュリティで保護されたクラスターに接続する方法はいくつかあります。 認証にクライアント証明書を使用した場合、証明書の詳細は、クラスター ノードにデプロイされた証明書と一致する必要があります。 証明書に証明機関 (CA) がある場合、信頼された CA も指定する必要があります。

`sfctl cluster select` コマンドを使用してクラスターに接続できます。

証明書とキーのペアとして、または単一の PFX ファイルとして、2 つの異なる方法でクライアント証明書を指定できます。 パスワードで保護された PEM ファイルの場合、自動的にパスワードの入力が求められます。 クライアント証明書を PFX ファイルとして入手した場合は、最初に次のコマンドを使用して PFX ファイルを PEM ファイルに変換します。 

```shell
openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
```

.pfx ファイルがパスワードで保護されていない場合は、最後のパラメーターとして -passin pass: を使用します。

クライアント証明書を pem ファイルとして指定するには、`--pem` 引数でファイル パスを指定します。 次に例を示します。

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

パスワードで保護された pem ファイルにより、コマンドを実行する前にパスワードの入力が求められます。

証明書を指定するために、キー ペアは `--cert` および `--key` 引数を使用して、各ファイルにファイル パスを指定します。

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

テスト クラスターまたは開発クラスターのセキュリティ保護に使用される証明書では、証明書の検証が失敗することがあります。 証明書の検証をバイパスするには、`--no-verify` オプションを指定します。 次に例を示します。

> [!WARNING]
> 運用環境の Service Fabric クラスターに接続するときに `no-verify` オプションを使用しないでください。

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

さらに、信頼された CA 証明書のディレクトリ、または個々の証明書へのパスを指定することもできます。 これらのパスを指定するには、`--ca` 引数を使用します。 次に例を示します。

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

接続後、[他の sfctl コマンドを実行](service-fabric-cli.md)してクラスターの対話操作を実行することができます。

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>PowerShell を使用してクラスターに接続する
PowerShell を使ってクラスターでの操作を実行する前に、まず、クラスターへの接続を確立します。 このクラスター接続は、特定の PowerShell セッションにおけるすべての後続コマンドに使用されます。

### <a name="connect-to-an-unsecure-cluster"></a>セキュリティで保護されていないクラスターに接続する

セキュリティ保護されていないクラスターに接続するには、クラスター エンドポイントのアドレスを **Connect-ServiceFabricCluster** コマンドに提供します。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>セキュリティで保護されたクラスターに Azure Active Directory を使用して接続する

Azure Active Directory を使用してクラスター管理者のアクセスを承認する、セキュリティで保護されたクラスターに接続するには、クラスター証明書の拇印を指定して *AzureActiveDirectory* フラグを使用します。  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>セキュリティで保護されたクラスターにクライアント証明書を使用して接続する
次の PowerShell コマンドを実行して、クライアント証明書を使用して管理者のアクセスを承認する、セキュリティで保護されたクラスターに接続します。 

#### <a name="connect-using-certificate-common-name"></a>証明書共通名を使用して接続する
クラスター証明書の共通名と、クラスター管理のアクセス許可が付与されているクライアント証明書の共通名を指定します。 証明書の詳細は、クラスター ノード上の証明書と一致する必要があります。

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
*ServerCommonName* は、クライアント ノードにインストールされているサーバー証明書の共通名です。 *FindValue* は、管理用クライアント証明書の共通名です。 以下に示したのは、パラメーターを指定した状態のコマンドの例です。
```powershell
$ClusterName= "sf-commonnametest-scus.southcentralus.cloudapp.azure.com:19000"
$certCN = "sfrpe2eetest.southcentralus.cloudapp.azure.com"

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName $certCN  `
    -FindType FindBySubjectName `
    -FindValue $certCN `
    -StoreLocation CurrentUser `
    -StoreName My 
```

#### <a name="connect-using-certificate-thumbprint"></a>証明書の拇印を使用して接続する
クラスター証明書の拇印、およびクラスター管理のアクセス許可が付与されているクライアント証明書の拇印を指定します。 証明書の詳細は、クラスター ノード上の証明書と一致する必要があります。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `  
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `  
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* は、クライアント ノードにインストールされているサーバー証明書の拇印です。 *FindValue* は、管理用クライアント証明書の拇印です。  以下に示したのは、パラメーターを指定した状態のコマンドの例です。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `  
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `  
          -StoreLocation CurrentUser -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>セキュリティで保護されたクラスターに Windows Active Directory を使用して接続する
スタンドアロン クラスターが AD のセキュリティを使用してデプロイされている場合は、"WindowsCredential" スイッチを追加してクラスターに接続します。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>FabricClient API を使用してクラスターに接続する
Service Fabric SDK によって、クラスター管理用に [FabricClient](/dotnet/api/system.fabric.fabricclient) クラスが提供されます。 FabricClient API を使用するには、Microsoft.ServiceFabric NuGet パッケージを取得します。

### <a name="connect-to-an-unsecure-cluster"></a>セキュリティで保護されていないクラスターに接続する

リモートのセキュリティ保護されていないクラスターに接続するには、FabricClient インスタンスを作成し、クラスター アドレスを指定します。

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

リライアブル サービス内など、クラスター内から実行しているコードでは、クラスター アドレスを指定 *せずに* FabricClient を作成します。 FabricClient は、追加のネットワーク ホップなしで、コードを現在実行中のノードのローカル管理ゲートウェイに接続します。

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>セキュリティで保護されたクラスターにクライアント証明書を使用して接続する

クラスター内のノードには、[FabricClient](/dotnet/api/system.fabric.fabricclient) で設定された [RemoteCommonNames プロパティ](/dotnet/api/system.fabric.x509credentials)に共通名または SAN の DNS 名が表示される、有効な証明書が必要です。 このプロセスに従うことで、クライアントとクラスター ノードの間の相互認証が可能になります。

```csharp
using System.Fabric;
using System.Security.Cryptography.X509Certificates;

string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
var fc = new FabricClient(xc, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "My";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = clientCertThumb;
    xc.RemoteCommonNames.Add(name);
    xc.RemoteCertThumbprints.Add(serverCertThumb);
    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>セキュリティで保護されたクラスターに Azure Active Directory を使用して対話形式で接続する

次の例では、クライアント ID に Azure Active Directory、サーバー ID にサーバー証明書を使用しています。

クラスターに接続すると、対話形式でのサインイン用のダイアログ ウィンドウが自動でポップアップ表示されます。

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}
```

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>セキュリティで保護されたクラスターに Azure Active Directory を使用して非対話形式で接続する

次の例では、Microsoft.IdentityModel.Clients.ActiveDirectory, Version: 2.19.208020213 を使用しています。

AAD トークンの取得の詳細については、[Microsoft.IdentityModel.Clients.ActiveDirectory](/dotnet/api/microsoft.identitymodel.clients.activedirectory) に関するページを参照してください。

```csharp
string tenantId = "C15CFCEA-02C1-40DC-8466-FBD0EE0B05D2";
string clientApplicationId = "118473C2-7619-46E3-A8E4-6DA8D5F56E12";
string webApplicationId = "53E6948C-0897-4DA6-B26A-EE2A38A690B4";

string token = GetAccessToken(
    tenantId,
    webApplicationId,
    clientApplicationId,
    "urn:ietf:wg:oauth:2.0:oob");

string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);
claimsCredentials.LocalClaims = token;

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(
    string tenantId,
    string resource,
    string clientId,
    string redirectUri)
{
    string authorityFormat = @"https://login.microsoftonline.com/{0}";
    string authority = string.Format(CultureInfo.InvariantCulture, authorityFormat, tenantId);
    var authContext = new AuthenticationContext(authority);

    var authResult = authContext.AcquireToken(
        resource,
        clientId,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>セキュリティ保護されたクラスターにメタデータの事前知識なしで Azure Active Directory を使用して接続する

次の例では非対話形式のトークン取得を利用していますが、カスタムの対話形式のトークン取得操作を構築するのと同じ方法を用いることもできます。 トークンの取得に必要な Azure Active Directory メタデータは、クラスターの構成から読み取ります。

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

fc.ClaimsRetrieval += (o, e) =>
{
    return GetAccessToken(e.AzureActiveDirectoryMetadata);
};

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(AzureActiveDirectoryMetadata aad)
{
    var authContext = new AuthenticationContext(aad.Authority);

    var authResult = authContext.AcquireToken(
        aad.ClusterApplication,
        aad.ClientApplication,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

<a id="connectsecureclustersfx"></a>

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Service Fabric Explorer を使用してセキュリティ保護されたクラスターに接続する
特定のクラスターの [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) にアクセスするには、ブラウザーで次の場所にアクセスします。

`http://<your-cluster-endpoint>:19080/Explorer`

完全な URL は Azure Portal の [Cluster Essentials] ウィンドウにもあります。

ブラウザーを使用して Windows または OS X のセキュリティで保護されたクラスターに接続する場合、クライアント証明書をインポートすると、ブラウザーによって、クラスターへの接続に使用する証明書を要求されます。  Linux マシンでは、高度なブラウザーの設定 (ブラウザーによってしくみが異なる) を使用して証明書をインポートし、ディスク上の証明書の場所をポイントする必要があります。 詳細については、[クライアント証明書の設定](#connectsecureclustersetupclientcert)に関するページを参照してください。

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>セキュリティで保護されたクラスターに Azure Active Directory を使用して接続する

AAD でセキュリティ保護されているクラスターに接続するには、ブラウザーを次の場所にポイントします。

`https://<your-cluster-endpoint>:19080/Explorer`

自動的に、AAD へのサインインを求められます。

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>セキュリティで保護されたクラスターにクライアント証明書を使用して接続する

証明書でセキュリティ保護されているクラスターに接続するには、ブラウザーで次の場所にアクセスします。

`https://<your-cluster-endpoint>:19080/Explorer`

自動的に、クライアント証明書を選択するように求められます。

<a id="connectsecureclustersetupclientcert"></a>

## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>リモート コンピューター上でクライアント証明書を設定する

クラスターをセキュリティで保護するには、少なくとも 2 つの証明書 (クラスターおよびサーバー用の証明書と、クライアント アクセス用の証明書) を使用する必要があります。  追加のセカンダリ証明書とクライアント アクセス用証明書も使用することをお勧めします。  証明書セキュリティを使用して、クライアントとクラスター ノード間の通信をセキュリティで保護するには、クライアント証明書を取得してインストールします。 この証明書は、現在のユーザーまたはローカル コンピューターの個人用 (マイ) ストアにインストールすることができます。  クライアントがクラスターを認証できるように、サーバー証明書の拇印も必要になります。

* Windows の場合:PFX ファイルをダブルクリックし、プロンプトに従って個人用ストア `Certificates - Current User\Personal\Certificates` に証明書をインストールします。 または、PowerShell コマンドを使用できます。

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

    これが自己署名証明書である場合は、セキュリティで保護されたクラスターへの接続にこの証明書を使用する前に、コンピューターの "信頼されたユーザー" ストアにインポートする必要があります。

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
    -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
    -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

* Mac の場合:PFX ファイルをダブルクリックし、プロンプトに従ってキーチェーンに証明書をインストールします。

## <a name="next-steps"></a>次のステップ

* [Service Fabric クラスターのアップグレード プロセスと機能](service-fabric-cluster-upgrade.md)
* [Visual Studio での Service Fabric アプリケーションの管理](service-fabric-manage-application-in-visual-studio.md)
* [Service Fabric の正常性モデルの概要](service-fabric-health-introduction.md)
* [アプリケーションのセキュリティと RunAs](service-fabric-application-runas-security.md)
* [Service Fabric CLI の概要](service-fabric-cli.md)
