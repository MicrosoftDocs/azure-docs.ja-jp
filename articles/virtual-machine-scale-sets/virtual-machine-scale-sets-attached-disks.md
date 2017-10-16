---
title: "Azure 仮想マシン スケール セットの接続されたデータ ディスク | Microsoft Docs"
description: "接続されたデータ ディスクを仮想マシン スケール セットで使用する方法について説明します。"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/25/2017
ms.author: guybo
ms.openlocfilehash: 22c7e589efa9a9f401549ec9b95c58c4eaf07b94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-vm-scale-sets-and-attached-data-disks"></a>Azure VM スケール セットと接続されたデータ ディスク
Azure [仮想マシン スケール セット](/azure/virtual-machine-scale-sets/)で、接続されたデータ ディスクを備えた仮想マシンがサポートされるようになりました。 データ ディスクは、Azure Managed Disks で作成したスケール セットのストレージ プロファイルに定義できます。 以前は、スケール セット内の VM で使用できる直接接続されたストレージのオプションは、OS ドライブと一時ドライブだけでした。

> [!NOTE]
>  接続されたデータ ディスクが定義されたスケール セットを作成する場合、(スタンドアロン型の Azure VM と同様に) ディスクを使用する VM 内からディスクをマウントおよびフォーマットする必要があります。 これを行う便利な方法は、標準スクリプトを呼び出して VM 上のすべてのデータ ディスクをパーティション化およびフォーマットするカスタム スクリプト拡張機能を使用することです。

## <a name="create-a-scale-set-with-attached-data-disks"></a>接続されたデータ ディスクを備えたスケール セットの作成
接続されたディスクを備えたスケール セットを簡単に作成する方法の 1 つに、[Azure CLI](https://github.com/Azure/azure-cli) の _vmss create_ コマンドを使う方法があります。 次の例では、Azure リソース グループと、それぞれ 50 GB と 100 GB の 2 つの接続されたデータ ディスクを備えた 10 台の Ubuntu VM の VM スケール セットを作成します。
```bash
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```
_vmss create_ コマンドでは、構成値を指定しない場合に既定で特定の構成値が使用されます。 上書きできる使用可能なオプションを表示するには、次のように入力します。
```bash
az vmss create --help
```
接続されたデータ ディスクを備えたスケール セットを作成するもう 1 つの方法として、Azure Resource Manager テンプレートにスケール セットを定義し、_storageProfile_ に _dataDisks_ セクションを含め、テンプレートをデプロイする方法があります。 上記の 50 GB および 100 GB のディスクの例をテンプレートで定義するには、次のように記述します。
```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    }
]
```
[https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data](https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data) で、接続されたディスクが定義された、すぐにデプロイできる完全版のスケール セット テンプレートの例を確認できます。

## <a name="adding-a-data-disk-to-an-existing-scale-set"></a>既存のスケール セットへのデータ ディスクの追加
> [!NOTE]
>  データ ディスクは、[Azure Managed Disks](./virtual-machine-scale-sets-managed-disks.md) で作成されたスケール セットにのみアタッチできます。

Azure CLI の _az vmss disk attach_ コマンドを使うと、データ ディスクを VM スケール セットに追加することができます。 まだ使用されていない LUN を指定していることを確認します。 次の CLI の例では、50 GB のドライブを LUN 3 に追加しています。
```bash
az vmss disk attach -g dsktest -n dskvmss --size-gb 50 --lun 3
```

次の PowerShell の例では、50 GB のドライブを LUN 3 に追加しています。
```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myvmssrg -VMScaleSetName myvmss
$vmss = Add-AzureRmVmssDataDisk -VirtualMachineScaleSet $vmss -Lun 3 -Caching 'ReadWrite' -CreateOption Empty -DiskSizeGB 50 -StorageAccountType StandardLRS
Update-AzureRmVmss -ResourceGroupName myvmssrg -Name myvmss -VirtualMachineScaleSet $vmss
```

> [!NOTE]
> VM サイズが異なると、サポートされる接続されたドライブの数に関する制限も異なります。 新しいディスクを追加する前に、[仮想マシンのサイズ特性](../virtual-machines/windows/sizes.md)を確認してください。

また、スケール セット定義の _storageProfile_ の _dataDisks_ プロパティに新しいエントリを追加し、その変更を適用してディスクを追加する方法もあります。 これをテストするには、[Azure リソース エクスプローラー](https://resources.azure.com/)で既存のスケール セット定義を見つけます。 _[編集]_ を選択し、データ ディスクの一覧に新しいディスクを追加します。 例: ここでは、上の例を使用しています。
```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    },
    {
    "lun": 3,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 20
    }          
]
```

次に、_[PUT]_ を選択して、スケール セットに変更を適用します。 この例は、3 台以上の接続されたデータ ディスクがサポートされる VM サイズを使用している場合に有効です。

> [!NOTE]
> データ ディスクを追加または削除するなど、スケール セット定義に変更を加えた場合、変更された定義は新しく作成されるすべての VM に適用されます。既存の VM に対しては、_upgradePolicy_ プロパティが "Automatic" に設定されている場合にのみ、変更された定義が適用されます。 このプロパティが "Manual" に設定されている場合は、新しいモデルを既存の VM に手動で適用する必要があります。 これをポータルで実行するには、_Update-AzureRmVmssInstance_ PowerShell コマンドを使用するか、_az vmss update-instances_ CLI コマンドを使用します。

## <a name="adding-pre-populated-data-disks-to-an-existent-scale-set"></a>データ投入済みディスクを現行のスケール セットに追加する 
> 現行のスケール セット モデルにディスクを追加すると、そのディスクは常時、意図的に空の状態で作成されます。 このシナリオには、スケール セットによって作成される新しいインスタンスも含まれます。 この振る舞いの理由は、スケールセット定義に空のデータ ディスクが存在するためです。 現行のスケール セット モデル用にデータ投入済みドライブを作成するには、次の 2 とおりの方法があります。

* カスタム スクリプトを実行してインスタンス 0 の VM から他の VM のデータ ディスクにデータをコピーする。
* OS ディスクと (必要なデータが格納された) データ ディスクとを含んだマネージ イメージを作成し、そのイメージを使って新しいスケールセットを作成する。 そうすることで、そのスケールセットの定義に指定されたデータ ディスクが、新たに作成されるすべての VM に割り当てられるようになります。 この定義によって参照されるイメージには、カスタマイズされたデータを格納したデータ ディスクが含まれるため、そのスケールセットの各仮想マシンは自動的に、それらの変更が反映された状態で作成されます。

> カスタム イメージを作成する方法については、「[Azure で一般化された VM の管理対象イメージを作成する](/azure/virtual-machines/windows/capture-image-resource/)」を参照してください。 

> 必要なデータを含んだインスタンス 0 の VM をユーザーがキャプチャしたうえで、その vhd をイメージの定義に使用する必要があります。

## <a name="removing-a-data-disk-from-a-scale-set"></a>スケール セットからのデータ ディスクの削除
Azure CLI の _az vmss disk detach_ コマンドを使用して、データ ディスクを VM スケール セットから削除することができます。 たとえば、次のコマンドを実行すると、LUN 2 で定義されているディスクが削除されます。
```bash
az vmss disk detach -g dsktest -n dskvmss --lun 2
```  
同様に、_storageProfile_ 内の _dataDisks_ プロパティからエントリを削除し、その変更を適用することによって、スケール セットからディスクを削除することもできます。 

## <a name="additional-notes"></a>その他のメモ
Azure Managed Disks と、スケール セットに接続されたデータ ディスクのサポートは、Microsoft.Compute API のバージョン [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) 以降で使用できます。

スケール セットに対する接続されたディスクのサポートの初期の実装では、スケール セット内の個々の VM に対し、データ の接続や接続解除を行うことはできません。

スケール セット内の接続されたデータ ディスクに対する Azure Portal でのサポートは、最初は制限されています。 要件に応じて、Azure テンプレート、CLI、PowerShell、SDK、および REST API を使用して、接続されたディスクを管理できます。


