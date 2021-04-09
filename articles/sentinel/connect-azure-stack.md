---
title: Azure Stack Hub 仮想マシンを Azure Sentinel にオンボードする | Microsoft Docs
description: この記事では、Azure Monitor, Update, and Configuration Management 仮想マシン拡張機能を Azure Stack Hub 仮想マシン上にプロビジョニングし、Azure Sentinel を使用してそれらの監視を開始する方法を示します。
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 5999e8da5dffce85dd12ecd01cd5991ea4abc098
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100590239"
---
# <a name="connect-azure-stack-hub-virtual-machines-to-azure-sentinel"></a>Azure Stack Hub 仮想マシンを Azure Sentinel に接続する

Azure Sentinel では、Azure 上で稼動する VM と Azure Stack Hub で稼動する VM を 1 か所で監視できます。 Azure Stack マシンを Azure Sentinel にオンボードするにはまず、既存の Azure Stack Hub 仮想マシンに仮想マシン拡張機能を追加する必要があります。 

Azure Stack Hub マシンを接続したら、データに基づく分析情報を可視化したダッシュボードをギャラリーから選択します。 これらのダッシュボードは、お客様のニーズに合わせて簡単にカスタマイズすることができます。

## <a name="add-the-virtual-machine-extension"></a>仮想マシン拡張機能を追加する 

Azure Stack Hub で動作している仮想マシンに **Azure Monitor, Update and Configuration Management** 仮想マシン拡張機能を追加します。 

1. 新しいブラウザー タブで、[Azure Stack Hub ポータル](/azure-stack/user/azure-stack-use-portal#access-the-portal)にログインします。

1. **[仮想マシン]** ページに移動し、Azure Sentinel を使用して保護したい仮想マシンを選択します。 Azure Stack Hub で仮想マシンを作成する方法については、「[Azure Stack Hub ポータルを使用して Windows サーバー VM を作成する](/azure-stack/user/azure-stack-quick-windows-portal)」または「[Azure Stack Hub ポータルを使用して Linux サーバー VM を作成する](/azure-stack/user/azure-stack-quick-linux-portal)」を参照してください。

1. **[拡張機能]** を選択します。 この仮想マシンにインストールされている仮想マシン拡張機能の一覧が表示されます。

1. **[Add (追加)]** タブをクリックします。 **[新しいリソース]** メニュー ブレードが開かれ、使用可能な仮想マシン拡張機能の一覧が表示されます。 

1. **Azure Monitor, Update, and Configuration Management** 拡張機能を選択し、 **[作成]** をクリックします。 **[拡張機能のインストール]** 構成ウィンドウが開きます。

   ![Azure Monitor, Update, and Configuration Management の設定](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > **Azure Monitor, Update and Configuration Management** 拡張機能が Marketplace に一覧表示されていない場合は、担当の Azure Stack Hub オペレーターに連絡して使用可能にしてください。

1. Azure Sentinel メニューで、 **[ワークスペースの設定]** 、 **[Advanced]\(詳細\)** の順に選択し、 **[ワークスペース ID]** と **[ワークスペース キー (主キー)]** をコピーします。 

1. Azure Stack Hub の **[拡張機能のインストール]** ウィンドウで、指定されたフィールドにそれらを貼り付け、 **[OK]** をクリックします。

1. 拡張機能のインストールが完了すると、その状態が **[プロビジョニング成功]** と表示されます。 Azure Sentinel ポータルに仮想マシンが表示されるまでに、最大で 1 時間かかる場合があります。

Windows 用エージェントのインストールと構成の詳細については、[Windows コンピューターの接続](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard)に関するページを参照してください。

エージェントの問題に対する Linux のトラブルシューティングについては、[Azure Log Analytics Linux エージェントのトラブルシューティング](../azure-monitor/agents/agent-linux-troubleshoot.md)に関するページを参照してください。

Azure の Azure Sentinel ポータルの **[Virtual Machines]** に、すべての VM とコンピューターの概要がその状態と共に表示されます。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になった拡張機能は、Azure Stack Hub ポータルを使用して仮想マシンから削除できます。

拡張機能を削除するには:

1. **Azure Stack Hub ポータル** を開きます。

1. **[仮想マシン]** ページに移動し、拡張機能を削除したい仮想マシンを選択します。

1. **[拡張機能]** を選択し、拡張機能 **Microsoft.EnterpriseCloud.Monitoring** を選択します。

1. **[アンインストール]** をクリックし、選択内容を確認します。

## <a name="next-steps"></a>次のステップ

Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [一般的なイベント形式のアプライアンス](connect-common-event-format.md)から Azure Sentinel へのデータのストリーム配信。
