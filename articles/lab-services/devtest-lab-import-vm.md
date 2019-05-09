---
title: Azure DevTest Labs で別のラボから VM をインポートする | Microsoft Docs
description: Azure DevTest Labs の現在のラボに、別のラボから仮想マシンをインポートする方法
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: cb4a3ec9be82957b4c0366ec232f1147c52d0251
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60148774"
---
# <a name="import-vms-from-another-lab-in-azure-devtest-labs"></a>Azure DevTest Labs で別のラボから VM をインポートする
Azure DevTest Labs サービスにより、開発とテスト用の仮想マシン (VM) の管理が大幅に向上します。 この方法を使用すると、チームやインフラストラクチャの要件の変化に応じて、あるラボから別のラボに、VM を移動できます。 これが必要となる一般的なシナリオを次に示します。

- チームのある担当者は、企業内で別のグループに移動となり、新しいチームのラボに向けた VM を開発したいと考えています。
- グループは、サブスクリプション レベルのクォータに達し、複数のサブスクリプションに分割することにしました。
- 会社は Express Route (またはいくつかその他の新しいネットワーク トポロジ) に移行する予定で、チームはこの新しいインフラストラクチャに VM を移動したいと考えています。

## <a name="solution-and-constraints"></a>ソリューションと制約
Azure DevTest Labs では、ラボの所有者がインポート元のラボの VM をインポート先のラボにインポートできます。 ラボ所有者は、必要に応じてプロセスのインポート先の VM に新しい名前を指定できます。 インポート処理には、ディスク、スケジュール、ネットワーク設定などのすべての依存関係が含まれています。プロセスはある程度時間がかかり、インポート元のマシンに接続されているディスクの数やサイズ、およびインポート元とインポート先との距離 (たとえば、東南アジア リージョンに米国東部リージョン) による影響を受けます。 インポート処理が完了すると、インポート元の VM はシャットダウンしたままとなり、インポート先のラボでは新しい VM が動作します。

VM を別のラボにインポートする際に注意すべき 2 つの重要な制約があります。

- サブスクリプションやリージョンが異なる VM のインポートはサポートされていますが、サブスクリプションには、同じ Azure Active Directory テナントが関連付けられている必要があります。
- VM がインポート元のラボで要求可能な状態にない必要があります。

さらに、VM を 1 つのラボから別のラボにインポートできるようにするには、インポート元のラボの VM の所有者であり、かつインポート先ラボの所有者である必要があります。

## <a name="steps-to-import-a-vm-from-another-lab"></a>別のラボから VM をインポートする手順
現時点では、Azure PowerShell と REST API を使用してのみ 1 つのラボから別のラボに VM をインポートできます。

### <a name="use-powershell"></a>PowerShell の使用
PowerShell スクリプト ファイル ImportVirtualMachines.ps1 を [Azure DevTest Lab の Git リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines)からローカル ドライブにダウンロードします。

#### <a name="import-a-single-vm"></a>1 つの VM をインポートする
ImportVirtualMachines.ps1 スクリプトを実行して 1 つの VM をインポート元ラボからインポート先ラボにインポートします。 DestinationVirtualMachineName paramer を使用して、コピーされている VM に新しい名前を指定することができます。

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -SourceVirtualMachineName "<Name of the machine. Optional. If not specified, all VMs are copied>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VM is copied>" `
                            -DestinationVirtualMachineName "<New name for the VM. Optional>"
```


#### <a name="importing-all-vms"></a>すべての VM をインポートする
ImportVirtualMachines.ps1 スクリプトを実行する場合、インポート元ラボで VM を指定しないと、インポート元ラボの VM がすべてインポート先ラボにインポートされます。

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VMs are copied>"
```

### <a name="use-rest-api"></a>REST API を使用する
次の例に示すように、対象のインポート先ラボに対して REST API を呼び出し、インポート元、サブスクリプション、および VM 情報にパラメーターとして渡します。

```json
POST https://management.azure.com/subscriptions/<ID of the target/destination subscription>/resourceGroups/<Name of the resource group that contains the destination lab>/providers/Microsoft.DevTestLab/labs/<Name of the lab to which the VMs are copied>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<ID of the subscription that contains the source VM>/resourcegroups/<Name of the resource group that contains the source lab>/providers/microsoft.devtestlab/labs/<Name of the lab that contains the source VM>/virtualmachines/MyVm",
   destinationVirtualMachineName: "MyVmNew"
}
```

## <a name="next-steps"></a>次の手順

- VM のサイズ変更方法の詳細については、[VM のサイズ変更](devtest-lab-resize-vm.md)をご覧ください。
- VM の再デプロイの詳細については、[VM の再デプロイ](devtest-lab-redeploy-vm.md)をご覧ください。
