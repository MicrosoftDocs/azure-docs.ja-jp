---
title: Azure VM スケール セット VM のインスタンス ID を理解する | Microsoft Docs
description: Azure VM スケール セット VM のインスタンス ID を理解する
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: manayar
ms.openlocfilehash: 6aeba722a0661979664f8d61efdb9b2bf47ad801
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981657"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Azure VM スケール セット VM のインスタンス ID を理解する
この記事では、スケール セットのインスタンス ID と、さまざまな使用方法について説明します。

## <a name="scale-set-instance-ids"></a>スケール セットのインスタンス ID

スケール セットの各 VM には、それを一意に識別するインスタンス ID が割り当てられます。 このインスタンス ID は、スケール セット内の特定の VM に対する操作を実行するために、スケール セット API で使用されます。 たとえば、再イメージ化 API を使用する場合は、再イメージ化する特定のインスタンス ID を指定できます。

REST API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (詳しくは、[REST API のドキュメント](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage)をご覧ください)

Powershell: `Set-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (詳しくは、[Powershell のドキュメント](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm)をご覧ください)

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (詳しくは、[CLI のドキュメント](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)をご覧ください)。

インスタンス ID の一覧を取得するには、スケール セット内のすべてのインスタンスを一覧表示します。

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (詳しくは、[REST API のドキュメント](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/list)をご覧ください)

Powershell: `Get-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (詳しくは、[Powershell のドキュメント](https://docs.microsoft.com/powershell/module/az.compute/get-azvmssvm)をご覧ください)

CLI: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (詳しくは、[CLI のドキュメント](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)をご覧ください)。

[resources.azure.com](https://resources.azure.com) または [Azure SDK](https://azure.microsoft.com/downloads/) を使用して、スケール セット内の VM を一覧表示することもできます。

出力の正確な表示はコマンドに指定したオプションによって異なりますが、CLI からのサンプル出力を次に示します。

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
[
  {
    "instanceId": "85",
    "latestModelApplied": true,
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

このように、"instanceId" プロパティは単純な 10 進数です。 インスタンス ID は、古いインスタンスが削除されると、新しいインスタンス用に再利用されることがあります。

>[!NOTE]
> インスタンス ID がスケール セット内の VM にどのように割り当てられるかは**一定ではありません**。 番号が順次増分しているように見える場合もありますが、常にそうとは限りません。 インスタンス ID が VM に特定の方法で割り当てられると推測しないでください。

## <a name="scale-set-vm-names"></a>スケール セット VM の名前

上記の出力のように、VM には "name" もあります。 この名前は、"{スケール セット名}_{インスタンス ID}" の形式になります。 この名前は、スケール セット内のインスタンスを一覧表示したときに、Azure ポータルで表示されます。

![](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

名前の {インスタンス ID} 部分は、前述の "instanceId" のプロパティと同じ 10 進数です。

## <a name="instance-metadata-vm-name"></a>Instance Metadata VM の名前

スケール セット VM 内から[インスタンス メタデータ](../virtual-machines/windows/instance-metadata-service.md)を問い合わせると、出力に "name" が含まれます。

```
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

この名前は、前述の名前と同じです。

## <a name="scale-set-vm-computer-name"></a>スケール セット VM のコンピューター名

スケール セット内の各 VM にもコンピューター名が割り当てられます。 このコンピューター名は、[仮想ネットワーク内の Azure によって提供された DNS 名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)による VM のホスト名です。 このコンピューター名は、"{コンピューター名のプレフィックス}{36 進数のインスタンス ID}"の形式になります。

{36 進数のインスタンス ID} は [36 進数](https://en.wikipedia.org/wiki/Base36)で、桁数は常に 6 桁です。 数字の 36 進数表記が 6 桁より少ない桁で表される場合は、{36 進数のインスタンス ID} は 6 桁になるまでゼロが挿入されます。 たとえば、{コンピューター名プレフィックス} "nsgvmss" とインスタンス ID 85 のインスタンスのコンピューター名は "nsgvmss00002D" になります。

>[!NOTE]
> コンピューター名のプレフィックスは、スケール セット モデルのプロパティで、スケール セット名自体と異なるよう設定できます。
