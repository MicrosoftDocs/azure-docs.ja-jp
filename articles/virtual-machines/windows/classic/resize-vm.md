---
title: "クラシック デプロイ モデルの Windows VM のサイズ変更 - Azure | Microsoft Docs"
description: "クラシック デプロイメント モデルで作成された Windows 仮想マシンのサイズを Azure Powershell を使用して変更します。"
services: virtual-machines-windows
documentationcenter: 
author: Drewm3
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: e3038215-001c-406e-904d-e0f4e326a4c7
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/19/2016
ms.author: drewm
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 522463673b9ac6e53da616e435a465d2d2ce0524
ms.lasthandoff: 03/25/2017


---
# <a name="resize-a-windows-vm-created-in-the-classic-deployment-model"></a>クラシック デプロイメント モデルで作成された Windows VM のサイズ変更
この記事では、クラシック デプロイメント モデルで作成された Windows VM のサイズを Azure Powershell を使用して変更する方法について説明します。

VM のサイズ変更機能について検討する際、仮想マシンのサイズ変更に使用可能なサイズの範囲を制御する 2 つの概念があります。 1 つ目の概念は、VM がデプロイされているリージョンです。 リージョンで使用可能な VM サイズの一覧は、Azure リージョンの Web ページの [サービス] タブにあります。 2 つ目の概念は、現在 VM をホストしている物理ハードウェアです。 VM をホストしている物理サーバーは、一般的な物理ハードウェアのクラスターにグループ化されます。 VM のサイズを変更する方法は、現在 VM をホストしているハードウェア クラスターで、新しく必要なサイズがサポートされているかどうかによって異なります。

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 [Resource Manager デプロイメント モデルで作成された VM のサイズを変更する](../../virtual-machines-windows-resize-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)こともできます。

## <a name="add-your-account"></a>アカウントの追加
Azure クラシック リソースを操作できるように Azure PowerShell を構成する必要があります。 クラシック リソースを管理するよう Azure PowerShell を構成するには、次の手順に従います。

1. PowerShell プロンプトで「`Add-AzureAccount`」と入力し、**Enter** キーを押します。 
2. お使いの Azure サブスクリプションに関連付けられている電子メール アドレスを入力し、 **[続行]**をクリックします。 
3. アカウントのパスワードを入力します。 
4. **[サインイン]**をクリックします。 

## <a name="resize-in-the-same-hardware-cluster"></a>同じハードウェア クラスター内でのサイズ変更
VM をホストしているハードウェア クラスター内で使用可能なサイズに VM のサイズを変更するには、次の手順を実行します。

1. 次の PowerShell コマンドを実行して、VM が含まれているクラウド サービスをホストしているハードウェア クラスター内で利用可能な VM サイズの一覧を表示します。
   
    ```powershell
    Get-AzureService | where {$_.ServiceName -eq "<cloudServiceName>"}
    ```
2. 次のコマンドを実行して、VM のサイズを変更します。
   
    ```powershell
    Get-AzureVM -ServiceName <cloudServiceName> -Name <vmName> | Set-AzureVMSize -InstanceSize <newVMSize> | Update-AzureVM
    ```

## <a name="resize-on-a-new-hardware-cluster"></a>新しいハードウェア クラスター上でのサイズ変更
VM をホストしているハードウェア クラスターで使用できないサイズに VM のサイズを変更するには、クラウド サービスとクラウド サービス内のすべての VM を再作成する必要があります。 各クラウド サービスは 1 つのハードウェア クラスターでホストされるため、クラウド サービス内のすべての VM は、ハードウェア クラスターでサポートされているサイズである必要があります。 次の手順では、クラウド サービスを削除して作成し直すことで、VM のサイズを変更する方法を説明します。

1. 次の PowerShell コマンドを実行して、リージョン内で使用可能な VM サイズの一覧を表示します。 
   
    ```powershell
    Get-AzureLocation | where {$_.Name -eq "<locationName>"}
    ```
2. クラウド サービス内の各 VM (サイズ変更する VM を含む) のすべての構成設定をメモします。 
3. 各 VM のディスクを保持するオプションを選択して、クラウド サービス内のすべての VM を削除します。
4. 必要な VM サイズを使用して、サイズを変更する VM を再作成します。
5. 現在クラウド サービスをホストしているハードウェア クラスターで使用可能な VM サイズを使用して、クラウド サービスにあった他のすべての VM を再作成します。

新しい VM サイズを使用したクラウド サービスの削除と再作成のサンプル スクリプトについては、[こちら](https://github.com/Azure/azure-vm-scripts)を参照してください。 

## <a name="next-steps"></a>次のステップ
* [Resource Manager デプロイメント モデルで作成された VM のサイズを変更する](../../virtual-machines-windows-resize-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。


