---
title: Shared Image Gallery を使用してカスタム イメージ プールを作成する
description: カスタム イメージ プールは、Batch ワークロードを実行する計算ノードを構成するための効率的な方法です。
ms.topic: conceptual
ms.date: 03/04/2021
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 8623c47952540717ae50538fd7b0282c9c8629bb
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124246"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-image-pool"></a>Shared Image Gallery を使用してカスタム イメージ プールを作成する

仮想マシンの構成を使用して Azure Batch プールを作成するときは、プールの各コンピューティング ノードにオペレーティング システムを提供する VM イメージを指定します。 サポートされている Azure Marketplace イメージを使用するか、[Shared Image Gallery のイメージ](../virtual-machines/shared-image-galleries.md)を使用してカスタム イメージを作成するかのいずれかの方法で、仮想マシンのプールを作成することができます。

## <a name="benefits-of-the-shared-image-gallery"></a>Shared Image Gallery の利点

カスタム イメージのために Shared Image Gallery を使用すると、データ ディスクの種類に加えて、オペレーティング システムの種類と構成を制御できます。 共有イメージには、Batch プール ノードがプロビジョニングされると、そのすべてのノードですぐに使用可能になるアプリケーションと参照データを含めることができます。

お使いの環境に必要な複数のイメージ バージョンも保持できます。 イメージ バージョンを使用して VM を作成する場合、イメージ バージョンは VM 用の新しいディスクを作成するために使用されます。

共有イメージを使用すると、Batch ワークロードを実行できるように、プールのコンピューティング ノードを準備する時間を短縮できます。 プロビジョニング後、Azure Marketplace イメージを使用して各コンピューティング ノードにソフトウェアをインストールすることは可能ですが、通常は共有イメージを使用する方が効率的です。 また、共有イメージに複数のレプリカを指定できるため、多数の VM がある (VM が 600 個より多い) プールを作成するときに、プールの作成にかかる時間を節約できます。

シナリオに合わせて構成された共有イメージを使用すると、次のようなメリットがあります。

- **リージョン間で同じイメージを使用します。** すべてのプールが同じイメージを利用するよう、異なるリージョン間で共有イメージのレプリカを作成できます。
- **オペレーティング システム (OS) の構成。** イメージのオペレーティング システム ディスクの構成をカスタマイズできます。
- **アプリケーションの事前インストール。** OS ディスクにアプリケーションを事前インストールすると、開始タスクを使用してコンピューティング ノードをプロビジョニングしてからアプリケーションをインストールするよりも、効率的で、エラーが発生しにくくなります。
- **大量のデータを一度にコピー。** マネージド共有イメージの静的データの部分を、マネージド イメージのデータ ディスクにコピーすることで作成できます。 この作業は 1 回行うだけで、プールの各ノードでデータを使用できるようになります。
- **プールのサイズを拡張する。** Shared Image Gallery を使用すれば、より多くの共有イメージ レプリカに加えてカスタマイズされたイメージを使用して、より大きなプールを作成できます。
- **マネージド イメージだけをカスタム イメージとして使用するよりもパフォーマンスが向上します。** Shared Image のカスタム イメージ プールの場合、安定状態になるまでの時間は最大 25% 速くなり、VM のアイドル待機時間は最大で 30% 短くなります。
- **管理を容易にするイメージのバージョン管理とグループ化。** イメージのグループ化の定義には、イメージが作成された理由、対象の OS、イメージの使用に関する情報などの情報が含まれます。 イメージをグループ化すると、イメージを簡単に管理できるようになります。 詳細については、「[イメージ定義](../virtual-machines/shared-image-galleries.md#image-definitions)」を参照してください。

## <a name="prerequisites"></a>前提条件

> [!NOTE]
> Azure AD を使用して認証する必要があります。 共有キー認証を使用すると、認証エラーが発生します。  

- **Azure Batch アカウント。** Batch アカウントを作成するには、[Azure portal](quick-create-portal.md) または [Azure CLI](quick-create-cli.md) を使用した Batch のクイック スタートを参照してください。

- **Shared Image Gallery のイメージ**。 共有イメージを作成するには、マネージド イメージ リソースが必要です。ない場合は作成する必要があります。 イメージは、VM の OS ディスクと、それに接続されたデータ ディスク (後者はオプション) のスナップショットから作成する必要があります。

> [!NOTE]
> 共有イメージが Batch アカウントと同じサブスクリプションに含まれていない場合は、そのサブスクリプションの [Microsoft.Batch リソース プロバイダーを登録](../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)する必要があります。 2 つのサブスクリプションが同じ Azure AD テナントに存在する必要があります。
>
> Batch アカウントと同じリージョンにレプリカがある限り、イメージを異なるリージョンに配置できます。

Azure AD アプリケーションを使用して Shared Image Gallery のイメージを持つカスタム イメージ プールを作成する場合、そのアプリケーションに Shared Image へのアクセスを許可する [Azure 組み込みロール](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)が付与されている必要があります。 Azure portal でこのアクセス権を付与するには、Shared Image に移動し、 **[アクセス制御 (IAM)]** を選択して、アプリケーションにロールの割り当てを追加します。

## <a name="prepare-a-shared-image"></a>Shared Image を準備する

Azure では、以下から作成できるマネージド イメージから共有イメージを準備できます。

- Azure VM の OS とデータ ディスクのスナップショット
- マネージド ディスクで汎用化された Azure VM
- クラウドにアップロードされた、汎用化されたオンプレミス VHD

> [!NOTE]
> Batch では汎用化された共有イメージのみがサポートされます。特殊な共有イメージを使用してプールを作成することはできません。

次の手順では、VM を準備し、スナップショットを作成し、スナップショットからイメージを作成する方法を説明します。

### <a name="prepare-a-vm"></a>VM を準備する

イメージ用に新しい VM を作成する場合は、Batch によってサポートされているファースト パーティ Azure Marketplace イメージをマネージド イメージのベース イメージとして使用します。 ファースト パーティのイメージのみを、基本イメージとして使用できます。 Azure Batch でサポートされている Azure Marketplace イメージ参照のフルリストを取得するには、[ノード エージェント SKU の一覧表示](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus)操作に関する記事をご覧ください。

> [!NOTE]
> 基本イメージとして追加のライセンスと購入条件のあるサード パーティのイメージを使用することはできません。 このような Marketplace イメージについては、[Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms) VM または [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms) VM のガイダンスを参照してください。

VM を作成するときは、次のガイドラインに従ってください。

- VM がマネージド ディスクを使用して作成されていることを確認してください。 これは VM を作成するときの既定のストレージ設定です。
- VM には、Azure 拡張機能 (カスタム スクリプト拡張機能など) をインストールしないでください。 イメージにプレインストールされた拡張機能が含まれる場合、Azure で Batch プールのデプロイ時に問題が発生する可能性があります。
- 添付データ ディスクを含める場合は、それらを使用する VM 内からディスクを マウントおよびフォーマットする必要があります。
- 提供するベース OS イメージには、必ず既定の一時ドライブを使用するようにしてください。 現在、Batch ノード エージェントでは、既定の一時ドライブを使用する必要があります。
- OS ディスクが暗号化されていないことを確認します。
- VM が実行状態になったら、RDP (Windows の場合) または SSH (Linux の場合) を使用して VM に接続します。 必要なソフトウェアをインストールしたり、必要なデータをコピーしてください。
- プールのプロビジョニングを速くするには、VM の OS ディスクの [ReadWrite ディスク キャッシュ設定](../virtual-machines/premium-storage-performance.md#disk-caching)を使用します。

### <a name="create-a-vm-snapshot"></a>VM スナップショットを作成する

スナップショットは、VHD の完全な読み取り専用コピーです。 VM の OS やデータ ディスクのスナップショットを作成するには、Azure portal またはコマンドライン ツールを使用できます。 スナップショットを作成するための手順とオプションについては、[Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) または [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) VM のガイドをご覧ください。

### <a name="create-an-image-from-one-or-more-snapshots"></a>1 つ以上のスナップショットからイメージを作成する

スナップショットからマネージド イメージを作成するには、[az image create](/cli/azure/image) コマンドなどの Azure コマンドライン ツールを使用します。 イメージを作成する際には、OS ディスクのスナップショットを指定し、必要に応じて 1 つ以上のデータ ディスク スナップショットを指定します。

### <a name="create-a-shared-image-gallery"></a>Shared Image Gallery を作成する

マネージド イメージを正常に作成したら、Shared Image Gallery を作成して、カスタム イメージを使用できるようにする必要があります。 イメージ用に Shared Image Gallery を作成する方法については、「[Azure CLI を使用して共有イメージ ギャラリーを作成する](../virtual-machines/shared-images-cli.md)」または「[Azure portal を使用して共有イメージ ギャラリーを作成する](../virtual-machines/linux/shared-images-portal.md)」を参照してください。

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Azure CLI を使用して共有イメージからプールを作成する

Azure CLI を使用して共有イメージからプールを作成するには、`az batch pool create` コマンドを使用します。 `--image` フィールドに共有イメージ ID を指定します。 OS の種類と SKU が、`--node-agent-sku-id` によって指定されたバージョンと一致していることを確認してください。

> [!NOTE]
> Azure AD を使用して認証する必要があります。 共有キー認証を使用すると、認証エラーが発生します。  

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>C# を使用して共有イメージからプールを作成する

C# SDK を使用して共有イメージからプールを作成することもできます。

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
    ...
}
```

## <a name="create-a-pool-from-a-shared-image-using-python"></a>Python を使用して共有イメージからプールを作成する

また、Python SDK を使用して共有イメージからプールを作成することもできます。 

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch as batch
import azure.batch.models as batchmodels
from azure.common.credentials import ServicePrincipalCredentials

# Specify Batch account and service principal account credentials
account = "{batch-account-name}"
batch_url = "{batch-account-url}"
ad_client_id = "{sp-client-id}"
ad_tenant = "{tenant-id}"
ad_secret = "{sp-secret}"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client with Azure AD authentication
creds = ServicePrincipalCredentials(
    client_id=ad_client_id,
    secret=ad_secret,
    tenant=ad_tenant,
    resource="https://batch.core.windows.net/"
)
client = batch.BatchServiceClient(creds, batch_url)

# Configure the start task for the pool
start_task = batchmodels.StartTask(
    command_line="printenv AZ_BATCH_NODE_STARTUP_DIR"
)
start_task.run_elevated = True

# Create an ImageReference which specifies the image from
# Shared Image Gallery to install on the nodes.
ir = batchmodels.ImageReference(
    virtual_machine_image_id="/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}"
)

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04"
)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(
    id=pool_id,
    vm_size=vm_size,
    target_dedicated_nodes=node_count,
    virtual_machine_configuration=vmc,
    start_task=start_task
)

# Create pool in the Batch service
client.pool.add(new_pool)
```

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>Azure portal を使用して共有イメージからプールを作成する

次の手順を使用し、Azure portal で共有イメージからプールを作成します。

1. [Azure Portal](https://portal.azure.com)を開きます。
1. **Batch アカウント** に進み、自分のアカウントを選択します。
1. **[プール]** を選択し、次に **[追加]** を選択し、新しいプールを作成します。
1. **[イメージの種類]** セクションで **[Shared Image Gallery]** を選択します。
1. 残りのセクションにはマネージド イメージに関する情報を入力します。
1. **[OK]** を選択します。

![ポータルを使用して共有イメージからプールを作成します。](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>サイズの大きなプールに関する考慮事項

共有イメージを使用して、数百または数千以上の VM を含むプールを作成する場合は、次のガイダンスを参考にしてください。

- **Shared Image Gallery のレプリカ番号。**  最大 300 個のインスタンスを含むプールごとに、少なくとも 1 つのレプリカを保持することをお勧めします。 たとえば、3000 個の VM を含むプールを作成する場合は、イメージのレプリカを少なくとも 10 個は保持するようにしてください。 パフォーマンスを向上させるために、最小要件よりも多くのレプリカを保持することを常にお勧めします。

- **サイズ変更のタイムアウト。** プールに含まれるノード数が固定の場合 (自動スケーリングしない場合) は、プールのサイズに応じてプールの `resizeTimeout` プロパティの値を大きくしてください。 サイズ変更のタイムアウトの推奨値は、VM 1000 個ごとに最短で 15 分です。 たとえば、2000 個の VM があるプールの場合、サイズ変更のタイムアウトの推奨値は最短で 30 分です。

## <a name="next-steps"></a>次のステップ

- Batch の詳細については、「[Batch サービスのワークフローとリソース](batch-service-workflow-features.md)」を参照してください。
- [Shared Image Gallery](../virtual-machines/shared-image-galleries.md) に関するページをご覧ください。