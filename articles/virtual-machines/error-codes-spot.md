---
title: Azure Spot Virtual Machines とスケール セットのインスタンスのエラー コード
description: Azure Spot Virtual Machines とスケール セットのインスタンスを使用する場合に表示される可能性があるエラー コードについて説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 6d43935365580233063deb0e523d222351c22d54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670620"
---
# <a name="error-messages-for-azure-spot-virtual-machines-and-scale-sets"></a>Azure Spot Virtual Machines とスケール セットのエラー メッセージ

ここでは、Azure Spot Virtual Machines とスケール セットを使用する場合に受け取る可能性のあるエラー コードを示します。


| Key | Message | 説明 |
|-----|---------|-------------|
| SkuNotAvailable | リソース "\<resource\>" に要求された階層は、場所 "\<location\>" ではサブスクリプション "\<subscriptionID\>" に対して現在使用できません。 別の階層を試すか、別の場所にデプロイしてください。 | この場所には、VM またはスケール セットのインスタンスを作成するための十分な Azure Spot Virtual Machine の容量がありません。 |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  排除ポリシーは、Azure Spot Virtual Machines のみで設定できます。 | この VM は Azure Spot Virtual Machine ではないため、削除ポリシーを設定することはできません。 |
| AzureSpotVMNotSupportedInAvailabilitySet  |  Azure Spot Virtual Machine は、可用性セットではサポートされていません。 | Azure Spot Virtual Machine を使用するか、可用性セット内の VM を使用するかを選択する必要があります。両方を選択することはできません。 |
| AzureSpotFeatureNotEnabledForSubscription  |  Azure Spot Virtual Machine 機能でサブスクリプションが有効になっていません。 | Azure Spot Virtual Machines をサポートするサブスクリプションを使用してください。 |
| VMPriorityCannotBeApplied  |  仮想マシンの作成時に優先順位が指定されていないため、指定された優先順位値 '{0}' を仮想マシン '{1}' に適用できません。 | VM の作成時に優先順位を指定してください。 |
| SpotPriceGreaterThanProvidedMaxPrice  |  指定された最大価格 "{1} 米国ドル" が、Azure Spot Virtual Machine サイズ "{3}" の現在のスポット価格 "{2} 米国ドル" より低いため、操作 "{0}" を実行できません。 | より高い最大価格を選択してください。 詳細については、[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) または [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) での料金情報を参照してください。|
| MaxPriceValueInvalid  |  最大価格の値が無効です。 最大価格でサポートされている値は、-1 または 0 より大きい小数のみです。 最大価格値 -1 は、Azure Spot Virtual Machine が価格を理由に削除されないことを示します。 | 有効な最大価格を入力してください。 詳細については、[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) または [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) での価格を参照してください。 |
| MaxPriceChangeNotAllowedForAllocatedVMs | VM '{0}' が現在割り当てられている場合、最大価格の変更は許可されません。 割り当てを解除してから、もう一度お試しください。 | VM を停止 / 割り当て解除して、最大価格を変更できるようにします。 |
| MaxPriceChangeNotAllowed | 最大価格の変更は許可されていません。 | この VM の最大価格を変更することはできません。 |
| AzureSpotIsNotSupportedForThisAPIVersion  |  Azure Spot Virtual Machine は、この API バージョンではサポートされていません。 | API バージョンは 2019-03-01 である必要があります。 |
| AzureSpotIsNotSupportedForThisVMSize  |  この VM サイズ {0} に対しては Azure Spot Virtual Machine はサポートされていません。 | 別の VM サイズを選択してください。 詳細については、[Azure Spot Virtual Machines](./spot-vms.md) に関するページを参照してください。 |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  最大価格は、Azure Spot Virtual Machines のみでサポートされています。 | 詳細については、[Azure Spot Virtual Machines](./spot-vms.md) に関するページを参照してください。 |
| MoveResourcesWithAzureSpotVMNotSupported  |  リソースの移動要求に、Azure Spot Virtual Machine が含まれています。 サポートされていません。 仮想マシン ID のエラーの詳細を確認してください。 | Azure Spot Virtual Machines を移動することはできません。 |
| MoveResourcesWithAzureSpotVmssNotSupported  |  リソースの移動要求に、Azure スポット仮想マシン スケール セットが含まれています。 サポートされていません。 仮想マシン スケール セット ID のエラーの詳細を確認してください。 | Azure スポット仮想マシン スケール セット インスタンスを移動することはできません。 |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | VM オーケストレーション モードの仮想マシン スケール セットでは、Azure Spot Virtual Machine はサポートされていません。 | Azure Spot Virtual Machine インスタンスを使用するには、オーケストレーション モードを仮想マシン スケール セットに設定します。 |


**次の手順** の詳細については、「[Spot Virtual Machines](./spot-vms.md)」を参照してください。