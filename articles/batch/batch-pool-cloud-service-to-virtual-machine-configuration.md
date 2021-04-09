---
title: Batch プールの構成を Cloud Services から Virtual Machines に移行する
description: プールの構成を最新の推奨構成に更新する方法について説明します
ms.topic: how-to
ms.date: 03/11/2021
ms.openlocfilehash: a176c4df1737a340a546b4ab7926447cd821350d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200556"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machine"></a>Batch プールの構成を Cloud Services から仮想マシンに移行する

現在 Batch プールは、[virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) または [cloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration) を使用して作成できます。 仮想マシンの構成のみを使用することをお勧めします。この構成ではすべての Batch 機能がサポートされるためです。

Cloud Services 構成プールは、現在の Batch 機能の一部をサポートしておらず、新しく追加された機能もサポートしていません。 [2024 年 2 月 29 日を過ぎると](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/)、新しい  'CloudServiceConfiguration' プールを作成したり、既存のプールに新しいノードを追加したりすることはできなくなります。

お使いの Batch ソリューションで現在 'cloudServiceConfiguration' プールが使用されている場合には、できるだけ早く 'virtualMachineConfiguration' に変更することをお勧めします。 これにより、拡大された [VM シリーズの選択](batch-pool-vm-sizes.md)、Linux VM、[コンテナー](batch-docker-container-workloads.md)、[Azure Resource Manager 仮想ネットワーク](batch-virtual-network.md)、および[ノード ディスクの暗号化](disk-encryption.md)など、すべての Batch 機能を利用できるようになります。

## <a name="create-a-pool-using-virtual-machine-configuration"></a>仮想マシンの構成を使用してプールを作成する

'CloudServiceConfiguration' を使用する既存のアクティブなプールを、'virtualMachineConfiguration' を使用するように切り替えることはできません。 代わりに、新しいプールを作成する必要があります。 新しい 'virtualMachineConfiguration' プールを作成し、すべてのジョブとタスクをレプリケートしたら、使用しなくなった古い 'cloudServiceConfiguration' プールを削除できます。

'virtualMachineConfiguration' を使用したプールの作成は、すべての Batch API、コマンドライン ツール、Azure portal、および Batch Explorer UI を使用して実行できます。

'virtualMachineConfiguration' を使用するプールを作成するプロセスのチュートリアルについては、 [.NET チュートリアル](tutorial-parallel-dotnet.md)または [Python チュートリアル](tutorial-parallel-python.md)を参照してください。

## <a name="pool-configuration-differences"></a>プールの構成の違い

2 つの構成の主な違いは次のとおりです。

- 'cloudServiceConfiguration' プール ノードは、Windows OS のみを使用します。 'virtualMachineConfiguration' プールは、Linux または Windows OS を使用できます。
- "cloudServiceConfiguration" プールと比較すると、"virtualMachineConfiguration" プールには、コンテナー サポート、データ　ディスク、ディスク暗号化など、豊富な機能セットが用意されています。
- プールとノードのスタートアップと削除の時間は、"cloudServiceConfiguration" プールと "virtualMachineConfiguration" プールの間で若干異なる場合があります。
- "virtualMachineConfiguration" プール ノードでは、マネージド OS ディスクが使用されます。 各ノードに使用される[マネージド ディスクの種類](../virtual-machines/disks-types.md)は、プールに対して選択されている VM サイズによって決まります。 "Standard_D2s_v3" など、プールに "s バージョン" の VM サイズが指定されている場合は、Premium SSD が使用されます。 "Standard_D2_v3" など、"s バージョン以外" の VM サイズが指定されている場合は、標準の HDD が使用されます。

   > [!IMPORTANT]
   > Virtual Machines や Virtual Machine Scale Sets と同様に、各ノードで使用される OS マネージド ディスクにはコストがかかります。これは、VM のコストに追加されます。 'cloudServiceConfiguration' ノードでは、ノードのローカル SSD に OS ディスクが作成されるため、OS ディスクのコストは発生しません。

## <a name="azure-data-factory-custom-activity-pools"></a>Azure Data Factory カスタム アクティビティ プール

Azure Batch プールを使用して、Data Factory カスタム アクティビティを実行することができます。 カスタム アクティビティを実行するために使用される "cloudServiceConfiguration" プールをすべて削除し、新しい "virtualMachineConfiguration" プールを作成する必要があります。

Data Factory カスタム アクティビティを実行するために新しいプールを作成する場合は、これらの方法に従ってください。

- 新しいプールを作成して古いものを削除する前に、すべてのパイプラインを一時停止して、実行が中断されないようにします。
- 同じプール ID を使用して、リンクされたサービス構成の変更を回避できます。
- 新しいプールが作成されたら、パイプラインを再開します。

Azure Batch を使用して Data Factory カスタム アクティビティを実行する方法の詳細については、「[Azure Batch のリンクされたサービス](../data-factory/compute-linked-services.md#azure-batch-linked-service)」および [Data Factory パイプラインのカスタム アクティビティ](../data-factory/transform-data-using-dotnet-custom-activity.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

- [プール構成](nodes-and-pools.md#configurations)について学習します。
- [プールのベスト プラクティス](best-practices.md#pools)について学習します。
- [プールの追加](/rest/api/batchservice/pool/add)および [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) の REST API リファレンスを参照してください。