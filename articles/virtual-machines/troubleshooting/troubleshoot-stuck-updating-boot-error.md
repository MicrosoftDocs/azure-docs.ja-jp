---
title: Azure VM の起動が Windows Update で停止する | Microsoft Docs
description: Windows Update で Azure VM の起動が停止しているときに問題のトラブルシューティングを行う方法について説明します。
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: genli
ms.openlocfilehash: 226151d81319dc4e6f132e76ce2d310f88a484e8
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087016"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Azure VM の起動が Windows Update で停止する

この記事では、仮想マシン (VM) が起動中に Windows Update の段階で停止する場合の問題の解決を支援します。 

> [!NOTE] 
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、Resource Manager デプロイ モデルの使用について説明します。 新しいデプロイでは、クラシック デプロイ モデルではなく、このモデルを使用することをお勧めします。

## <a name="symptom"></a>症状

 Windows VM が起動しません。 [[ブート診断]](../troubleshooting/boot-diagnostics.md) ウィンドウでスクリーンショットを調べると、起動が更新プロセスで停止していることがわかります。 表示されるメッセージの例を次に示します。

- Installing Windows ##% Don't turn off your PC.\(Windows をインストールしています ##% PC の電源を切らないでください。\) This will take a while Your PC will restart several times\(処理にしばらくかかります PC は複数回再起動されます\)
- Keep your PC on until this is done.\(処理が完了するまで PC の電源を切らないでください。\) Installing update # of #...\(更新をインストールしています #/#...\) 
- We couldn't complete the updates Undoing changes Don't turn off your computer\(更新を完了できませんでした 元に戻しています コンピューターの電源を切らないでください\)
- Failure configuring Windows updates Reverting changes Do not turn off your computer\(Windows Update の構成に失敗しました 変更を元に戻しています コンピューターの電源を切らないでください\)
- Error < error code > applying update operations ##### of ##### (\Regist...)\(更新操作 #####/##### の適用中にエラー < error code > が発生しました (\Regist...)\)
- Fatal Error < error code >  applying update operations ##### of ##### ($$...)\(更新操作 #####/##### の適用中に致命的なエラー < error code > が発生しました ($$...)\)


## <a name="solution"></a>解決策

インストールまたはロールバックしている更新の数によっては、更新プロセスに時間がかかる場合があります。 VM を 8 時間この状態のままにします。 この期間の経過後も VM がこの状態の場合、Azure portal から VM を再起動し、正常に起動できるかどうかを確認します。 この手順でうまくいかない場合は、次の解決策をお試しください。

### <a name="remove-the-update-that-causes-the-problem"></a>問題の原因となっている更新プログラムを削除する

1. バックアップとして、影響を受ける VM の OS ディスクのスナップショットを取得します。 詳細については、[ディスクのスナップショット](../windows/snapshot-copy-managed-disk.md)に関する記事を参照してください。 
2. [復旧 VM に OS ディスクを接続します](troubleshoot-recovery-disks-portal-windows.md)。
3. OS ディスクが復旧 VM に接続されたら、**diskmgmt.msc** を実行して [ディスクの管理] を開き、接続されたディスクが **[オンライン]** になっていることを確認します。 \Windows フォルダーを保持している接続された OS ディスクに割り当てられているドライブ文字をメモしておきます。 ディスクが暗号化されている場合は、このドキュメントの次の手順に進む前にディスクを復号化します。

4. 管理者特権でのコマンド プロンプト インスタンス ([管理者として実行]) を開きます。 次のコマンドを実行して、接続された OS ディスク上にある更新プログラム パッケージの一覧を取得します。

        dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt

    たとえば、接続された OS ディスクがドライブ F の場合は、次のコマンドを実行します。

        dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
5. C:\temp\Patch_level.txt ファイルを開き、下から順に読み取ります。 **インストールの保留中**または**アンインストールの保留中**状態にある更新プログラムを特定します。  更新状態のサンプルを次に示します。

     ```
    Package Identity : Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    State : Install Pending
    Release Type : Security Update
    Install Time :
    ```
6. 問題の原因となっている更新プログラムを削除します。
    
    ```
    dism /Image:<Attached OS disk>:\ /Remove-Package /PackageName:<PACKAGE NAME TO DELETE>
    ```
    例: 

    ```
    dism /Image:F:\ /Remove-Package /PackageName:Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    ```

    > [!NOTE] 
    > パッケージのサイズに応じて、DISM ツールではアンインストールの処理に時間がかかります。 通常、プロセスは 16 分以内に完了します。

7. [OS ディスクを切断して、VM を再作成します](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk)。 その後、問題が解決されているかどうかを確認します。
