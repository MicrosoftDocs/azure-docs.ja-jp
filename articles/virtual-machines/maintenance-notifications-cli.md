---
title: CLI を使用してメンテナンス通知を取得する
description: Azure で実行されている仮想マシンのメンテナンス通知を表示し、Azure CLI を使用してセルフサービス メンテナンスを開始します。
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: cd042ce09533cbefe37cb2e4d311a3857e3dfdec
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552407"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Azure CLI に対する計画済みメンテナンスの通知の処理

**この記事は、Linux と Windows の両方を実行する仮想マシンに適用されます。**

Azure CLI を使用して、VM の[メンテナンス](maintenance-notifications.md)の予定を確認できます。 計画メンテナンスの情報は、[az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view) から確認できます。
 
計画メンテナンスがある場合にのみ、メンテナンス情報が返されます。 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

出力
```
      "maintenanceRedeployStatus": {
      "additionalProperties": {},
      "isCustomerInitiatedMaintenanceAllowed": true,
      "lastOperationMessage": null,
      "lastOperationResultCode": "None",
      "maintenanceWindowEndTime": "2018-06-04T16:30:00+00:00",
      "maintenanceWindowStartTime": "2018-05-21T16:30:00+00:00",
      "preMaintenanceWindowEndTime": "2018-05-19T12:30:00+00:00",
      "preMaintenanceWindowStartTime": "2018-05-14T12:30:00+00:00"
```

## <a name="start-maintenance"></a>メンテナンスを開始する

`IsCustomerInitiatedMaintenanceAllowed` が true に設定されている場合、次の呼び出しによって VM に対するメンテナンスが開始されます。

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>クラシック デプロイ

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

クラシック デプロイ モデルを使用してデプロイされたレガシ VM がまだある場合は、Azure クラシック CLI を使用して、VM を照会し、メンテナンスを開始できます。

次のように入力して、クラッシック VM を操作する正しいモードになっていることを確認します。

```
azure config mode asm
```

*myVM* という名前の VM のメンテナンスの状態を取得するには、次のように入力します。

```
azure vm show myVM 
``` 

*myService* サービスおよび *myDeployment* デプロイの *myVM* という名前のクラシック VM でメンテナンスを開始するには、次のように入力します。

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>次の手順

また、[Azure PowerShell](maintenance-notifications-powershell.md) または [portal](maintenance-notifications-portal.md) を使用して、計画メンテナンスを扱うこともできます。
