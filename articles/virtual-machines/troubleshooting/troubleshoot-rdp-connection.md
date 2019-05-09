---
title: RDP を使って Azure の Windows VM に接続できない | Microsoft Docs
description: リモート デスクトップを使用して Azure で Windows 仮想マシンに接続できない場合の問題のトラブルシューティング
keywords: リモート デスクトップ エラー,リモート デスクトップ接続エラー,VM に接続できない,リモート デスクトップのトラブルシューティング
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/23/2018
ms.author: roiyz
ms.openlocfilehash: a4fb31721da679b21fa311340269cf07f93cd903
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981266"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Azure 仮想マシンへのリモート デスクトップ接続に関するトラブルシューティング
Windows ベースの Azure 仮想マシン (VM) に対するリモート デスクトップ プロトコル (RDP) 接続は、さまざまな理由で失敗する可能性があり、VM にアクセスできない場合があります。 VM 上のリモート デスクトップ サービス、ネットワーク接続、またはホスト コンピューター上のリモート デスクトップ クライアントに問題がある可能性があります。 この記事では、RDP の接続問題を解決する、最も一般的な方法について説明します。 

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>簡単なトラブルシューティング手順
トラブルシューティングの各手順を実行した後、VM に再接続してみてください。

1. リモート デスクトップ構成をリセットする。
2. ネットワーク セキュリティ グループの規則/クラウド サービス エンドポイントを確認する。
3. VM コンソールのログを確認する。
4. VM の NIC をリセットする。
5. VM リソース ヘルスを確認する。
6. VM のパスワードをリセットする。
7. VM を再起動する。
8. アプリケーションを再デプロイする。

詳しい手順と説明が必要な場合は、この後の説明をお読みください。 ルーターやファイアウォールなどのローカル ネットワーク機器が、送信 TCP ポート 3389 をブロックしていないことを確認します。この方法については、[Azure 上の Windows VM へのリモート デスクトップ接続に関する問題の詳細なトラブルシューティング手順](detailed-troubleshoot-rdp.md)に関する記事をご覧ください。

> [!TIP]
> ポータルで **[接続]** ボタンが淡色表示され、[Express Route](../../expressroute/expressroute-introduction.md) や[サイト間 VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) 接続で Azure に接続されていない場合は、VM を作成してパブリック IP アドレスを割り当ててから RDP を使用する必要があります。 [Azure におけるパブリック IP アドレス](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)に関するページをご覧ください。


## <a name="ways-to-troubleshoot-rdp-issues"></a>RDP 問題をトラブルシューティングする方法
次のメソッドのいずれかを使用して、Resource Manager デプロイ モデルを使用して作成された VM のトラブルシューティングを行うことができます。

* Azure portal - RDP 構成またはユーザーの資格情報をすばやくリセットする必要があり、Azure ツールをインストールしていない場合に最適です。
* Azure PowerShell - PowerShell プロンプトに慣れている場合、Azure PowerShell コマンドレットを使用して、RDP の構成またはユーザーの資格情報をすばやくリセットします。

[クラシック デプロイ モデル](#troubleshoot-vms-created-using-the-classic-deployment-model)を使用して作成された VM のトラブルシューティングの手順も記載されています。

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Azure Portal を使用したトラブルシューティング
トラブルシューティングの各手順を実行した後、再度 VM に接続してみてください。 それでも接続できない場合は、次の手順をお試しください。

1. **RDP 接続をリセットする**。 このトラブルシューティングの手順では、リモート接続が無効になっていたり、Windows ファイアウォール規則によって RDP がブロックされている場合などに、RDP 構成をリセットします。
   
    Azure Portal で VM を選択します。 [設定] ウィンドウを、一覧の下部付近の **[サポートとトラブルシューティング]** セクションまで下にスクロールします。 **[Reset password]** ボタンをクリックします。 **[モード]** を **[Reset configuration only]** に設定し、**[更新]** ボタンをクリックします。
   
    ![Azure Portal で RDP 構成をリセットする](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **ネットワーク セキュリティ グループの規則を確認する**。 [IP フロー検証](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md)を使用して、ネットワーク セキュリティ グループ規則によって、仮想マシンから送受信されるトラフィックがブロックされていないかどうかを確認します。 有効なセキュリティ グループ規則を確認して、RDP ポート (既定では 3389) に対して受信 "許可" NSG 規則が存在し、優先されていることを確認することもできます。 詳細については、「[有効なセキュリティ規則を使用した VM トラフィック フローのトラブルシューティング](../../virtual-network/diagnose-network-traffic-filter-problem.md)」を参照してください。

3. **VM のブート診断を確認する**。 このトラブルシューティングの手順では、VM コンソール ログを確認し、VM が問題を報告しているかどうかを確認します。 すべての VM でブート診断を有効にしているとは限らないため、このトラブルシューティングの手順は省略できます。
   
    この記事では、具体的なトラブルシューティング手順については説明しませんが、RDP 接続に影響を与える、より広範な問題は示す場合があります。 コンソール ログと VM のスクリーン ショットの確認について詳しくは、「[VM のブート診断 (ブログの投稿)](boot-diagnostics.md)」をご覧ください。

4. **VM の NIC をリセットする**。 詳細については、[Azure Windows VM の NIC をリセットする方法](../windows/reset-network-interface.md)に関するページを参照してください。
5. **VM リソース ヘルスを確認する**。 このトラブルシューティングの手順では、VM への接続に影響を受ける可能性のある Azure プラットフォームで既知の問題がないことを確認します。
   
    Azure Portal で VM を選択します。 [設定] ウィンドウを、一覧の下部付近の **[サポートとトラブルシューティング]** セクションまで下にスクロールします。 **[Resource health]** ボタンをクリックします。 正常な VM には **[使用可能]** と表示されます。
   
    ![Azure Portal で VM リソース ヘルスを確認する](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **ユーザーの資格情報をリセットする**。 このトラブルシューティングの手順では、資格情報が明確でないか、覚えていない場合に、ローカル管理者アカウントのパスワードをリセットします。  VM にログインしたら、そのユーザーのパスワードをリセットしてください。
   
    Azure Portal で VM を選択します。 [設定] ウィンドウを、一覧の下部付近の **[サポートとトラブルシューティング]** セクションまで下にスクロールします。 **[Reset password]** ボタンをクリックします。 **[モード]** が **[パスワードのリセット]** に設定されていることを確認し、ユーザー名と新しいパスワードを入力します。 最後に、**[更新]** ボタンをクリックします。
   
    ![Azure Portal でユーザーの資格情報をリセットする](./media/troubleshoot-rdp-connection/reset-password.png)
7. **VM を再起動する**。 このトラブルシューティングの手順では、VM 自体で発生している根底にある問題を修正できます。
   
    Azure Portal で VM を選択し、**[概要]** タブをクリックします。**[再起動]** ボタンをクリックします。
   
    ![Azure Portal で VM を再起動する](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **アプリケーションを再デプロイする**。 このトラブルシューティングの手順では、プラットフォームやネットワークの根底にある問題を修正するために、Azure 内の別のホストに VM を再デプロイします。
   
    Azure Portal で VM を選択します。 [設定] ウィンドウを、一覧の下部付近の **[サポートとトラブルシューティング]** セクションまで下にスクロールします。 **[再デプロイ]** ボタンをクリックし、 **[再デプロイ]** をクリックします。
   
    ![Azure Portal で VM を再デプロイする](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    この操作を行うと、一時ディスクのデータが失われ、VM に関連付けられている動的 IP アドレスが更新されます。

9. **ルーティングを確認します**。 Network Watcher の[次ホップ](../../network-watcher/network-watcher-check-next-hop-portal.md)機能を使用して、ルートが仮想マシンとの間でトラフィックのルーティングを妨げていないことを確認します。 有効なルートを見直し、ネットワーク インターフェイスのすべての有効なルートを確認することもできます。 詳細については、「[有効なルートを使用した VM トラフィック フローのトラブルシューティング](../../virtual-network/diagnose-network-routing-problem.md)」を参照してください。

10. オンプレミスのファイアウォールまたはコンピューター上のファイアウォールが Azure に対する送信 TCP 3389 トラフィックを許可していることを確認します。

RDP の問題が解決しない場合は、[サポート要求を申請](https://azure.microsoft.com/support/options/)するか、[RDP トラブルシューティングの考え方と手順](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事をご覧ください。

## <a name="troubleshoot-using-azure-powershell"></a>Azure PowerShell を使用したトラブルシューティング
まだインストールしていない場合は、 [最新の Azure PowerShell をインストールして構成します](/powershell/azure/overview)。

次の例では、`myResourceGroup`、`myVM`、`myVMAccessExtension` などの変数を使用します。 これらの変数の名前と場所は、実際の値に置き換えてください。

> [!NOTE]
> [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) PowerShell コマンドレットを使用して、ユーザーの資格情報と RDP 構成をリセットします。 次の例では、`myVMAccessExtension` がプロセスの一部として指定した名前になります。 VMAccessAgent を前に操作したことがある場合は、`Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"` を使用して VM のプロパティをチェックすることで、既存の拡張機能の名前を取得できます。 名前を確認するには、出力の 'Extensions' セクションを調べます。

トラブルシューティングの各手順を実行した後、再度 VM に接続してみてください。 それでも接続できない場合は、次の手順をお試しください。

1. **RDP 接続をリセットする**。 このトラブルシューティングの手順では、リモート接続が無効になっていたり、Windows ファイアウォール規則によって RDP がブロックされている場合などに、RDP 構成をリセットします。
   
    次の例では、`WestUS` 内と `myResourceGroup` という名前のリソース グループ内にある `myVM` という名前の VM でRDP 接続をリセットします。
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **ネットワーク セキュリティ グループの規則を確認する**。 このトラブルシューティングの手順では、RDP トラフィックを許可するように、ネットワーク セキュリティ グループに規則があることを確認します。 RDP の既定のポートは、TCP ポート 3389 です。 RDP トラフィックを許可する規則は、VM を作成する際に自動で作成されない場合があります。
   
    最初に、ネットワーク セキュリティ グループのすべての構成データを `$rules` 変数に割り当てます。 次の例では、`myResourceGroup` という名前のリソース グループ内の `myNetworkSecurityGroup` という名前のネットワーク セキュリティ グループに関する情報を取得します。
   
    ```powershell
    $rules = Get-AzNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    ここで、このネットワーク セキュリティ グループに対して構成されている規則を確認します。 次のように着信接続用の TCP ポート 3389 を許可する規則が存在することを確認します。
   
    ```powershell
    $rules.SecurityRules
    ```
   
    次の例では、RDP トラフィックを許可する有効なセキュリティ規則が表示されています。 `Protocol`、`DestinationPortRange`、 `Access`、`Direction` が正しく構成されていることがわかります。
   
    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```
   
    RDP トラフィックを許可する規則がない場合、[ネットワーク セキュリティ グループの規則を作成します](../windows/nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 TCP ポート 3389 を使用できます。
3. **ユーザーの資格情報をリセットする**。 このトラブルシューティングの手順では、資格情報が明確でないか、覚えていない場合に、指定したローカル管理者アカウントのパスワードをリセットします。
   
    最初に、次のように資格情報を `$cred` 変数に割り当てて、ユーザー名と新しいパスワードを指定します。
   
    ```powershell
    $cred=Get-Credential
    ```
   
    ここで、VM の資格情報を更新します。 次の例では、`WestUS` 内と `myResourceGroup` という名前のリソース グループ内にある `myVM` という名前の VM で資格情報を更新します。
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **VM を再起動する**。 このトラブルシューティングの手順では、VM 自体で発生している根底にある問題を修正できます。
   
    次の例では、`myResourceGroup` という名前のリソース グループ内にある `myVM` という名前の VM を再起動します。
   
    ```powershell
    Restart-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **アプリケーションを再デプロイする**。 このトラブルシューティングの手順では、プラットフォームやネットワークの根底にある問題を修正するために、Azure 内の別のホストに VM を再デプロイします。
   
    次の例では、`WestUS` 内と `myResourceGroup` という名前のリソース グループ内にある `myVM` という名前の VM を再デプロイします。
   
    ```powershell
    Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **ルーティングを確認します**。 Network Watcher の[次ホップ](../../network-watcher/network-watcher-check-next-hop-portal.md)機能を使用して、ルートが仮想マシンとの間でトラフィックのルーティングを妨げていないことを確認します。 有効なルートを見直し、ネットワーク インターフェイスのすべての有効なルートを確認することもできます。 詳細については、「[有効なルートを使用した VM トラフィック フローのトラブルシューティング](../../virtual-network/diagnose-network-routing-problem.md)」を参照してください。

7. オンプレミスのファイアウォールまたはコンピューター上のファイアウォールが Azure に対する送信 TCP 3389 トラフィックを許可していることを確認します。

RDP の問題が解決しない場合は、[サポート要求を申請](https://azure.microsoft.com/support/options/)するか、[RDP トラブルシューティングの考え方と手順](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事をご覧ください。

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>クラシック デプロイ モデルを使用して作成された VM のトラブルシューティング
トラブルシューティングの各手順を実行した後、VM に再接続してみてください。

1. **RDP 接続をリセットする**。 このトラブルシューティングの手順では、リモート接続が無効になっていたり、Windows ファイアウォール規則によって RDP がブロックされている場合などに、RDP 構成をリセットします。
   
    Azure Portal で VM を選択します。 **[...More]** ボタンをクリックして、**[リモート アクセスのリセット]** をクリックします。
   
    ![Azure Portal で RDP 構成をリセットする](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Cloud Services エンドポイントを確認する**。 このトラブルシューティングの手順では、RDP トラフィックを許可するように、Cloud Services にエンドポイントがあることを確認します。 RDP の既定のポートは、TCP ポート 3389 です。 RDP トラフィックを許可する規則は、VM を作成する際に自動で作成されない場合があります。
   
   Azure Portal で VM を選択します。 **[エンドポイント]** ボタンをクリックして、VM 用に現在構成されているエンドポイントを表示します。 TCP ポート 3389 の RDP トラフィックを許可するエンドポイントが存在することを確認します。
   
   次の例は、RDP トラフィックを許可する有効なエンドポイントを示しています。
   
   ![Azure Portal で Cloud Services エンドポイントを確認する](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   RDP トラフィックを許可するエンドポイントがない場合、[ エンドポイントを作成します](../windows/classic/setup-endpoints.md)。 プライベート ポート 3389 に TCP を許可します。
3. **VM のブート診断を確認する**。 このトラブルシューティングの手順では、VM コンソール ログを確認し、VM が問題を報告しているかどうかを確認します。 すべての VM でブート診断を有効にしているとは限らないため、このトラブルシューティングの手順は省略できます。
   
    この記事では、具体的なトラブルシューティング手順については説明しませんが、RDP 接続に影響を与える、より広範な問題は示す場合があります。 コンソール ログと VM のスクリーン ショットの確認について詳しくは、「[VM のブート診断 (ブログの投稿)](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)」をご覧ください。
4. **VM リソース ヘルスを確認する**。 このトラブルシューティングの手順では、VM への接続に影響を受ける可能性のある Azure プラットフォームで既知の問題がないことを確認します。
   
    Azure Portal で VM を選択します。 [設定] ウィンドウを、一覧の下部付近の **[サポートとトラブルシューティング]** セクションまで下にスクロールします。 **[Resource health]** ボタンをクリックします。 正常な VM には **[使用可能]** と表示されます。
   
    ![Azure Portal で VM リソース ヘルスを確認する](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **ユーザーの資格情報をリセットする**。 このトラブルシューティングの手順では、資格情報が明確でないか、覚えていない場合に、指定したローカル管理者アカウントのパスワードをリセットします。  VM にログインしたら、そのユーザーのパスワードをリセットしてください。
   
    Azure Portal で VM を選択します。 [設定] ウィンドウを、一覧の下部付近の **[サポートとトラブルシューティング]** セクションまで下にスクロールします。 **[Reset password]** ボタンをクリックします。 ユーザー名と新しいパスワードを入力します。 最後に、**[保存]** ボタンをクリックします。
   
    ![Azure Portal でユーザーの資格情報をリセットする](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **VM を再起動する**。 このトラブルシューティングの手順では、VM 自体で発生している根底にある問題を修正できます。
   
    Azure Portal で VM を選択し、**[概要]** タブをクリックします。**[再起動]** ボタンをクリックします。
   
    ![Azure Portal で VM を再起動する](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. オンプレミスのファイアウォールまたはコンピューター上のファイアウォールが Azure に対する送信 TCP 3389 トラフィックを許可していることを確認します。

RDP の問題が解決しない場合は、[サポート要求を申請](https://azure.microsoft.com/support/options/)するか、[RDP トラブルシューティングの考え方と手順](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事をご覧ください。

## <a name="troubleshoot-specific-rdp-errors"></a>特定の RDP エラーのトラブルシューティング
お使いの VM に RDP 経由で接続しようとすると、特定のエラー メッセージが表示される場合があります。 一般的なエラー メッセージは次のとおりです。

* [ライセンスを提供するためのリモート デスクトップ ライセンス サーバーがないため、リモート セッションは切断されました](troubleshoot-specific-rdp-errors.md#rdplicense)。
* [リモート デスクトップは、コンピューター "name" を見つけることができません](troubleshoot-specific-rdp-errors.md#rdpname)。
* [認証エラーが発生しました。ローカル セキュリティ機関にアクセスできません。](troubleshoot-specific-rdp-errors.md#rdpauth)
* [Windows セキュリティ エラー:お使いの資格情報は機能しませんでした](troubleshoot-specific-rdp-errors.md#wincred)。
* [このコンピューターはリモート コンピューターに接続できません](troubleshoot-specific-rdp-errors.md#rdpconnect)。

## <a name="additional-resources"></a>その他のリソース
上記のエラーが発生していないにもかかわらず、リモート デスクトップを介して VM に接続できない場合は、 [リモート デスクトップのトラブルシューティング](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。
* VM で実行されているアプリケーションへのアクセスにおけるトラブルシューティング手順については、[Azure VM で実行されているアプリケーションへのアクセスのトラブルシューティング](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。
* Secure Shell (SSH) を使用した Azure の Linux VM への接続で問題が発生している場合は、[Azure の Linux VM への SSH 接続のトラブルシューティング](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。


