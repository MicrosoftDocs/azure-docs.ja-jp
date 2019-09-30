---
title: Azure エージェントなしでローカルの Windows パスワードをリセットする | Microsoft Docs
description: Azure のゲスト エージェントが VM 上にインストールされていない場合または Azure のゲスト エージェントが VM 上で機能していない場合に、ローカルの Windows ユーザー アカウントのパスワードをリセットする方法
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: genli
ms.openlocfilehash: 6faab5bffaddbbd5d8deb9c3834bf3d8fe3e3445
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058656"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Azure VM のローカルの Windows パスワードをオフラインでリセットする
Azure ゲスト エージェントがインストールされている場合、[Azure Portal または Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を使用して、Azure 内の VM のローカルの Windows パスワードをリセットできます。 これは、Azure VM のパスワードをリセットする最も一般的な方法です。 Azure のゲスト エージェントが応答しない場合やカスタム イメージのアップロード後にインストールに失敗する場合、Windows のパスワードを手動でリセットできます。 この記事では、ソース OS の仮想ディスクを別の VM に接続してローカル アカウントのパスワードをリセットする方法について説明します。 この記事に記載されている手順は、Windows ドメイン コントローラーには適用されません。 

> [!WARNING]
> この方法は、他の方法を利用できない場合のみ使用してください。 必ず最初に、[Azure Portal または Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を使用したパスワードのリセットをお試しください。

## <a name="overview-of-the-process"></a>プロセスの概要
Azure ゲスト エージェントへのアクセス権がない場合に Azure 内の Windows VM のローカル パスワードをリセットする基本的な手順は次のとおりです。

1. 影響を受けている VM を停止します。
1. VM の OS ディスクのスナップショットを作成します。
1. スナップショットから OS ディスクのコピーを作成します。
1. コピーした OS ディスクを別の Windows VM にアタッチしてマウントし、ディスク上にいくつかの構成ファイルを作成します。 ファイルは、パスワードのリセットに役立ちます。
1. コピーした OS ディスクのマウントを解除し、トラブルシューティング用 VM から切断します。
1. 影響を受けている VM の OS ディスクを交換します。

## <a name="detailed-steps-for-the-vm-with-resource-manager-deployment"></a>Resource Manager のデプロイを使用した VM の詳細な手順

> [!NOTE]
> この手順は、Windows ドメイン コントローラーには適用されません。 スタンドアロン サーバーまたはドメインのメンバーであるサーバーでのみ機能します。

次の手順を試す前に、[Azure Portal または Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を使用したパスワードのリセットを必ずお試しください。 開始する前に、必ず VM のバックアップをおとりください。

1. 影響を受けている VM の OS ディスクのスナップショットを取得し、スナップショットからディスクを作成して、トラブルシューティング用 VM にディスクをアタッチします。 詳細については、[Azure portal を使用した OS ディスクの復旧 VM へのアタッチによる Windows VM のトラブルシューティング](troubleshoot-recovery-disks-portal-windows.md)に関するページを参照してください。
2. リモート デスクトップを使用してトラブルシューティング用 VM に接続します。
3. ソース VM のドライブ上の `\Windows\System32\GroupPolicy` に `gpt.ini` を作成します (gpt.ini が存在する場合は、gpt.ini.bak に名前を変更します)。
   
   > [!WARNING]
   > トラブルシューティング VM の OS ドライブである C:\Windows に誤って次のファイルを作成していないかご確認ください。 次のファイルは、データ ディスクとして接続されているソース VM の OS ドライブに作成してください。
   
   * 作成した `gpt.ini` ファイルに次の行を追加します。
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![gpt.ini を作成する](./media/reset-local-password-without-agent/create-gpt-ini.png)

4. `\Windows\System32\GroupPolicy\Machines\Scripts\` に `scripts.ini` を作成します。 非表示のフォルダーが表示されていることを確認します。 必要に応じて、`Machine` フォルダーまたは `Scripts` フォルダーを作成します。
   
   * 作成した `scripts.ini` ファイルに次の行を追加します。
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![scripts.ini を作成する](./media/reset-local-password-without-agent/create-scripts-ini.png)

5. `\Windows\System32` に次の内容を含む `FixAzureVM.cmd` を作成します。`<username>` と `<newpassword>` は実際の値に置き換えます。
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![FixAzureVM.cmd を作成する](./media/reset-local-password-without-agent/create-fixazure-cmd.png)
   
    新しいパスワードを決めるときには、VM のパスワードの複雑さの要件を満たす必要があります。

6. Azure portal で、トラブルシューティング VM からディスクの接続を切断します。

7. [影響を受けている VM の OS ディスクを変更します](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm)。

8. 新しい VM が起動したら、`FixAzureVM.cmd` スクリプトに指定した新しいパスワードを使って、VM に接続します。

9. リモート セッションから新しい VM までの次のファイルを削除して環境をクリーンアップします。
    
    * %Windir%\System32 から
      * FixAzureVM.cmd を削除します
    * %windir%\System32\GroupPolicy\Machine\Scripts から
      * scripts.ini を削除します
    * %windir%\System32\GroupPolicy から
      * gpt.ini を削除します (以前に gpt.ini が存在し、gpt.ini.bak に名前を変更した場合、この .bak ファイルの名前を変更して gpt.ini に戻します)

## <a name="detailed-steps-for-classic-vm"></a>クラシック VM での詳しい手順

> [!NOTE]
> この手順は、Windows ドメイン コントローラーには適用されません。 スタンドアロン サーバーまたはドメインのメンバーであるサーバーでのみ機能します。

次の手順を試す前に、[Azure Portal または Azure PowerShell](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/reset-rdp) を使用したパスワードのリセットを必ずお試しください。 開始する前に、必ず VM のバックアップをおとりください。 

1. Azure Portal で、影響を受ける VM を削除します。 VM を削除しても削除されるのは、Azure 内の VM の参照であるメタデータのみです。 VMが削除されても、仮想ディスクは保持されます。
   
   * Azure portal で VM を選んで、 *[削除]* をクリックします。
     
     ![既存の VM を削除する](./media/reset-local-password-without-agent/delete-vm-classic.png)

2. トラブルシューティング VM に VM の OS ディスクを接続します。 トラブルシューティング VM は、ソース VM の OS ディスクと同じリージョン (`West US` など) にある必要があります。
   
   1. Azure Portal でトラブルシューティング VM を選びます。 *[Disks]*  |  *[Attach existing]* をクリックします。
     
      ![既存のディスクを接続する](./media/reset-local-password-without-agent/disks-attach-existing-classic.png)
     
   2. *[VHD File]* を選択してから、ソース VM を含むストレージ アカウントを選びます。
     
      ![ストレージ アカウントを選択する](./media/reset-local-password-without-agent/disks-select-storage-account-classic.png)
     
   3. *[従来のストレージ アカウントの表示]* というチェック ボックスをオンにして、ソース コンテナーを選択します。 ソース コンテナーは、通常 *VHD* です。
     
      ![ストレージ コンテナーを選ぶ](./media/reset-local-password-without-agent/disks-select-container-classic.png)

      ![ストレージ コンテナーを選ぶ](./media/reset-local-password-without-agent/disks-select-container-vhds-classic.png)
     
   4. 接続する OS VHD を選びます。 *[選択]* をクリックしてプロセスを完了します。
     
      ![ソース仮想ディスクを選択する](./media/reset-local-password-without-agent/disks-select-source-vhd-classic.png)

   5. [OK] をクリックしてディスクをアタッチします。

      ![既存のディスクを接続する](./media/reset-local-password-without-agent/disks-attach-okay-classic.png)

3. リモート デスクトップを使用してトラブルシューティング VM に接続し、ソース VM の OS ディスクが表示されていることを確認します。

   1. Azure Portal でトラブルシューティング VM を選択して、 *[接続]* をクリックします。

   2. ダウンロードを行う RDP ファイルを開きます。 トラブルシューティング VM のユーザー名とパスワードを入力します。

   3. エクスプローラーで、接続されているデータ ディスクを探します。 ソース VM の VHD がトラブルシューティングの VM に接続されている唯一のデータ ディスクの場合は、ソース VM の VHD が F: ドライブになっている必要があります。
     
      ![接続されたデータ ディスクを表示する](./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer-classic.png)

4. ソース VM のドライブ上の `\Windows\System32\GroupPolicy` に `gpt.ini` を作成します (`gpt.ini` が存在する場合は、`gpt.ini.bak` に名前を変更します)。
   
   > [!WARNING]
   > トラブルシューティング VM の OS ドライブである `C:\Windows` に誤って次のファイルを作成していないかご確認ください。 次のファイルは、データ ディスクとして接続されているソース VM の OS ドライブに作成してください。
   
   * 作成した `gpt.ini` ファイルに次の行を追加します。
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![gpt.ini を作成する](./media/reset-local-password-without-agent/create-gpt-ini-classic.png)

5. `\Windows\System32\GroupPolicy\Machines\Scripts\` に `scripts.ini` を作成します。 非表示のフォルダーが表示されていることを確認します。 必要に応じて、`Machine` フォルダーまたは `Scripts` フォルダーを作成します。
   
   * 作成した `scripts.ini` ファイルに次の行を追加します。

     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![scripts.ini を作成する](./media/reset-local-password-without-agent/create-scripts-ini-classic.png)

6. `\Windows\System32` に次の内容を含む `FixAzureVM.cmd` を作成します。`<username>` と `<newpassword>` は実際の値に置き換えます。
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![FixAzureVM.cmd を作成する](./media/reset-local-password-without-agent/create-fixazure-cmd-classic.png)
   
    新しいパスワードを決めるときには、VM のパスワードの複雑さの要件を満たす必要があります。

7. Azure Portal で、トラブルシューティング VM からディスクを取り外します。
   
   1. Azure Portal でトラブルシューティング VM を選び、 *[Disks]* をクリックします。
   
   2. 手順 2 で接続したデータ ディスクを選択し、 **[デタッチ]** をクリックし、 **[OK]** をクリックします。

     ![ディスクを取り外す](./media/reset-local-password-without-agent/data-disks-classic.png)
     
     ![ディスクを取り外す](./media/reset-local-password-without-agent/detach-disk-classic.png)

8. ソース VM の OS ディスクから VM を作成します。
   
     ![テンプレートから VM を作成する](./media/reset-local-password-without-agent/create-new-vm-from-template-classic.png)

     ![テンプレートから VM を作成する](./media/reset-local-password-without-agent/choose-subscription-classic.png)

     ![テンプレートから VM を作成する](./media/reset-local-password-without-agent/create-vm-classic.png)

## <a name="complete-the-create-virtual-machine-experience"></a>仮想マシンの作成エクスペリエンスを完了する

1. 新しい VM が起動したら、`FixAzureVM.cmd` スクリプトに指定した新しいパスワードを使って、VM に接続します。

2. リモート セッションから新しい VM までの次のファイルを削除して環境をクリーンアップします。
    
    * `%windir%\System32` から
      * `FixAzureVM.cmd` を削除します
    * `%windir%\System32\GroupPolicy\Machine\Scripts` から
      * `scripts.ini` を削除します
    * `%windir%\System32\GroupPolicy` から
      * `gpt.ini` を削除します (以前に `gpt.ini` が存在し、`gpt.ini.bak` に名前を変更した場合、この `.bak` ファイルの名前を変更して `gpt.ini` に戻します)

## <a name="next-steps"></a>次の手順
それでもリモート デスクトップを使用して接続できない場合は、[RDP トラブルシューティング ガイド](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)をご覧ください。 [詳細な RDP トラブルシューティングガイド](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)では、具体的な手順というよりは、トラブルシューティングの手法について説明しています。 また、直接のサポートについては、[Azure のサポートに依頼](https://azure.microsoft.com/support/options/)できます。
