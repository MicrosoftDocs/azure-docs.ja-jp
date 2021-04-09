---
title: Azure 仮想マシン スケール セットの接続されたデータ ディスク
description: 特定のユース ケースの概要を通して、接続されたデータ ディスクを仮想マシン スケール セットで使用する方法について説明します。
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 4/25/2017
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 9e4bdf868d3f8ddf3a049509ead30a4b1ba341b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86527440"
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Azure 仮想マシン スケール セットと接続されたデータ ディスク
[Virtual Machine Scale Sets](./index.yml) では、使用できるストレージを拡張するために、データ ディスクをアタッチした VM インスタンスがサポートされています。 新たに作成するスケール セットや既存のスケール セットにデータ ディスクをアタッチすることができます。

> [!NOTE]
> データ ディスクがアタッチされたスケール セットを作成する場合、(スタンドアロン型の Azure VM と同様に) ディスクを使用する VM 内からディスクをマウントおよびフォーマットする必要があります。 このプロセスを完了するには、スクリプトを呼び出して VM 上のすべてのデータ ディスクをパーティション化およびフォーマットするカスタム スクリプト拡張機能を使用する方法が便利です。 これの例については、[Azure CLI または ](tutorial-use-disks-cli.md#prepare-the-data-disks) [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks) を参照してください。


## <a name="create-and-manage-disks-in-a-scale-set"></a>スケール セットのディスクを作成、管理する
データ ディスクがアタッチされたスケール セットの作成、データ ディスクの準備とフォーマット、データ ディスクの追加と削除の方法について詳しくは、次のいずれかのチュートリアルを参照してください。

- [Azure CLI](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

以降この記事では、データ ディスクを必要とする Service Fabric クラスターや、既に内容を含んだデータ ディスクをスケール セットにアタッチする方法など、特定のユース ケースについて概説します。


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>データ ディスクをアタッチして Service Fabric クラスターを作成する
Azure で実行されている [Service Fabric](../service-fabric/index.yml) クラスター内の各[ノード タイプ](../service-fabric/service-fabric-cluster-nodetypes.md)は、仮想マシン スケール セットによって提供されます。 Azure Resource Manager テンプレートを使用して、Service Fabric クラスターを構成するスケール セットにデータ ディスクを接続できます。 [既存のテンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates)を出発点として使用できます。 テンプレートで _Microsoft.Compute/virtualMachineScaleSets_ リソースの _storageProfile_ 内に _dataDisks_ セクションを追加して、そのテンプレートをデプロイします。 次の例では、128 GB のデータ ディスクがアタッチされます。

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

クラスターがデプロイされるときに、データ ディスクのパーティション、フォーマット、マウントを自動的に行えます。 カスタム スクリプト拡張機能をスケール セットの _virtualMachineProfile_ の _extensionProfile_ に追加します。

Windows クラスターでデータ ディスクを自動的に準備するには、以下を追加します。

```json
{
    "name": "customScript",
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.8",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1"
        ],
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
        }
    }
}
```
Linux クラスターでデータ ディスクを自動的に準備するには、以下を追加します。
```json
{
    "name": "lapextension",
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"
        ],
        "commandToExecute": "bash prepare_vm_disks.sh"
        }
    }
}
```


## <a name="adding-pre-populated-data-disks-to-an-existing-scale-set"></a>データ投入済みディスクを既存のスケール セットに追加する
スケール セット モデルで指定されているデータ ディスクは常に空です。 ただし、スケール セット内の特定の VM に、既存のデータ ディスクを接続することができます。 スケール セット内のすべての VM にデータを伝達したい場合は、データ ディスクを複製してスケール セット内の各 VM にそれを接続するか、データを含むカスタム イメージを作成してこのカスタム イメージからスケール セットをプロビジョニングする、または、 Azure Files や同様のデータ ストレージ オファーを使用することができます。


## <a name="additional-notes"></a>その他のメモ
Azure マネージド ディスクと、スケール セットに接続されたデータ ディスクのサポートは、Microsoft.Compute API のバージョン [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/compute/resource-manager/Microsoft.Compute/preview/2016-04-30-preview/compute.json) 以降で使用できます。

スケール セット内の接続されたデータ ディスクに対する Microsoft Azure Portal でのサポートは限定的です。 要件に応じて、Azure テンプレート、CLI、PowerShell、SDK、および REST API を使用して、接続されたディスクを管理できます。
