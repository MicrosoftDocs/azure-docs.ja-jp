---
title: Azure 内の Windows 10 または Windows Server 2016 VM にリモート接続するときの netvsc.sys の問題のトラブルシューティング | Microsoft Docs
description: Azure 内の Windows 10 または Windows Server 2016 の VM に接続するときの netsvc.sys に関する RDP の問題をトラブルシューティングする方法を説明します。
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/19/2018
ms.author: genli
ms.openlocfilehash: 4c10a2dcd55c1605cfafe6c67cfefd9d8a3c5f9d
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057987"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>netvsc.sys のために Azure 内の Windows 10 または Windows Server 2016 VM にリモート接続できない

この記事では、Hyper-V Server 2016 ホスト上の Windows 10 または Windows Server 2016 Datacenter の仮想マシン (VM) に接続するときに、ネットワーク接続が存在しない問題をトラブルシューティングする方法について説明します。

## <a name="symptoms"></a>現象

リモート デスクトップ プロトコル (RDP) を使用して、Windows 10 または Windows Server 2016 の Azure VM に接続できません。 [ブート診断](boot-diagnostics.md)の画面では、ネットワーク インターフェイス カード (NIC) の上に赤い十字が表示されます。 これは、オペレーティング システムが完全に読み込まれた後で、VM への接続がないことを示します。

通常、この問題は、Windows の[ビルド 14393](https://support.microsoft.com/help/4093120/) および[ビルド 15063](https://support.microsoft.com/help/4015583/) で発生します。 お使いのオペレーティング システムのバージョンがこれらのバージョンより新しい場合は、発生しているシナリオにこの記事は当てはまりません。 システムのバージョンを確認するには、[シリアル アクセス コンソール機能](serial-console-windows.md)で CMD セッションを開き、**Ver** を実行します。

## <a name="cause"></a>原因

この問題は、インストールされている netvsc.sys システム ファイルのバージョンが **10.0.14393.594** または **10.0.15063.0** である場合に、発生する可能性があります。 これらのバージョンの netvsc.sys では、システムが Azure プラットフォームと対話できない場合があります。


## <a name="solution"></a>解決策

これらの手順を実行する前に、バックアップとして、影響を受ける VM の[システム ディスクのスナップショットを取得](../windows/snapshot-copy-managed-disk.md)します。 この問題のトラブルシューティングを行うには、シリアル コンソールを使用するか、VM のシステム ディスクを復旧 VM にアタッチして [VM をオフライン修復](#repair-the-vm-offline)します。


### <a name="use-the-serial-console"></a>シリアル コンソールの使用

[シリアル コンソールに接続して、PowerShell インスタンスを開き](serial-console-windows.md)、以下の手順のようにします。

> [!NOTE]
> VM でシリアル コンソールが有効になっていない場合は、「[VM をオフライン修復する](#repair-the-vm-offline)」セクションに移動します。

1. PowerShell インスタンスで次のコマンドを実行して、ファイル (**c:\windows\system32\drivers\netvsc.sys**) のバージョンを取得します。

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. 同じリージョンから作業用 VM にアタッチされている新規または既存のデータ ディスクに、適切な更新プログラムをダウンロードします。

   - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562)  以降の更新プログラム
   - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) 以降の更新プログラム

3. 作業用 VM からユーティリティ ディスクをデタッチして、破損した VM にアタッチします。

4. 次のコマンドを実行して、VM に更新プログラムをインストールします。

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. VM を再起動します。

### <a name="repair-the-vm-offline"></a>VM をオフライン修復する

1. [復旧 VM にシステム ディスクを取り付ける](../windows/troubleshoot-recovery-disks-portal.md)。

2. 復旧 VM へのリモート デスクトップ接続を開始します。

3. ディスクが [ディスクの管理] コンソールで **[オンライン]** になっていることを確認します。 アタッチしたシステム ディスクに割り当てられているドライブ文字をメモします。

4. 変更のロールバックが必要なケースに備えて、 **\Windows\System32\config** フォルダーのコピーを作成します。

5. 復旧用 VM で、レジストリ エディター (regedit.exe) を開始します。

6. **HKEY_LOCAL_MACHINE** キーを選択し、メニューから **[ファイル]**  >  **[ハイブの読み込み]** を選択します。

7. **\Windows\System32\config** フォルダーの SYSTEM ファイルを探します。

8. **[開く]** を選択し、名前に「**BROKENSYSTEM**」と入力して、**HKEY_LOCAL_MACHINE** キーを展開し、**BROKENSYSTEM** という名前の追加したキーを探します。

9. 次の場所に移動します。

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. 各サブキー (0000 など) で、**Microsoft HYPER-V Network Adapter** として表示される **DriverDesc** の値を調べます。

11. サブキーで、VM のネットワーク アダプターのドライバーのバージョンである **DriverVersion** の値を確認します。

12. 適切な更新プログラムをダウンロードします。

    - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562)  以降の更新プログラム
    - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) 以降の更新プログラム

13. 更新プログラムをダウンロードできる復旧用 VM で、データ ディスクとしてシステム ディスクをアタッチします。

14. 次のコマンドを実行して、VM に更新プログラムをインストールします。

    ```
    dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
    ```

15. 次のコマンドを実行して、ハイブのマウントを解除します。

    ```
    reg unload HKLM\BROKENSYSTEM
    ```

16. [システム ディスクをデタッチして、VM を再度作成します](../windows/troubleshoot-recovery-disks-portal.md)。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、問題を迅速に解決するために、[Azure サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
