---
title: Azure 仮想マシン スケール セットの接続されたデータ ディスク | Microsoft Docs
description: 接続されたデータ ディスクを仮想マシン スケール セットで使用する方法について説明します。
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 4/25/2017
ms.author: manayar
ms.openlocfilehash: 5482e082e3e37d279c4374a8642f2cb6db588dd5
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57781665"
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Azure 仮想マシン スケール セットと接続されたデータ ディスク
[Virtual Machine Scale Sets](/azure/virtual-machine-scale-sets/) では、使用できるストレージを拡張するために、データ ディスクをアタッチした VM インスタンスがサポートされています。 新たに作成するスケール セットや既存のスケール セットにデータ ディスクをアタッチすることができます。

> [!NOTE]
> データ ディスクがアタッチされたスケール セットを作成する場合、(スタンドアロン型の Azure VM と同様に) ディスクを使用する VM 内からディスクをマウントおよびフォーマットする必要があります。 このプロセスを完了するには、スクリプトを呼び出して VM 上のすべてのデータ ディスクをパーティション化およびフォーマットするカスタム スクリプト拡張機能を使用する方法が便利です。 詳細については、[Azure CLI](tutorial-use-disks-cli.md#prepare-the-data-disks) または [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks) の例を参照してください。


## <a name="create-and-manage-disks-in-a-scale-set"></a>スケール セットのディスクを作成、管理する
データ ディスクがアタッチされたスケール セットの作成、データ ディスクの準備とフォーマット、データ ディスクの追加と削除の方法について詳しくは、次のいずれかのチュートリアルを参照してください。

- [Azure CLI](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

以降この記事では、データ ディスクを必要とする Service Fabric クラスターや、既に内容を含んだデータ ディスクをスケール セットにアタッチする方法など、特定のユース ケースについて概説します。


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>データ ディスクをアタッチして Service Fabric クラスターを作成する
Azure で実行されている [Service Fabric](/azure/service-fabric) クラスター内の各[ノード タイプ](../service-fabric/service-fabric-cluster-nodetypes.md)は、仮想マシン スケール セットによって提供されます。 Azure Resource Manager テンプレートを使用して、Service Fabric クラスターを構成するスケール セットにデータ ディスクを接続できます。 [既存のテンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates)を出発点として使用できます。 テンプレートで _Microsoft.Compute/virtualMachineScaleSets_ リソースの _storageProfile_ 内に _dataDisks_ セクションを追加して、そのテンプレートをデプロイします。 次の例では、128 GB のデータ ディスクがアタッチされます。

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
スケール セット モデルで指定されているデータ ディスクは常に空です。 ただし、スケール セット内の特定の VM に、既存のデータ ディスクを接続することができます。 この機能はプレビュー段階であり、[GitHub](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk) に例があります。 スケール セット内のすべての VM にデータを反映したい場合は、データ ディスクを複製してスケール セット内の各 VM にそれを接続すること、データを含むカスタム イメージを作成してこのカスタム イメージからスケール セットをプロビジョニングすること、または Azure Files や同様のデータ ストレージ オファーを使用することができます。


## <a name="additional-notes"></a>その他のメモ
Azure マネージド ディスクと、スケール セットに接続されたデータ ディスクのサポートは、Microsoft.Compute API のバージョン [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/compute/resource-manager/Microsoft.Compute/preview/2016-04-30-preview/compute.json) 以降で使用できます。

スケール セット内の接続されたデータ ディスクに対する Azure Portal でのサポートは、最初は制限されています。 要件に応じて、Azure テンプレート、CLI、PowerShell、SDK、および REST API を使用して、接続されたディスクを管理できます。


