---
title: CLI を使用した Azure 仮想マシンのメンテナンス コントロール
description: メンテナンス コントロールと CLI を使用して、ご利用の Azure VM にメンテナンスを適用するタイミングを制御する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/20/2020
ms.author: cynthn
ms.openlocfilehash: 9425759de1e08bc83cac80cd1b56c602edb59fb1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562964"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-cli"></a>メンテナンス コントロールと Azure CLI を使用して更新を制御する

メンテナンス コントロールを使用すると、分離された VM や Azure 専用ホストのホスト インフラストラクチャにプラットフォームの更新プログラムを適用するタイミングを決定できます。 このトピックでは、メンテナンス コントロール用の Azure CLI オプションについて説明します。 メンテナンス コントロールを使用する利点、その制限、およびその他の管理オプションの詳細については、[メンテナンス コントロールを使用したプラットフォーム更新プログラムの管理](maintenance-control.md)に関する記事を参照してください。

## <a name="create-a-maintenance-configuration"></a>メンテナンス構成を作成する

`az maintenance configuration create` を使用して、メンテナンス構成を作成します。 この例では、ホストを対象とした *myConfig* という名前のメンテナンス構成を作成します。 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope host\
   --location eastus
```

後で使用するために、出力から構成 ID をコピーします。

`--maintenance-scope host` を使用して、そのメンテナンス構成が、ホスト インフラストラクチャに対する更新をコントロールするために確実に使用されているか確認します。

同じ名前の構成を別の場所に作成しようとすると、エラーが発生します。 構成名は、リソース グループに対して一意である必要があります。

`az maintenance configuration list` を使用すると、使用可能なメンテナンス構成に対してクエリを実行できます。

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>日程計画された期間でメンテナンス構成を作成する
Azure でリソースに更新プログラムを適用する日程計画された期間を宣言することもできます。 この例では、毎月第 4 月曜日に 5 時間という日程計画で myConfig という名前のメンテナンス構成が作成されます。 日程計画された期間を作成すると、更新プログラムを手動で適用する必要がなくなります。

```azurecli-interactive
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope host \
   --location eastus \
   --maintenance-window-duration "05:00" \
   --maintenance-window-recur-every "Month Fourth Monday" \
   --maintenance-window-start-date-time "2020-12-30 08:00" \
   --maintenance-window-time-zone "Pacific Standard Time"
```

> [!IMPORTANT]
> メンテナンスの **期間** は、"*2 時間*" 以上である必要があります。 メンテナンスの **繰り返し** は少なくとも 35 日間に 1 回に行われるように設定する必要があります。

メンテナンスの繰り返しは、日、週、月単位で表すことができます。 いくつかの例を次に示します。
- **daily**- maintenance-window-recur-every:"Day" **または** "3Days"
- **weekly**- maintenance-window-recur-every:"3Weeks" **または** "Week Saturday,Sunday"
- **monthly**- maintenance-window-recur-every:"Month day23,day24" **または** "Month Last Sunday" **または** "Month Fourth Monday"


## <a name="assign-the-configuration"></a>構成を割り当てる

分離された VM または Azure Dedicated Host に構成を割り当てるには、`az maintenance assignment create` を使用します。

### <a name="isolated-vm"></a>分離された VM

構成の ID を使用して、構成を VM に適用します。 `--resource-type virtualMachines` を指定し、`--resource-name` には VM の名前、`--resource-group` には VM のリソース グループ、`--location` には VM の場所を指定します。 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig"
```

### <a name="dedicated-host"></a>専用ホスト

専用ホストに構成を適用するには、`--resource-type hosts` と `--resource-parent-name` を含める必要があります。これらには、ホスト グループの名前と `--resource-parent-type hostGroups` を使用します。 

パラメーター `--resource-id` は、ホストの ID です。 [az vm host get-instance-view](/cli/azure/vm/host#az-vm-host-get-instance-view) を使用すると、専用ホストの ID を取得できます。

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## <a name="check-configuration"></a>構成を確認する

`az maintenance assignment list` を使用することにより、構成が正しく適用されていることを確認したり、現在適用されている構成を確認したりできます。

### <a name="isolated-vm"></a>分離された VM

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### <a name="dedicated-host"></a>専用ホスト 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## <a name="check-for-pending-updates"></a>保留中の更新プログラムを確認する

`az maintenance update list` を使用すると、保留中の更新プログラムがあるかどうかを確認できます。 --subscription が、その VM を含むサブスクリプションの ID になるように更新します。

更新プログラムが存在しない場合、コマンドは "`Resource not found...StatusCode: 404`" というテキストを含むエラー メッセージを返します。

更新プログラムがある場合は、保留中の更新プログラムが複数あるときでも、1 つだけ返されます。 以下に示すように、この更新プログラムのデータはオブジェクトで返されます。

```text
[
  {
    "impactDurationInSec": 9,
    "impactType": "Freeze",
    "maintenanceScope": "Host",
    "notBefore": "2020-03-03T07:23:04.905538+00:00",
    "resourceId": "/subscriptions/9120c5ff-e78e-4bd0-b29f-75c19cadd078/resourcegroups/DemoRG/providers/Microsoft.Compute/hostGroups/demoHostGroup/hosts/myHost",
    "status": "Pending"
  }
]
  ```

### <a name="isolated-vm"></a>分離された VM

分離された VM の保留中の更新プログラムを確認します。 この例では、読みやすくするために出力を表形式で表示しています。

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>専用ホスト

専用ホストの保留中の更新プログラムを確認します。 この例では、読みやすくするために出力を表形式で表示しています。 リソースの値は実際の値に置き換えてください。

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## <a name="apply-updates"></a>更新プログラムの適用

`az maintenance apply update` を使用して、保留中の更新プログラムを適用します。 成功すると、このコマンドは更新プログラムの詳細を含む JSON を返します。

### <a name="isolated-vm"></a>分離された VM

分離された VM に更新プログラムを適用する要求を作成します。

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>専用ホスト

専用ホストに更新プログラムを適用します。

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>更新プログラムの適用状態を確認する 

`az maintenance applyupdate get` を使用すると、更新プログラムの進行状況を確認できます。 

更新プログラムの名前に `default` を使用して、最後の更新プログラムの結果を確認したり、`az maintenance applyupdate create` を実行したときに返された更新プログラムの名前で `myUpdateName` を置き換えたりすることができます。

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime は、更新が完了した時刻になります。これは、ユーザーによって開始されたか、またはセルフ メンテナンス ウィンドウが使用されなかった場合にプラットフォームによって開始されました。 今までメンテナンス コントロールによって更新プログラムが適用されたことがない場合は、既定値が表示されます。

### <a name="isolated-vm"></a>分離された VM

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name default 
```

### <a name="dedicated-host"></a>専用ホスト

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name myUpdateName \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>メンテナンス構成を削除する

メンテナンス構成を削除するには、`az maintenance configuration delete` を使用します。 構成を削除すると、関連付けられているリソースからそのメンテナンス コントロールが削除されます。

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --resource-name myConfig
```

## <a name="next-steps"></a>次のステップ
詳細については、[メンテナンスと更新プログラム](maintenance-and-updates.md)に関するページを参照してください。
