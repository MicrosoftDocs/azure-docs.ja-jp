---
title: マネージド イメージを使用してカスタム イメージ プールを作成する
description: マネージド イメージから Batch カスタム イメージ プールを作成して、アプリケーション用のソフトウェアとデータを含むコンピューティング ノードをプロビジョニングします。
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 0a357a1d8a22341297f3bee73fb0867fb03f374f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94916578"
---
# <a name="use-a-managed-image-to-create-a-custom-image-pool"></a>マネージド イメージを使用してカスタム イメージ プールを作成する

Batch プールの仮想マシン (VM) のカスタム イメージ プールを作成するために、マネージド イメージを使用して [Shared Image Gallery イメージ](batch-sig-images.md)を作成できます。 マネージド イメージだけを使用することもできますが、2019-08-01 以前の API バージョンでのみサポートされます。

> [!IMPORTANT]
> ほとんどの場合、Shared Image Gallery を使用してカスタム イメージを作成する必要があります。 Shared Image Gallery を使用すると、プールを迅速にプロビジョニングしたり、VM の数を増やしたり、VM のプロビジョニング時に信頼性を向上させたりすることができます。 詳細については、「[Shared Image Gallery を使用してカスタム プールを作成する](batch-sig-images.md)」を参照してください。

このトピックでは、マネージド イメージだけを使用してカスタム イメージ プールを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

- **マネージド イメージ リソース**。 カスタム イメージを使用して仮想マシンのプールを作成するには、Batch アカウントと同じ Azure サブスクリプションおよびリージョンに、マネージド イメージ リソースを配置または作成する必要があります。 イメージは、VM の OS ディスクと、それに接続されたデータ ディスク (後者はオプション) のスナップショットから作成する必要があります。
  - 作成する各プールには、一意のカスタム イメージを使用します。
  - Batch API を使用してイメージでプールを作成するには、イメージの **リソース ID** を指定します。形式は次のとおりです。`/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`
  - マネージド イメージ リソースは、スケール アップするプールの有効期間を通じて存在する必要があり、プールを削除した後は削除することができます。

- **Azure Active Directory (Azure AD) 認証**。 Batch クライアント API では、Azure AD 認証を使用する必要があります。 Azure AD の Azure Batch のサポートについては、「[Batch サービスの認証に Active Directory を使用する](batch-aad-auth.md)」に記載されています。

## <a name="prepare-a-managed-image"></a>マネージド イメージを準備する

Azure では、次のものからマネージド イメージを準備できます。

- Azure VM の OS とデータ ディスクのスナップショット
- マネージド ディスクで汎用化された Azure VM
- クラウドにアップロードされた、汎用化されたオンプレミス VHD

マネージド イメージを使って Batch プールを安定的にスケーリングするには、最初の方法 (VM のディスクのスナップショットを使用する) *のみ* を使用してマネージド イメージを作成することをお勧めします。 次の手順では、VM を準備し、スナップショットを作成し、スナップショットからマネージド イメージを作成する方法を説明します。

### <a name="prepare-a-vm"></a>VM を準備する

イメージ用に新しい VM を作成する場合は、Batch によってサポートされているファースト パーティ Azure Marketplace イメージをマネージド イメージのベース イメージとして使用します。 ファースト パーティのイメージのみを、基本イメージとして使用できます。 Azure Batch でサポートされている Azure Marketplace イメージ参照のフルリストを取得するには、[ノード エージェント SKU の一覧表示](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus)操作に関する記事をご覧ください。

> [!NOTE]
> 基本イメージとして追加のライセンスと購入条件のあるサード パーティのイメージを使用することはできません。 このような Marketplace イメージについては、[Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms) VM または [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms) VM のガイダンスを参照してください。

- VM がマネージド ディスクを使用して作成されていることを確認してください。 これは VM を作成するときの既定のストレージ設定です。
- VM には、Azure 拡張機能 (カスタム スクリプト拡張機能など) をインストールしないでください。 イメージにプレインストールされた拡張機能が含まれる場合、Azure で Batch プールのデプロイ時に問題が発生する可能性があります。
- 添付データ ディスクを含める場合は、それらを使用する VM 内からディスクを マウントおよびフォーマットする必要があります。
- 提供するベース OS イメージには、必ず既定の一時ドライブを使用するようにしてください。 現在、Batch ノード エージェントでは、既定の一時ドライブを使用する必要があります。
- OS ディスクが暗号化されていないことを確認します。
- VM が実行状態になったら、RDP (Windows の場合) または SSH (Linux の場合) を使用して VM に接続します。 必要なソフトウェアをインストールしたり、必要なデータをコピーしてください。  

### <a name="create-a-vm-snapshot"></a>VM スナップショットを作成する

スナップショットは、VHD の完全な読み取り専用コピーです。 VM の OS やデータ ディスクのスナップショットを作成するには、Azure portal またはコマンドライン ツールを使用できます。 スナップショットを作成するための手順とオプションについては、[Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) または [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) VM のガイドをご覧ください。

### <a name="create-an-image-from-one-or-more-snapshots"></a>1 つ以上のスナップショットからイメージを作成する

スナップショットからマネージド イメージを作成するには、[az image create](/cli/azure/image) コマンドなどの Azure コマンドライン ツールを使用します。 イメージを作成する際には、OS ディスクのスナップショットを指定し、必要に応じて 1 つ以上のデータ ディスク スナップショットを指定することができます。

## <a name="create-a-pool-from-a-managed-image"></a>マネージド イメージからプールを作成する

マネージド イメージのリソース ID が見つかったら、そのイメージからカスタム イメージ プールを作成します。 次の手順では、Batch サービスまたは Batch Management を使用してカスタム イメージ プールを作成する方法を説明します。

> [!NOTE]
> Azure AD 認証に使用する ID に、イメージ リソースに対するアクセス許可があることを確認してください。 「[Batch サービスの認証に Active Directory を使用する](batch-aad-auth.md)」を参照してください。
>
> マネージド イメージのリソースは、プールの有効期間にわたって存在する必要があります。 基になるリソースが削除されると、プールはスケールできません。

### <a name="batch-service-net-sdk"></a>Batch サービス .NET SDK

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
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image definition name}");
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
```

### <a name="batch-management-rest-api"></a>Batch Management REST API

REST API URI

```http
 PUT https://management.azure.com/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Batch/batchAccounts/{account name}/pools/{pool name}?api-version=2020-03-01
```

要求本文

```json
 {
   "properties": {
     "vmSize": "{VM size}",
     "deploymentConfiguration": {
       "virtualMachineConfiguration": {
         "imageReference": {
           "id": "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image name}"
         },
         "nodeAgentSkuId": "{Node Agent SKU ID}"
       }
     }
   }
 }
```

## <a name="considerations-for-large-pools"></a>サイズの大きなプールに関する考慮事項

カスタム イメージを使用して数百個以上の VM を含んだプールを作成する場合は、前述のガイダンスに従い、VM スナップショットから作成されたイメージを使用することが重要です。

次の考慮事項にも注意してください。

- **サイズ制限** - Batch では、カスタム イメージを使用する場合、2500 個の専用コンピューティング ノードか、1000 個の低優先ノードにプール サイズが制限されます。

  同じイメージ (または同じスナップショットに基づく複数のイメージ) を使用して複数のプールを作成する場合は、プール内のコンピューティング ノードの合計数が、上記の制限を超えないようにする必要があります。 1 つのイメージや基盤スナップショットを、複数のプールに使用することは推奨されません。

  制限は、[受信 NAT プール](pool-endpoint-configuration.md)を使ってプールを構成することで緩和される場合があります。

- **サイズ変更のタイムアウト** - プールに固定数のノードが含まれている (自動スケールではない) 場合は、プールの resizeTimeout プロパティの値を 20 ～ 30 分に増やしてください。 タイムアウト時間内にプールがターゲット サイズに達しなかった場合は、別の[サイズ変更操作](/rest/api/batchservice/pool/resize)を実行してください。

  プールに 300 個以上のコンピューティング ノードを含める場合は、ターゲット サイズに達するまでに、プールのサイズを複数回変更しなければならない場合あります。
  
[Shared Image Gallery](batch-sig-images.md) を使用すれば、より多くの共有イメージ レプリカに加えてカスタマイズされたイメージを使用して、より大きなプールを作成できます。 共有イメージを使用すると、プールが安定した状態になるまでにかかる時間は最大 25% 速くなり、VM のアイドル待機時間は最大で 30% 短くなります。

## <a name="considerations-for-using-packer"></a>Packer の使用に関する注意点

ユーザー サブスクリプション モードの Batch アカウントがある場合のみ、マネージド イメージ リソースをPacker で直接作成できます。 Batch サービス モードのアカウントでは、最初に VHD を作成し、次にマネージド イメージ リソースへ VHD をインポートする必要があります。 マネージド イメージ リソースを作成する手順は、プール割り当てモード (ユーザーのサブスクリプションまたは Batch サービス) によって異なります。

カスタム イメージを参照するすべてのプールの有効期間用に、マネージド イメージを作成するために使用するリソースが存在することを確認します。 これに失敗すると、プールの割り当てやサイズ変更に失敗する可能性があります。

イメージまたは基になるリソースが削除された場合は、`There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed` に似たエラーが発生します。 このようなエラーが発生した場合は、基になるリソースが削除されていないことを確認します。

Packer を使用して VM を作成する詳細については、「[Build a Linux image with Packer](../virtual-machines/linux/build-image-with-packer.md)」 (Packer で Linux イメージを作成) または「[Build a Windows image with Packer](../virtual-machines/windows/build-image-with-packer.md)」 (Packer で Windows イメージを作成) を参照してください。

## <a name="next-steps"></a>次のステップ

- [Shared Image Gallery](batch-sig-images.md) を使用してカスタム プールを作成する方法を確認してください。
- Batch の詳細については、「[Batch サービスのワークフローとリソース](batch-service-workflow-features.md)」を参照してください。
