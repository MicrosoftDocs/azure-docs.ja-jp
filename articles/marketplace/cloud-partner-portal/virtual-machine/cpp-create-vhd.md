---
title: Azure Marketplace 向けの Azure と互換性のある VHD の作成 | Microsoft Docs
description: Azure Marketplace での仮想マシン プランのための VHD を作成する方法について説明します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/27/2018
ms.author: pbutlerm
ms.openlocfilehash: 04a1741bbe4e60567a22445c5674ec03b232640c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59793078"
---
# <a name="create-an-azure-compatible-vhd"></a>Azure と互換性のある VHD の作成

この記事では、Azure Marketplace での仮想マシン (VM) プランのための仮想ハード ディスク (VHD) を作成するのに必要な手順を詳しく説明します。  リモート デスクトップ プロトコル (RDP) の使用、VM サイズの選択、最新の Windows 更新プログラムのインストール、VHD イメージの汎用化などのさまざまな側面に関するベスト プラクティスも紹介します。  次のセクションでは、Windows ベースの VHD に主に注目します。Linux ベースの VHD の作成の詳細については、「[Azure で動作保証済みの Linux ディストリビューション](../../../virtual-machines/linux/endorsed-distros.md)」を参照してください。 

> [!WARNING]
> Azure を使って事前構成済みで動作保証済みのオペレーティング システムを含む VM を作成するには、このトピックにあるガイダンスに従うことを強くお勧めします。  もしお使いのソリューションと互換性がない場合は、承認済みのオペレーティング システムを使って、オンプレミスの VM を作成し、構成することができます。  その場合は、「[Azure にアップロードする Windows VHD または VHDX を準備する](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image)」で説明されている方法で、アップロードのための構成と準備を行うことができます。


## <a name="select-an-approved-base"></a>承認済みのベースを選択する
VM イメージのオペレーティング システム VHD は、Azure 承認の基本イメージに基づいている必要があります (Windows Server、SQL Server など)。
始めに、Microsoft Azure portal にある次のイメージの 1 つから VM を作成します。

-   Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016)、[2012 R2 Datacenter](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/)、[2012 Datacenter](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/)、[2008 R2 SP1](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/))
-   [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise、Standard、Web)
-   [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise、Standard、Web)

> [!TIP]
> 現行の Azure ポータルまたは PowerShell を使用している場合は、2014 年 9 月 8 日以降に発行された Windows Server イメージが承認されます。

または、さまざまな承認済みの Linux ディストリビューションが Azure から提供されています。  現在のリストについては、「[Azure で動作保証済みの Linux ディストリビューション](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)」を参照してください。


## <a name="create-vm-in-the-azure-portal"></a>Azure portal で VM を作成する 

次の手順を使用して、Microsoft [Azure portal](https://ms.portal.azure.com/) で基本イメージを作成します。

1. VM プランを発行する Azure サブスクリプションの Microsoft アカウントでポータルにサインインします。
2. 新しいリソース グループを作成して、 **リソース グループ名**、**サブスクリプション**、**リソース グループの場所**を指定します。  詳細なガイダンスについては、[リソース グループの管理](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)に関するページを参照してください。
3. 左のメニュー バーの **[Virtual Machines]** をクリックして、仮想マシンの詳細ページを表示します。 
4. この新しいページで、**[+ 追加]** をクリックして、**[コンピューティング]** ブレードを表示します。  最初の画面に VM の種類が表示されない場合は、基本 VM の名前を検索することができます。例:

    ![新しい VM のコンピューティング ブレード](./media/publishvm_014.png)

5. 適切な仮想イメージを選択した後、次の値を指定します。
   * **[基本]** ブレードで、仮想マシンの**名前**を 1 から 15 文字の英数字で入力します。 (この例では、`DemoVm009` を使用します。)
   * VM にローカル アカウントを作成する際に使用する**ユーザー名**と強力な**パスワード**を入力します。  (ここでは、`adminUser` を使用します。)パスワードは、8 ～ 123 文字で指定する必要があります。また、1 つの小文字、1 つの大文字、1 つの数字、1 つの特殊文字という複雑さの 4 要件のうち、3 つを満たしている必要があります。 詳細については、[ユーザー名とパスワードの要件](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm)に関するページを参照してください。
   * 先ほど作成したリソース グループを選択します (ここでは `DemoResourceGroup`)。
   * Azure データセンターの**場所**を選択します (ここでは `West US`)。
   * **[OK]** をクリックして値を保存します。 

6. 次の推奨事項に従ってデプロイする VM のサイズを選択します。
   * VHD をオンプレミスで開発する場合は、サイズの選択は必要ありません。 小さいサイズの VM を使用することを検討してください。
   * Azure でイメージを開発する場合は、選択したイメージに推奨される VM サイズを使用することを検討してください。
   * 価格情報については、ポータルに表示される**推奨される価格レベル**のセレクターを参照してください。 発行元から提供されるサイズとして推奨される 3 つのサイズが表示されます。 (ここでは、発行元は Microsoft。)

   ![新しい VM のサイズ ブレード](./media/publishvm_015.png)

7. **[設定]** ブレードで、**[マネージド ディスクの使用]** オプションを **[いいえ]** に設定します。  これにより、新しい VHD を手動で管理できるようになります。 (**[設定]** ブレードでは、他にもストレージやネットワークのオプションを変更することができます。例: **[ディスクの種類]** で **[Premium (SSD)]** を選択します。)**[OK]** をクリックして続行します。

    ![新しい VM の設定ブレード](./media/publishvm_016.png)

8. **[概要]** をクリックして、選択内容を確認します。 **"検証に成功しました"** というメッセージが表示されたら、**[OK]** をクリックします。

    ![新しい VM の概要ブレード](./media/publishvm_017.png)

Azure では、指定した仮想マシンのプロビジョニングを開始します。  左側の **[Virtual Machines]** タブをクリックして、進捗状況を追跡することができます。  作成後に、状態は **[実行中]** に変わります。  その時点で、[仮想マシンに接続](./cpp-connect-vm.md)できます。


## <a name="next-steps"></a>次の手順

Azure ベースの新しい VHD の作成で問題が発生した場合は、「[VHD 作成における一般的な問題](./cpp-common-vhd-creation-issues.md)」を参照してください。  問題がなければ、次に Azure で作成した [VM に接続](./cpp-connect-vm.md)する必要があります。 
