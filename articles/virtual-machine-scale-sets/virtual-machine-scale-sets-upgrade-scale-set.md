---
title: "仮想マシン スケール セットでのアプリのデプロイ | Microsoft Docs"
description: "仮想マシン スケール セットでアプリをデプロイします。"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2016
ms.author: guybo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a5158c51149e75952eaf91af14f3fcf2dd1ed2af


---
# <a name="upgrade-a-virtual-machine-scale-set"></a>仮想マシン スケール セットのアップグレード
この記事では、ダウンタイムなしで Azure 仮想マシン スケール セットに OS の更新を展開する方法について説明します。 ここでは、OS の更新により、OS のバージョン、OS の SKU、またはカスタム イメージの URI が変更されます。 ダウンタイムのない更新とは、すべての仮想マシンを一度に更新するのではなく、一度に 1 つずつ、またはグループごとに更新する (一度に 1 つの障害ドメインなど) ということです。 そうすることで、アップグレード中ではない仮想マシンを実行し続けることができます。

あいまいにならないように、3 種類の OS の更新を区別してみましょう。

* プラットフォーム イメージのバージョンまたは SKU を変更します。 たとえば、Ubuntu 14.04.2-LTS バージョンを 14.04.201506100 から 14.04.201507060 に変更、または Ubuntu 15.10/最新 SKU を 16.04.0-LTS/最新に変更します。 この記事では、このシナリオを説明します。
* 構築するカスタム イメージの新しいバージョンを示す URI を変更します (**properties** > **virtualMachineProfile** > **storageProfile** > **osDisk** > **image** > **uri**)。 この記事では、このシナリオを説明します。
* 仮想マシン内から OS を更新します (この例には、セキュリティ更新プログラムのインストールと Windows Update の実行が含まれます)。 このシナリオはサポートされますが、この記事では説明されていません。

最初の 2 つのオプションでは、この記事で扱う要件はサポートされています。 3 つ目のオプションを実行するには、新しいスケール セットを作成する必要があります。

[Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) クラスターの一部としてデプロイされる仮想マシン スケール セットについては、ここでは説明しません。

プラットフォーム イメージの OS バージョンまたは SKU、あるいはカスタム イメージの URI を変更するための基本的なシーケンスは次のようになります。

1. 仮想マシン スケール セットのモデルを取得します。
2. モデル内のバージョン、SKU、または URI 値を変更します。
3. モデルを更新します。
4. スケール セット内の仮想マシンで *manualUpgrade* の呼び出しを実行します。 この手順は、スケール セットの *upgradePolicy* が **Manual** に設定されている場合にのみ有効です。 **Automatic**に設定されている場合は、すべての仮想マシンがすぐにアップグレードされ、ダウンタイムが発生します。

このことを認識したうえで、REST API を使用して PowerShell のスケール セットのバージョンを更新する方法を確認してみましょう。 これらの例ではプラットフォーム イメージを取り上げますが、この記事ではこのプロセスをカスタム イメージに適用するために必要な情報も提供します。

## <a name="powershell"></a>PowerShell
この例では、Windows 仮想マシン スケール セットを新しいバージョン 4.0.20160229 に更新します。 モデル更新後に、一度に 1 つの仮想マシン インスタンスを更新します。

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the virtual machine scale set model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

プラットフォーム イメージのバージョン変更の代わりにカスタム イメージの URI を更新する場合は、次のように "set the new version" 行を置き換えます。

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```


## <a name="the-rest-api"></a>REST API
次に、Azure REST API を使用して OS バージョンの更新を展開する Python の例を示します。 この 2 つの例では、スケール セット モデル上で GET を実行するための Azure REST API ラッパー関数の lightweight [azurerm](https://pypi.python.org/pypi/azurerm) ライブラリを使用し、更新されたモデルで PUT を行います。 更新ドメインごとに、仮想マシンを識別するための仮想マシン インスタンスのビューを確認できます。

### <a name="vmssupgrade"></a>Vmssupgrade
 [Vmssupgrade](https://github.com/gbowerman/vmsstools) は実行中の仮想マシン スケール セットに OS のアップグレードを展開するために使用する Python スクリプトで、一度に 1 つの更新ドメインを更新します。

![Vmssupgrade script for choosing virtual machines or an update domain](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

このスクリプトでは、更新または更新ドメインの指定を行う、特定の仮想マシンを選択できます。 このスクリプトは、プラットフォーム イメージのバージョン変更とカスタム イメージの URI の変更をサポートしています。

### <a name="vmsseditor"></a>Vmsseditor
[Vmsseditor](https://github.com/gbowerman/vmssdashboard) は仮想マシン スケール セット向けの汎用エディターであり、1 つの行が 1 つの更新ドメインを表すヒートマップとして、仮想マシンの状態が表示されます。 特に、新しいバージョン、SKU、またはカスタム イメージ URI でスケール セットのモデルを更新し、アップグレードする障害ドメインを選択できます。 この操作を行う場合、更新ドメイン内のすべての仮想マシンが新しいモデルにアップグレードされます。 または、選択したバッチ サイズに基づいてローリング アップグレードを実行することもできます。  

次のスクリーン ショットでは、Ubuntu 14.04-2LTS バージョン 14.04.201507060 のスケール セットのモデルを示しています。 このツールには、このスクリーン ショットが撮られてからも、多くのオプションが追加されています。

![Vmsseditor model of a scale set for Ubuntu 14.04-2LTS](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

**[Upgrade (アップグレード)]**、**[Get Details (詳細の取得)]** の順にクリックすると、UD 0 の仮想マシンの更新が開始されます。

![Vmsseditor showing update in progress](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)




<!--HONumber=Nov16_HO3-->


