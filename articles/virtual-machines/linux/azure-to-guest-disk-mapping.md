---
title: Azure ディスクを Linux VM のゲスト ディスクにマップする方法
description: Linux VM のゲスト ディスクの土台となる Azure ディスクを特定する方法を説明します。
author: timbasham
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.collection: linux
ms.openlocfilehash: bc6c6273ab3d1a4403763e4ed0a8c491995fb2df
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556725"
---
# <a name="how-to-map-azure-disks-to-linux-vm-guest-disks"></a>Azure ディスクを Linux VM のゲスト ディスクにマップする方法

VM のゲスト ディスクをバックアップする Azure ディスクの特定が必要になる場合があります。 場合によっては、ディスクまたはボリュームのサイズを、接続されている Azure ディスクのサイズと比較することができます。 同じサイズの複数の Azure ディスクが VM に接続されているシナリオでは、データ ディスクの論理ユニット番号 (LUN) を使用する必要があります。 

## <a name="what-is-a-lun"></a>LUN について

論理ユニット番号 (LUN) は、特定のストレージ デバイスを識別するために使用される番号です。 各ストレージ デバイスには、0 から始まる一意の数値識別子が割り当てられます。 デバイスの完全なパスは、バス番号、ターゲット ID 番号、および論理ユニット番号 (LUN) で表されます。 

次に例を示します。***バス番号 0、ターゲット ID 0、LUN 3***

この演習では、LUN を使用するだけで十分です。

## <a name="finding-the-lun"></a>LUN の検索

Linux でディスクの LUN を検索する 2 つの方法を次に示します。

### <a name="lsscsi"></a>lsscsi

1. VM に接続します
1. `sudo lsscsi`

リストされる最初の列に LUN が示されます。形式は [Host:Channel:Target:**LUN**] です。

### <a name="listing-block-devices"></a>ブロック デバイスのリスト

1. VM に接続します
1. `sudo ls -l /sys/block/*/device`

リストされる最後の列に LUN が示されます。形式は [Host:Channel:Target:**LUN**] です。

## <a name="finding-the-lun-for-the-azure-disks"></a>Azure ディスクの LUN の検索

Azure portal、Azure CLI を使用して、Azure ディスクの LUN を見つけることができます。

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Azure portal での Azure ディスクの LUN の検索

1. Azure portal で [仮想マシン] を選択して、仮想マシンの一覧を表示します。
1. 仮想マシンを選択します
1. [ディスク] を選択します。
1. 接続されているディスクの一覧からデータ ディスクを選択します。
1. ディスクの LUN がディスクの詳細ウィンドウに表示されます。 ここに表示される LUN は、lsscsi を使用して、またはブロック デバイスのリストによってゲストで検索した LUN に関連付けられます。

### <a name="finding-an-azure-disks-lun-using-azure-cli"></a>Azure CLI を使用した Azure ディスクの LUN の検索

```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```
