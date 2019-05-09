---
title: オフライン モードでの Azure VM エージェントのインストール | Microsoft Docs
description: オフライン モードで Azure VM エージェントをインストールする方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: e9fc8351b5e9a4f2274f0906d4071f86dcbcff26
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259684"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>オフライン モードでの Azure 仮想マシン エージェントのインストール 

Azure 仮想マシン エージェント (VM エージェント) は、ローカル管理者パスワードのリセットやスクリプトのプッシュなどの便利な機能を備えています。 この記事では、オフラインの Windows 仮想マシン (VM) に対して VM エージェントをインストールする方法について説明します。 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>オフライン モードで VM エージェントを使用するタイミング

次のシナリオでは、オフライン モードで VM エージェントをインストールします。

- デプロイされた Azure VM に VM エージェントがインストールされていないか、またはエージェントが動作していない。
- VM の管理者パスワードを忘れたか、または VM にアクセスできない場合。

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>オフライン モードで VM エージェントをインストールする方法

オフライン モードで VM エージェントをインストールするには、次の手順に従います。

> [!NOTE]
> オフライン モードで VM エージェントをインストールするプロセスを自動化できます。
> これを行うには、[Azure VM の復旧スクリプト](https://github.com/Azure/azure-support-scripts/blob/master/VMRecovery/ResourceManager/README.md)を使用します。 Azure VM 復旧スクリプトを使用することを選択した場合は、次のプロセスを使用できます。
> 1. スクリプトを使用して影響を受ける VM の OS ディスクを復旧 VM にアタッチすることで、手順 1 をスキップします。
> 2. 手順 2 ～ 10 に従って軽減策を適用します。
> 3. スクリプトを使用して VM を再構築することで、手順 11 をスキップします。
> 4. 手順 12 に従います。

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>手順 1:VM の OS ディスクをデータ ディスクとして別の VM に接続する

1.  VM を削除します。 VM を削除するときは、必ず**ディスクを保持する**オプションを選択します。

2.  OS ディスクをデータ ディスクとして別の VM ("_トラブルシューティング ツール_" VM と呼ばれます) に接続します。 詳細については、[Azure Portal での Windows VM へのデータ ディスクの接続](../windows/attach-managed-disk-portal.md)に関するページをご覧ください。

3.  トラブルシューティング ツール VM に接続します。 **[コンピューターの管理]** > **[ディスクの管理]** の順に開きます。 OS ディスクがオンラインであることと、ドライブ文字がディスク パーティションに割り当てられていることを確認します。

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>手順 2: Azure VM エージェントをインストールするように OS ディスクを変更する

1.  トラブルシューティング ツール VM へのリモート デスクトップ接続を作成します。

2.  接続した OS ディスクで、\windows\system32\config フォルダーを参照します。 ロールバックが必要な場合に備えて、このフォルダーのすべてのファイルをバックアップとしてコピーします。

3.  **レジストリ エディター** (regedit.exe) を起動します。

4.  **HKEY_LOCAL_MACHINE** キーを選択します。 メニューで、**[ファイル]** > **[ハイブの読み込み]** を選択します。

    ![ハイブを読み込む](./media/install-vm-agent-offline/load-hive.png)

5.  接続した OS ディスクで、\windows\system32\config\SYSTEM フォルダーを参照します。 ハイブの名前として、「**BROKENSYSTEM**」と入力します。 **HKEY_LOCAL_MACHINE** キーの下に、新しいレジストリ ハイブが表示されます。

6.  接続した OS ディスクで、\windows\system32\config\SOFTWARE フォルダーを参照します。 ハイブ ソフトウェアの名前として、「**BROKENSYSTEM**」と入力します。

7. 接続された OS ディスクに VM エージェントがインストールされている場合は、現在の構成のバックアップを実行します。 VM エージェントがインストールされていない場合は、次の手順に進みます。
      
    1. \windowsazure フォルダーの名前を \windowsazure.old に変更します。

    2. 次のレジストリをエクスポートします。
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  トラブルシューティング ツール VM 上の既存のファイルを、VM エージェントのインストール用リポジトリとして使用します。 次の手順を完了します。

    1. トラブルシューティング ツール VM から、次のサブキーをレジストリ形式 (.reg) でエクスポートします。 
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\RdAgent

          ![レジストリ サブキーをエクスポートする](./media/install-vm-agent-offline/backup-reg.png)

    2. レジストリ ファイルを編集します。 各ファイルで、(次の図に示すように) エントリ値 **SYSTEM** を **BROKENSYSTEM** に変更し、ファイルを保存します。 現在の VM エージェントの **ImagePath** を思い出してください。 該当するフォルダーを、接続された OS ディスクにコピーする必要があります。 

        ![レジストリ サブキーの値を変更する](./media/install-vm-agent-offline/change-reg.png)

    3. 各レジストリ ファイルをダブルクリックして、レジストリ ファイルをリポジトリにインポートします。

    4. 次の 3 つのサブキーが **BROKENSYSTEM** ハイブに正常にインポートされていることを確認します。
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - RdAgent

    5. 接続された OS ディスクに、現在の VM エージェントのインストール フォルダーをコピーします。 

        1.  接続された OS ディスクのルート パスに、WindowsAzure という名前のフォルダーを作成します。

        2.  トラブルシューティングしている VM の C:\WindowsAzure に移動し、C:\WindowsAzure\GuestAgent_X.X.XXXX.XXX という名前のフォルダーを探します。 最新のバージョン番号を持つ GuestAgent フォルダーを、C:\WindowsAzure から 接続された OS ディスクの WindowsAzure フォルダーにコピーします。 コピーする必要があるフォルダーがどれかわからない場合は、すべての GuestAgent フォルダーをコピーしてください。 次の図は、接続された OS ディスクにコピーされた GuestAgent フォルダーの例を示しています。

             ![GuestAgent フォルダーをコピーする](./media/install-vm-agent-offline/copy-files.png)

9.  **[BROKENSYSTEM]** を選択します。 メニューから、**[ファイル]** > **[ハイブのアンロード]** を選択します。

10.  **[BROKENSOFTWARE]** を選択します。 メニューから、**[ファイル]** > **[ハイブのアンロード]** を選択します。

11.  OS ディスクの接続を解除してから、OS ディスクを使用して VM を再作成します。

12.  VM にアクセスします。 RdAgent が実行されていて、ログが生成されていることがわかります。

Resource Manager デプロイ モデルを使用して VM を作成した場合は、これで完了です。

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>クラシック VM での ProvisionGuestAgent プロパティの使用

クラシック モデルを使用して VM を作成した場合は、Azure PowerShell モジュールを使用して **ProvisionGuestAgent** プロパティを更新します。 このプロパティによって、Azure は VM に VM エージェントがインストールされていることを認識します。

**ProvisionGuestAgent** プロパティを設定するには、Azure PowerShell で次のコマンドを実行します。

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

`Get-AzureVM` コマンドを実行します。 **GuestAgentStatus** プロパティにデータが設定されていることがわかります。

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>次の手順

- [Azure 仮想マシン エージェントの概要](../extensions/agent-windows.md)
- [Windows 用の仮想マシン拡張機能とその機能](../extensions/features-windows.md)
