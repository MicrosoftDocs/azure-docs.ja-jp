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
ms.date: 2/6/2017
ms.author: guybo
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 91d36d5321f455a2af31093fa460ddf6640942d4
ms.lasthandoff: 03/31/2017


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
Azure CLI の _az vmss disk attach_ コマンドを使うと、データ ディスクを VM スケール セットに追加することができます。 まだ使用されていない LUN を指定していることを確認します。 次の CLI の例では、50 GB のドライブを LUN 3 に追加しています。
```bash
az vmss disk attach -g dsktest -n dskvmss --size-gb 50 --lun 3
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

## <a name="removing-a-data-disk-from-a-scale-set"></a>スケール セットからのデータ ディスクの削除
Azure CLI の _az vmss disk detach_ コマンドを使用して、データ ディスクを VM スケール セットから削除することができます。 たとえば、次のコマンドを実行すると、LUN 2 で定義されているディスクが削除されます。
```bash
az vmss disk detach -g dsktest -n dskvmss --lun 2
```  
同様に、_storageProfile_ 内の _dataDisks_ プロパティからエントリを削除し、その変更を適用することによって、スケール セットからディスクを削除することもできます。 

## <a name="additional-notes"></a>その他のメモ
Azure Managed Disks と、スケール セットに接続されたデータ ディスクのサポートは、Microsoft.Compute API の [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) バージョンで追加されました。 このバージョン以降の API で構築された任意の SDK またはコマンドライン ツールを使用できます。

スケール セットに対する接続されたディスクのサポートの初期の実装では、スケール セット内の個々の VM に対し、データ の接続や接続解除を行うことはできません。

スケール セット内の接続されたデータ ディスクに対する Azure Portal でのサポートは、最初は制限されています。 要件に応じて、Azure テンプレート、CLI、PowerShell、SDK、および REST API を使用して、接続されたディスクを管理できます。



