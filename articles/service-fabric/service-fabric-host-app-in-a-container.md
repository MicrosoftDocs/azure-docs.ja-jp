---
title: コンテナー内の .NET アプリを Azure Service Fabric にデプロイする | Microsoft Docs
description: Visual Studio を使って既存の .NET アプリケーションをコンテナーに格納し、Service Fabric 内のコンテナーをローカルでデバッグする方法を紹介します。 コンテナーに格納されたアプリケーションは Azure のコンテナー レジストリにプッシュされ、Service Fabric クラスターにデプロイされます。 Azure にデプロイされたアプリケーションは、データの保持に Azure SQL DB を使用します。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/08/2019
ms.author: atsenthi
ms.openlocfilehash: 66d668821c47854d006f2efa425d38f9bf1df7d0
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599504"
---
# <a name="tutorial-deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>チュートリアル:Windows コンテナー内の .NET アプリケーションを Azure Service Fabric にデプロイする

このチュートリアルでは、既存の ASP.NET アプリケーションをコンテナーに格納して Service Fabric アプリケーションとしてパッケージ化する方法を説明します。  ローカルの Service Fabric 開発クラスターでコンテナーを実行した後、アプリケーションを Azure にデプロイします。  アプリケーションは、[Azure SQL Database](/azure/sql-database/sql-database-technical-overview) にデータを保持します。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Visual Studio を使った既存のアプリケーションのコンテナー格納
> * Azure SQL データベースの作成
> * Azure コンテナー レジストリの作成
> * Azure への Service Fabric アプリケーションのデプロイ


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

1. Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成します。
2. Windows 10 でコンテナーを実行できるように、[Docker CE for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) をインストールします。
3. [Service Fabric ランタイムのバージョン 6.2 以降](service-fabric-get-started.md)と、[Service Fabric SDK のバージョン 3.1](service-fabric-get-started.md) 以降をインストールします。
4. [Visual Studio 2019 のバージョン 16.1](https://www.visualstudio.com/) 以降をインストールし、**Azure の開発**ワークロードと **ASP.NET と Web 開発**ワークロードをインストールします。
5. [Azure PowerShell][link-azure-powershell-install] をインストールする
 

## <a name="download-and-run-fabrikam-fiber-callcenter"></a>Fabrikam Fiber CallCenter をダウンロードして実行する
[Fabrikam Fiber CallCenter][link-fabrikam-github] というサンプル アプリケーションをダウンロードします。  **[download archive]\(アーカイブのダウンロード\)** リンクをクリックします。  *fabrikam.zip* ファイルの *sourceCode* ディレクトリにある *sourceCode.zip* ファイルを展開してから、お使いのコンピューターに *VS2015* ディレクトリを展開します。

Fabrikam Fiber CallCenter アプリケーションがビルドされ、問題なく実行できていることを確認します。  Visual Studio を**管理者**として起動し、[FabrikamFiber.CallCenter.sln][link-fabrikam-github] ファイルを開きます。  F5 キーを押して、アプリケーションをデバッグおよび実行します。

![Fabrikam の Web のサンプル][fabrikam-web-page]

## <a name="containerize-the-application"></a>アプリケーションのコンテナー格納
**FabrikamFiber.Web** プロジェクトを右クリックし、 **[追加]**  >  **[コンテナー オーケストレーター サポート]** の順に選択します。  コンテナー オーケストレーターとして **[Service Fabric]** を選択し、 **[OK]** をクリックします。

**[はい]** をクリックして Docker を Windows コンテナーに切り替えます。

ソリューションに、新しい Service Fabric アプリケーション プロジェクト **FabrikamFiber.CallCenterApplication** が作成されます。  既存の **FabrikamFiber.Web** プロジェクトに Docker ファイルが追加されます。  **FabrikamFiber.Web** プロジェクトには、ほかにも **PackageRoot** ディレクトリが追加されます。このディレクトリには、新しい FabrikamFiber.Web サービスのサービス マニフェストと各種の設定が格納されています。 

これで、コンテナーを Service Fabric アプリケーションにビルドしてパッケージ化する準備ができました。 コンピューター上にコンテナー イメージをビルドすると、コンテナー レジストリにプッシュしたり、ホストにプルして実行したりできるようになります。

## <a name="create-an-azure-sql-db"></a>Azure SQL DB を作成する
運用環境で Fabrikam Fiber CallCenter アプリケーションを実行するときは、データベースにデータを保持する必要があります。 現在コンテナー内のデータの永続化を保証する方法はありません。そのため、コンテナー内に SQL Server の運用データを格納することはできません。

Microsoft では、[Azure SQL Database](/azure/sql-database/sql-database-get-started-powershell) の使用をお勧めしています。 Azure でマネージド SQL Server DB を設定して実行するには、次のスクリプトを実行します。  スクリプトは必要に応じて変更してください。 *clientIP* は、開発用コンピューターの IP アドレスです。 スクリプトから出力されたサーバーの名前はメモしておいてください。 

```powershell
$subscriptionID="<subscription ID>"

# Sign in to your Azure account and select your subscription.
Login-AzAccount -SubscriptionId $subscriptionID 

# The data center and resource name for your resources.
$dbresourcegroupname = "fabrikam-fiber-db-group"
$location = "southcentralus"

# The logical server name: Use a random value or replace with your own value (do not capitalize).
$servername = "fab-fiber-$(Get-Random)"

# Set an admin login and password for your database.
# The login information for the server.
$adminlogin = "ServerAdmin"
$password = "Password@123"

# The IP address of your development computer that accesses the SQL DB.
$clientIP = "<client IP>"

# The database name.
$databasename = "call-center-db"

# Create a new resource group for your deployment and give it a name and a location.
New-AzResourceGroup -Name $dbresourcegroupname -Location $location

# Create the SQL server.
New-AzSqlServer -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# Create the firewall rule to allow your development computer to access the server.
New-AzSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClient" -StartIpAddress $clientIP -EndIpAddress $clientIP

# Creeate the database in the server.
New-AzSqlDatabase  -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -RequestedServiceObjectiveName "S0"

Write-Host "Server name is $servername"
```
> [!TIP]
> 開発用コンピューターが会社のファイアウォールにより保護されている場所に置かれている場合には、そのコンピューターの IP アドレスとインターネットに公開される IP アドレスとが異なることもあります。 ファイアウォール規則に使用する正しい IP アドレスがデータベースにあることを確認するには、[Azure portal](https://portal.azure.com) にアクセスして、[SQL データベース] セクションで目的のデータベースを探してください。 その名前をクリックし、[概要] セクションの [サーバー ファイアウォールの設定] をクリックします。 "クライアント IP アドレス" は、開発マシンの IP アドレスです。 "AllowClient" ルール内の IP アドレスと一致していることを確認してください。

## <a name="update-the-web-config"></a>Web 構成の更新
**FabrikamFiber.Web** プロジェクトに戻り、**web.config** ファイル内の接続文字列を更新し、コンテナー内の SQL Server を指定します。  前のスクリプトで作成したサーバー名の接続文字列の *Server* の部分を更新します。 "fab-fiber-751718376.database.windows.net" のようになっている必要があります。

```xml
<add name="FabrikamFiber-Express" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
<add name="FabrikamFiber-DataWarehouse" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
  
```
>[!NOTE]
>ローカル デバッグには、ホストからアクセスできる範囲であれば好きな SQL Server を使用できます。 ただし、**localdb** は `container -> host` 通信をサポートしていません。 Web アプリケーションのリリース ビルドを作成するときに別の SQL データベースを使用する場合は、*web.release.config* ファイルに別の接続文字列を追加します。

## <a name="run-the-containerized-application-locally"></a>コンテナーに格納されたアプリケーションをローカルで実行する
**F5** キーを押し、ローカルの Service Fabric 開発クラスターのコンテナーの中にあるアプリケーションを実行およびデバッグします。 'ServiceFabricAllowedUsers' グループに Visual Studio プロジェクト ディレクトリの読み取りおよび実行アクセス許可を付与するように求めるメッセージ ボックスが表示された場合は、 **[はい]** をクリックします。

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成
アプリケーションをローカルで実行できたので、Azure にデプロイする準備を始めましょう。  コンテナー レジストリにコンテナー イメージを格納する必要があります。  次のスクリプトを使って、[Azure コンテナー レジストリ](/azure/container-registry/container-registry-intro)を作成します。 コンテナー レジストリ名は他の Azure サブスクリプションからも表示できるため、一意にする必要があります。
Azure にアプリケーションをデプロイする前に、このレジストリにコンテナー イメージをプッシュします。  Azure 内のクラスターにアプリケーションをデプロイすると、このレジストリからコンテナー イメージがプルされます。

```powershell
# Variables
$acrresourcegroupname = "fabrikam-acr-group"
$location = "southcentralus"
$registryname="fabrikamregistry$(Get-Random)"

New-AzResourceGroup -Name $acrresourcegroupname -Location $location

$registry = New-AzContainerRegistry -ResourceGroupName $acrresourcegroupname -Name $registryname -EnableAdminUser -Sku Basic
```

## <a name="create-a-service-fabric-cluster-on-azure"></a>Azure に Service Fabric クラスターを作成する
Service Fabric アプリケーションは、ネットワークに接続された一連の物理マシンであるクラスター上で実行されます。  Azure にアプリケーションをデプロイする前に、Azure に Service Fabric クラスターを作成する必要があります。

次のようにすることができます。
- Visual Studio でテスト クラスターを作成する。 このオプションでは、お好きな構成を使用して、セキュリティで保護されたクラスターを Visual Studio で直接作成できます。 
- [テンプレートからセキュリティで保護されたクラスターを作成する](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

このチュートリアルでは、Visual Studio からクラスターを作成します。テストのシナリオでは、こちらの方法が適しているからです。 別の方法でクラスターを作成する場合や、既存のクラスターを使用する場合には、接続エンドポイントをコピーして貼り付けるか、サブスクリプションからクラスターを選択します。 

開始する前に、ソリューション エクスプローラーで [FabrikamFiber.Web]、[PackageRoot]、[ServiceManifest.xml] の順に開きます。 **[エンドポイント]** に記載されている Web フロントエンドのポートをメモします。 

クラスターを作成する際は、次の手順に従います。 

1. ソリューション エクスプローラーで **FabrikamFiber.CallCenterApplication** アプリケーション プロジェクトを右クリックし、 **[発行]** を選択します。

2. サブスクリプションにアクセスできるように、Azure アカウントを使用してサインインします。 

3. **[接続のエンドポイント]** ドロップダウンを選択して、 **[新しいクラスターの作成...]** オプションを選択します。    
        
4. **[クラスターの作成]** ダイアログで、次のように設定を変更します。

    a. **[クラスター名]** フィールドでクラスターの名前を指定します。また、使用するサブスクリプションと場所を指定します。 クラスター リソース グループの名前をメモしておいてください。

    b. 省略可能:ノードの数を変更できます。 既定では、Service Fabric のシナリオをテストするのに最低限必要な 3 ノードになっています。

    c. **[証明書]** タブを選択します。このタブでは、クラスターの証明書をセキュリティで保護するために使用されるパスワードを入力します。 この証明書は、クラスターのセキュリティ保護に役立ちます。 また、証明書を保存したい場所にパスを変更することもできます。 アプリケーションをクラスターに発行するうえで必要な手順であるため、Visual Studio では証明書を自動でインポートすることもできます。

    d. **[VM の詳細]** タブを選択します。クラスターを構成する仮想マシン (VM) に使用したいパスワードを指定します。 ユーザー名とパスワードは、VM へのリモート接続に使用できます。 また、VM マシン サイズを選択できるほか、必要に応じて VM イメージを変更できます。 

    > [!IMPORTANT]
    >実行中のコンテナーをサポートする SKU を選択します。 クラスター ノード上の Windows Server OS は、コンテナーの Windows Server OS と互換性を持っている必要があります。 詳細については、[Windows Server コンテナーの OS とホスト OS の互換性](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility)に関するページを参照してください。 このチュートリアルでは、既定では Windows Server 2016 LTSC に基づいて Docker イメージを作成します。 このイメージに基づくコンテナーは、コンテナー搭載 Windows Server 2016 Datacenter で作成されたクラスター上で実行されます。 ただし、コンテナー搭載 Windows Server Datacenter Core 1709 に基づいてクラスターを作成するか、既存のクラスターを使用する場合は、コンテナーの基になる Windows Server OS イメージを変更する必要があります。 **FabrikamFiber.Web** プロジェクトの **Dockerfile** を開き、(`windowsservercore-ltsc` に基づいて) 既存の `FROM` ステートメントをコメントアウトし、`windowsservercore-1709` に基づいて `FROM` ステートメントのコメントを解除します。 

    e. **[詳細]** タブで、クラスターをデプロイする際にロード バランサーで開放するアプリケーション ポートを列挙します。 これは、クラスターの作成を始める前にメモしておいたポートです。 さらに、アプリケーション ログ ファイルのルーティングに使用される既存の Application Insights キーを追加することもできます。

    f. 設定の変更が完了したら、 **[作成]** ボタンを選択します。 
1. 作成の完了には数分かかります。クラスターが完全に作成されると、出力ウィンドウにその旨が表示されます。
    

## <a name="allow-your-application-running-in-azure-to-access-the-sql-db"></a>Azure で実行しているアプリケーションに SQL DB へのアクセスを許可する
ローカルで実行しているアプリケーションにアクセス権を付与するための SQL ファイアウォール規則は、既に作成しました。  次は、Azure で実行しているアプリケーションが SQL DB にアクセスできるようにする必要があります。  Service Fabric クラスターの[仮想ネットワーク サービス エンドポイント](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)を作成してから、そのエンドポイントに対して SQL DB へのアクセスを許可する規則を作成します。 クラスターを作成する際にメモしておいたクラスター リソース グループ変数を指定してください。 

```powershell
# Create a virtual network service endpoint
$clusterresourcegroup = "<cluster resource group>"
$resource = Get-AzResource -ResourceGroupName $clusterresourcegroup -ResourceType Microsoft.Network/virtualNetworks | Select-Object -first 1
$vnetName = $resource.Name

Write-Host 'Virtual network name: ' $vnetName 

# Get the virtual network by name.
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $clusterresourcegroup `
  -Name              $vnetName

Write-Host "Get the subnet in the virtual network:"

# Get the subnet, assume the first subnet contains the Service Fabric cluster.
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet | Select-Object -first 1

$subnetName = $subnet.Name
$subnetID = $subnet.Id
$addressPrefix = $subnet.AddressPrefix

Write-Host "Subnet name: " $subnetName " Address prefix: " $addressPrefix " ID: " $subnetID

# Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $subnetName `
  -AddressPrefix   $addressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint Microsoft.Sql | Set-AzVirtualNetwork

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

# Add a SQL DB firewall rule for the virtual network service endpoint
$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $subnetName `
  -VirtualNetwork $vnet;

$VNetRuleName="ServiceFabricClusterVNetRule"
$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $dbresourcegroupname `
  -ServerName             $servername `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnetID;
```
## <a name="deploy-the-application-to-azure"></a>Azure にアプリケーションを展開する
これでアプリケーションの準備ができたので、Visual Studio から直接 Azure にあるクラスターにデプロイできるようになりました。  ソリューション エクスプローラーで **FabrikamFiber.CallCenterApplication** アプリケーション プロジェクトを右クリックし、 **[発行]** を選択します。  **[接続エンドポイント]** では、以前に作成したクラスターのエンドポイントを選択します。  **[Azure Container Registry]** では、以前に作成したコンテナー レジストリを選択します。  **[発行]** をクリックして、Azure にあるクラスターにアプリケーションをデプロイします。

![アプリケーションの発行][publish-app]

出力ウィンドウでデプロイの進行状況を確認します。  アプリケーションのデプロイが終わったらブラウザーを開き、クラスターのアドレスとアプリケーション ポートを入力します。 たとえば、「http:\//fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:8659/」と入力します。

![Fabrikam の Web のサンプル][fabrikam-web-page-deployed]

## <a name="set-up-continuous-integration-and-deployment-cicd-with-a-service-fabric-cluster"></a>Service Fabric クラスターを使用した継続的インテグレーションとデプロイ (CI/CD) の設定
Azure DevOps を使用して、Service Fabric クラスターへの CI/CD アプリケーションのデプロイを構成する方法については、「[チュートリアル: CI/CD を使用して Service Fabric クラスターへアプリケーションをデプロイする](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)」を参照してください。 このチュートリアルで説明されているプロセスは、この (FabrikamFiber) プロジェクトと同じですが、投票サンプルのダウンロードはスキップし、リポジトリ名として Voting の代わりに FabrikamFiber を指定してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ
一連の作業が終わったら、作成したリソースをすべて削除してください。  最も簡単なのは、Service Fabric クラスター、Azure SQL DB、Azure コンテナー レジストリが含まれるリソース グループを削除するという方法です。

```powershell
$dbresourcegroupname = "fabrikam-fiber-db-group"
$acrresourcegroupname = "fabrikam-acr-group"
$clusterresourcegroupname="fabrikamcallcentergroup"

# Remove the Azure SQL DB
Remove-AzResourceGroup -Name $dbresourcegroupname

# Remove the container registry
Remove-AzResourceGroup -Name $acrresourcegroupname

# Remove the Service Fabric cluster
Remove-AzResourceGroup -Name $clusterresourcegroupname
```

## <a name="next-steps"></a>次の手順
このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Visual Studio を使った既存のアプリケーションのコンテナー格納
> * Azure SQL データベースの作成
> * Azure コンテナー レジストリの作成
> * Azure への Service Fabric アプリケーションのデプロイ

このチュートリアルの次の部分では、[CI/CD を使用して Service Fabric クラスターへコンテナー アプリケーションをデプロイする](service-fabric-tutorial-deploy-container-app-with-cicd-vsts.md)方法について説明します。

[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-azure-powershell-install]: /powershell/azure/install-Az-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[fabrikam-web-page]: media/service-fabric-host-app-in-a-container/fabrikam-web-page.png
[fabrikam-web-page-deployed]: media/service-fabric-host-app-in-a-container/fabrikam-web-page-deployed.png
[publish-app]: media/service-fabric-host-app-in-a-container/publish-app.png
m-web-page.png
[fabrikam-web-page-deployed]: media/service-fabric-host-app-in-a-container/fabrikam-web-page-deployed.png
[publish-app]: media/service-fabric-host-app-in-a-container/publish-app.png
