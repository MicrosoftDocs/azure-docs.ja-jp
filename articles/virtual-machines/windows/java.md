---
title: Java を使用した Azure 仮想マシンの作成および管理
description: Java と Azure Resource Manager を使用して、仮想マシンとそれをサポートするすべてのリソースをデプロイします。
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure
ms.topic: how-to
ms.date: 07/17/2017
ms.custom: devx-track-java
ms.author: cynthn
ms.openlocfilehash: ea9d5d92d45db50470276929b6e7b4200bb427d6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557524"
---
# <a name="create-and-manage-windows-vms-in-azure-using-java"></a>Java を使用して Azure で Windows VM を作成および管理する

[Azure 仮想マシン](overview.md) (VM) には、いくつかのサポート Azure リソースが必要です。 この記事では、Java を使って VM リソースを作成、管理、削除する方法について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Maven プロジェクトを作成する
> * 依存関係を追加する
> * 資格情報を作成する
> * リソースを作成する
> * 管理タスクを実行する
> * リソースを削除する
> * アプリケーションの実行

これらの手順を実行するには約 20 分かかります。

## <a name="create-a-maven-project"></a>Maven プロジェクトを作成する

1. [Java](/azure/developer/java/fundamentals/java-jdk-long-term-support) をまだインストールしていない場合はインストールします。
2. [Maven](https://maven.apache.org/download.cgi) をインストールします。
3. 新しいフォルダーとプロジェクトを作成します。
    
    ```
    mkdir java-azure-test
    cd java-azure-test
    
    mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

## <a name="add-dependencies"></a>依存関係を追加する

1. `testAzureApp` フォルダーの中の `pom.xml` ファイルを開き、ビルド構成を &lt;プロジェクト&gt; に追加して、アプリケーションのビルドを有効にします。

    ```xml
    <build>
      <plugins>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <configuration>
                <mainClass>com.fabrikam.testAzureApp.App</mainClass>
            </configuration>
        </plugin>
      </plugins>
    </build>
    ```

2. Azure の Java SDK へのアクセスに必要な依存関係を追加します。

    ```xml
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-compute</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-resources</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-network</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.squareup.okio</groupId>
      <artifactId>okio</artifactId>
      <version>1.13.0</version>
    </dependency>
    <dependency>
      <groupId>com.nimbusds</groupId>
      <artifactId>nimbus-jose-jwt</artifactId>
      <version>3.6</version>
    </dependency>
    <dependency>
      <groupId>net.minidev</groupId>
      <artifactId>json-smart</artifactId>
      <version>1.0.6.3</version>
    </dependency>
    <dependency>
      <groupId>javax.mail</groupId>
      <artifactId>mail</artifactId>
      <version>1.4.5</version>
    </dependency>
    ```

3. ファイルを保存します。

## <a name="create-credentials"></a>資格情報を作成する

この手順を開始する前に、[Active Directory サービス プリンシパル](../../active-directory/develop/howto-create-service-principal-portal.md)にアクセスできることを確認します。 また、後の手順で必要になるので、アプリケーション ID、認証キー、テナント ID を控えておく必要があります。

### <a name="create-the-authorization-file"></a>承認ファイルを作成する

1. `azureauth.properties` という名前のファイルを作成し、次のプロパティを追加します。

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.microsoft.com/
    ```

    **&lt;subscription-id&gt;** をサブスクリプション ID に、 **&lt;application-id&gt;** を Active Directory アプリケーション ID に、 **&lt;authentication-key&gt;** をアプリケーション キーに、 **&lt;tenant-id&gt;** をテナント識別子に置き換えます。

2. ファイルを保存します。
3. シェルの AZURE_AUTH_LOCATION という名前の環境変数に、認証ファイルへの完全なパスを設定します。

### <a name="create-the-management-client"></a>管理クライアントを作成する

1. `src\main\java\com\fabrikam` の中の `App.java` ファイルを開き、次の package ステートメントが先頭にあることを確認します。

    ```java
    package com.fabrikam.testAzureApp;
    ```

2. package ステートメントの下に、次の import ステートメントを追加します。
   
    ```java
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.compute.AvailabilitySet;
    import com.microsoft.azure.management.compute.AvailabilitySetSkuTypes;
    import com.microsoft.azure.management.compute.CachingTypes;
    import com.microsoft.azure.management.compute.InstanceViewStatus;
    import com.microsoft.azure.management.compute.DiskInstanceView;
    import com.microsoft.azure.management.compute.VirtualMachine;
    import com.microsoft.azure.management.compute.VirtualMachineSizeTypes;
    import com.microsoft.azure.management.network.PublicIPAddress;
    import com.microsoft.azure.management.network.Network;
    import com.microsoft.azure.management.network.NetworkInterface;
    import com.microsoft.azure.management.resources.ResourceGroup;
    import com.microsoft.azure.management.resources.fluentcore.arm.Region;
    import com.microsoft.azure.management.resources.fluentcore.model.Creatable;
    import com.microsoft.rest.LogLevel;
    import java.io.File;
    import java.util.Scanner;
    ```

2. 要求を行うために必要な Active Directory 資格情報を作成するために、App クラスの main メソッドに次のコードを追加します。
   
    ```java
    try {
        final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));
        Azure azure = Azure.configure()
            .withLogLevel(LogLevel.BASIC)
            .authenticate(credFile)
            .withDefaultSubscription();
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    ```

## <a name="create-resources"></a>リソースを作成する

### <a name="create-the-resource-group"></a>リソース グループの作成

すべてのリソースは、[リソース グループ](../../azure-resource-manager/management/overview.md)に含まれる必要があります。

アプリケーションに値を指定し、リソース グループを作成するには、次のコードを main メソッドの try ブロックに追加します。

```java
System.out.println("Creating resource group...");
ResourceGroup resourceGroup = azure.resourceGroups()
    .define("myResourceGroup")
    .withRegion(Region.US_EAST)
    .create();
```

### <a name="create-the-availability-set"></a>可用性セットを作成する

[可用性セット](tutorial-availability-sets.md)を使うと、アプリケーションで使う仮想マシンの管理が容易になります。

可用性セットを作成するには、次のコードを main メソッドの try ブロックに追加します。

```java
System.out.println("Creating availability set...");
AvailabilitySet availabilitySet = azure.availabilitySets()
    .define("myAvailabilitySet")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withSku(AvailabilitySetSkuTypes.MANAGED)
    .create();
```
### <a name="create-the-public-ip-address"></a>パブリック IP アドレスの作成

仮想マシンと通信するには、[パブリック IP アドレス](../../virtual-network/public-ip-addresses.md)が必要です。

仮想マシンのパブリック IP アドレスを作成するには、次のコードを main メソッドの try ブロックに追加します。

```java
System.out.println("Creating public IP address...");
PublicIPAddress publicIPAddress = azure.publicIPAddresses()
    .define("myPublicIP")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withDynamicIP()
    .create();
```

### <a name="create-the-virtual-network"></a>仮想ネットワークの作成

仮想マシンは、[仮想ネットワーク](../../virtual-network/virtual-networks-overview.md)のサブネット内に存在する必要があります。

サブネットと仮想ネットワークを作成するには、次のコードを main メソッドの try ブロックに追加します。

```java
System.out.println("Creating virtual network...");
Network network = azure.networks()
    .define("myVN")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withAddressSpace("10.0.0.0/16")
    .withSubnet("mySubnet","10.0.0.0/24")
    .create();
```

### <a name="create-the-network-interface"></a>ネットワーク インターフェイスの作成

仮想マシンが仮想ネットワーク上で通信するには、ネットワーク インターフェイスが必要です。

ネットワーク インターフェイスを作成するには、次のコードを main メソッドの try ブロックに追加します。

```java
System.out.println("Creating network interface...");
NetworkInterface networkInterface = azure.networkInterfaces()
    .define("myNIC")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetwork(network)
    .withSubnet("mySubnet")
    .withPrimaryPrivateIPAddressDynamic()
    .withExistingPrimaryPublicIPAddress(publicIPAddress)
    .create();
```

### <a name="create-the-virtual-machine"></a>仮想マシンの作成

すべての関連リソースを作成したので、仮想マシンを作成できます。

仮想マシンを作成するには、次のコードを main メソッドの try ブロックに追加します。

```java
System.out.println("Creating virtual machine...");
VirtualMachine virtualMachine = azure.virtualMachines()
    .define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .withAdminUsername("azureuser")
    .withAdminPassword("Azure12345678")
    .withComputerName("myVM")
    .withExistingAvailabilitySet(availabilitySet)
    .withSize("Standard_DS1")
    .create();
Scanner input = new Scanner(System.in);
System.out.println("Press enter to get information about the VM...");
input.nextLine();
```

> [!NOTE]
> このチュートリアルでは、Windows Server オペレーティング システムのバージョンを実行する仮想マシンを作成します。 他のイメージの選択の詳細については、 [Windows PowerShell と Azure CLI による Azure 仮想マシン イメージのナビゲーションと選択](../linux/cli-ps-findimage.md)に関する記事をご覧ください。
> 
>

Marketplace イメージではなく、既存のディスクを使用する場合は、このコードを使用します。 

```java
ManagedDisk managedDisk = azure.disks.define("myosdisk")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .withSizeInGB(128)
    .withSku(DiskSkuTypes.PremiumLRS)
    .create();

azure.virtualMachines.define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .withExistingAvailabilitySet(availabilitySet)
    .withSize(VirtualMachineSizeTypes.StandardDS1)
    .create();
```

## <a name="perform-management-tasks"></a>管理タスクを実行する

仮想マシンのライフサイクルで、各種の管理タスクを実行する必要がある場合があります (仮想マシンの起動、停止、削除など)。 また、何度も行う作業や複雑な作業は、コードを作成して自動化したい場合もあるでしょう。

VM で何かを実行する必要がある場合、そのインスタンスを取得する必要があります。 次のコードを main メソッドの try ブロックに追加します。

```java
VirtualMachine vm = azure.virtualMachines().getByResourceGroup("myResourceGroup", "myVM");
```

### <a name="get-information-about-the-vm"></a>VM に関する情報を取得する

仮想マシンに関する情報を取得するには、次のコードを main メソッドの try ブロックに追加します。

```java
System.out.println("hardwareProfile");
System.out.println("    vmSize: " + vm.size());
System.out.println("storageProfile");
System.out.println("  imageReference");
System.out.println("    publisher: " + vm.storageProfile().imageReference().publisher());
System.out.println("    offer: " + vm.storageProfile().imageReference().offer());
System.out.println("    sku: " + vm.storageProfile().imageReference().sku());
System.out.println("    version: " + vm.storageProfile().imageReference().version());
System.out.println("  osDisk");
System.out.println("    osType: " + vm.storageProfile().osDisk().osType());
System.out.println("    name: " + vm.storageProfile().osDisk().name());
System.out.println("    createOption: " + vm.storageProfile().osDisk().createOption());
System.out.println("    caching: " + vm.storageProfile().osDisk().caching());
System.out.println("osProfile");
System.out.println("    computerName: " + vm.osProfile().computerName());
System.out.println("    adminUserName: " + vm.osProfile().adminUsername());
System.out.println("    provisionVMAgent: " + vm.osProfile().windowsConfiguration().provisionVMAgent());
System.out.println("    enableAutomaticUpdates: " + vm.osProfile().windowsConfiguration().enableAutomaticUpdates());
System.out.println("networkProfile");
System.out.println("    networkInterface: " + vm.primaryNetworkInterfaceId());
System.out.println("vmAgent");
System.out.println("  vmAgentVersion: " + vm.instanceView().vmAgent().vmAgentVersion());
System.out.println("    statuses");
for(InstanceViewStatus status : vm.instanceView().vmAgent().statuses()) {
    System.out.println("    code: " + status.code());
    System.out.println("    displayStatus: " + status.displayStatus());
    System.out.println("    message: " + status.message());
    System.out.println("    time: " + status.time());
}
System.out.println("disks");
for(DiskInstanceView disk : vm.instanceView().disks()) {
    System.out.println("  name: " + disk.name());
    System.out.println("  statuses");
    for(InstanceViewStatus status : disk.statuses()) {
        System.out.println("    code: " + status.code());
        System.out.println("    displayStatus: " + status.displayStatus());
        System.out.println("    time: " + status.time());
    }
}
System.out.println("VM general status");
System.out.println("  provisioningStatus: " + vm.provisioningState());
System.out.println("  id: " + vm.id());
System.out.println("  name: " + vm.name());
System.out.println("  type: " + vm.type());
System.out.println("VM instance status");
for(InstanceViewStatus status : vm.instanceView().statuses()) {
    System.out.println("  code: " + status.code());
    System.out.println("  displayStatus: " + status.displayStatus());
}
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="stop-the-vm"></a>VM を停止する

仮想マシンを停止してそのすべての設定を保持する (引き続き課金されます)、または仮想マシンを停止して割り当てを解除します。 仮想マシンの割り当てを解除すると、それに関連付けられているすべてのリソースの割り当ても解除され、仮想マシンに対する課金が終了します。

仮想マシンの割り当てを解除せずに仮想マシンを停止するには、次のコードを main メソッドの try ブロックに追加します。

```java
System.out.println("Stopping vm...");
vm.powerOff();
System.out.println("Press enter to continue...");
input.nextLine();
```

仮想マシンの割り当てを解除する場合は、PowerOff 呼び出しを次のコードに変更します。

```java
vm.deallocate();
```

### <a name="start-the-vm"></a>VM を起動する

仮想マシンを起動するには、次のコードを main メソッドの try ブロックに追加します。

```java
System.out.println("Starting vm...");
vm.start();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="resize-the-vm"></a>VM のサイズを変更する

仮想マシンのサイズを決定するときは、デプロイのさまざまな面を考慮する必要があります。 詳しくは、[VM サイズ](../sizes.md)をご覧ください。  

仮想マシンのサイズを変更するには、次のコードを main メソッドの try ブロックに追加します。

```java
System.out.println("Resizing vm...");
vm.update()
    .withSize(VirtualMachineSizeTypes.STANDARD_DS2)
    .apply();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>VM にデータ ディスクを追加する

仮想マシンにサイズが 2 GB で、LUN が 0 で、キャッシングの種類が ReadWrite のデータ ディスクを追加するには、次のコードを main メソッドの try ブロックに追加します。

```java
System.out.println("Adding data disk...");
vm.update()
    .withNewDataDisk(2, 0, CachingTypes.READ_WRITE)
    .apply();
System.out.println("Press enter to delete resources...");
input.nextLine();
```

## <a name="delete-resources"></a>リソースを削除する

Azure で使用されるリソースに対して課金されるため、不要になったリソースは削除することを常にお勧めします。 仮想マシンとすべての関連リソースを削除する場合、必要な操作はリソース グループの削除だけです。

1. リソース グループを削除するには、次のコードを main メソッドの try ブロックに追加します。
   
    ```java
    System.out.println("Deleting resources...");
    azure.resourceGroups().deleteByName("myResourceGroup");
    ```

2. App.java ファイルを保存します。

## <a name="run-the-application"></a>アプリケーションの実行

このコンソール アプリケーションが実行を開始してから完全に終了するまでには、約 5 分かかります。

1. アプリケーションを実行するには、次の Maven コマンドを使用します。

    ```
    mvn compile exec:java
    ```

2. **Enter** キーを押してリソースの削除を開始する前に、Azure Portal でリソースの作成状況を確認することもできます。 デプロイに関する情報を参照するには、デプロイ状態をクリックします。


## <a name="next-steps"></a>次のステップ
* [Azure の Java ライブラリ](/java/azure/java-sdk-azure-overview)の使用方法について詳しく学習します。