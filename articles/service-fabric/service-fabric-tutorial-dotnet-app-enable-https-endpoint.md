---
title: Kestrel を使用して Azure の Service Fabric アプリに HTTPS エンドポイントを追加する | Microsoft Docs
description: このチュートリアルでは、Kestrel を使用して ASP.NET Core フロントエンド Web サービスに HTTPS エンドポイントを追加し、アプリケーションをクラスターにデプロイする方法を学習します。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 6ce702a9d27523b97a92ac8b4210e8e3151ae518
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598802"
---
# <a name="tutorial-add-an-https-endpoint-to-an-aspnet-core-web-api-front-end-service-using-kestrel"></a>チュートリアル:Kestrel を使用して ASP.NET Core Web API フロントエンド サービスに HTTPS エンドポイントを追加する

このチュートリアルは、シリーズの第 3 部です。  Service Fabric 上で実行されている ASP.NET Core サービスで HTTPS を有効にする方法を学習します。 完了すると、ポート 443 でリッスンする、HTTPS が有効な ASP.NET Core Web フロントエンドを備えた投票アプリケーションを作成できます。 [.NET Service Fabric アプリケーションの構築](service-fabric-tutorial-deploy-app-to-party-cluster.md)に関するページの投票アプリケーションを手動で作成しない場合は、完成したアプリケーションの[ソース コードをダウンロード](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/)できます。

シリーズの第 3 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * サービスに HTTPS エンドポイントを定義する
> * HTTPS を使用するように Kestrel を構成する
> * SSL 証明書をリモート クラスター ノードにインストールする
> * 証明書の秘密キーへのアクセス権を "ネットワーク サービス" に与える
> * Azure Load Balancer のポート 443 を開く
> * アプリケーションをリモート クラスターにデプロイする

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [.NET Service Fabric アプリケーションを構築する](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [アプリケーションをリモート クラスターにデプロイする](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * ASP.NET Core フロントエンド サービスに HTTPS エンドポイントを追加する
> * [Azure Pipelines を使用して CI/CD を構成する](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [アプリケーションの監視と診断を設定する](service-fabric-tutorial-monitoring-aspnet.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションを持っていない場合は[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成する
* [Visual Studio 2019 のバージョン 15.5 以降](https://www.visualstudio.com/)をインストールし、**Azure 開発**ワークロードと **ASP.NET および Web 開発**ワークロードをインストールします。
* [Service Fabric SDK をインストール](service-fabric-get-started.md)します。

## <a name="obtain-a-certificate-or-create-a-self-signed-development-certificate"></a>証明書を取得する、または開発用の自己署名証明書を作成する

運用アプリケーションの場合は、[証明機関 (CA)](https://wikipedia.org/wiki/Certificate_authority) から取得した証明書を使用してください。 開発およびテスト用には、自己署名証明書を作成して使用することができます。 Service Fabric SDK には、自己署名証明書を作成して `Cert:\LocalMachine\My` 証明書ストアにインポートするための *CertSetup.ps1* スクリプトが含まれています。 コマンド プロンプトを管理者として開き、次のコマンドを実行して、サブジェクト名が "CN=mytestcert" の証明書を作成します。

```powershell
PS C:\program files\microsoft sdks\service fabric\clustersetup\secure> .\CertSetup.ps1 -Install -CertSubjectName CN=mytestcert
```

既に証明書 PFX ファイルがある場合は、次のコマンドを実行して証明書を `Cert:\LocalMachine\My` 証明書ストアにインポートします。

```powershell

PS C:\mycertificates> Import-PfxCertificate -FilePath .\mysslcertificate.pfx -CertStoreLocation Cert:\LocalMachine\My -Password (ConvertTo-SecureString "!Passw0rd321" -AsPlainText -Force)


   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

## <a name="define-an-https-endpoint-in-the-service-manifest"></a>サービス マニフェストに HTTPS エンドポイントを定義する

Visual Studio を**管理者**として起動し、投票ソリューションを開きます。 ソリューション エクスプローラーで、*VotingWeb/PackageRoot/ServiceManifest.xml* を開きます。 サービス マニフェストは、サービス エンドポイントを定義します。  **Endpoints** セクションを見つけ、既存の "ServiceEndpoint" エンドポイントを編集します。  名前を "EndpointHttps" に変更し、プロトコルを *https*、種類を *Input*、ポートを *443* に設定します。  変更を保存します。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="configure-kestrel-to-use-https"></a>HTTPS を使用するように Kestrel を構成する

ソリューション エクスプローラーで、*VotingWeb/VotingWeb.cs* ファイルを開きます。  HTTPS を使用し、`Cert:\LocalMachine\My` ストア内の証明書を参照するように、Kestrel を構成します。 次の using ステートメントを追加します。

```csharp
using System.Net;
using Microsoft.Extensions.Configuration;
using System.Security.Cryptography.X509Certificates;
```

新しい *EndpointHttps* エンドポイントを使用し、ポート 443 でリッスンするように、`ServiceInstanceListener` を更新します。 Kestrel サーバーを使用するよう Web ホストを構成する際は、すべてのネットワーク インターフェイスで IPv6 アドレスをリッスンするよう Kestrel を構成する必要があります: `opt.Listen(IPAddress.IPv6Any, port, listenOptions => {...}`。

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

さらに、次のメソッドを追加して、Kestrel がサブジェクトを使用して `Cert:\LocalMachine\My` ストア内の証明書を見つけることができるようにします。  

前の PowerShell コマンドを使用して自己署名証明書を作成した場合は、"&lt;your_CN_value&gt;" を "mytestcert" に置き換えます。または、証明書の CN を使用します。

```csharp
private X509Certificate2 GetCertificateFromStore()
{
    var store = new X509Store(StoreName.My, StoreLocation.LocalMachine);
    try
    {
        store.Open(OpenFlags.ReadOnly);
        var certCollection = store.Certificates;
        var currentCerts = certCollection.Find(X509FindType.FindBySubjectDistinguishedName, "CN=<your_CN_value>", false);
        return currentCerts.Count == 0 ? null : currentCerts[0];
    }
    finally
    {
        store.Close();
    }
}
```

## <a name="give-network-service-access-to-the-certificates-private-key"></a>証明書の秘密キーへのアクセス権を "ネットワーク サービス" に与える

前の手順で、証明書を開発用コンピューターの `Cert:\LocalMachine\My` ストアにインポートしました。  ここで、サービスを実行しているアカウント (既定では "ネットワーク サービス") に証明書の秘密キーへのアクセス権を明示的に与える必要があります。 この手順は (certlm.msc ツールを使用して) 手動で行うことができますが、サービス マニフェストの **SetupEntryPoint** に[スタートアップ スクリプトを構成](service-fabric-run-script-at-service-startup.md)して自動的に PowerShell スクリプトを実行することをお勧めします。

### <a name="configure-the-service-setup-entry-point"></a>サービス セットアップ エントリ ポイントを構成する

ソリューション エクスプローラーで、*VotingWeb/PackageRoot/ServiceManifest.xml* を開きます。  **CodePackage** セクションに、**SetupEntryPoint** ノードと **ExeHost** ノードを順に追加します。  **ExeHost** で、**Program** を "Setup.bat" に設定し、**WorkingFolder** を "CodePackage" に設定します。  VotingWeb サービスが開始されるとき、VotingWeb.exe が起動される前に Setup.bat スクリプトが CodePackage フォルダー内で実行されます。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Setup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>

    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

### <a name="add-the-batch-and-powershell-setup-scripts"></a>バッチ スクリプトと PowerShell セットアップ スクリプトを追加する

**SetupEntryPoint** ポイントから PowerShell を実行するには、PowerShell ファイルを指し示すバッチ ファイルで PowerShell.exe を実行します。 最初に、バッチ ファイルをサービス プロジェクトに追加します。  ソリューション エクスプローラーで、 **[VotingWeb]** を右クリックし、 **[追加]** -> **[新しい項目]** の順に選択し、"Setup.bat" という名前の新しいファイルを追加します。  *Setup.bat* ファイルを編集して、次のコマンドを追加します。

```bat
powershell.exe -ExecutionPolicy Bypass -Command ".\SetCertAccess.ps1"
```

*Setup.bat* ファイルのプロパティを変更して **[出力ディレクトリにコピー]** を [新しい場合はコピーする] に設定します。

![ファイルのプロパティを設定する][image1]

ソリューション エクスプローラーで、 **[VotingWeb]** を右クリックし、 **[追加]** -> **[新しい項目]** の順に選択し、"SetCertAccess.ps1" という名前の新しいファイルを追加します。  *SetCertAccess.ps1* ファイルを編集して、次のスクリプトを追加します。

```powershell
$subject="mytestcert"
$userGroup="NETWORK SERVICE"

Write-Host "Checking permissions to certificate $subject.." -ForegroundColor DarkCyan

$cert = (gci Cert:\LocalMachine\My\ | where { $_.Subject.Contains($subject) })[-1]

if ($cert -eq $null)
{
    $message="Certificate with subject:"+$subject+" does not exist at Cert:\LocalMachine\My\"
    Write-Host $message -ForegroundColor Red
    exit 1;
}elseif($cert.HasPrivateKey -eq $false){
    $message="Certificate with subject:"+$subject+" does not have a private key"
    Write-Host $message -ForegroundColor Red
    exit 1;
}else
{
    $keyName=$cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName

    $keyPath = "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys\"
    $fullPath=$keyPath+$keyName
    $acl=(Get-Item $fullPath).GetAccessControl('Access')


    $hasPermissionsAlready = ($acl.Access | where {$_.IdentityReference.Value.Contains($userGroup.ToUpperInvariant()) -and $_.FileSystemRights -eq [System.Security.AccessControl.FileSystemRights]::FullControl}).Count -eq 1

    if ($hasPermissionsAlready){
        Write-Host "Account $userGroup already has permissions to certificate '$subject'." -ForegroundColor Green
        return $false;
    } else {
        Write-Host "Need add permissions to '$subject' certificate..." -ForegroundColor DarkYellow

        $permission=$userGroup,"Full","Allow"
        $accessRule=new-object System.Security.AccessControl.FileSystemAccessRule $permission
        $acl.AddAccessRule($accessRule)
        Set-Acl $fullPath $acl

        Write-Output "Permissions were added"

        return $true;
    }
}

```

*SetCertAccess.ps1* ファイルのプロパティを変更して **[出力ディレクトリにコピー]** を [新しい場合はコピーする] に設定します。

### <a name="run-the-setup-script-as-a-local-administrator"></a>セットアップ スクリプトをローカル管理者として実行する

既定では、サービス セットアップ エントリ ポイント実行可能ファイルは、Service Fabric と同じ資格情報で実行されます (通常は NetworkService アカウント)。 *SetCertAccess.ps1* を実行するには、管理者特権が必要です。 アプリケーション マニフェストでは、ローカル管理者アカウントで、スタートアップ スクリプトを実行するセキュリティのアクセス許可を変更できます。

ソリューション エクスプローラーで *Voting/ApplicationPackageRoot/ApplicationManifest.xml* を開きます。 最初に、**Principals** セクションを作成し、新しいユーザー (たとえば、"SetupAdminUser") を追加します。 SetupAdminUser ユーザー アカウントを Administrators システム グループに追加します。
次に、VotingWebPkg の **ServiceManifestImport** セクションで、**RunAsPolicy** を構成して、SetupAdminUser プリンシパルをセットアップ エントリ ポイントに適用します。 このポリシーでは、Setup.bat ファイルは、(管理者特権を持つ) SetupAdminUser として実行することを Service Fabric に通知します。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="run-the-application-locally"></a>ローカルでアプリケーションを実行する

ソリューション エクスプローラーで、**Voting** アプリケーションを選択し、**Application URL** プロパティを "https:\//localhost:443" に設定します。

すべてのファイルを保存した後、F5 キーを押してアプリケーションをローカルで実行します。  アプリケーションのデプロイ後、Web ブラウザーで https:\//localhost:443 が開かれます。 自己署名証明書を使用している場合、この Web サイトのセキュリティが PC によって信頼されていないことを示す警告が表示されます。  Web ページに進みます。

![投票アプリケーション][image2]

## <a name="install-certificate-on-cluster-nodes"></a>クラスター ノードに証明書をインストールする

アプリケーションを Azure にデプロイする前に、すべてのリモート クラスター ノードの `Cert:\LocalMachine\My` ストアに証明書をインストールします。  サービスは、クラスターの別のノードに移動することができます。  フロントエンド Web サービスがクラスター ノードで開始されると、スタートアップ スクリプトによって証明書が参照され、アクセス許可が構成されます。

最初に、証明書を PFX ファイルにエクスポートします。 certlm.msc アプリケーションを開き、 **[個人]** > **[証明書]** の順に移動します。  *[mytestcert]* 証明書を右クリックし、 **[すべてのタスク]** > **[エクスポート]** の順に選択します。

![証明書をエクスポートします。][image4]

エクスポート ウィザードで、 **[はい、秘密キーをエクスポートします]** を選択し、Personal Information Exchange (PFX) 形式を選択します。  ファイルを *C:\Users\sfuser\votingappcert.pfx* にエクスポートします。

次に、[Add-AzServiceFabricApplicationCertificate](/powershell/module/az.servicefabric/Add-azServiceFabricApplicationCertificate) コマンドレットを使用して、リモート クラスターに証明書をインストールします。

> [!Warning]
> アプリケーションの開発およびテスト用には自己署名証明書で十分です。 運用アプリケーションの場合は、自己署名証明書ではなく、[証明機関 (CA)](https://wikipedia.org/wiki/Certificate_authority) から取得した証明書を使用してください。

```powershell
Connect-AzAccount

$vaultname="sftestvault"
$certname="VotingAppPFX"
$certpw="!Password321#"
$groupname="voting_RG"
$clustername = "votinghttps"
$ExistingPfxFilePath="C:\Users\sfuser\votingappcert.pfx"

$appcertpwd = ConvertTo-SecureString -String $certpw -AsPlainText -Force

Write-Host "Reading pfx file from $ExistingPfxFilePath"
$cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2 $ExistingPfxFilePath, $certpw

$bytes = [System.IO.File]::ReadAllBytes($ExistingPfxFilePath)
$base64 = [System.Convert]::ToBase64String($bytes)

$jsonBlob = @{
   data = $base64
   dataType = 'pfx'
   password = $certpw
   } | ConvertTo-Json

$contentbytes = [System.Text.Encoding]::UTF8.GetBytes($jsonBlob)
$content = [System.Convert]::ToBase64String($contentbytes)

$secretValue = ConvertTo-SecureString -String $content -AsPlainText -Force

# Upload the certificate to the key vault as a secret
Write-Host "Writing secret to $certname in vault $vaultname"
$secret = Set-AzureKeyVaultSecret -VaultName $vaultname -Name $certname -SecretValue $secretValue

# Add a certificate to all the VMs in the cluster.
Add-AzServiceFabricApplicationCertificate -ResourceGroupName $groupname -Name $clustername -SecretIdentifier $secret.Id -Verbose
```

## <a name="open-port-443-in-the-azure-load-balancer"></a>Azure Load Balancer のポート 443 を開く

ロード バランサーのポート 443 がまだ開かれていない場合は開きます。

```powershell
$probename = "AppPortProbe6"
$rulename="AppPortLBRule6"
$RGname="voting_RG"
$port=443

# Get the load balancer resource
$resource = Get-AzResource | Where {$_.ResourceGroupName –eq $RGname -and $_.ResourceType -eq "Microsoft.Network/loadBalancers"}
$slb = Get-AzLoadBalancer -Name $resource.Name -ResourceGroupName $RGname

# Add a new probe configuration to the load balancer
$slb | Add-AzLoadBalancerProbeConfig -Name $probename -Protocol Tcp -Port $port -IntervalInSeconds 15 -ProbeCount 2

# Add rule configuration to the load balancer
$probe = Get-AzLoadBalancerProbeConfig -Name $probename -LoadBalancer $slb
$slb | Add-AzLoadBalancerRuleConfig -Name $rulename -BackendAddressPool $slb.BackendAddressPools[0] -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -Probe $probe -Protocol Tcp -FrontendPort $port -BackendPort $port

# Set the goal state for the load balancer
$slb | Set-AzLoadBalancer
```

## <a name="deploy-the-application-to-azure"></a>Azure にアプリケーションを展開する

すべてのファイルを保存します。"デバッグ" から "リリース" に切り替えた後、F6 キーを押してリビルドします。  ソリューション エクスプローラーで、 **[Voting]** を右クリックし、 **[発行]** を選択します。 [クラスターへのアプリケーションのデプロイ](service-fabric-tutorial-deploy-app-to-party-cluster.md)に関するページで作成したクラスターの接続エンドポイントを選択するか、別のクラスターを選択します。  **[発行]** をクリックして、リモート クラスターにアプリケーションを発行します。

アプリケーションがデプロイされたら、Web ブラウザーを開き、[https://mycluster.region.cloudapp.azure.com:443](https://mycluster.region.cloudapp.azure.com:443) に移動します (クラスターの接続エンドポイントで URL を更新します)。 自己署名証明書を使用している場合、この Web サイトのセキュリティが PC によって信頼されていないことを示す警告が表示されます。  Web ページに進みます。

![投票アプリケーション][image3]

## <a name="next-steps"></a>次の手順

チュートリアルのこの部分で学習した内容は次のとおりです。

> [!div class="checklist"]
> * サービスに HTTPS エンドポイントを定義する
> * HTTPS を使用するように Kestrel を構成する
> * SSL 証明書をリモート クラスター ノードにインストールする
> * 証明書の秘密キーへのアクセス権を "ネットワーク サービス" に与える
> * Azure Load Balancer のポート 443 を開く
> * アプリケーションをリモート クラスターにデプロイする

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [Azure Pipelines を使用して CI/CD を構成する](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)

[image1]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/SetupBatProperties.png
[image2]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/VotingAppLocal.png
[image3]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/VotingAppAzure.png
[image4]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/ExportCert.png
