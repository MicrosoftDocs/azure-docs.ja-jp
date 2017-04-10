
* [Azure での仮想マシンの簡易作成](#quick-create-a-vm-in-azure)
* [テンプレートから Azure への仮想マシンのデプロイ](#deploy-a-vm-in-azure-from-a-template)
* [カスタム イメージからの仮想マシンの作成](#create-a-custom-vm-image)
* [仮想ネットワークおよびロード バランサーを使用する仮想マシンのデプロイ](#deploy-a-multi-vm-application-that-uses-a-virtual-network-and-an-external-load-balancer)
* [リソース グループの削除](#remove-a-resource-group)
* [リソース グループ デプロイのログの表示](#show-the-log-for-a-resource-group-deployment)
* [仮想マシンに関する情報の表示](#display-information-about-a-virtual-machine)
* [Linux ベースの仮想マシンへの接続](#log-on-to-a-linux-based-virtual-machine)
* [仮想マシンの停止](#stop-a-virtual-machine)
* [仮想マシンの起動](#start-a-virtual-machine)
* [データ ディスクの接続](#attach-a-data-disk)

## <a name="getting-ready"></a>準備
Azure リソース グループで Azure CLI を使用するには、適切な Azure CLI のバージョンと Azure アカウントを用意する必要があります。 Azure CLI がない場合は、 [インストールします](../articles/cli-install-nodejs.md)。

### <a name="update-your-azure-cli-version-to-090-or-later"></a>Azure CLI のバージョンを 0.9.0 以降に更新する
「 `azure --version` 」と入力し、バージョン 0.9.0 以降が既にインストールされているかどうかを確認します。

```azurecli
azure --version
0.9.0 (node: 0.10.25)
```

バージョンが 0.9.0 以降ではない場合、いずれかのネイティブ インストーラーまたは **npm** コマンド (「`npm update -g azure-cli`」と入力) を使って更新する必要があります。

また、次の [Docker イメージ](https://registry.hub.docker.com/u/microsoft/azure-cli/)を使用して、Azure CLI を Docker コンテナーとして実行することもできます。 Docker ホストから次のコマンドを実行します。

```bash
docker run -it microsoft/azure-cli
```

### <a name="set-your-azure-account-and-subscription"></a>Azure アカウントとサブスクリプションを設定する
Azure サブスクリプションをまだ持っていない場合でも、MSDN サブスクリプションがあれば、 [MSDN サブスクライバー向けの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)をアクティブ化できます。 または、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。

「`azure login`」と入力し、Azure アカウントへの対話型ログイン エクスペリエンスのプロンプトに従って、[Azure アカウントに対話形式でログイン](../articles/xplat-cli-connect.md#scenario-1-azure-login-with-interactive-login)します。 

> [!NOTE]
> 職場または学校の ID を所有していて、2 要素認証が有効になっていないことがわかっている場合は、職場または学校の ID と共に `azure login -u` を使うと、"対話型セッションを使わずに" ログインすることもできます。****** 職場または学校の ID がない場合は、[個人の Microsoft アカウントから職場または学校の ID を作成 して](../articles/virtual-machines/windows/create-aad-work-id.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)同じ方法でログインできます。
>
>

アカウントには、複数のサブスクリプションが含まれる場合があります。 `azure account list`と入力することで、次のようにサブスクリプションの一覧を表示できます。

```azurecli
azure account list
info:    Executing command account list
data:    Name                              Id                                    Tenant Id                            Current
data:    --------------------------------  ------------------------------------  ------------------------------------  -------
data:    Contoso Admin                     xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  true
data:    Fabrikam dev                      xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
data:    Fabrikam test                     xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
data:    Contoso production                xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
```

以下を入力すると、現在の Azure サブスクリプションを設定できます。 管理するリソースを含むサブスクリプション名または ID を使用します。

```azurecli
azure account set <subscription name or ID> true
```

### <a name="switch-to-the-azure-cli-resource-group-mode"></a>Azure CLI リソース グループ モードに切り替える
既定では、Azure CLI はサービス管理モード (**asm** モード) で起動します。 以下を入力してリソース グループ モードに切り替えます。

```azurecli
azure config mode arm
```

## <a name="understanding-azure-resource-templates-and-resource-groups"></a>Azure リソース テンプレートおよびリソース グループについて
大部分のアプリケーションは、異なる種類のリソースの組み合わせ (1 つ以上の VM やストレージ アカウント、SQL データベース、仮想ネットワーク、コンテンツ配信ネットワークなど) から構築されます。 既定の Azure サービス管理 API と Azure クラシック ポータルでは、サービス単位のアプローチを使用してこれらの項目を表していました。 この方法では、個々のサービスを 1 つの論理的なデプロイ単位としてではなく、個別にデプロイ、管理 (またはこのことを実行するその他のツールを検索) する必要があります。

ただし、*Azure リソース マネージャー テンプレート*では、これらの異なるリソースを 1 つの論理的なデプロイ単位として、宣言型の方法でデプロイし、管理することが可能になります。 何をデプロイするのかを Azure に 1 コマンドずつ命令するのではなく、JSON ファイル内にデプロイ全体、つまりすべてのリソースと、関連する構成およびデプロイ パラメーターを記述し、Azure にそれらのリソースを 1 つのグループとしてデプロイするよう指示します。

その後は、Azure CLI リソース管理コマンドを使用して以下を実行することで、そのグループのリソースのライフ サイクル全体を管理できます。

* グループ内のすべてのリソースを一度に停止、開始、または削除する。
* ロールベースの Access Control (RBAC) ルールを適用し、リソースへのセキュリティ アクセス許可をロック ダウンする。
* 操作を監査する。
* 追跡機能を向上させるために追加のメタデータでリソースのタグ付けを行う。

Azure リソース グループとその機能の詳細については、「[Azure Resource Manager の概要](../articles/azure-resource-manager/resource-group-overview.md)」を参照してください。 テンプレートの作成に興味がある場合は、「 [Azure リソース マネージャーのテンプレートの作成](../articles/resource-group-authoring-templates.md)」を参照してください。

## <a id="quick-create-a-vm-in-azure"></a>タスク: Azure での VM の簡易作成
どのようなイメージが必要か理解していて、今すぐそのイメージの VM が必要で、しかもインフラストラクチャにはそれほどこだわらないという場合があります。たとえばクリーンな VM で何かをテストする必要があるような場合です。 そのようなときは、`azure vm quick-create` コマンドを使用し、VM とそのインフラストラクチャの作成に必要な引数を渡します。

まずは、リソース グループを作成します。

```azurecli
azure group create coreos-quick westus
info:    Executing command group create
+ Getting resource group coreos-quick
+ Creating resource group coreos-quick
info:    Created resource group coreos-quick
data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick
data:    Name:                coreos-quick
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags:
data:
info:    group create command OK
```

次に、イメージが必要になります。 Azure CLI でイメージを検索するには、 [PowerShell と Azure CLI による Azure 仮想マシン イメージのナビゲーションと選択](../articles/virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページを参照してください。 ただし、この記事については、一般的なイメージの簡単な一覧を用意しています。 この簡易作成には、CoreOS の安定版イメージを使用します。

> [!NOTE]
> ComputeImageVersion では、テンプレートの言語と Azure CLI の両方のパラメーターとして "latest" を指定することもできます。 こうすることで、スクリプトやテンプレートを変更しなくても、常にパッチが適用された最新バージョンのイメージを使用できます。 一般的なイメージを以下に示します。
>
>

| 発行元 | プラン | SKU | バージョン |
|:--- |:--- |:--- |:--- |
| OpenLogic |CentOS |7 |7.0.201503 |
| OpenLogic |CentOS |7.1 |7.1.201504 |
| CoreOS |CoreOS |Beta |647.0.0 |
| CoreOS |CoreOS |Stable |633.1.0 |
| MicrosoftDynamicsNAV |DynamicsNAV |2015 |8.0.40459 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2013 |1.0.0 |
| msopentech |Oracle-Database-12c-Weblogic-Server-12c |Standard |1.0.0 |
| msopentech |Oracle-Database-12c-Weblogic-Server-12c |Enterprise |1.0.0 |
| MicrosoftSQLServer |SQL2014-WS2012R2 |Enterprise-Optimized-for-DW |12.0.2430 |
| MicrosoftSQLServer |SQL2014-WS2012R2 |Enterprise-Optimized-for-OLTP |12.0.2430 |
| Canonical |UbuntuServer |12.04.5-LTS |12.04.201504230 |
| Canonical |UbuntuServer |14.04.2-LTS |14.04.201503090 |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |3.0.201503 |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |4.0.201503 |
| MicrosoftWindowsServer |WindowsServer |Windows-Server-Technical-Preview |5.0.201504 |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |1.0.141204 |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |4.3.4665 |

VM を作成するために、`azure vm quick-create` コマンドを入力して、メッセージが表示されるのを待ちます。 これは、次のようになります。

```azurecli
azure vm quick-create
info:    Executing command vm quick-create
Resource group name: coreos-quick
Virtual machine name: coreos
Location name: westus
Operating system Type [Windows, Linux]: linux
ImageURN (format: "publisherName:offer:skus:version"): coreos:coreos:stable:latest
User name: ops
Password: *********
Confirm password: *********
+ Looking up the VM "coreos"
info:    Using the VM Size "Standard_A1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Retrieving storage accounts
info:    Could not find any storage accounts in the region "westus", trying to create new one
+ Creating storage account "cli9fd3fce49e9a9b3d14302" in "westus"
+ Looking up the storage account cli9fd3fce49e9a9b3d14302
+ Looking up the NIC "coreo-westu-1430261891570-nic"
info:    An nic with given name "coreo-westu-1430261891570-nic" not found, creating a new one
+ Looking up the virtual network "coreo-westu-1430261891570-vnet"
info:    Preparing to create new virtual network and subnet
/ Creating a new virtual network "coreo-westu-1430261891570-vnet" [address prefix: "10.0.0.0/16"] with subnet "coreo-westu-1430261891570-sne+" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "coreo-westu-1430261891570-vnet"
+ Looking up the subnet "coreo-westu-1430261891570-snet" under the virtual network "coreo-westu-1430261891570-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "coreo-westu-1430261891570-pip"
info:    PublicIP with given name "coreo-westu-1430261891570-pip" not found, creating a new one
+ Creating public ip "coreo-westu-1430261891570-pip"
+ Looking up the public ip "coreo-westu-1430261891570-pip"
+ Creating NIC "coreo-westu-1430261891570-nic"
+ Looking up the NIC "coreo-westu-1430261891570-nic"
+ Creating VM "coreos"
+ Looking up the VM "coreos"
+ Looking up the NIC "coreo-westu-1430261891570-nic"
+ Looking up the public ip "coreo-westu-1430261891570-pip"
data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick/providers/Microsoft.Compute/virtualMachines/coreos
data:    ProvisioningState               :Succeeded
data:    Name                            :coreos
data:    Location                        :westus
data:    FQDN                            :coreo-westu-1430261891570-pip.westus.cloudapp.azure.com
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_A1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :coreos
data:        Offer                       :coreos
data:        Sku                         :stable
data:        Version                     :633.1.0
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :cli9fd3fce49e9a9b3d-os-1430261892283
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli9fd3fce49e9a9b3d14302.blob.core.windows.net/vhds/cli9fd3fce49e9a9b3d-os-1430261892283.vhd
data:
data:    OS Profile:
data:      Computer Name                 :coreos
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :false
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick/providers/Microsoft.Network/networkInterfaces/coreo-westu-1430261891570-nic
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-30-72-E3
data:          Provisioning State        :Succeeded
data:          Name                      :coreo-westu-1430261891570-nic
data:          Location                  :westus
data:            Private IP alloc-method :Dynamic
data:            Private IP address      :10.0.1.4
data:            Public IP address       :104.40.24.124
data:            FQDN                    :coreo-westu-1430261891570-pip.westus.cloudapp.azure.com
info:    vm quick-create command OK
```

これで新しい VM が作成されました。

## <a id="deploy-a-vm-in-azure-from-a-template"></a>タスク: テンプレートから Azure への VM のデプロイ
Azure CLI でテンプレートを使用して新しい Azure VM をデプロイするには、以下のセクションの手順に従います。 このテンプレートは、1 つのサブネットを持つ新しい仮想ネットワークに単一の仮想マシンを作成しますが、 `azure vm quick-create`とは異なり、何が必要かを正確に記述し、エラーなしでそれを繰り返すことができます。 このテンプレートによって作成されるものを次に示します。

![](./media/virtual-machines-common-cli-deploy-templates/new-vm.png)

### <a name="step-1-examine-the-json-file-for-the-template-parameters"></a>手順 1. テンプレート パラメーターの JSON ファイルを確認する
テンプレートの JSON ファイルの内容を次に示します。 (テンプレートは [GitHub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-linux/azuredeploy.json)にもあります)。

テンプレートは柔軟性が高いことから、デザイナーはテンプレートで多くのパラメーターを提供することがあります。逆に、比較的固定的なテンプレートを作成し、ごく少数のパラメーターだけを提供していることもあります。 テンプレートをパラメーターとして渡すために必要な情報を収集するには、テンプレート ファイル (このトピックでは、以下にインラインのテンプレートが含まれています) を開き、**parameters** の値を確認する必要があります。

この場合、下記のテンプレートから次の情報を求められます。

* 一意のストレージ アカウント名。
* VM の管理者ユーザー名。
* パスワード。
* 外部で使用されるドメイン名。
* Ubuntu Server のバージョン番号。ただし、リストのうち 1 つだけを許可します。

[ユーザー名とパスワードの要件](../articles/virtual-machines/linux/faq.md#what-are-the-username-requirements-when-creating-a-vm)に関するページで詳細を確認してください。

これらの値が決まると、グループを作成し、Azure サブスクリプションにこのテンプレートをデプロイする準備が整います。

```json
{
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "newStorageAccountName": {
    "type": "string",
    "metadata": {
        "description": "Unique DNS name for the storage account where the virtual machine's disks will be placed."
    }
    },
    "adminUsername": {
    "type": "string",
    "metadata": {
        "description": "User name for the virtual machine."
    }
    },
    "adminPassword": {
    "type": "securestring",
    "metadata": {
        "description": "Password for the virtual machine."
    }
    },
    "dnsNameForPublicIP": {
    "type": "string",
    "metadata": {
        "description": "Unique DNS name for the public IP used to access the virtual machine."
    }
    },
    "ubuntuOSVersion": {
    "type": "string",
    "defaultValue": "14.04.2-LTS",
    "allowedValues": [
        "12.04.5-LTS",
        "14.04.2-LTS",
        "15.04"
    ],
    "metadata": {
        "description": "The Ubuntu version for the VM. This will pick a fully patched image of this given Ubuntu version. Allowed values: 12.04.5-LTS, 14.04.2-LTS, 15.04."
    }
    }
},
"variables": {
    "location": "West US",
    "imagePublisher": "Canonical",
    "imageOffer": "UbuntuServer",
    "OSDiskName": "osdiskforlinuxsimple",
    "nicName": "myVMNic",
    "addressPrefix": "10.0.0.0/16",
    "subnetName": "Subnet",
    "subnetPrefix": "10.0.0.0/24",
    "storageAccountType": "Standard_LRS",
    "publicIPAddressName": "myPublicIP",
    "publicIPAddressType": "Dynamic",
    "vmStorageAccountContainerName": "vhds",
    "vmName": "MyUbuntuVM",
    "vmSize": "Standard_D1",
    "virtualNetworkName": "MyVNET",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
},
"resources": [
    {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('newStorageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[variables('location')]",
    "properties": {
        "accountType": "[variables('storageAccountType')]"
    }
    },
    {
    "apiVersion": "2015-05-01-preview",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('publicIPAddressName')]",
    "location": "[variables('location')]",
    "properties": {
        "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
        "dnsSettings": {
        "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
        }
    }
    },
    {
    "apiVersion": "2015-05-01-preview",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
    "location": "[variables('location')]",
    "properties": {
        "addressSpace": {
        "addressPrefixes": [
            "[variables('addressPrefix')]"
        ]
        },
        "subnets": [
        {
            "name": "[variables('subnetName')]",
            "properties": {
            "addressPrefix": "[variables('subnetPrefix')]"
            }
        }
        ]
    }
    },
    {
    "apiVersion": "2015-05-01-preview",
    "type": "Microsoft.Network/networkInterfaces",
    "name": "[variables('nicName')]",
    "location": "[variables('location')]",
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
    ],
    "properties": {
        "ipConfigurations": [
        {
            "name": "ipconfig1",
            "properties": {
            "privateIPAllocationMethod": "Dynamic",
            "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
            },
            "subnet": {
                "id": "[variables('subnetRef')]"
            }
            }
        }
        ]
    }
    },
    {
    "apiVersion": "2015-05-01-preview",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[variables('location')]",
    "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
        "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
        },
        "osProfile": {
        "computername": "[variables('vmName')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
        "imageReference": {
            "publisher": "[variables('imagePublisher')]",
            "offer": "[variables('imageOffer')]",
            "sku": "[parameters('ubuntuOSVersion')]",
            "version": "latest"
        },
        "osDisk": {
            "name": "osdisk",
            "vhd": {
            "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"
        }
        },
        "networkProfile": {
        "networkInterfaces": [
            {
            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
        ]
        }
    }
    }
]
}
```

### <a name="step-2-create-the-virtual-machine-by-using-the-template"></a>手順 2. テンプレートを使用して仮想マシンを作成する
パラメーター値を用意したら、テンプレートのデプロイ用のリソース グループを作成し、テンプレートをデプロイする必要があります。

リソース グループを作成するには、使用するグループ名とデプロイ先のデータセンターの場所を指定して、「 `azure group create <group name> <location>` 」を入力します。 この作成はすばやく実行されます。

```azurecli
azure group create myResourceGroup westus
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags:
data:
info:    group create command OK
```

デプロイを作成するには、 `azure group deployment create` を呼び出し、次の情報を渡します。

* テンプレート ファイル (上記の JSON テンプレートをローカル ファイルに保存した場合)
* テンプレートの URI (GitHub や他の Web アドレスにあるファイルをポイントする場合)
* デプロイ先のリソース グループ
* デプロイ名 (省略可能)

JSON ファイルの "parameters" セクションのパラメーター値を指定するよう求められます。 すべてのパラメーター値を指定すると、デプロイが開始されます。

たとえば次のようになります。

```azurecli
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-linux/azuredeploy.json myResourceGroup firstDeployment
info:    Executing command group deployment create
info:    Supply values for the following parameters
newStorageAccountName: storageaccount
adminUsername: ops
adminPassword: password
dnsNameForPublicIP: newdomainname
```

次の種類の情報が表示されます。

```azurecli
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "firstDeployment"
+ Registering providers
info:    Registering provider microsoft.storage
info:    Registering provider microsoft.network
info:    Registering provider microsoft.compute
+ Waiting for deployment to complete
data:    DeploymentName     : firstDeployment
data:    ResourceGroupName  : myResourceGroup
data:    ProvisioningState  : Succeeded
data:    Timestamp          : 2015-04-28T07:53:55.1828878Z
data:    Mode               : Incremental
data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json
data:    ContentVersion     : 1.0.0.0
data:    Name                   Type          Value
data:    ---------------------  ------------  -------------
data:    newStorageAccountName  String        storageaccount
data:    adminUsername          String        ops
data:    adminPassword          SecureString  undefined
data:    dnsNameForPublicIP     String        newdomainname
data:    ubuntuOSVersion        String        14.10
info:    group deployment create command OK
```


## <a id="create-a-custom-vm-image"></a>タスク: カスタム VM イメージの作成
テンプレートの基本的な使用方法は前述したので、同様の手順に従うことで、Azure CLI でテンプレートを使用して、Azure にある特定の .vhd ファイルからカスタム VM を作成できます。 このテンプレートは、指定した仮想ハード ディスク (VHD) から単一の仮想マシンを作成するという点が異なります。

### <a name="step-1-examine-the-json-file-for-the-template"></a>手順 1. テンプレートの JSON ファイルを確認する
以下は、このセクションで例として使用するテンプレートの JSON ファイルの内容です。 (テンプレートは [GitHub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json)にもあります)。

ここでも、既定値がないパラメーター用に入力する値を見つける必要があります。 `azure group deployment create` コマンドを実行すると、Azure CLI からその値を入力するよう求められます。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters" : {
        "userImageStorageAccountName": {
            "type": "string",
            "defaultValue" : "userImageStorageAccountName"
        },
        "userImageStorageContainerName" : {
            "type" : "string",
            "defaultValue" : "userImageStorageContainerName"
        },
        "userImageVhdName" : {
            "type" : "string",
            "defaultValue" : "userImageVhdName"
        },
        "dnsNameForPublicIP" : {
            "type" : "string",
            "defaultValue": "uniqueDnsNameForPublicIP"
        },
        "adminUserName": {
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring"
        },
        "osType" : {
            "type" : "string",
            "allowedValues" : [
                "windows",
                "linux"
            ]
        },
        "subscriptionId":{
            "type" : "string"
        },
        "location": {
            "type": "String",
            "defaultValue" : "West US"
        },
        "vmSize": {
            "type": "string",
            "defaultValue": "Standard_A2"
        },
        "publicIPAddressName": {
            "type": "string",
            "defaultValue" : "myPublicIP"
        },
        "vmName": {
            "type": "string",
            "defaultValue" : "myVM"
        },
        "virtualNetworkName":{
            "type" : "string",
            "defaultValue" : "myVNET"
        },
        "nicName":{
            "type" : "string",
            "defaultValue":"myNIC"
        }
    },
    "variables": {
        "addressPrefix":"10.0.0.0/16",
        "subnet1Name": "Subnet-1",
        "subnet2Name": "Subnet-2",
        "subnet1Prefix" : "10.0.0.0/24",
        "subnet2Prefix" : "10.0.1.0/24",
        "publicIPAddressType" : "Dynamic",
        "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
        "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
        "userImageName" : "[concat('http://',parameters('userImageStorageAccountName'),'.blob.core.windows.net/',parameters('userImageStorageContainerName'),'/',parameters('userImageVhdName'))]",
        "osDiskVhdName" : "[concat('http://',parameters('userImageStorageAccountName'),'.blob.core.windows.net/',parameters('userImageStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
    },
    "resources": [
    {
        "apiVersion": "2014-12-01-preview",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[parameters('publicIPAddressName')]",
        "location": "[parameters('location')]",
        "properties": {
            "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
            }
        }
    },
    {
        "apiVersion": "2014-12-01-preview",
        "type": "Microsoft.Network/virtualNetworks",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "properties": {
        "addressSpace": {
            "addressPrefixes": [
            "[variables('addressPrefix')]"
            ]
        },
        "subnets": [
            {
            "name": "[variables('subnet1Name')]",
            "properties" : {
                "addressPrefix": "[variables('subnet1Prefix')]"
            }
            },
            {
            "name": "[variables('subnet2Name')]",
            "properties" : {
                "addressPrefix": "[variables('subnet2Prefix')]"
            }
            }
        ]
        }
    },
    {
        "apiVersion": "2014-12-01-preview",
        "type": "Microsoft.Network/networkInterfaces",
        "name": "[parameters('nicName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
            "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
        ],
        "properties": {
            "ipConfigurations": [
            {
                "name": "ipconfig1",
                "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                    },
                    "subnet": {
                        "id": "[variables('subnet1Ref')]"
                    }
                }
            }
            ]
        }
    },
    {
        "apiVersion": "2014-12-01-preview",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[parameters('vmName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
        ],
        "properties": {
            "hardwareProfile": {
                "vmSize": "[parameters('vmSize')]"
            },
            "osProfile": {
                "computername": "[parameters('vmName')]",
                "adminUsername": "[parameters('adminUsername')]",
                "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
                "osDisk" : {
                    "name" : "[concat(parameters('vmName'),'-osDisk')]",
                    "osType" : "[parameters('osType')]",
                    "caching" : "ReadWrite",
                    "image" : {
                        "uri" : "[variables('userImageName')]"
                    },
                    "vhd" : {
                        "uri" : "[variables('osDiskVhdName')]"
                    }
                }
            },
            "networkProfile": {
                "networkInterfaces" : [
                {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                }
                ]
            }
        }
    }
    ]
}
```

### <a name="step-2-obtain-the-vhd"></a>手順 2. VHD を取得する
当然ながら、これには .vhd が必要になります。 既に Azure にあるものを使用するか、アップロードすることができます。

Windows ベースの仮想マシンについては、「[Windows Server VHD の作成と Azure へのアップロード](../articles/virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」を参照してください。

Linux ベースの仮想マシンについては、「[Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード](../articles/virtual-machines/linux/classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」を参照してください。

### <a name="step-3-create-the-virtual-machine-by-using-the-template"></a>手順 3. テンプレートを使用して仮想マシンを作成する
これで、.vhd に基づく新しい仮想マシンを作成する準備が整いました。 デプロイ先のグループを作成するには、次のように `azure group create <location>` を使用します。

```azurecli
azure group create myResourceGroupUser eastus
info:    Executing command group create
+ Getting resource group myResourceGroupUser
+ Creating resource group myResourceGroupUser
info:    Created resource group myResourceGroupUser
data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupUser
data:    Name:                myResourceGroupUser
data:    Location:            eastus
data:    Provisioning State:  Succeeded
data:    Tags:
data:
info:    group create command OK
```

次に、テンプレートを直接呼び出す `--template-uri` オプションを使用してデプロイを作成します (または `--template-file` オプションを使用してローカルに保存したファイルを使用することもできます)。 テンプレートは既定値を指定されているため、求められる入力値は少数です。 さまざまな場所にテンプレートをデプロイする場合、既定値 (特に、作成する DNS 名) との名前付けの競合が発生する場合があります。

```azurecli
azure group deployment create \
> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json \
> myResourceGroup \
> customVhdDeployment
info:    Executing command group deployment create
info:    Supply values for the following parameters
adminUserName: ops
adminPassword: password
osType: linux
subscriptionId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

出力は次のようになります。

```azurecli
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "customVhdDeployment"
+ Registering providers
info:    Registering provider microsoft.network
info:    Registering provider microsoft.compute
+ Waiting for deployment to complete
error:   Deployment provisioning state was not successful
data:    DeploymentName     : customVhdDeployment
data:    ResourceGroupName  : myResourceGroupUser
data:    ProvisioningState  : Succeeded
data:    Timestamp          : 2015-04-28T14:55:48.0963829Z
data:    Mode               : Incremental
data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json
data:    ContentVersion     : 1.0.0.0
data:    Name                           Type          Value
data:    -----------------------------  ------------  ------------------------------------
data:    userImageStorageAccountName    String        userImageStorageAccountName
data:    userImageStorageContainerName  String        userImageStorageContainerName
data:    userImageVhdName               String        userImageVhdName
data:    dnsNameForPublicIP             String        uniqueDnsNameForPublicIP
data:    adminUserName                  String        ops
data:    adminPassword                  SecureString  undefined
data:    osType                         String        linux
data:    subscriptionId                 String        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
data:    location                       String        West US
data:    vmSize                         String        Standard_A2
data:    publicIPAddressName            String        myPublicIP
data:    vmName                         String        myVM
data:    virtualNetworkName             String        myVNET
data:    nicName                        String        myNIC
info:    group deployment create command OK
```

## <a id="deploy-a-multi-vm-application-that-uses-a-virtual-network-and-an-external-load-balancer"></a>タスク: 仮想ネットワークと外部ロード バランサーを使用する複数 VM アプリケーションのデプロイ
このテンプレートでは、ロード バランサーの下に 2 つの仮想マシンを作成し、ポート 80 の負荷分散ルールを構成することができます。 このテンプレートは、ストレージ アカウント、仮想ネットワーク、パブリック IP アドレス、可用性セット、ネットワーク インターフェイスもデプロイします。

![](./media/virtual-machines-common-cli-deploy-templates/multivmextlb.png)

Azure PowerShell コマンドで GitHub テンプレート リポジトリのリソース マネージャー テンプレートを使用して、仮想ネットワークとロード バランサーを使用する複数 VM アプリケーションをデプロイするには、次の手順に従います。

### <a name="step-1-examine-the-json-file-for-the-template"></a>手順 1. テンプレートの JSON ファイルを確認する
テンプレートの JSON ファイルの内容を次に示します。 最新のバージョンについては、[GitHub リポジトリのテンプレート](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json)をご覧ください。 このトピックでは、テンプレートの呼び出しに `--template-uri` スイッチを使用しますが、ローカル バージョンを渡すために `--template-file` スイッチを使用することもできます。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location of resources"
            }
        },
        "storageAccountName": {
            "type": "string",
            "metadata": {
                "description": "Name of storage account"
            }
        },
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "Admin user name"
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Admin password"
            }
        },
        "dnsNameforLBIP": {
            "type": "string",
            "metadata": {
                "description": "DNS for load balancer IP"
            }
        },
        "backendPort": {
            "type": "int",
            "defaultValue": 3389,
            "metadata": {
                "description": "Back-end port"
            }
        },
        "vmNamePrefix": {
            "type": "string",
            "defaultValue": "myVM",
            "metadata": {
                "description": "Prefix to use for VM names"
            }
        },
        "vmSourceImageName": {
            "type": "string",
            "defaultValue": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201412.01-en.us-127GB.vhd"
        },
        "lbName": {
            "type": "string",
            "defaultValue": "myLB",
            "metadata": {
                "description": "Load balancer name"
            }
        },
        "nicNamePrefix": {
            "type": "string",
            "defaultValue": "nic",
            "metadata": {
                "description": "Network interface name prefix"
            }
        },
        "publicIPAddressName": {
            "type": "string",
            "defaultValue": "myPublicIP",
            "metadata": {
                "description": "Public IP name"
            }
        },
        "vnetName": {
            "type": "string",
            "defaultValue": "myVNET",
            "metadata": {
                "description": "Virtual network name"
            }
        },
        "vmSize": {
            "type": "string",
            "defaultValue": "Standard_A1",
            "metadata": {
                "description": "Size of the VM"
            }
        }
    },
    "variables": {
        "storageAccountType": "Standard_LRS",
        "vmStorageAccountContainerName": "vhds",
        "availabilitySetName": "myAvSet",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet-1",
        "subnetPrefix": "10.0.0.0/24",
        "publicIPAddressType": "Dynamic",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
        "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
        "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
        "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
        "numberOfInstances": 2,
        "nicId1": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'), 0))]",
        "nicId2": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'), 1))]",
        "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LBFE')]",
        "backEndIPConfigID1": "[concat(variables('nicId1'),'/ipConfigurations/ipconfig1')]",
        "backEndIPConfigID2": "[concat(variables('nicId2'),'/ipConfigurations/ipconfig1')]",
        "sourceImageName": "[concat('/', subscription().subscriptionId,'/services/images/',parameters('vmSourceImageName'))]",
        "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/LBBE')]",
        "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccountName')]",
            "apiVersion": "2015-05-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "[variables('availabilitySetName')]",
            "apiVersion": "2015-05-01-preview",
            "location": "[parameters('location')]",
            "properties": { }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameforLBIP')]"
                }
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnetName')]",
                        "properties": {
                            "addressPrefix": "[variables('subnetPrefix')]"
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
            "location": "[parameters('location')]",
            "copy": {
                "name": "nicLoop",
                "count": "[variables('numberOfInstances')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            }
                        },
                        "loadBalancerBackendAddressPools": [
                            {
                                "id": "[concat('Microsoft.Network/loadBalancers/',parameters('lbName'),'/backendAddressPools/LBBE')]"
                            }
                        ],
                        "loadBalancerInboundNatRules": [
                            {
                                "id": "[concat('Microsoft.Network/loadBalancers/',parameters('lbName'),'/inboundNatRule/RDP-VM', copyindex())]"
                            }
                        ]


                    }
                ]

            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "name": "[parameters('lbName')]",
            "type": "Microsoft.Network/loadBalancers",
            "location": "[parameters('location')]",
            "dependsOn": [
                "nicLoop",
                "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
            ],
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LBFE",
                        "properties": {
                            "publicIPAddress": {
                                "id": "[variables('publicIPAddressID')]"
                            }
                        }
                    }
                ],
                "backendAddressPools": [
                    {
                        "name": "LBBE"

                    }
                ],
                "inboundNatRules": [
                    {
                        "name": "RDP-VM1",
                        "properties": {
                            "frontendIPConfiguration":
                                {
                                    "id": "[variables('frontEndIPConfigID')]"
                                },
                            "protocol": "tcp",
                            "frontendPort": 50001,
                            "backendPort": 3389,
                            "enableFloatingIP": false
                        }
                    },
                    {
                        "name": "RDP-VM2",
                        "properties": {
                            "frontendIPConfiguration":
                                {
                                    "id": "[variables('frontEndIPConfigID')]"
                                },
                            "protocol": "tcp",
                            "frontendPort": 50002,
                            "backendPort": 3389,
                            "enableFloatingIP": false
                        }
                    }
                ],
                "loadBalancingRules": [
                    {
                        "name": "LBRule",
                        "properties": {
                            "frontendIPConfiguration": {
                                "id": "[variables('frontEndIPConfigID')]"
                            },
                            "backendAddressPool": {
                                "id": "[variables('lbPoolID')]"
                            },
                            "protocol": "tcp",
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 5,
                            "probe": {
                                "id": "[variables('lbProbeID')]"
                            }
                        }
                    }
                ],
                "probes": [
                    {
                        "name": "tcpProbe",
                        "properties": {
                            "protocol": "tcp",
                            "port": 80,
                            "intervalInSeconds": "5",
                            "numberOfProbes": "2"
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
            "copy": {
                "name": "virtualMachineLoop",
                "count": "[variables('numberOfInstances')]"
            },
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
                "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
            ],
            "properties": {
                "availabilitySet": {
                    "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
                },
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computername": "[concat(parameters('vmNamePrefix'), copyIndex())]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "sourceImage": {
                        "id": "[variables('sourceImageName')]"
                    },
                    "destinationVhdsContainer": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/')]"
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="step-2-create-the-deployment-by-using-the-template"></a>手順 2. テンプレートを使用してデプロイを作成する
`azure group create <location>` を使用して、テンプレートのリソース グループを作成します。 `azure group deployment create` を使用してそのリソース グループへのデプロイを作成し、リソース グループを渡します。デプロイ名を渡して、既定値がないテンプレートのパラメーターに関するプロンプトに答えます。

```azurecli
azure group create lbgroup westus
info:    Executing command group create
+ Getting resource group lbgroup
+ Creating resource group lbgroup
info:    Created resource group lbgroup
data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/lbgroup
data:    Name:                lbgroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags:
data:
info:    group create command OK
```

次に、`azure group deployment create` コマンドと `--template-uri` オプションを使用してテンプレートをデプロイします。 以下に示すように入力を求められた場合に備えてパラメーター値を用意しておいてください。

```azurecli
azure group deployment create \
> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json \
> lbgroup \
> newdeployment
info:    Executing command group deployment create
info:    Supply values for the following parameters
location: westus
newStorageAccountName: storagename
adminUsername: ops
adminPassword: password
dnsNameforLBIP: lbdomainname
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "newdeployment"
+ Registering providers
info:    Registering provider microsoft.storage
info:    Registering provider microsoft.compute
info:    Registering provider microsoft.network
+ Waiting for deployment to complete
data:    DeploymentName     : newdeployment
data:    ResourceGroupName  : lbgroup
data:    ProvisioningState  : Succeeded
data:    Timestamp          : 2015-04-28T20:58:40.1678876Z
data:    Mode               : Incremental
data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json
data:    ContentVersion     : 1.0.0.0
data:    Name                   Type          Value
data:    ---------------------  ------------  ----------------------
data:    location               String        westus
data:    newStorageAccountName  String        storagename
data:    adminUsername          String        ops
data:    adminPassword          SecureString  undefined
data:    dnsNameforLBIP         String        lbdomainname
data:    backendPort            Int           3389
data:    vmNamePrefix           String        myVM
data:    imagePublisher         String        MicrosoftWindowsServer
data:    imageOffer             String        WindowsServer
data:    imageSKU               String        2012-R2-Datacenter
data:    lbName                 String        myLB
data:    nicNamePrefix          String        nic
data:    publicIPAddressName    String        myPublicIP
data:    vnetName               String        myVNET
data:    vmSize                 String        Standard_A1
info:    group deployment create command OK
```

このテンプレートでは Windows Server イメージをデプロイしますが、Linux イメージに簡単に置き換えることができます。 複数の Swarm マネージャーを備えた Docker クラスターを作成する必要がある場合には、 [こちらから作成できます](https://azure.microsoft.com/documentation/templates/docker-swarm-cluster/)。

## <a id="remove-a-resource-group"></a>タスク: リソース グループの削除
リソース グループへの再デプロイは可能ですが、実行した場合は `azure group delete <group name>`を使用してリソース グループを削除できることに注意してください。

```azurecli
azure group delete myResourceGroup
info:    Executing command group delete
Delete resource group myResourceGroup? [y/n] y
+ Deleting resource group myResourceGroup
info:    group delete command OK
```

## <a id="show-the-log-for-a-resource-group-deployment"></a>タスク: リソース グループ デプロイのログの表示
これは、テンプレートの作成時や使用時によく行われます。 グループのデプロイ ログを表示するための呼び出しは `azure group log show <groupname>` ですが、これにより、何かが発生した場合、または発生しなかった場合、その理由を把握するのに役立つ大量の情報が表示されます  (デプロイのトラブルシューティングの詳細、および問題に関するその他の情報については、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](../articles/azure-resource-manager/resource-manager-common-deployment-errors.md)」を参照してください)。

特定のエラーを対象にするには、たとえば **jq** のようなツールを使用すると、個々のエラーのうちどれを修正する必要があるかなど、もう少し的確な照会ができます。 次の例では、**jq** を使用して **lbgroup** のデプロイ ログを解析し、エラーを探しています。

```azurecli
azure group log show lbgroup -l --json | jq '.[] | select(.status.value == "Failed") | .properties'
```
発生している問題をすばやく見つけて、修正し、やり直すことができます。 次のケースでは、テンプレートによって 2 つの VM が同時に作成されたため、.vhd にロックがかけられています。 (テンプレートの修正後、デプロイは速やかに正常終了しました)。

```json
{
    "statusCode": "Conflict",
    "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"AcquireDiskLeaseFailed\",\"message\":\"Failed to acquire lease while creating disk 'osdisk' using blob with URI http://storage.blob.core.windows.net/vhds/osdisk.vhd.\"}]}}"
}
```

## <a id="display-information-about-a-virtual-machine"></a>タスク: 仮想マシンに関する情報の表示
`azure vm show <groupname> <vmname>` コマンドを使用して、リソース グループ内の特定の VM に関する情報を表示できます。 複数の VM がある場合、まずは `azure vm list <groupname>`を使用してグループ内の VM を一覧表示する必要があります。

```azurecli
azure vm list zoo
info:    Executing command vm list
+ Getting virtual machines
data:    Name   ProvisioningState  Location  Size
data:    -----  -----------------  --------  -----------
data:    myVM0  Succeeded          westus    Standard_A1
data:    myVM1  Failed             westus    Standard_A1
```

その後、次のように myVM1 を検索します。

```azurecli
azure vm show zoo myVM1
info:    Executing command vm show
+ Looking up the VM "myVM1"
+ Looking up the NIC "nic1"
data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Failed
data:    Name                            :myVM1
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_A1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :MicrosoftWindowsServer
data:        Offer                       :WindowsServer
data:        Sku                         :2012-R2-Datacenter
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Windows
data:        Name                        :osdisk
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :http://zoostorageralph.blob.core.windows.net/vhds/osdisk.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Windows Configuration:
data:        Provision VM Agent          :true
data:        Enable automatic updates    :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Network/networkInterfaces/nic1
data:          Primary                   :false
data:          Provisioning State        :Succeeded
data:          Name                      :nic1
data:          Location                  :westus
data:            Private IP alloc-method :Dynamic
data:            Private IP address      :10.0.0.5
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Compute/availabilitySets/MYAVSET
info:    vm show command OK
```

> [!NOTE]
> プログラムによってコンソール コマンドの出力を格納および操作する場合は、**[jq](https://github.com/stedolan/jq)**、**[jsawk](https://github.com/micha/jsawk)** などの JSON 解析ツール、またはそのタスクに適した言語ライブラリを使用することをお勧めします。
>
>

## <a id="log-on-to-a-linux-based-virtual-machine"></a>タスク: Linux ベースの仮想マシンへのログオン
通常、Linux マシンは SSH によって接続されます。 詳細については、[Azure 上の Linux における SSH の使用方法](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページをご覧ください。

## <a id="stop-a-virtual-machine"></a>タスク: VM の停止
次のコマンドを実行します。

```azurecli
azure vm stop <group name> <virtual machine name>
```

> [!IMPORTANT]
> このパラメーターは、VM が VNET 内の最後の VM である場合に、その VNET の仮想 IP (VIP) を保持するために使用します。 <br><br> `StayProvisioned` パラメーターを使用する場合、その VM に対して引き続き課金されます。
>
>

## <a id="start-a-virtual-machine"></a>タスク: VM の起動
次のコマンドを実行します。

```azurecli
azure vm start <group name> <virtual machine name>
```

## <a id="attach-a-data-disk"></a>タスク: データ ディスクの接続
新しいディスクとデータを含むディスクのどちらを接続するかについても決める必要があります。 新しいディスクの場合、コマンドによって .vhd ファイルが作成され、それが同じコマンドで接続されます。

新しいディスクを接続するには、次のコマンドを実行します。

```azurecli
    azure vm disk attach-new <resource-group> <vm-name> <size-in-gb>
```

既存のデータ ディスクを接続するには、次のコマンドを実行します。

```azurecli
azure vm disk attach <resource-group> <vm-name> [vhd-url]
```

その後、Linux で通常行うように、ディスクをマウントする必要があります。

## <a name="next-steps"></a>次のステップ
**arm** モードでの Azure CLI のその他の使用例については、「 [Azure リソース マネージャーでの、Mac、Linux、および Windows 用 Azure CLI の使用](../articles/xplat-cli-azure-resource-manager.md)」を参照してください。 Azure リソースとその概念の詳細については、「[Azure Resource Manager の概要](../articles/azure-resource-manager/resource-group-overview.md)」を参照してください。

使用できる他のテンプレートについては、「[Azure クイックスタート テンプレート](https://azure.microsoft.com/documentation/templates/)」と、[テンプレートを使用したアプリケーション フレームワーク](../articles/virtual-machines/linux/app-frameworks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページを参照してください。
