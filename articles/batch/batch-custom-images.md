---
title: Shared Image Gallery を使用してプールを作成する - Azure Batch | Microsoft Docs
description: Shared Image Gallery を使用して Batch プールを作成し、アプリケーションに必要なソフトウェアとデータを含むカスタム イメージをコンピューティング ノードにプロビジョニングします。 カスタム イメージは、Batch ワークロードを実行するコンピューティング ノードを構成するための効率的な方法です。
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 08/14/2019
ms.author: lahugh
ms.openlocfilehash: 00da17512cbc2e713955ea83c7d9fa7517958169
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036699"
---
# <a name="use-the-shared-image-gallery-to-create-a-pool"></a>Shared Image Gallery を使用してプールを作成する

仮想マシンの構成を使用して Azure Batch プールを作成するときは、プールの各コンピューティング ノードにオペレーティング システムを提供する VM イメージを指定します。 サポートされている Azure Marketplace イメージを使用するか、[Shared Image Gallery](../virtual-machines/windows/shared-image-galleries.md) を使用してカスタム イメージを作成するかのいずれかの方法で、仮想マシンのプールを作成することができます。

## <a name="benefits-of-the-shared-image-gallery"></a>Shared Image Gallery の利点

カスタム イメージのために Shared Image Gallery を使用すると、データ ディスクの種類に加えて、オペレーティング システムの種類と構成を制御できます。 共有イメージには、Batch プール ノードがプロビジョニングされると、そのすべてのノードですぐに使用可能になるアプリケーションと参照データを含めることができます。

お使いの環境に必要な複数のイメージ バージョンも保持できます。 イメージ バージョンを使用して VM を作成する場合、イメージ バージョンは VM 用の新しいディスクを作成するために使用されます。 

共有イメージを使用すると、Batch ワークロードを実行できるように、プールのコンピューティング ノードを準備する時間を短縮できます。 プロビジョニング後、Azure Marketplace イメージを使用して各コンピューティング ノードにソフトウェアをインストールすることは可能ですが、通常は共有イメージを使用する方が効率的です。 また、共有イメージに複数のレプリカを指定できるため、多数の VM がある (VM が 600 個より多い) プールを作成するときに、プールの作成にかかる時間を節約できます。

シナリオに合わせて構成された共有イメージを使用すると、次のようなメリットがあります。

* **リージョン間で同じイメージを使用します。** すべてのプールが同じイメージを利用するよう、異なるリージョン間で共有イメージのレプリカを作成できます。
* **オペレーティング システム (OS) の構成。** イメージのオペレーティング システム ディスクの構成をカスタマイズできます。
* **アプリケーションの事前インストール。** OS ディスクにアプリケーションを事前インストールすると、開始タスクを使用してコンピューティング ノードをプロビジョニングしてからアプリケーションをインストールするよりも、効率的で、エラーが発生しにくくなります。
* **大量のデータを一度にコピー。** マネージド共有イメージの静的データの部分を、マネージド イメージのデータ ディスクにコピーすることで作成できます。 この作業は 1 回行うだけで、プールの各ノードでデータを使用できるようになります。
* **プールのサイズを拡張する。** Shared Image Gallery を使用すれば、より多くの共有イメージ レプリカに加えてカスタマイズされたイメージを使用して、より大きなプールを作成できます。
* **カスタム イメージよりも優れたパフォーマンス。** 共有イメージを使用すると、プールが安定した状態になるまでにかかる時間は最大 25% 速くなり、VM のアイドル待機時間は最大で 30% 短くなります。
* **管理を容易にするイメージのバージョン管理とグループ化。** イメージのグループ化の定義には、イメージが作成された理由、対象の OS、イメージの使用に関する情報などの情報が含まれます。 イメージをグループ化すると、イメージを簡単に管理できるようになります。 詳細については、「[イメージ定義](../virtual-machines/windows/shared-image-galleries.md#image-definitions)」を参照してください。

## <a name="prerequisites"></a>前提条件

* **Azure Batch アカウント。** Batch アカウントを作成するには、[Azure portal](quick-create-portal.md) または [Azure CLI](quick-create-cli.md) を使用した Batch のクイック スタートを参照してください。

* **Shared Image Gallery のイメージ**。 共有イメージの詳細および準備手順については、「[Azure CLI を使用して共有イメージ ギャラリーを作成する](../virtual-machines/linux/shared-images.md)」または「[Azure portal を使用して共有イメージ ギャラリーを作成する](../virtual-machines/linux/shared-images-portal.md)」を参照してください。

> [!NOTE]
> 共有イメージは、Batch アカウントと同じサブスクリプションに存在する必要があります。 Batch アカウントと同じリージョンにレプリカがある限り、共有イメージを異なるリージョンに配置できます。

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Azure CLI を使用して共有イメージからプールを作成する

Azure CLI を使用して共有イメージからプールを作成するには、`az batch pool create` コマンドを使用します。 `--image` フィールドに共有イメージ ID を指定します。 OS の種類と SKU が、`--node-agent-sku-id` によって指定されたバージョンと一致していることを確認してください。

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

## <a name="considerations-for-large-pools"></a>サイズの大きなプールに関する考慮事項

共有イメージを使用して、数百または数千以上の VM を含むプールを作成する場合は、次のガイダンスを参考にしてください。

* **Shared Image Gallery のレプリカ番号。**  最大 600 個のインスタンスを含むプールごとに、少なくとも 1 つのレプリカを保持することをお勧めします。 たとえば、3000 個の VM を含むプールを作成する場合は、イメージのレプリカを少なくとも 5 つは保持するようにしてください。 パフォーマンスを向上させるために、最小要件よりも多くのレプリカを保持することを常にお勧めします。

* **サイズ変更のタイムアウト** プールに含まれるノード数が固定の場合 (自動スケーリングしない場合) は、プールのサイズに応じてプールの `resizeTimeout` プロパティの値を大きくしてください。 サイズ変更のタイムアウトの推奨値は、VM 1000 個ごとに最短で 15 分です。 たとえば、2000 個の VM があるプールの場合、サイズ変更のタイムアウトの推奨値は最短で 30 分です。

## <a name="next-steps"></a>次の手順

* Batch の詳細な概要については、「[Batch を使って大規模な並列コンピューティング ソリューションを開発する](batch-api-basics.md)」を参照してください。
