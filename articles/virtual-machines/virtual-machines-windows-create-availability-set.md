---
title: "Azure での VM の可用性セットの作成 | Microsoft Docs"
description: "Resource Manager デプロイメント モデルで Azure PowerShell またはポータルを使用して、仮想マシンの管理対象可用性セットまたは非管理対象可用性セットを作成する方法について説明します。"
keywords: "可用性セット"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a3db8659-ace8-4e78-8b8c-1e75c04c042c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 2f4ea765df71ed9aac7e8657e6be3706b635407e
ms.lasthandoff: 03/06/2017

---
# <a name="increase-vm-availability-by-creating-an-azure-availability-set"></a>Azure 可用性セットを作成することによる VM の可用性の向上 
可用性セットにより、アプリケーションに冗長性が得られます。 1 つの可用性セット内に&2; つ以上の仮想マシンをグループ化することをお勧めします。 このような構成により、計画済み、または計画外メンテナンス イベント中に、少なくとも 1 つの仮想マシンが利用可能となり、99.95% の Azure SLA を満たします。 詳細については、「 [Virtual Machines の SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)」を参照してください。

> [!IMPORTANT]
> VM は、可用性セットと同じリソース グループ内に作成する必要があります。
> 

VM を可用性セットに含めるには、まずその可用性セットを作成するか、セットに含める最初の VM の作成中に可用性セットを作成する必要があります。 Managed Disks を使用する場合、可用性セットは管理対象可用性セットとして作成する必要があります。

可用性セットの作成と使用の詳細については、「 [仮想マシンの可用性管理](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

## <a name="use-the-portal-to-create-an-availability-set-before-creating-your-vms"></a>ポータルを使用して、VM を作成する前に可用性セットを作成する
1. ハブ メニューで **[参照]** をクリックして、**[可用性セット]** を選択します。
2. **[可用性セットブレード]** で、**[追加]** をクリックします。
   
    ![新しい可用性セットを作成するための [追加] ボタンを示すスクリーン ショット。](./media/virtual-machines-windows-create-availability-set/add-availability-set.png)
3. **可用性セットの作成** ブレードで、セットの情報を入力します。
   
    ![可用性セットの作成のために、入力する必要がある情報を示すスクリーン ショット。](./media/virtual-machines-windows-create-availability-set/create-availability-set.png)
   
   * **名前** - 名前は 1 から 80 文字の数字、文字、ピリオド、アンダー スコアおよびダッシュで構成する必要があります。 先頭の文字は、文字または数字にする必要があります。 末尾の文字は、文字、数字、またはアンダースコアにする必要があります。
   * **障害ドメイン** - 障害ドメインは電源とネットワーク スイッチを共有する仮想マシンのグループを定義します。 既定で、VM は最大 3 つの障害ドメイン間で分散され、1 から 3 の間で変更することができます。
   * **更新ドメイン** - 5 つの更新ドメインが既定で割り当てられ、1 から 20 に設定することができます。 更新ドメインは、仮想マシンと、同時に再起動できる基礎となる物理ハードウェアのグループを示しています。 たとえば、5 つの更新ドメインを指定する場合、1 つの可用性セット内に&5; つ以上の仮想マシンが構成されているとき、6 つ目の仮想マシンは&1; つ目の仮想マシンと同じ更新ドメイン内に配置され、7 つ目は&2; つ目の仮想マシンと同じ UD 内に配置されるという方法で処理されます。 再起動は順番に処理されない場合がありますが、一度に再起動される更新ドメインは&1; つのみです。
   * **サブスクリプション** - 複数のサブスクリプションがある場合は、使用するサブスクリプションを選択します。
   * **リソース グループ** - 矢印をクリックしてドロップダウンからリソース グループを選択することにより、既存のリソース グループを選択します。 名前を入力して、新しいリソース グループを作成することもできます。 名前には、次の文字を含めることができます。文字、数字、ピリオド、ダッシュ、アンダースコアおよびかっこです。 名前の末尾を句点にすることはできません。 可用性グループのすべての VM は、可用性セットと同じリソース グループ内に作成する必要があります。
   * **場所** -ドロップダウン から場所を選択します。
   * **管理対象** - ストレージに Managed Disks を使用する VM 用の管理対象可用性セットを作成する場合は *[はい]* を選択します。 セットに含める VM でストレージ アカウントの非管理対象ディスクを使用する場合は、**[いいえ]**を選択します。
   
4. 情報の入力を完了したら、 **[作成]**をクリックします。 

## <a name="use-the-portal-to-create-a-virtual-machine-and-an-availability-set-at-the-same-time"></a>ポータルを使用して、仮想マシンと可用性セットを同時に作成する
ポータルを使用して新しい VM を作成する場合は、新しい可用性セットの最初の VM の作成中に、VM の可用性セットも作成することができます。 VM で Managed Disks を使用することを選択した場合は、管理対象可用性セットが作成されます。

![VM の作成時に、新しい可用性セットを作成するためのプロセスを示すスクリーン ショット。](./media/virtual-machines-windows-create-availability-set/new-vm-avail-set.png)

## <a name="add-a-new-vm-to-an-existing-availability-set-in-the-portal"></a>ポータルで既存の可用性セットに新しい VM を追加する
セットに属する各追加 VM が、同じ **リソース グループ** で作成されているかを確認し、手順 3 で既存の可用性セットを選択します。 

![VM に使用する既存の可用性セットを選択する方法を示すスクリーン ショット。](./media/virtual-machines-windows-create-availability-set/add-vm-to-set.png)

## <a name="use-powershell-to-create-an-availability-set"></a>PowerShell を使用して可用性セットを作成する
この例では、**West US** という場所の **myResourceGroup** リソース グループに **myAvailabilitySet** という名前の可用性セットを作成します。 これは、可用性セットに最初の VM を作成する前に実行する必要があります。

始める前に、AzureRM.Compute PowerShell モジュールの最新バージョンがあることを確認してください。 インストールするには次のコマンドを実行します。

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
詳細については、[Azure PowerShell のバージョン管理に関するページ](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning)をご覧ください。


VM で管理対象ディスクを使用する場合は次のように入力します。

```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "myResourceGroup" '
    -Name "myAvailabilitySet" -Location "West US" -managed
```

VM でストレージ アカウントを使用する場合は次のように入力します。

```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "myResourceGroup" '
    -Name "myAvailabilitySet" -Location "West US" 
```

詳細については、 [New-AzureRmAvailabilitySet](/powershell/new-azurermavailabilityset)を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング
* VM を作成するときに、必要な可用性セットがポータルのドロップダウン リストにない場合は、可用性セットが別のリソース グループに作成された可能性があります。 可用性セットのリソース グループがわからない場合は、ハブ メニューに移動し、[参照]、[可用性セット] をクリックして、可用性セットが属するリソース グループの一覧を確認します。

## <a name="next-steps"></a>次のステップ
[データ ディスク](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)を追加して、記憶域を VM にさらに追加します。


