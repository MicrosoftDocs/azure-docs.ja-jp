---
title: Azure の vCPU クォータ | Microsoft Docs
description: Azure の vCPU クォータについて説明します。
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 48da419cdf5b4555e06b2a87d4b6821276777826
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102465"
---
# <a name="virtual-machine-vcpu-quotas"></a>仮想マシンの vCPU クォータ

仮想マシンの vCPU クォータと仮想マシン スケール セットは、各リージョンでサブスクリプションごとに 2 つのレベルで決められています。 1 番目のレベルはリージョンの vCPU の合計で、2 番目のレベルは D シリーズの vCPU などのさまざまな VM サイズ ファミリ コアです。 新しい VM をデプロイするときは常に、VM の vCPU がその VM サイズ ファミリの vCPU クォータ、またはリージョンの vCPU クォータの合計を超えてはなりません。 いずれかのクォータを超えている場合、VM のデプロイは許可されません。 リージョン内の仮想マシンの全体的な数にもクォータがあります。 各クォータの詳細については、[Azure Portal](https://portal.azure.com) の **[サブスクリプション]** ページの **[使用量 + クォータ]** セクションで確認したり、PowerShell を使用して値を照会することができます。

 [!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)] 
 
## <a name="check-usage"></a>使用量を確認する

クォータの使用量を確認するには、[Get-AzVMUsage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmusage) コマンドレットを使用することができます。

```azurepowershell-interactive
Get-AzVMUsage -Location "East US"
```

出力は次のようになります。

```
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional vCPUs                         4   260 Count
Virtual Machines                             4 10000 Count
Virtual Machine Scale Sets                   1  2000 Count
Standard B Family vCPUs                      1    10 Count
Standard DSv2 Family vCPUs                   1   100 Count
Standard Dv2 Family vCPUs                    2   100 Count
Basic A Family vCPUs                         0   100 Count
Standard A0-A7 Family vCPUs                  0   250 Count
Standard A8-A11 Family vCPUs                 0   100 Count
Standard D Family vCPUs                      0   100 Count
Standard G Family vCPUs                      0   100 Count
Standard DS Family vCPUs                     0   100 Count
Standard GS Family vCPUs                     0   100 Count
Standard F Family vCPUs                      0   100 Count
Standard FS Family vCPUs                     0   100 Count
Standard NV Family vCPUs                     0    24 Count
Standard NC Family vCPUs                     0    48 Count
Standard H Family vCPUs                      0     8 Count
Standard Av2 Family vCPUs                    0   100 Count
Standard LS Family vCPUs                     0   100 Count
Standard Dv2 Promo Family vCPUs              0   100 Count
Standard DSv2 Promo Family vCPUs             0   100 Count
Standard MS Family vCPUs                     0     0 Count
Standard Dv3 Family vCPUs                    0   100 Count
Standard DSv3 Family vCPUs                   0   100 Count
Standard Ev3 Family vCPUs                    0   100 Count
Standard ESv3 Family vCPUs                   0   100 Count
Standard FSv2 Family vCPUs                   0   100 Count
Standard ND Family vCPUs                     0     0 Count
Standard NCv2 Family vCPUs                   0     0 Count
Standard NCv3 Family vCPUs                   0     0 Count
Standard LSv2 Family vCPUs                   0     0 Count
Standard Storage Managed Disks               2 10000 Count
Premium Storage Managed Disks                1 10000 Count
```


## <a name="reserved-vm-instances"></a>予約 VM インスタンス
予約 VM インスタンスは、VM サイズの柔軟性はなく、1 つのサブスクリプションを対象とし、vCPU クォータに新しい側面を追加するものです。 これらの値は、そのサブスクリプションにデプロイできる必要がある、規定サイズのインスタンス数を示します。 これらは、確実に予約 VM インスタンスをサブスクリプションにデプロイできるようにするための、クォータ システムのプレースホルダーとして機能します。 たとえば、特定のサブスクリプションに 10 個の Standard_D1 予約 VM インスタンスがある場合、Standard_D1 予約 VM インスタンスの使用量の上限は 10 なります。 これにより、Azure では、Standard_D1 インスタンスで使用される、リージョンの vCPU の合計クォータで利用できる vCPU が常に少なくとも 10 個確保され、Standard_D1 インスタンスで使用される Standard D ファミリ vCPU クォータで利用できる vCPU が少なくとも 10 個確保されます。

単一サブスクリプション RI を購入するためにクォータの引き上げが必要な場合は、そのサブスクリプションの[クォータの引き上げを要求](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)できます。

## <a name="next-steps"></a>次の手順

課金とクォータの詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json)」を参照してください。
