---
title: C# を使用した Azure 仮想マシンの作成および管理 | Microsoft Docs
description: C# と Azure Resource Manager を使用して、仮想マシンとそれをサポートするすべてのリソースをデプロイします。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: b88bade886bf8cf22387e8733b8710414c944988
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361140"
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>C# を使用して Azure で Windows VM を作成および管理する #

[Azure 仮想マシン](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) には、いくつかのサポート Azure リソースが必要です。 この記事では、C# を使って VM リソースを作成、管理、削除する方法について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Visual Studio プロジェクトを作成する
> * パッケージをインストールする
> * 資格情報を作成する
> * リソースを作成する
> * 管理タスクを実行する
> * リソースを削除する
> * アプリケーションの実行

これらの手順を実行するには約 20 分かかります。

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

1. まだ [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio) をインストールしていない場合は、インストールを実行します。 [ワークロード] ページで **[.NET デスクトップ開発]** を選び、 **[インストール]** をクリックします。 サマリーで、 **[.NET Framework 4 から 4.6 の開発ツール]** が自動的に選択されていることが確認できます。 Visual Studio を既にインストールしてある場合は、Visual Studio 起動ツールを使って .NET ワークロードを追加できます。
2. Visual Studio で、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** をクリックします。
3. **[テンプレート]**  >  **[Visual C#]** で **[コンソール アプリ (.NET Framework)]** を選択し、プロジェクトの名前に「*myDotnetProject*」と入力して、プロジェクトの場所を選んだ後、 **[OK]** をクリックします。

## <a name="install-the-package"></a>パッケージをインストールする

NuGet パッケージを使用すると、手順を完了するために必要なライブラリを簡単にインストールできます。 Visual Studio で必要なライブラリを入手するには、次の手順に従います。

1. **[ツール]**  >  **[NuGet パッケージ マネージャー]** をクリックし、 **[パッケージ マネージャー コンソール]** をクリックします。
2. コンソールに次のコマンドを入力します。

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>資格情報を作成する

この手順を開始する前に、[Active Directory サービス プリンシパル](../../active-directory/develop/howto-create-service-principal-portal.md)にアクセスできることを確認します。 また、後の手順で必要になるので、アプリケーション ID、認証キー、テナント ID を控えておく必要があります。

### <a name="create-the-authorization-file"></a>認証ファイルを作成する

1. ソリューション エクスプ ローラーで、 *[myDotnetProject]*  >  **[追加]**  >  **[新しい項目]** を右クリックしてから、 *[Visual C# アイテム]* で **[テキスト ファイル]** を選択します。 ファイルに *azureauth.properties* と名前を付けて、 **[追加]** をクリックします。
2. 次の承認プロパティを追加します。

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    **&lt;subscription-id&gt;** をサブスクリプション ID に、 **&lt;application-id&gt;** を Active Directory アプリケーション ID に、 **&lt;authentication-key&gt;** をアプリケーション キーに、 **&lt;tenant-id&gt;** をテナント識別子に置き換えます。

3. azureauth.properties ファイルを保存します。 
4. AZURE_AUTH_LOCATION という Windows 環境変数を、作成した認証ファイルへのフルパスに設定します。 たとえば、次の PowerShell コマンドを使用します。

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>管理クライアントを作成する

1. 作成したプロジェクトの Program.cs ファイルを開きます。 次に、ファイルの先頭にある既存のステートメントに次の using ステートメントを追加します。

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    ```

2. 管理クライアントを作成するには、次のコードを Main メソッドに追加します。

    ```csharp
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-resources"></a>リソースを作成する

### <a name="create-the-resource-group"></a>リソース グループの作成

すべてのリソースは、[リソース グループ](../../azure-resource-manager/resource-group-overview.md)に含まれる必要があります。

アプリケーションに値を指定し、リソース グループを作成するには、次のコードを Main メソッドに追加します。

```csharp
var groupName = "myResourceGroup";
var vmName = "myVM";
var location = Region.USWest;
    
Console.WriteLine("Creating resource group...");
var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

### <a name="create-the-availability-set"></a>可用性セットを作成する

[可用性セット](tutorial-availability-sets.md)を使うと、アプリケーションで使う仮想マシンの管理が容易になります。

可用性セットを作成するには、次のコードを Main メソッドに追加します。

```csharp
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>パブリック IP アドレスの作成

仮想マシンと通信するには、[パブリック IP アドレス](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)が必要です。

仮想マシンのパブリック IP アドレスを作成するには、次のコードを Main メソッドに追加します。
   
```csharp
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>仮想ネットワークの作成

仮想マシンは、[仮想ネットワーク](../../virtual-network/virtual-networks-overview.md)のサブネット内に存在する必要があります。

サブネットと仮想ネットワークを作成するには、次のコードを Main メソッドに追加します。

```csharp
Console.WriteLine("Creating virtual network...");
var network = azure.Networks.Define("myVNet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithAddressSpace("10.0.0.0/16")
    .WithSubnet("mySubnet", "10.0.0.0/24")
    .Create();
```

### <a name="create-the-network-interface"></a>ネットワーク インターフェイスの作成

仮想マシンが仮想ネットワーク上で通信するには、ネットワーク インターフェイスが必要です。

ネットワーク インターフェイスを作成するには、次のコードを Main メソッドに追加します。

```csharp
Console.WriteLine("Creating network interface...");
var networkInterface = azure.NetworkInterfaces.Define("myNIC")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetwork(network)
    .WithSubnet("mySubnet")
    .WithPrimaryPrivateIPAddressDynamic()
    .WithExistingPrimaryPublicIPAddress(publicIPAddress)
    .Create();
 ```

### <a name="create-the-virtual-machine"></a>仮想マシンの作成

すべての関連リソースを作成したので、仮想マシンを作成できます。

仮想マシンを作成するには、次のコードを Main メソッドに追加します。

```csharp
Console.WriteLine("Creating virtual machine...");
azure.VirtualMachines.Define(vmName)
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .WithAdminUsername("azureuser")
    .WithAdminPassword("Azure12345678")
    .WithComputerName(vmName)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

> [!NOTE]
> このチュートリアルでは、Windows Server オペレーティング システムのバージョンを実行する仮想マシンを作成します。 他のイメージの選択の詳細については、 [Windows PowerShell と Azure CLI による Azure 仮想マシン イメージのナビゲーションと選択](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。
> 
>

Marketplace イメージではなく、既存のディスクを使用する場合は、このコードを使用します。

```csharp
var managedDisk = azure.Disks.Define("myosdisk")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .WithSizeInGB(128)
    .WithSku(DiskSkuTypes.PremiumLRS)
    .Create();

azure.VirtualMachines.Define("myVM")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

## <a name="perform-management-tasks"></a>管理タスクを実行する

仮想マシンのライフサイクルで、各種の管理タスクを実行する必要がある場合があります (仮想マシンの起動、停止、削除など)。 また、何度も行う作業や複雑な作業は、コードを作成して自動化したい場合もあるでしょう。

VM で何かを実行する必要がある場合、そのインスタンスを取得する必要があります。

```csharp
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>VM に関する情報を取得する

仮想マシンに関する情報を取得するには、次のコードを Main メソッドに追加します。

```csharp
Console.WriteLine("Getting information about the virtual machine...");
Console.WriteLine("hardwareProfile");
Console.WriteLine("   vmSize: " + vm.Size);
Console.WriteLine("storageProfile");
Console.WriteLine("  imageReference");
Console.WriteLine("    publisher: " + vm.StorageProfile.ImageReference.Publisher);
Console.WriteLine("    offer: " + vm.StorageProfile.ImageReference.Offer);
Console.WriteLine("    sku: " + vm.StorageProfile.ImageReference.Sku);
Console.WriteLine("    version: " + vm.StorageProfile.ImageReference.Version);
Console.WriteLine("  osDisk");
Console.WriteLine("    osType: " + vm.StorageProfile.OsDisk.OsType);
Console.WriteLine("    name: " + vm.StorageProfile.OsDisk.Name);
Console.WriteLine("    createOption: " + vm.StorageProfile.OsDisk.CreateOption);
Console.WriteLine("    caching: " + vm.StorageProfile.OsDisk.Caching);
Console.WriteLine("osProfile");
Console.WriteLine("  computerName: " + vm.OSProfile.ComputerName);
Console.WriteLine("  adminUsername: " + vm.OSProfile.AdminUsername);
Console.WriteLine("  provisionVMAgent: " + vm.OSProfile.WindowsConfiguration.ProvisionVMAgent.Value);
Console.WriteLine("  enableAutomaticUpdates: " + vm.OSProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);
Console.WriteLine("networkProfile");
foreach (string nicId in vm.NetworkInterfaceIds)
{
    Console.WriteLine("  networkInterface id: " + nicId);
}
Console.WriteLine("vmAgent");
Console.WriteLine("  vmAgentVersion" + vm.InstanceView.VmAgent.VmAgentVersion);
Console.WriteLine("    statuses");
foreach (InstanceViewStatus stat in vm.InstanceView.VmAgent.Statuses)
{
    Console.WriteLine("    code: " + stat.Code);
    Console.WriteLine("    level: " + stat.Level);
    Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
    Console.WriteLine("    message: " + stat.Message);
    Console.WriteLine("    time: " + stat.Time);
}
Console.WriteLine("disks");
foreach (DiskInstanceView disk in vm.InstanceView.Disks)
{
    Console.WriteLine("  name: " + disk.Name);
    Console.WriteLine("  statuses");
    foreach (InstanceViewStatus stat in disk.Statuses)
    {
        Console.WriteLine("    code: " + stat.Code);
        Console.WriteLine("    level: " + stat.Level);
        Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
        Console.WriteLine("    time: " + stat.Time);
    }
}
Console.WriteLine("VM general status");
Console.WriteLine("  provisioningStatus: " + vm.ProvisioningState);
Console.WriteLine("  id: " + vm.Id);
Console.WriteLine("  name: " + vm.Name);
Console.WriteLine("  type: " + vm.Type);
Console.WriteLine("  location: " + vm.Region);
Console.WriteLine("VM instance status");
foreach (InstanceViewStatus stat in vm.InstanceView.Statuses)
{
    Console.WriteLine("  code: " + stat.Code);
    Console.WriteLine("  level: " + stat.Level);
    Console.WriteLine("  displayStatus: " + stat.DisplayStatus);
}
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="stop-the-vm"></a>VM を停止する

仮想マシンを停止してそのすべての設定を保持する (引き続き課金されます)、または仮想マシンを停止して割り当てを解除します。 仮想マシンの割り当てを解除すると、それに関連付けられているすべてのリソースの割り当ても解除され、仮想マシンに対する課金が終了します。

仮想マシンの割り当てを解除せずに仮想マシンを停止するには、次のコードを Main メソッドに追加します。

```csharp
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

仮想マシンの割り当てを解除する場合は、PowerOff 呼び出しを次のコードに変更します。

```csharp
vm.Deallocate();
```

### <a name="start-the-vm"></a>VM を起動する

仮想マシンを起動するには、次のコードを Main メソッドに追加します。

```csharp
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>VM のサイズを変更する

仮想マシンのサイズを決定するときは、デプロイのさまざまな面を考慮する必要があります。 詳しくは、[VM サイズ](sizes.md)をご覧ください。  

仮想マシンのサイズを変更するには、次のコードを Main メソッドに追加します。

```csharp
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>VM にデータ ディスクを追加する

仮想マシンにデータ ディスクを追加するには、Main メソッドに次のコードを追加します。 この例では、2 GB のサイズ、LUN が 0、キャッシュの種類が ReadWrite のデータ ディスクを追加します。

```csharp
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>リソースを削除する

Azure で使用されるリソースに対して課金されるため、不要になったリソースは削除することを常にお勧めします。 仮想マシンとすべての関連リソースを削除する場合、必要な操作はリソース グループの削除だけです。

リソース グループを削除するには、次のコードを Main メソッドに追加します。

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>アプリケーションの実行

このコンソール アプリケーションが実行を開始してから完全に終了するまでには、約 5 分かかります。 

1. コンソール アプリケーションを実行するには、 **[開始]** をクリックします。

2. **Enter** キーを押してリソースの削除を開始する前に、Azure Portal でリソースの作成状況を確認することもできます。 デプロイに関する情報を参照するには、デプロイ状態をクリックします。

## <a name="next-steps"></a>次の手順
* テンプレートを使用して仮想マシンを作成する方法については、「 [C# と Resource Manager テンプレートを使用した Azure の仮想マシンのデプロイ](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。
* [Azure libraries for .NET](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet) の使用方法の詳細について学習します。
