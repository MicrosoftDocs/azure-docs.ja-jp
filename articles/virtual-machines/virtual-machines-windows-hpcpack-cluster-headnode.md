---
title: "Azure VM で HPC Pack ヘッド ノードを作成する | Microsoft Docs"
description: "Azure Portal と Resource Manager デプロイメント モデルを利用し、Azure VM で Microsoft HPC Pack 2012 R2 ヘッド ノードを作成する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,hpc-pack
ms.assetid: e6a13eaf-9124-47b4-8d75-2bc4672b8f21
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 84d909b3eaa17cb4a1b8a4465615001896000dca
ms.lasthandoff: 03/25/2017


---
# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>Marketplace イメージを利用し、Azure VM で HPC Pack クラスターのヘッド ノードを作成する
Azure Marketplace の [Microsoft HPC Pack 2012 R2 仮想マシン イメージ](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)と Azure Portal を使用して、HPC クラスターのヘッド ノードを作成します。 この HPC Pack VM イメージは、HPC Pack 2012 R2 更新プログラム 3 を事前インストールした Windows Server 2012 R2 Datacenter に基づいています。 このヘッド ノードを Azure の HPC Pack の概念実証デプロイとして利用し、 クラスターにコンピューティング ノードを追加して HPC ワークロードを実行できます。

> [!TIP]
> ヘッド ノードと計算ノードが含まれる完全な HPC Pack 2012 R2 クラスターを Azure にデプロイする場合には、自動化手法を利用することをお勧めします。 オプションには、[HPC Pack IaaS デプロイ スクリプト](windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)と、[Windows ワークロードの HPC Pack クラスター](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)のような Resource Manager テンプレートが含まれます。 [Microsoft HPC Pack 2016 クラスター](https://github.com/MsHpcPack/HPCPack2016/tree/master/newcluster-templates)用の Resource Manager テンプレートも利用できます。 
> 
> 

## <a name="planning-considerations"></a>計画に関する考慮事項
次の図に示すように、Azure 仮想ネットワークの Active Directory ドメインに HPC Pack ヘッド ノードをデプロイします。

![HPC Pack ヘッド ノート][headnode]

* **Active Directory ドメイン**: VM 上で HPC サービスを開始する前に、HPC Pack 2012 R2 ヘッド ノードを Azure の Active Directory ドメインに参加させる必要があります。 この記事で示すように、概念実証デプロイとして、HPC サービスを開始する前に、ヘッド ノードに作成した VM をドメイン コントローラーとして昇格できます。 もう 1 つの選択肢としては、別個のドメイン コントローラー/フォレストを Azure にデプロイし、それにヘッド ノード VM を参加させます。

* **デプロイメント モデル**: 最新のデプロイメントでは、Resource Manager デプロイメント モデルを使用することをお勧めします。 この記事では、このデプロイメント モデルを使用することを前提としています。

* **Azure 仮想ネットワーク**: Resource Manager デプロイメント モデルを使用してヘッド ノードをデプロイする場合は、Azure 仮想ネットワークを指定するか作成します。 この仮想ネットワークは、ヘッド ノードを既存の Active Directory ドメインに参加させる場合に使用します。 また、後から計算ノード VM をクラスターに追加する場合にも必要になります。

## <a name="steps-to-create-the-head-node"></a>ヘッド ノードの作成手順
ここでは、Azure ポータルで Resource Manager デプロイメント モデルを使用して、HPC Pack ヘッド ノード用の Azure VM を作成する手順の概要を示します。 

1. Azure で別のドメイン コントローラー VM を持つ新しい Active Directory フォレストを作成する場合は、 [Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/active-directory-new-domain-ha-2-dc)を使用する方法があります。 単純な概念実証のデプロイの場合、この手順を省略して、ヘッド ノード VM 自体をドメイン コントローラーとして構成してもかまいません。 このオプションは、この記事の後半で説明します。
2. Azure Marketplace の [HPC Pack 2012 R2 on Windows Server 2012 R2R2 のページ](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) で、 **[仮想マシンの作成]**をクリックします。 
3. ポータルの **HPC Pack 2012 R2 on Windows Server 2012 R2** のページで **Resource Manager** デプロイメント モデルを選択し、**[作成]** をクリックします。
   
    ![HPC Pack イメージ][marketplace]
4. ポータルを使用して設定を構成し、VM を作成します。 Azure を初めて利用する場合は、[Azure Portal での Windows 仮想マシンの作成](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するチュートリアルをご覧ください。 概念実証デプロイの場合は、既定値または推奨設定を選択することをお勧めします。
   
   > [!NOTE]
   > ヘッド ノードを Azure の既存の Active Directory ドメインに参加させる場合は、VM を作成するときに、必ずそのドメイン内の仮想ネットワークを指定します。
   > 
   > 
5. VM を作成して実行したら、リモート デスクトップで[その VM に接続](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)します。 
6. 次のいずれかのオプションを選択して、VM を Active Directory ドメイン フォレストに参加させます。
   
   * 既存のドメイン フォレストを持つ Azure 仮想ネットワークで VM を作成した場合は、標準のサーバー マネージャーまたは Windows PowerShell ツールを使用して、その VM をフォレストに参加させます。 それから再起動します。
   * (既存のドメイン フォレストのない) 新しい仮想ネットワークで VM を作成した場合は、VM をドメイン コントローラーとして昇格させます。 Active Directory ドメイン サービスのロールをヘッド ノードにインストールして構成するための標準的な手順を使用します。 詳細については、「 [新しい Windows Server 2012 Active Directory フォレストのインストール](https://technet.microsoft.com/library/jj574166.aspx)」を参照してください。
7. VM が実行され、Active Directory フォレストに参加したら、次のように HPC Pack サービスを開始します。
   
    a. ローカル管理者グループに属するドメイン アカウントを利用し、ヘッド ノード VM に接続します。 たとえば、ヘッド ノード VM を作成したときに設定した管理者アカウントを使用します。
   
    b. 既定のヘッド ノード構成の場合、Windows PowerShell を管理者として起動し、次を入力します。
   
    ```PowerShell
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```
   
    HPC Pack サービスが開始するまでに数分かかることがあります。
   
    追加のヘッド ノード構成オプションについては、「 `get-help HPCHNPrepare.ps1`」を入力します。

## <a name="next-steps"></a>次のステップ
* これで、HPC Pack クラスターのヘッド ノードを操作できます。 たとえば、HPC クラスター マネージャーを起動し、 [デプロイ作業一覧](https://technet.microsoft.com/library/jj884141.aspx)の作業を完了します。
* オンデマンドでクラスター コンピューティング能力を向上させる場合には、クラウド サービスに [Azure バースト ノード](windows/classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)を追加します。 
* クラスターでテスト ワークロードを実行してみます。 たとえば、HPC Pack [ファースト ステップ ガイド](https://technet.microsoft.com/library/jj884144)を参照してください。

<!--Image references-->
[headnode]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/marketplace.png

