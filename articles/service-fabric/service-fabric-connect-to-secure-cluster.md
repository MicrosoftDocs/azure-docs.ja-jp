---
title: "Azure Service Fabric クラスターへの安全な接続 | Microsoft Docs"
description: "Service Fabric クラスターへのクライアント アクセスを認証する方法、およびクライアントとクラスター間の通信をセキュリティで保護する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 759a539e-e5e6-4055-bff5-d38804656e10
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 52f9a3146852ef83c31bd93e1c538e12f0d953eb
ms.openlocfilehash: e44ecf5860becffb39d199e36d36d96f50bf7cf3
ms.lasthandoff: 02/16/2017


---
# <a name="connect-to-a-secure-cluster"></a>セキュリティ保護されたクラスターに接続する
クライアントが Service Fabric クラスター ノードに接続する際には、証明書セキュリティまたは Azure Active Directory (AAD) を使用してクライアント認証が行われ、セキュリティ保護された通信が確立します。 この認証により、許可されたユーザーのみが、クラスターやデプロイ済みアプリケーションにアクセスし、管理タスクを実行できるようになります。  証明書または AAD セキュリティは、クラスターの作成時に、クラスターであらかじめ有効にしておく必要があります。  クラスターのセキュリティ シナリオの詳細については、 [クラスター セキュリティ](service-fabric-cluster-security.md)に関する記事を参照してください。 証明書によるセキュリティで保護されたクラスターに接続している場合は、クラスターに接続するコンピューターで[クライアント証明書を設定](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert)します。 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-cli"></a>セキュリティで保護されたクラスターに Azure CLI を使用して接続する
セキュリティで保護されたクラスターに接続する方法を以下の Azure CLI コマンドで説明します。 

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>セキュリティで保護されたクラスターにクライアント証明書を使用して接続する
証明書の詳細は、クラスター ノード上の証明書と一致する必要があります。 

ご使用の証明書に証明機関 (CA) が含まれている場合は、次の例のようにパラメーター `--ca-cert-path` を追加する必要があります。 

```
 azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
複数の CA がある場合は、コンマで区切ります。 

証明書の共通名が接続エンドポイントと一致しない場合は、 `--strict-ssl-false` パラメーターを指定して検証を省略することができます。 

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 --strict-ssl-false 
```

CA 検証を省略したい場合、次のコマンドのように ``--reject-unauthorized-false`` パラメーターを追加します。

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized-false 
```

自己署名証明書で保護されたクラスターに接続する場合は、次のコマンドを実行して、CA 検証と共通名検証の両方を無効にしてください。

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl-false --reject-unauthorized-false
```

接続後、[他の CLI コマンドを実行](service-fabric-azure-cli.md)してクラスターの対話操作を実行することができます。 

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
次の PowerShell コマンドを実行して、クライアント証明書を使用して管理者のアクセスを承認する、セキュリティで保護されたクラスターに接続します。 クラスター証明書の拇印、およびクラスター管理のアクセス許可が付与されているクライアント証明書の拇印を指定します。 証明書の詳細は、クラスター ノード上の証明書と一致する必要があります。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* は、クライアント ノードにインストールされているサーバー証明書の拇印です。 *FindValue* は、管理用クライアント証明書の拇印です。
以下に示したのは、パラメーターを指定した状態のコマンドの例です。 

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
Service Fabric SDK によって、クラスター管理用に [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) クラスが提供されます。 FabricClient API を使用するには、Microsoft.ServiceFabric NuGet パッケージを取得します。

### <a name="connect-to-an-unsecure-cluster"></a>セキュリティで保護されていないクラスターに接続する

リモートのセキュリティ保護されていないクラスターに接続するには、FabricClient インスタンスを作成し、クラスター アドレスを指定します。

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

リライアブル サービス内など、クラスター内から実行しているコードでは、クラスター アドレスを指定*せずに* FabricClient を作成します。 FabricClient は、追加のネットワーク ホップなしで、コードを現在実行中のノードのローカル管理ゲートウェイに接続します。

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>セキュリティで保護されたクラスターにクライアント証明書を使用して接続する

クラスター内のノードには、[FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) で設定された [RemoteCommonNames プロパティ](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials#System_Fabric_X509Credentials_RemoteCommonNames)に共通名または SAN の DNS 名が表示される、有効な証明書が必要です。 このプロセスに従うことで、クライアントとクラスター ノードの間の相互認証が可能になります。

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

AAD トークンの取得の詳細については、[Microsoft.IdentityModel.Clients.ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx) に関するページを参照してください。

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

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>セキュリティで保護されたクラスターに Azure Active Directory を使用して接続する

AAD でセキュリティ保護されているクラスターに接続するには、ブラウザーを次の場所にポイントします。

`https://<your-cluster-endpoint>:19080/Explorer`

自動的に、AAD を使用したログインを求められます。

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>セキュリティで保護されたクラスターにクライアント証明書を使用して接続する

証明書でセキュリティ保護されているクラスターに接続するには、ブラウザーで次の場所にアクセスします。

`https://<your-cluster-endpoint>:19080/Explorer`

自動的に、クライアント証明書を選択するように求められます。

<a id="connectsecureclustersetupclientcert"></a>
## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>リモート コンピューター上でクライアント証明書を設定する
クラスターをセキュリティで保護するには、少なくとも&2; つの証明書 (クラスターおよびサーバー用の証明書と、クライアント アクセス用の証明書) を使用する必要があります。  追加のセカンダリ証明書とクライアント アクセス用証明書も使用することをお勧めします。  証明書セキュリティを使用して、クライアントとクラスター ノード間の通信をセキュリティで保護するには、クライアント証明書を取得してインストールします。 この証明書は、現在のユーザーまたはローカル コンピューターの個人用 (マイ) ストアにインストールすることができます。  クライアントがクラスターを認証できるように、サーバー証明書の拇印も必要になります。

次の PowerShell コマンドレットを実行して、クラスターへのアクセスに使用するコンピューターでクライアント証明書を設定します。

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

## <a name="next-steps"></a>次のステップ
* [Service Fabric クラスターのアップグレード プロセスと機能](service-fabric-cluster-upgrade.md)
* [Visual Studio での Service Fabric アプリケーションの管理](service-fabric-manage-application-in-visual-studio.md)。
* [Service Fabric の正常性モデルの概要](service-fabric-health-introduction.md)
* [アプリケーションのセキュリティと RunAs](service-fabric-application-runas-security.md)


