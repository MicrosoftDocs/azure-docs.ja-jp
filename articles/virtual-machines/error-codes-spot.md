---
title: Azure スポット VM とスケール セットのインスタンスのエラー コード
description: スポット VM とスケール セットのインスタンスを使用する場合に表示される可能性があるエラー コードについて説明します。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 3e151145bd3c4d1db87ef5d1b0d553555a580279
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816747"
---
# <a name="error-messages-for-spot-vms-and-scale-sets"></a>スポット VM とスケール セットのエラー メッセージ

ここでは、スポット VM とスケール セットを使用する場合に受け取る可能性のあるエラー コードを示します。


| Key | Message | 説明 |
|-----|---------|-------------|
| SkuNotAvailable | リソース "\<resource\>" に要求された階層は、場所 "\<location\>" ではサブスクリプション "\<subscriptionID\>" に対して現在使用できません。 別の階層を試すか、別の場所にデプロイしてください。 | この場所には、VM またはスケール セットのインスタンスを作成するための十分な Azure スポットの容量がありません。 |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  排除ポリシーは、Azure Spot Virtual Machines のみで設定できます。 | この VM はスポット VM ではないため、排除ポリシーを設定することはできません。 |
| AzureSpotVMNotSupportedInAvailabilitySet  |  Azure Spot Virtual Machine は、可用性セットではサポートされていません。 | スポット VM を使用するか、可用性セット内の VM を使用するかを選択する必要があります。両方を選択することはできません。 |
| AzureSpotFeatureNotEnabledForSubscription  |  Azure スポット機能でサブスクリプションが有効になっていません。 | スポット VM をサポートするサブスクリプションを使用してください。 |
| VMPriorityCannotBeApplied  |  仮想マシンの作成時に優先順位が指定されていないため、指定された優先順位値 '{0}' を仮想マシン '{1}' に適用できません。 | VM の作成時に優先順位を指定してください。 |
| SpotPriceGreaterThanProvidedMaxPrice  |  指定された最大価格 '{1} 米国ドル' が、Azure スポット VM サイズ '{3}' の現在のスポット価格 '{2} 米国ドル' より低いため、操作 '{0}' を実行できません。 | より高い最大価格を選択してください。 詳細については、[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) または [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) での料金情報を参照してください。|
| MaxPriceValueInvalid  |  最大価格の値が無効です。 最大価格でサポートされている値は、-1 または 0 より大きい小数のみです。 最大価格値 -1 は、Azure スポットの仮想マシンが価格を理由に排除されないことを示します。 | 有効な最大価格を入力してください。 詳細については、[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) または [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) での価格を参照してください。 |
| MaxPriceChangeNotAllowedForAllocatedVMs | VM '{0}' が現在割り当てられている場合、最大価格の変更は許可されません。 割り当てを解除してから、もう一度お試しください。 | VM を停止 / 割り当て解除して、最大価格を変更できるようにします。 |
| MaxPriceChangeNotAllowed | 最大価格の変更は許可されていません。 | この VM の最大価格を変更することはできません。 |
| AzureSpotIsNotSupportedForThisAPIVersion  |  Azure スポットは、この API バージョンではサポートされていません。 | API バージョンは 2019-03-01 である必要があります。 |
| AzureSpotIsNotSupportedForThisVMSize  |  この VM サイズ {0} に対しては Azure スポットはサポートされていません。 | 別の VM サイズを選択してください。 詳細については、[Spot Virtual Machines](./spot-vms.md) に関するページを参照してください。 |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  最大価格は、Azure Spot Virtual Machines のみでサポートされています。 | 詳細については、[Spot Virtual Machines](./spot-vms.md) に関するページを参照してください。 |
| MoveResourcesWithAzureSpotVMNotSupported  |  リソースの移動要求に、Azure スポット仮想マシンが含まれています。 現在これはサポートされていません。 仮想マシン ID のエラーの詳細を確認してください。 | スポット VM を移動することはできません。 |
| MoveResourcesWithAzureSpotVmssNotSupported  |  リソースの移動要求に、Azure スポット仮想マシン スケール セットが含まれています。 現在これはサポートされていません。 仮想マシン スケール セット ID のエラーの詳細を確認してください。 | スポット スケール セットのインスタンスを移動することはできません。 |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | VM オーケストレーション モードの仮想マシン スケール セットでは、Azure Spot Virtual Machine はサポートされていません。 | スポット インスタンスを使用するには、オーケストレーション モードを仮想マシン スケール セットに設定します。 |


**次の手順** の詳細については、「[Spot Virtual Machines](./spot-vms.md)」を参照してください。