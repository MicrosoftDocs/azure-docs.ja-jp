---
title: "C# を使用した Azure 仮想マシンのデプロイ | Microsoft Docs"
description: "C# と Azure Resource Manager を使用して、仮想マシンとそれをサポートするすべてのリソースをデプロイします。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: e96eacd689ead229d0c48d15f53a932b903c5281
ms.openlocfilehash: a03a33ca275d0d81f8968ed19b1cc2fb8907cb1c
ms.lasthandoff: 03/02/2017


---
# <a name="deploy-an-azure-virtual-machine-using-c"></a>C を使用した Azure 仮想マシンのデプロイ# #

この記事では、Azure 仮想マシンとそれをサポートするリソースを C# を使用して作成する方法を説明します。

これらの手順を実行するには約 20 分かかります。

## <a name="step-1-create-a-visual-studio-project"></a>手順 1: Visual Studio プロジェクトを作成する

この手順では、Visual Studio がインストールされていることを確認し、リソースを作成するために使用するコンソール アプリケーションを作成します。

1. まだ [Visual Studio](https://www.visualstudio.com/) をインストールしていない場合は、インストールを実行します。
2. Visual Studio で、**[ファイル]** > **[新規]** > **[プロジェクト]**をクリックします。
3. **[テンプレート]** > の **[Visual C#]** で **[コンソール アプリケーション]** を選択し、プロジェクトの名前と場所を入力して、**[OK]** をクリックします。

## <a name="step-2-install-libraries"></a>手順 2: ライブラリをインストールする

NuGet パッケージを使用すると、手順を完了するために必要なライブラリを簡単にインストールできます。 Visual Studio で必要なライブラリを入手するには、次の手順に従います。


1. ソリューション エクスプローラーでプロジェクト名を右クリックし、**[NuGet パッケージの管理]**をクリックし、**[参照]** をクリックします。
2. 検索ボックスに「*Microsoft.IdentityModel.Clients.ActiveDirectory*」と入力し、**[インストール]** をクリックします。指示に従ってパッケージをインストールします。
3. ページの上部で、 **[リリース前のパッケージを含める]**を選択します。 検索ボックスに「*Microsoft.Azure.Management.Compute*」と入力し、**[インストール]** をクリックします。指示に従ってパッケージをインストールします。
4. 検索ボックスに「*Microsoft.Azure.Management.Network*」と入力し、**[インストール]** をクリックします。指示に従ってパッケージをインストールします。
5. 検索ボックスに「*Microsoft.Azure.Management.Storage*」と入力し、**[インストール]** をクリックします。指示に従ってパッケージをインストールします。
6. 検索ボックスに「*Microsoft.Azure.Management.ResourceManager*」と入力し、**[インストール]** をクリックします。指示に従ってパッケージをインストールします。

これで、ライブラリを使用してアプリケーションの作成を開始する準備が整いました。

## <a name="step-3-create-the-credentials-used-to-authenticate-requests"></a>手順 3. 要求の認証に使用される資格情報を作成する

この手順を開始する前に、[Active Directory サービス プリンシパル](../azure-resource-manager/resource-group-authenticate-service-principal.md)にアクセスできることを確認します。 このサービス プリンシパルから、Azure Resource Manager への要求を認証するためのトークンを取得します。

1. 作成したプロジェクトの Program.cs ファイルを開き、次の using ステートメントをファイルの先頭に追加します。
   
    ```
    using Microsoft.Azure;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Storage;
    using Microsoft.Azure.Management.Storage.Models;
    using Microsoft.Azure.Management.Network;
    using Microsoft.Azure.Management.Network.Models;
    using Microsoft.Azure.Management.Compute;
    using Microsoft.Azure.Management.Compute.Models;
    using Microsoft.Rest;
    ```

2. 資格情報の作成に必要なトークンを取得するために、次のメソッドを Program クラスに追加します。
   
    ```    
    private static async Task<AuthenticationResult> GetAccessTokenAsync()
    {
      var cc = new ClientCredential("{client-id}", "{client-secret}");
      var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
      var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
      if (token == null)
      {
        throw new InvalidOperationException("Could not get the token");
      }
      return token;
    }
    ```

    次の値を置き換えます。
    
    - *{client-id}*: Azure Active Directory アプリケーションの ID。 この ID は、AD アプリケーションの [プロパティ] ブレードで確認できます。 Azure Portal で AD アプリケーションを見つけるには、リソース メニューの **[Azure Active Directory]** をクリックし、**[アプリの登録]** をクリックします。
    - *{client-secret}*: AD アプリケーションのアクセス キー。 この ID は、AD アプリケーションの [プロパティ] ブレードで確認できます。
    - *{tenant-id}*: サブスクリプションのテナント ID。 テナント ID は、Azure Portal で Azure Active Directory の [プロパティ] ブレードで確認できます。 *[ディレクトリ ID]* というラベルが付いています。

3. 前に作成したメソッドを呼び出すために、次のコードを Program.cs ファイルの Main メソッドに追加します。
   
    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

4. Program.cs ファイルを保存します。

## <a name="step-3-create-the-resources"></a>手順 3: リソースを作成する

### <a name="register-the-providers-and-create-a-resource-group"></a>プロバイダーを登録し、リソース グループを作成する

すべてのリソースは、リソース グループに含まれる必要があります。 リソースをグループに追加する前に、リソース プロバイダーにサブスクリプションを登録する必要があります。

1. Program クラスの Main メソッドに変数を追加して、リソースに使用する名前を指定します。

    ```   
    var groupName = "myResourceGroup";
    var subscriptionId = "subsciptionId";
    var storageName = "myStorageAccount";
    var ipName = "myPublicIP";
    var subnetName = "mySubnet";
    var vnetName = "myVnet";
    var nicName = "myNIC";
    var avSetName = "myAVSet";
    var vmName = "myVM";  
    var location = "location";
    var adminName = "adminName";
    var adminPassword = "adminPassword";
    ```

    次の値を置き換えます。

    - *my* から始まるすべてのリソース名: 使用する環境で意味のある名前。
    - *subscriptionId*: サブスクリプション ID。 サブスクリプション ID は、Azure Portal の [サブスクリプション] ブレードで確認できます。
    - *location*: リソースを作成する [Azure リージョン](https://azure.microsoft.com/regions/)。
    - *adminName*: 仮想マシンの管理者アカウントの名前とパスワード。
    - *adminPassword*: 管理者アカウントのパスワード。

2. リソース グループを作成してプロバイダーを登録するために、次のメソッドを Program クラスに追加します。
   
    ```
    public static async Task<ResourceGroup> CreateResourceGroupAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location)
    {
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
   
      Console.WriteLine("Registering the providers...");
      var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
      Console.WriteLine(rpResult.RegistrationState);
      rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
      Console.WriteLine(rpResult.RegistrationState);
      rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
      Console.WriteLine(rpResult.RegistrationState);
   
      Console.WriteLine("Creating the resource group...");
      var resourceGroup = new ResourceGroup { Location = location };
      return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(
        groupName, 
        resourceGroup);
    }
    ```

3. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。
   
    ```   
    var rgResult = CreateResourceGroupAsync(
      credential,
      groupName,
      subscriptionId,
      location);
    Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
    Console.ReadLine();
    ```

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

非管理対象ディスクを使用する場合は、仮想マシン用に作成される仮想ハード ディスク ファイルを保存するための[ストレージ アカウント](../storage/storage-create-storage-account.md)が必要です。

1. ストレージ アカウントを作成するために、次のメソッドを Program クラスに追加します。

    ```   
    public static async Task<StorageAccount> CreateStorageAccountAsync(
      TokenCredentials credential,       
      string groupName,
      string subscriptionId,
      string location,
      string storageName)
    {
      var storageManagementClient = new StorageManagementClient(credential)
        { SubscriptionId = subscriptionId };
      
      Console.WriteLine("Creating the storage account...");

      return await storageManagementClient.StorageAccounts.CreateAsync(
        groupName,
        storageName,
        new StorageAccountCreateParameters()
          {
            Sku = new Microsoft.Azure.Management.Storage.Models.Sku() 
              { Name = SkuName.StandardLRS},
            Kind = Kind.Storage,
            Location = location
          }
      );
    }
    ```

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。
   
    ```
    var stResult = CreateStorageAccountAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      storageName);
    Console.WriteLine(stResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

仮想マシンと通信するには、パブリック IP アドレスが必要です。

1. 仮想マシンのパブリック IP アドレスを作成するために、次のメソッドを Program クラスに追加します。
   
    ```
    public static async Task<PublicIPAddress> CreatePublicIPAddressAsync(
      TokenCredentials credential,  
      string groupName,
      string subscriptionId,
      string location,
      string ipName)
    {
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
      
      Console.WriteLine("Creating the public ip...");

      return await networkManagementClient.PublicIPAddresses.CreateOrUpdateAsync(
        groupName,
        ipName,
        new PublicIPAddress
          {
            Location = location,
            PublicIPAllocationMethod = "Dynamic"
          }
      );
    }
    ```

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。
   
    ```   
    var ipResult = CreatePublicIPAddressAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      ipName);
    Console.WriteLine(ipResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成

リソース マネージャーのデプロイ モデルで作成された仮想マシンは、仮想ネットワーク内に存在する必要があります。

1. サブネットと仮想ネットワークを作成するために、次のメソッドを Program クラスに追加します。

    ```   
    public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location,
      string vnetName,
      string subnetName)
    {
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
   
      var subnet = new Subnet
        {
          Name = subnetName,
          AddressPrefix = "10.0.0.0/24"
        };
   
      var address = new AddressSpace 
        {
          AddressPrefixes = new List<string> { "10.0.0.0/16" }
        };
         
      Console.WriteLine("Creating the virtual network...");
      return await networkManagementClient.VirtualNetworks.CreateOrUpdateAsync(
        groupName,
        vnetName,
        new VirtualNetwork
          {
            Location = location,
            AddressSpace = address,
            Subnets = new List<Subnet> { subnet }
          }
      );
    }
    ```

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

    ```   
    var vnResult = CreateVirtualNetworkAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      vnetName,
      subnetName);
    Console.WriteLine(vnResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-a-network-interface"></a>ネットワーク インターフェイスの作成

仮想マシンが仮想ネットワーク上で通信するには、ネットワーク インターフェイスが必要です。

1. ネットワーク インターフェイスを作成するために、次のメソッドを Program クラスに追加します。

    ```   
    public static async Task<NetworkInterface> CreateNetworkInterfaceAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location,
      string subnetName,
      string vnetName,
      string ipName,
      string nicName)
    {
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
      var subnet = await networkManagementClient.Subnets.GetAsync(
        groupName,
        vnetName,
        subnetName
      );
      var publicIP = await networkManagementClient.PublicIPAddresses.GetAsync(
        groupName, 
        ipName
      );
   
      Console.WriteLine("Creating the network interface...");
      return await networkManagementClient.NetworkInterfaces.CreateOrUpdateAsync(
        groupName,
        nicName,
        new NetworkInterface
          {
            Location = location,
            IpConfigurations = new List<NetworkInterfaceIPConfiguration>
              {
                new NetworkInterfaceIPConfiguration
                  {
                    Name = nicName,
                    PublicIPAddress = pubipResponse,
                    Subnet = subnetResponse
                  }
              }
          }
      );
    }
    ```

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。
   
    ```
    var ncResult = CreateNetworkInterfaceAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      subnetName,
      vnetName,
      ipName,
      nicName);
    Console.WriteLine(ncResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-an-availability-set"></a>可用性セットの作成

可用性セットを使うと、アプリケーションで使用する仮想マシンのメンテナンスの管理が容易になります。

1. 可用性セットを作成するために、次のメソッドを Program クラスに追加します。

    ```   
    public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location,
      string avsetName)
    {
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
        
      Console.WriteLine("Creating the availability set...");
      return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
        groupName,
        avsetName,
        new AvailabilitySet()
          { Location = location }
      );
    }
    ```

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。
   
    ```
    var avResult = CreateAvailabilitySetAsync(
      credential,  
      groupName,
      subscriptionId,
      location,
      avSetName);
    Console.ReadLine();
    ```

### <a name="create-a-virtual-machine"></a>仮想マシンの作成

すべての関連リソースを作成したので、仮想マシンを作成できます。

1. 仮想マシンを作成するために、次のメソッドを Program クラスに追加します。

    ```   
    public static async Task<VirtualMachine> CreateVirtualMachineAsync(
      TokenCredentials credential, 
      string groupName,
      string subscriptionId,
      string location,
      string nicName,
      string avsetName,
      string storageName,
      string adminName,
      string adminPassword,
      string vmName)
    {
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };  
      var nic = await networkManagementClient.NetworkInterfaces.GetAsync(
        groupName, 
        nicName);
      var avSet = await computeManagementClient.AvailabilitySets.GetAsync(
        groupName, 
        avsetName);
   
      Console.WriteLine("Creating the virtual machine...");
      return await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(
        groupName,
        vmName,
        new VirtualMachine
          {
            Location = location,
            AvailabilitySet = new Microsoft.Azure.Management.Compute.Models.SubResource
              {
                Id = avSet.Id
              },
            HardwareProfile = new HardwareProfile
              {
                VmSize = "Standard_A0"
              },
            OsProfile = new OSProfile
              {
                AdminUsername = adminName,
                AdminPassword = adminPassword,
                ComputerName = vmName,
                WindowsConfiguration = new WindowsConfiguration
                  {
                    ProvisionVMAgent = true
                  }
              },
            NetworkProfile = new NetworkProfile
              {
                NetworkInterfaces = new List<NetworkInterfaceReference>
                  {
                    new NetworkInterfaceReference { Id = nic.Id }
                  }
              },
            StorageProfile = new StorageProfile
              {
                ImageReference = new ImageReference
                  {
                    Publisher = "MicrosoftWindowsServer",
                    Offer = "WindowsServer",
                    Sku = "2012-R2-Datacenter",
                    Version = "latest"
                  },
                OsDisk = new OSDisk
                  {
                    Name = "mytestod1",
                    CreateOption = DiskCreateOptionTypes.FromImage,
                    Vhd = new VirtualHardDisk
                      {
                        Uri = "http://" + storageName + ".blob.core.windows.net/vhds/mytestod1.vhd"
                      }
                  }
              }
          }
      );
    }
    ```

    > [!NOTE]
    > このチュートリアルでは、Windows Server オペレーティング システムのバージョンを実行する仮想マシンを作成します。 他のイメージの選択の詳細については、 [Windows PowerShell と Azure CLI による Azure 仮想マシン イメージのナビゲーションと選択](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。
    > 
    >

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。
   
    ```
    var vmResult = CreateVirtualMachineAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      nicName,
      avSetName,
      storageName,
      adminName,
      adminPassword,
      vmName);
    Console.WriteLine(vmResult.Result.ProvisioningState);
    Console.ReadLine();
    ```

## <a name="step-4-delete-the-resources"></a>手順 4: リソースを削除する

Azure で使用されるリソースに対して課金されるため、不要になったリソースは削除することを常にお勧めします。 仮想マシンとすべての関連リソースを削除する場合、必要な操作はリソース グループの削除だけです。

1. リソース グループを削除するために、次のメソッドを Program クラスに追加します。
   
    ```
    public static async void DeleteResourceGroupAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId)
    {
      Console.WriteLine("Deleting resource group...");
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
      await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
    }
    ```

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。
   
    ```   
    DeleteResourceGroupAsync(
      credential,
      groupName,
      subscriptionId);
    Console.ReadLine();
    ```

## <a name="step-5-run-the-console-application"></a>手順 5: コンソール アプリケーションの実行

1. コンソール アプリケーションを実行するには、Visual Studio で **[開始]** をクリックし、サブスクリプションで使用するのと同じユーザー名とパスワードを使用して Azure AD にサインインします。

2. "*[成功しました]*" 状態が表示されたら、**Enter** キーを押します。 
   
3. 仮想マシンが作成されたら、**Enter** キーを押してリソースの削除を開始する前に、Azure Portal でリソースの作成状況を確認できます。

## <a name="next-steps"></a>次のステップ
* テンプレートを使用して仮想マシンを作成する方法については、「 [C# と Resource Manager テンプレートを使用した Azure の仮想マシンのデプロイ](virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。
* 「[Azure Resource Manager と C# を使用した Azure Virtual Machines の管理](virtual-machines-windows-csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」で、作成した仮想マシンを管理する方法を確認します。


