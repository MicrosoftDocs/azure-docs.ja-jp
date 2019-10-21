---
title: Azure Security Center クイック スタート - Azure Stack 仮想マシンを Security Center にオンボードする | Microsoft Docs
description: このクイック スタートでは、Azure Monitor, Update and Configuration Management 仮想マシン拡張機能を Azure Stack 仮想マシンにプロビジョニングする方法を示します。
services: security-center
documentationcenter: na
author: pipposera
manager: dsavage
ms.assetid: 8982348a-0624-40c7-8a1e-642a523c7f6b
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: fiseraci
ms.openlocfilehash: 1772fd34a2d79b725b2b5ccaa66adb0b251b7e1d
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202855"
---
# <a name="quickstart--onboard-your-azure-stack-virtual-machines-to-security-center"></a>クイック スタート:Azure Stack 仮想マシンを Security Center にオンボードする
Azure サブスクリプションをオンボードした後、Azure Stack Marketplace から **Azure Monitor, Update and Configuration Management** 仮想マシン拡張機能を追加することにより、Security Center を有効にして、Azure Stack で動作している仮想マシンを保護できます。

このクイック スタートでは、Azure Stack で動作している仮想マシン (Linux と Windows の両方をサポート) に **Azure Monitor, Update and Configuration Management** 仮想マシン拡張機能を追加する方法を示します。

## <a name="prerequisites"></a>前提条件
セキュリティ センターを使用するには、Microsoft Azure のサブスクリプションが必要です。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。

このクイック スタートを開始する前に、Security Center の Standard レベルの Azure サブスクリプションを用意する必要があります。 アップグレード手順については、「[クイックスタート: Azure サブスクリプションでの Security Center Standard の利用開始](security-center-get-started.md)」をご覧ください。 Security Center の Standard レベルは、30 日間無料でお試しいただけます。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。

## <a name="select-your-workspace-in-azure-security-center"></a>Azure Security Center のワークスペースの選択

1. [Azure Portal](https://azure.microsoft.com/features/azure-portal/) にサインインします。
2. **[Microsoft Azure]** メニューの **[セキュリティ センター]** を選択します。 **[セキュリティ センター - 概要]** が開きます。 

   ![Security Center の概要][2]

3. Security Center メイン メニューの **[使用の開始]** を選択します。
4. **[Get started] (使用の開始)** タブを選択します。

   ![作業開始][3]

5. **[Azure 以外のコンピューターの新規追加]** で **[構成]** をクリックします。 Log Analytics ワークスペースの一覧が表示されます。 該当する場合、一覧には、自動プロビジョニングが有効になっているときに Security Center によって作成された既定のワークスペースが含まれます。 このワークスペースを選択するか、Azure Stack VM によるセキュリティ データのレポート先となる別のワークスペースを選択します。

    ![Azure 以外のコンピューターの追加](./media/quick-onboard-windows-computer/non-azure.png)

   **[ダイレクト エージェント]** ブレードが開かれ、エージェントをダウンロードするためのリンクと、エージェントの構成時に使用するワークスペース ID のキーが表示されます。

   >[!NOTE]
   > エージェントを手動でダウンロードする必要はありません。 エージェントは、以下の手順で、VM 拡張機能としてインストールされます。

6. **[ワークスペース ID]** の右側で、コピー アイコンをク選択し、ID をメモ帳に貼り付けます。

7. **[主キー]** の右側で、コピー アイコンを選択し、キーをメモ帳に貼り付けます。

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>既存の Azure Stack 仮想マシンへの仮想マシン拡張機能の追加
ここで、Azure Stack で動作している仮想マシンに **Azure Monitor, Update and Configuration Management** 仮想マシン拡張機能を追加する必要があります。

1. 新しいブラウザー タブで、**Azure Stack** ポータルにログインします。
2. **[仮想マシン]** ページに移動し、Security Center で保護したい仮想マシンを選択します。 Azure Stack に仮想マシンを作成する方法については、[Windows 仮想マシンの場合はこのクイック スタート](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal)、[Linux 仮想マシンの場合はこのクイック スタート](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal)を参照してください。
3. **[拡張機能]** を選択します。 この仮想マシンにインストールされている仮想マシン拡張機能の一覧が表示されます。
4. **[Add (追加)]** タブをクリックします。 **[新しいリソース]** メニュー ブレードが開かれ、使用可能な仮想マシン拡張機能の一覧が表示されます。 
5. **Azure Monitor, Update and Configuration Management** 拡張機能を選択し、 **[作成]** をクリックします。 **[拡張機能のインストール]** 構成ブレードが開かれます。

>[!NOTE]
> **Azure Monitor, Update and Configuration Management** 拡張機能が Marketplace に一覧表示されていない場合は、該当の Azure Stack オペレーターに連絡して使用可能にしてください。

6. **[拡張機能のインストール]** 構成ブレードに、前の手順でメモ帳にコピーしておいた**ワークスペース ID** と**ワークスペース キー (主キー)** を貼り付けます。
7.  必要な構成設定の指定が終了したら、 **[OK]** をクリックします。
8. 拡張機能のインストールが完了すると、その状態が **[プロビジョニング成功]** と表示されます。 Security Center ポータルに仮想マシンが表示されるまでに、最大で 1 時間かかる場合があります。

Windows 用エージェントのインストールと構成の詳細については、[Windows コンピューターの接続](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard)に関するページを参照してください。

エージェントの問題に対する Linux のトラブルシューティングについては、[Azure Log Analytics Linux エージェントのトラブルシューティング](../azure-monitor/platform/agent-linux-troubleshoot.md)に関するページを参照してください。

これで、Azure VM と Azure 以外のコンピューターを 1 か所で監視できます。 Azure の Security Center ポータルで、 **[コンピューティング]** の下に、すべての VM とコンピューターの概要が推奨事項と共に表示されます。 Security Center では、セキュリティ アラートでのこれらのコンピューターの検出も明らかになります。

  ![[コンピューティング] ブレード][6]

**[計算]** ブレードには 2 種類のアイコンが表示されます。

![アイコン 1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Azure 以外のコンピューター 

![アイコン 2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Azure VM (Azure Stack VM は、このグループに表示されます)

## <a name="clean-up-resources"></a>リソースのクリーンアップ
不要になったら、拡張機能は Azure Stack ポータルを使用して仮想マシンから削除できます。

拡張機能を削除するには:

1. **Azure Stack ポータル**を開きます。
2. **[仮想マシン]** ページに移動し、拡張機能を削除したい仮想マシンを選択します。
3. **[拡張機能]** を選択し、拡張機能 **Microsoft.EnterpriseCloud.Monitoring** を選択します。
4. **[アンインストール]** をクリックし、選択を確認するために **[はい]** をクリックします。

## <a name="next-steps"></a>次の手順
このクイック スタートでは、Azure Stack で動作している仮想マシンに Azure Monitor, Update and Configuration Management 拡張機能をプロビジョニングしました。 Security Center の使用方法について学習するには、セキュリティ ポリシーの構成とリソースのセキュリティの評価に関するチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル:セキュリティ ポリシーの定義と評価](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
