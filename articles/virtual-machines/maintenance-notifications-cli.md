---
title: CLI を使用してメンテナンス通知を取得する
description: Azure で実行されている仮想マシンのメンテナンス通知を表示し、Azure CLI を使用してセルフサービス メンテナンスを開始します。
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 633708219adaba2fb4c4889754b2112fbf3c4180
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87069360"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Azure CLI に対する計画済みメンテナンスの通知の処理

**この記事は、Linux と Windows の両方を実行する仮想マシンに適用されます。**

Azure CLI を使用して、VM の[メンテナンス](maintenance-notifications.md)の予定を確認できます。 計画メンテナンスの情報は、[az vm get-instance-view](/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view) から確認できます。
 
計画メンテナンスがある場合にのみ、メンテナンス情報が返されます。 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
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

## <a name="next-steps"></a>次のステップ

また、[Azure PowerShell](maintenance-notifications-powershell.md) または [portal](maintenance-notifications-portal.md) を使用して、計画メンテナンスを扱うこともできます。
