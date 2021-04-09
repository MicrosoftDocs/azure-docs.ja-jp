---
title: 仮想マシンのコンピューティング ノードで Linux を実行する
description: Azure Batch の Linux 仮想マシンのプールで並列コンピューティング ワークロードを処理する方法について説明します。
ms.topic: how-to
ms.date: 01/21/2021
ms.custom: H1Hack27Feb2017, devx-track-python, devx-track-csharp
ms.openlocfilehash: c711ec0d035b9b59ec7628a51fe3cff26de358bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98683702"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Batch プールでの Linux コンピューティング ノードのプロビジョニング

Azure Batch を使用すると、Linux と Windows の両方の仮想マシンで並列コンピューティング ワークロードを実行できます。 この記事では、[Batch Python](https://pypi.python.org/pypi/azure-batch) と[Batch .NET](/dotnet/api/microsoft.azure.batch) の両方のクライアント ライブラリを使用して、Batch サービスで Linux コンピューティング ノードのプールを作成する方法について詳しく説明します。 

## <a name="virtual-machine-configuration"></a>仮想マシンの構成

Batch でコンピューティング ノードのプールを作成する場合は、Cloud Services 構成と仮想マシン構成という 2 つのオプションから、ノード サイズとオペレーティング システムを選択できます。 [仮想マシン構成](nodes-and-pools.md#virtual-machine-configuration)プールは、Azure VM で構成されます。これは、Linux イメージまたは Windows イメージから作成できます。 仮想マシン構成でプールを作成する場合は、[使用可能なコンピューティング ノードのサイズ](../virtual-machines/sizes.md)、ノードにインストールされる仮想マシン イメージの参照、および Batch ノード エージェント SKU (各ノードで実行され、ノードと Batch サービスの間のインターフェイスを提供するプログラム) を指定する必要があります。

### <a name="virtual-machine-image-reference"></a>仮想マシン イメージの参照

Batch サービスでは、[仮想マシン スケール セット](../virtual-machine-scale-sets/overview.md)を使って、仮想マシン構成にコンピューティング ノードを提供します。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1) でイメージを指定できます。または、[Shared Image Gallery をし応してカスタム イメージを準備します](batch-sig-images.md)。

仮想マシン イメージの参照を作成する際は、次のプロパティを指定する必要があります。

| **イメージの参照のプロパティ** | **例** |
| --- | --- |
| Publisher |Canonical |
| プラン |UbuntuServer |
| SKU |18.04-LTS |
| Version |latest |

> [!TIP]
> これらのプロパティの詳細と、Marketplace のイメージを指定する方法については、「[Azure CLI を使用して Azure Marketplace の Linux VM イメージを見つける](../virtual-machines/linux/cli-ps-findimage.md)」を参照してください。 いくつかの Marketplace イメージには、現時点、Batch との互換性がないことにご注意ください。

### <a name="list-of-virtual-machine-images"></a>仮想マシン イメージの一覧

すべての Marketplace イメージが、現在使用可能な Batch ノード エージェントと互換性があるわけではありません。 Batch サービスでサポートされるすべての Marketplace 仮想マシン イメージと、それらに対応するノード エージェント SKU を一覧表示するには、[list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) (Python)、[ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) (Batch .NET)、他の言語 SDK で対応する API を使用してください。

### <a name="node-agent-sku"></a>ノード エージェント SKU

[Batch ノード エージェント](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md)は、プール内の各ノードで実行されるプログラムで、ノードと Batch サービスの間のコマンドと制御のインターフェイスを提供します。 オペレーティング システムに応じてさまざまなノード エージェントの実装 (SKU と呼ばれます) があります。 基本的には、仮想マシン構成を作成する場合は、最初に仮想マシン イメージの参照を指定してから、イメージにインストールするノード エージェントを指定します。 通常、各ノード エージェント SKU は、複数の仮想マシン イメージと互換性があります。 ノード エージェント SKU の例をいくつか次に示します。

- batch.node.ubuntu 18.04
- batch.node.centos 7
- batch.node.windows amd64

## <a name="create-a-linux-pool-batch-python"></a>Linux プールの作成: Batch Python

次のコード スニペットは、[Python 向けの Microsoft Azure Batch クライアント ライブラリ](https://pypi.python.org/pypi/azure-batch)を使用して、Ubuntu Server コンピューティング ノードのプールを作成する方法の例を示しています。 Batch Python モジュールの詳細については、[リファレンス ドキュメント](/python/api/overview/azure/batch)を参照してください。

このスニペットでは、[ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference) を明示的に作成し、各プロパティ (publisher、offer、SKU、version) を指定します。 ただし、運用環境のコードでは、[list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) メソッドを使用して、実行時に使用可能なイメージとノード エージェント SKU の組み合わせを選択することをお勧めします。

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="18.04-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent
# to install on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

前述のように ([ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference) を明示的に作成する代わりに)、[list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) メソッドを使用して、現在サポートされているノード エージェントと Marketplace イメージの組み合わせから動的に選択することをお勧めします。 次の Python スニペットでは、このメソッドの使用方法を示します。

```python
# Get the list of supported images from the Batch service
images = client.account.list_supported_images()

# Obtain the desired image reference
image = None
for img in images:
  if (img.image_reference.publisher.lower() == "canonical" and
        img.image_reference.offer.lower() == "ubuntuserver" and
        img.image_reference.sku.lower() == "18.04-lts"):
    image = img
    break

if image is None:
  raise RuntimeError('invalid image reference for desired configuration')

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=image.image_reference,
    node_agent_sku_id=image.node_agent_sku_id)
```

## <a name="create-a-linux-pool-batch-net"></a>Linux プールの作成: Batch .NET

次のコード スニペットは、[Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) クライアント ライブラリを使用して、Ubuntu Server コンピューティング ノードのプールを作成する方法の例を示しています。 Batch .NET の詳細については、[リファレンス ドキュメント](/dotnet/api/microsoft.azure.batch)を参照してください。

次のコード スニペットでは、[PoolOperations.ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) メソッドを使用して、現在サポートされている Marketplace イメージとノード エージェント SKU の組み合わせの一覧から選択します。 サポートされる組み合わせの一覧が変更される場合があるため、この手法をお勧めします。 通常は、サポートされる組み合わせが追加されます。

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_D2_V3";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<ImageInformation> images =
    batchClient.PoolOperations.ListSupportedImages().ToList();

// Find the appropriate image information
ImageInformation image = null;
foreach (var img in images)
{
    if (img.ImageReference.Publisher == "Canonical" &&
        img.ImageReference.Offer == "UbuntuServer" &&
        img.ImageReference.Sku == "18.04-LTS")
    {
        image = img;
        break;
    }
}

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(image.ImageReference, image.NodeAgentSkuId);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

上記のスニペットでは、P[oolOperations.ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) メソッドを使用して、サポートされているイメージとノード エージェント SKU の組み合わせを動的に一覧表示してから選択しますが (推奨)、[ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) を明示的に構成することもできます。

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="connect-to-linux-nodes-using-ssh"></a>SSH を使用した Linux ノードへの接続

開発時またはトラブルシューティング時に、プール内のノードにサインインすることが必要な場合があります。 Windows コンピューティング ノードとは異なり、リモート デスクトップ プロトコル (RDP) を使用して Linux ノードに接続することはできません。 代わりに、Batch サービスを使用して、各ノードでリモート接続用に SSH アクセスを有効にします。

次の Python コード スニペットでは、リモート接続に必要なユーザーをプール内の各ノードに作成します。 その後、各ノードの Secure Shell (SSH) 接続情報を出力します。

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
    credentials,
    base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

このコードでは、次の例のような出力が表示されます。 この場合、プールには 4 つの Linux ノードが含まれています。

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

ノードにユーザーを作成するときに、パスワードの代わりに、SSH 公開キーを指定できます。 Python SDK では、[ComputeNodeUser](/python/api/azure-batch/azure.batch.models.computenodeuser) の **ssh_public_key** パラメーターを使います。 .NET では、[ComputeNodeUser.SshPublicKey](/dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey) プロパティを使用します。

## <a name="pricing"></a>価格

Azure Batch は Azure Cloud Services と Azure Virtual Machines テクノロジに基づいて構築されています。 Batch サービス自体は、無料で提供されています。そのため、Batch ソリューションによって使用されたコンピューティング リソース (およびそれに付随する関連コスト) に対してのみ課金されます。 **仮想マシンの構成** を選択した場合は、[Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/)体系に基づいて課金されます。

[アプリケーション パッケージ](batch-application-packages.md)を使ってアプリケーションを Batch ノードにデプロイする場合は、アプリケーション パッケージで使われる Azure Storage リソースにも課金されます。

## <a name="next-steps"></a>次のステップ

- [azure-batch-samples GitHub リポジトリ](https://github.com/Azure/azure-batch-samples)にある [Python コード サンプル](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch)で、プール、ジョブ、タスクの作成などの一般的な Batch 操作の実行方法を確認できます。 Python サンプルに付属する [README](https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md) には、必要なパッケージのインストール方法の詳細が記載されています。
- [優先順位の低い VM](batch-low-pri-vms.md) と Batch の使用について確認します。
