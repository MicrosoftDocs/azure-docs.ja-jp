---
title: 承認済みのベース (Azure Marketplace) から Azure 仮想マシン (VM) オファーを作成する
description: 承認済みのベースから Azure 仮想マシン (VM) オファーを作成する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 94d21cb82290e59ebb003969a566c1bfc877713e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200440"
---
# <a name="how-to-create-a-virtual-machine-using-an-approved-base"></a>承認済みのベースを使用して Azure 仮想マシンを作成する方法

この記事では、Azure を使用して、事前に構成された動作保証済みのオペレーティング システムを含む仮想マシン (VM) を作成する方法について説明します。 これがお使いのソリューションと互換性がない場合は、承認済みのオペレーティング システムを使用して、[オンプレミスの VM を作成して構成する](azure-vm-create-using-own-image.md)ことができます。

> [!NOTE]
> この手順を開始する前に、Azure VM オファーに対する[技術要件](marketplace-virtual-machines.md#technical-requirements) (仮想ハードディスク (VHD) の要件を含む) をレビューしてください。

## <a name="select-an-approved-base-image"></a>承認済みのベース イメージを選択する

次の Windows または Linux イメージのいずれかをベースとして選択します。

### <a name="windows"></a>Windows

- [Windows Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)
- SQL Server [2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview)、[2014](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2014sp3-ws2012r2?tab=Overview)、[2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2012sp4-ws2012r2?tab=Overview)

### <a name="linux"></a>Linux

さまざまな承認済みの Linux ディストリビューションが Azure から提供されています。 現在のリストについては、「[Azure で動作保証済みの Linux ディストリビューション](../virtual-machines/linux/endorsed-distros.md)」を参照してください。

## <a name="create-vm-on-the-azure-portal"></a>Azure portal で VM を作成する

1. [Azure portal](https://ms.portal.azure.com/) にサインインします。
2. **[Virtual Machines]** を選択します。
3. **[+ 追加]** を選択して、 **[仮想マシンの作成]** 画面を開きます。
4. ドロップダウン リストからイメージを選択するか、または **[すべてのパブリックおよびプライベート イメージを参照する]** を選択して、使用可能なすべての仮想マシン イメージを検索または参照します。
5. **Gen 2** VM を作成するには、 **[詳細設定]** タブに移動し、 **[Gen 2]** オプションを選択します。

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Gen 1 または Gen 2 を選択します。":::

6. デプロイする VM のサイズを選択します。

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="選択したイメージで推奨される VM サイズを選択します。":::

7. VM の作成に必要なその他の詳細を指定します。
8. **[確認と作成]** を選択して、選択内容を確認します。 緑色の **検証に成功** のメッセージが表示されたら、 **[作成]** を選択します。

Azure で、指定した仮想マシンのプロビジョニングが開始されます。 左側のメニューで **[Virtual Machines]** タブを選択して、進捗状況を追跡します。 仮想マシンが作成されると、状態が **[実行中]** に変更されます。

## <a name="configure-the-vm"></a>VM を構成する

このセクションでは、Azure VM のサイズ変更、更新、一般化を行う方法について説明します。 これらの手順は、Azure Marketplace にデプロイする VM を準備するために必要です。

### <a name="connect-to-your-vm"></a>VM に接続する

[Windows](../virtual-machines/windows/connect-logon.md) または [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) VM に接続する方法については、次のドキュメントを参照してください。

### <a name="install-the-most-current-updates"></a>最新の更新プログラムをインストールする

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>追加のセキュリティ チェックを実行する

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>カスタム構成とスケジュールされたタスクを実行する

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>次の手順

- 推奨される次の手順:[お使いの VM イメージをテストし](azure-vm-image-test.md)、それが Azure Marketplace の公開要件を満たしていることを確認します。 これは省略可能です。
- VM イメージをテストしない場合は、 [パートナー センター](https://partner.microsoft.com/)にサインインし、イメージを公開します。
- お使いの Azure ベースの新しい VHD の作成で問題が発生した場合は、[Azure Marketplace の VM に関する FAQ](azure-vm-create-faq.md) を参照してください。
