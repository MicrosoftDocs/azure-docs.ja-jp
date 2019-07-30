---
title: 仮想マシン コンピューティング ノードでの Linux の実行 - Azure Batch | Microsoft Docs
description: Azure Batch の Linux 仮想マシンのプールで並列コンピューティング ワークロードを処理する方法について説明します。
services: batch
documentationcenter: python
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: na
ms.date: 06/01/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b4b381ff1f68935084e3dd30865cf539d4abbd16
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323527"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Batch プールでの Linux コンピューティング ノードのプロビジョニング

Azure Batch を使用すると、Linux と Windows の両方の仮想マシンで並列コンピューティング ワークロードを実行できます。 この記事では、[Batch Python][py_batch_package] and [Batch .NET][api_net] クライアント ライブラリを使用して、Batch サービスで Linux コンピューティング ノードのプールを作成する方法について説明します。

> [!NOTE]
> アプリケーション パッケージは、2017 年 7 月 5 日より後に作成されたすべての Batch プールでサポートされます。 これらは、プールがクラウド サービス構成を使って作成された場合にのみ、2016 年 3 月 10 日から 2017 年 7 月 5 日までの間に作成された Batch プールでサポートされます。 2016 年 3 月 10 日より前に作成された Batch プールは、アプリケーション パッケージをサポートしていません。 アプリケーション パッケージを使った Batch ノードへのアプリケーションのデプロイについて詳しくは、「[Batch アプリケーション パッケージを使用したコンピューティング ノードへのアプリケーションのデプロイ](batch-application-packages.md)」をご覧ください。
>
>

## <a name="virtual-machine-configuration"></a>仮想マシンの構成
Batch でコンピューティング ノードのプールを作成する場合は、Cloud Services 構成と仮想マシン構成という 2 つのオプションから、ノード サイズとオペレーティング システムを選択できます。

**Cloud Services の構成** では、Windows コンピューティング ノード *のみ*が提供されます。 使用可能なコンピューティング ノードのサイズについては、「[Cloud Services のサイズ](../cloud-services/cloud-services-sizes-specs.md)」を参照してください。使用可能なオペレーティング システムについては、「[Azure ゲスト OS リリースと SDK の互換性対応表](../cloud-services/cloud-services-guestos-update-matrix.md)」を参照してください。 Azure Cloud Services ノードを含むプールを作成する場合は、前に示した記事に記載されているノード サイズと OS ファミリを指定します。 Windows コンピューティング ノードのプールの場合は、Cloud Services が最もよく使用されます。

**仮想マシン構成** では、Linux と Windows の両方のコンピューティング ノード イメージが提供されます。 使用可能なコンピューティング ノード サイズについては、「[Azure の仮想マシンのサイズ](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」(Linux) および「[Azure の仮想マシンのサイズ](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」(Windows) を参照してください。 仮想マシンの構成ノードを含むプールを作成する場合は、ノードのサイズ、仮想マシン イメージの参照、およびノードにインストールする Batch ノード エージェント SKU を指定する必要があります。

### <a name="virtual-machine-image-reference"></a>仮想マシン イメージの参照
Batch サービスでは、[仮想マシン スケール セット](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)を使って、仮想マシン構成にコンピューティング ノードを提供します。 [Azure Marketplace][vm_marketplace] でイメージを指定できます。または、事前に準備したカスタム イメージを指定できます。 カスタム イメージの詳細については、[カスタム イメージを使用したプールの作成](batch-custom-images.md)に関するページを参照してください。

仮想マシン イメージの参照を構成する場合は、仮想マシン イメージのプロパティを指定します。 仮想マシン イメージの参照を作成する際は、次のプロパティが必要です。

| **イメージの参照プロパティ** | **例** |
| --- | --- |
| Publisher |Canonical |
| プラン |UbuntuServer |
| SKU |14.04.4-LTS |
| Version |latest |

> [!TIP]
> これらのプロパティと、Marketplace イメージを一覧表示する方法の詳細については、「[CLI または PowerShell を使用した Azure での Linux 仮想マシン イメージへの移動と選択](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。 現時点では、すべての Marketplace イメージに Batch との互換性があるわけではありません。 詳細については、「 [ノード エージェント SKU](#node-agent-sku)」を参照してください。
>
>

### <a name="node-agent-sku"></a>ノード エージェント SKU
Batch ノード エージェントは、プール内の各ノードで実行されるプログラムで、ノードと Batch サービスの間のコマンドと制御のインターフェイスを提供します。 オペレーティング システムに応じてさまざまなノード エージェントの実装 (SKU と呼ばれます) があります。 基本的には、仮想マシン構成を作成する場合は、最初に仮想マシン イメージの参照を指定してから、イメージにインストールするノード エージェントを指定します。 通常、各ノード エージェント SKU は、複数の仮想マシン イメージと互換性があります。 ノード エージェント SKU の例をいくつか次に示します。

* batch.node.ubuntu 14.04
* batch.node.centos 7
* batch.node.windows amd64

> [!IMPORTANT]
> Marketplace から入手できる仮想マシン イメージの一部には、現在利用可能な Batch ノード エージェントとの互換性がありません。 Batch SDK を使って、使用可能なノード エージェント SKU と、それと互換性のある仮想マシン イメージの一覧を表示します。 実行時に有効なイメージの一覧を取得する方法の詳細と例については、この記事で後述する「[仮想マシン イメージの一覧](#list-of-virtual-machine-images)」をご覧ください。
>
>

## <a name="create-a-linux-pool-batch-python"></a>Linux プールの作成: Batch Python
次のコード スニペットは、Read the Docs の [Python 向けの Microsoft Azure Batch クライアント ライブラリ][py_batch_package] to create a pool of Ubuntu Server compute nodes. Reference documentation for the Batch Python module can be found at [azure.batch package][py_batch_docs] を使用する方法の例を示しています。

このスニペットは、使用可能なイメージとノード エージェント SKU の組み合わせから実行時に決定して選択するために、[ImageReference][py_imagereference] explicitly and specifies each of its properties (publisher, offer, SKU, version). In production code, however, we recommend that you use the [list_node_agent_skus][py_list_skus] メソッドを作成します。

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
vm_size = "STANDARD_A1"
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
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="14.04.2-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

前述のように、[ImageReference][py_imagereference] explicitly, you use the [list_node_agent_skus][py_list_skus] メソッドを作成する代わりに、現在サポートされているノード エージェントと Marketplace イメージの組み合わせから動的に選択することをお勧めします。 次の Python スニペットでは、このメソッドの使用方法を示します。

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(
    agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id=ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>Linux プールの作成: Batch .NET
次のコード スニペットは、docs.microsoft.com の [Batch .NET][nuget_batch_net] client library to create a pool of Ubuntu Server compute nodes. You can find the [Batch .NET reference documentation][api_net] を使用する方法の例を示しています。

次のコード スニペットでは、[PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] メソッドを使用して、現在サポートされている Marketplace イメージとノード エージェント SKU の組み合わせの一覧から選択します。 サポートされる組み合わせの一覧が変更される場合があるため、この手法をお勧めします。 通常は、サポートされる組み合わせが追加されます。

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

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

上記のスニペットでは、[PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] メソッドを使用して、サポートされているイメージとノード エージェント SKU の組み合わせを動的に一覧表示してから選択しますが (推奨)、[ImageReference][net_imagereference] を明示的に構成することもできます。

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>仮想マシン イメージの一覧
この記事の最終更新時点で使用可能な Batch ノード エージェントと互換性のある Marketplace 仮想マシン イメージの一覧を、次の表に示します。 イメージとノード エージェントは随時追加または削除される可能性があるため、これは最終的な一覧ではないことに注意してください。 Batch アプリケーションとサービスでは、常に [list_node_agent_skus][py_list_skus] (Python) or [ListNodeAgentSkus][net_list_skus] (Batch .NET) を使用して、現在利用可能な SKU を確認してから選択することをお勧めします。

> [!WARNING]
> 次の一覧は、いつでも変更される可能性があります。 Batch ジョブを実行するときに、Batch API で使用できる **ListNodeAgentSkus** メソッドを常に使用して、互換性のある仮想マシンとノード エージェント SKU を一覧表示します。
>
>

| **発行元** | **プラン** | **イメージ SKU** | **バージョン** | **ノード エージェント SKU ID** |
| ------------- | --------- | ------------- | ----------- | --------------------- |
| batch (バッチ) | rendering-centos73 | rendering | latest | batch.node.centos 7 |
| batch (バッチ) | rendering-windows2016 | rendering | latest | batch.node.windows amd64 |
| Canonical | UbuntuServer | 16.04 LTS | latest | batch.node.ubuntu 16.04 |
| Canonical | UbuntuServer | 14.04.5-LTS | latest | batch.node.ubuntu 14.04 |
| Credativ | Debian | 9 | latest | batch.node.debian 9 |
| Credativ | Debian | 8 | latest | batch.node.debian 8 |
| microsoft-ads | linux-data-science-vm | linuxdsvm | latest | batch.node.centos 7 |
| microsoft-ads | standard-data-science-vm | standard-data-science-vm | latest | batch.node.windows amd64 |
| microsoft-azure-batch | centos-container | 7-4 | latest | batch.node.centos 7 |
| microsoft-azure-batch | centos-container-rdma | 7-4 | latest | batch.node.centos 7 |
| microsoft-azure-batch | ubuntu-server-container | 16-04-lts | latest | batch.node.ubuntu 16.04 |
| microsoft-azure-batch | ubuntu-server-container-rdma | 16-04-lts | latest | batch.node.ubuntu 16.04 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-smalldisk | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-with-Containers | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter-smalldisk | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter-smalldisk | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1-smalldisk | latest | batch.node.windows amd64 |
| OpenLogic | CentOS | 7.4 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.4 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.3 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.1 | latest | batch.node.centos 7 |
| Oracle | Oracle-Linux | 7.4 | latest | batch.node.centos 7 |
| SUSE | SLES-HPC | 12-SP2 | latest | batch.node.opensuse 42.1 |

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

4 つの Linux ノードを含むプールに対する上記のコードのサンプル出力を次に示します。

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

ノードにユーザーを作成するときに、パスワードの代わりに、SSH 公開キーを指定できます。 Python SDK では、[ComputeNodeUser][py_computenodeuser]. In .NET, use the [ComputeNodeUser][net_computenodeuser].[SshPublicKey][net_ssh_key] プロパティの **ssh_public_key** パラメーターを使います。

## <a name="pricing"></a>価格
Azure Batch は Azure Cloud Services と Azure Virtual Machines テクノロジに基づいて構築されています。 Batch サービス自体は、無料で提供されています。そのため、Batch ソリューションによって使用されたコンピューティング リソースに対してのみ課金されます。 **Cloud Services 構成**を選択した場合は、[Cloud Services の料金][cloud_services_pricing]体系に基づいて課金されます。 **仮想マシンの構成**を選択した場合は、[Virtual Machines の料金][vm_pricing]体系に基づいて課金されます。 

[アプリケーション パッケージ](batch-application-packages.md)を使ってアプリケーションを Batch ノードにデプロイする場合は、アプリケーション パッケージで使われる Azure Storage リソースにも課金されます。 一般に、Azure Storage のコストは最小限です。 

## <a name="next-steps"></a>次の手順

GitHub にある [Python コード サンプル][github_samples_py] in the [azure-batch-samples][github_samples] リポジトリには、プール、ジョブ、タスクの作成などの一般的な Batch 操作の実行方法を示すスクリプトが含まれています。 Python サンプルに付属する [README][github_py_readme] には、必要なパッケージのインストール方法の詳細が記載されています。

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.aspx
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.sshpublickey.aspx
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure-sdk-for-python.readthedocs.io/batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: https://docs.microsoft.com/python/api/azure.batch.models.computenodeuser
[py_imagereference]: https://docs.microsoft.com/python/api/azure.mgmt.batch.models.imagereference
[py_list_skus]: https://docs.microsoft.com/python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
