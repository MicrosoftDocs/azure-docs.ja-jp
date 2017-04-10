---
title: "Azure エージェントなしでローカルの Windows パスワードをリセットする | Microsoft Docs"
description: "Azure のゲスト エージェントが VM 上にインストールされていない場合または Azure のゲスト エージェントが VM 上で機能していない場合に、ローカルの Windows ユーザー アカウントのパスワードをリセットする方法"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/20/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 370bcf5189c855185f11277518e0cbd5377993ab
ms.openlocfilehash: 74b1282bf205bc38f13c51c5e05f55987d0cd12e


---
# <a name="how-to-reset-local-windows-password-for-azure-vm"></a>Azure VM のローカルの Windows パスワードをリセットする方法
Azure ゲスト エージェントがインストールされている場合、[Azure Portal または Azure PowerShell](virtual-machines-windows-reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を使用して、Azure 内の VM のローカルの Windows パスワードをリセットできます。 これは、Azure VM のパスワードをリセットする最も一般的な方法です。 Azure のゲスト エージェントが応答しない場合やカスタム イメージのアップロード後にインストールに失敗する場合、Windows のパスワードを手動でリセットできます。 この記事では、ソース OS の仮想ディスクを別の VM に接続してローカル アカウントのパスワードをリセットする方法について説明します。 

> [!WARNING]
> この方法は、他の方法を利用できない場合のみ使用してください。 必ず最初に、[Azure Portal または Azure PowerShell](virtual-machines-windows-reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を使用したパスワードのリセットをお試しください。
> 
> 

## <a name="overview-of-the-process"></a>プロセスの概要
Azure ゲスト エージェントへのアクセス権がない場合に Azure 内の Windows VM のローカル パスワードをリセットする基本的な手順は次のとおりです。

* ソース VM を準備します。 仮想ディスクは保持されます。
* Azure サブスクリプション内の別の VM に、ソース VM の OS ディスクを接続します。 この VM は、トラブルシューティング VM と呼ばれます。
* トラブルシューティング VM を使用して、ソース VM の OS ディスクに構成ファイルをいくつか作成します。
* トラブルシューティング VM から VM の OS ディスクを取り外します。
* Resource Manager テンプレートを使用して、オリジナル仮想ディスクを使って VM を作成します。
* 新しい VM が起動すると、作成した構成ファイルによって、必要なユーザーのパスワードが更新されます。

## <a name="detailed-steps"></a>詳細な手順
次の手順を試す前に、[Azure Portal または Azure PowerShell](virtual-machines-windows-reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を使用したパスワードのリセットを必ずお試しください。 開始する前に、必ず VM のバックアップをおとりください。 

1. Azure Portal で、影響を受ける VM を削除します。 VM を削除しても削除されるのは、Azure 内の VM の参照であるメタデータのみです。 VMが削除されても、仮想ディスクは保持されます。
   
   * Azure Portal で VM を選んで、*[削除]* をクリックします。
     
     ![既存の VM を削除する](./media/virtual-machines-windows-reset-local-password-without-guest-agent/delete_vm.png)
2. トラブルシューティング VM に VM の OS ディスクを接続します。 トラブルシューティング VM は、ソース VM の OS ディスクと同じリージョン (`West US` など) にある必要があります。
   
   * Azure Portal でトラブルシューティング VM を選びます。 *[Disks]* | *[Attach existing]* をクリックします。
     
     ![既存のディスクを接続する](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_attach_existing.png)
     
     *[VHD File]* を選択してから、ソース VM を含むストレージ アカウントを選びます。
     
     ![ストレージ アカウントを選択する](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_storageaccount.PNG)
     
     ソース コンテナーを選びます。 ソース コンテナーは、通常 *VHD* です。
     
     ![ストレージ コンテナーを選ぶ](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_container.png)
     
     接続する OS VHD を選びます。 *[選択]* をクリックしてプロセスを完了します。
     
     ![ソース仮想ディスクを選択する](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_source_vhd.png)
3. リモート デスクトップを使用してトラブルシューティング VM に接続し、ソース VM の OS ディスクが表示されていることを確認します。
   
   * Azure Portal でトラブルシューティング VM を選択して、*[接続]* をクリックします。
   * ダウンロードを行う RDP ファイルを開きます。 トラブルシューティング VM のユーザー名とパスワードを入力します。
   * エクスプローラーで、接続されているデータ ディスクを探します。 ソース VM の VHD がトラブルシューティングの VM に接続されている唯一のデータ ディスクの場合は、ソース VM の VHD が F: ドライブになっている必要があります。
     
     ![接続されたデータ ディスクを表示する](./media/virtual-machines-windows-reset-local-password-without-guest-agent/troubleshooting_vm_fileexplorer.png)
4. ソース VM のドライブ上の `\Windows\System32\GroupPolicy` に `gpt.ini` を作成します (gpt.ini が存在する場合は、gpt.ini.bak に名前を変更します)。
   
   > [!WARNING]
   > トラブルシューティング VM の OS ドライブである C:\Windows に誤って次のファイルを作成していないかご確認ください。 次のファイルは、データ ディスクとして接続されているソース VM の OS ドライブに作成してください。
   > 
   > 
   
   * 作成した `gpt.ini` ファイルに次の行を追加します。
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![gpt.ini を作成する](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_gpt_ini.png)
5. `\Windows\System32\GroupPolicy\Machine\Scripts` に `scripts.ini` を作成します。 非表示のフォルダーが表示されていることを確認します。 必要に応じて、`Machine` フォルダーまたは `Scripts` フォルダーを作成します。
   
   * 作成した `scripts.ini` ファイルに次の行を追加します。
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![scripts.ini を作成する](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_scripts_ini.png)
6. `\Windows\System32` に次の内容を含む `FixAzureVM.cmd` を作成します。`<username>` と `<newpassword>` は実際の値に置き換えます。
   
    ```
    NET USER <username> <newpassword>
    ```
   
    ![FixAzureVM.cmd を作成する](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_fixazure_cmd.png)
   
    新しいパスワードを決めるときには、VM のパスワードの複雑さの要件を満たす必要があります。
7. Azure Portal で、トラブルシューティング VM からディスクを取り外します。
   
   * Azure Portal でトラブルシューティング VM を選び、*[Disks]* をクリックします。
   * 手順 2 で接続したデータ ディスクを選んで、*[Detach]* をクリックします。
     
     ![ディスクを取り外す](./media/virtual-machines-windows-reset-local-password-without-guest-agent/detach_disk.png)
8. VM を作成する前に、ソース OS ディスクへの URI を取得します。
   
   * Azure Portal でストレージ アカウントを選んで、*[Blobs]* を選択します。
   * コンテナーを選びます。 ソース コンテナーは、通常 *VHD* です。
     
     ![ストレージ アカウント BLOB を選ぶ](./media/virtual-machines-windows-reset-local-password-without-guest-agent/select_storage_details.png)
     
     ソース VM の OS VHD を選び、*[URL]* 名の横にある *[コピー]* をクリックします。
     
     ![ディスクの URI をコピーする](./media/virtual-machines-windows-reset-local-password-without-guest-agent/copy_source_vhd_uri.png)
9. ソース VM の OS ディスクから VM を作成します。
   
   * [この Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd)を使って、特殊な VHD から VM を作成します。 [`Deploy to Azure`] をクリックして Azure Portal を開きます。テンプレートの情報が自動入力されています。
   * VM の以前の設定をすべて保持する場合は、*[Edit template]* を選んで、既存の VNet、サブネット、ネットワーク アダプター、パブリック IP のいずれかを入力します。
   * [`OSDISKVHDURI`] パラメーター テキスト ボックスに、前の手順で取得したソース VHD の URI を貼り付けます。
     
     ![テンプレートから VM を作成する](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_new_vm_from_template.png)
10. 新しい VM が起動したら、`FixAzureVM.cmd` スクリプトに指定した新しいパスワードを使って、VM に接続します。
11. リモート セッションから新しい VM までの次のファイルを削除して環境をクリーンアップします。
    
    * %Windir%\System32 から
      * FixAzureVM.cmd を削除します
    * %windir%\System32\GroupPolicy\Machine\ から
      * scripts.ini を削除します
    * %windir%\System32\GroupPolicy から
      * gpt.ini を削除します (以前に gpt.ini が存在し、gpt.ini.bak に名前を変更した場合、この .bak ファイルの名前を変更して gpt.ini に戻します)

## <a name="next-steps"></a>次のステップ
それでもリモート デスクトップを使用して接続できない場合は、[RDP トラブルシューティング ガイド](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)をご覧ください。 [詳細な RDP トラブルシューティングガイド](virtual-machines-windows-detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)では、具体的な手順というよりは、トラブルシューティングの手法について説明しています。 また、直接のサポートについては、[Azure のサポートに依頼](https://azure.microsoft.com/support/options/)できます。




<!--HONumber=Dec16_HO3-->


