---
title: VM がセーフ モードで起動するため、Azure Virtual Machines にリモート接続できない | Microsoft Docs
description: VM がセーフ モードで起動するため VM に対して RDP 接続できないという問題のトラブルシューティングを行う方法について説明します | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: cc1200d6acef077e36f701a75f613aba0ccbb75f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103403"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>VM がセーフ モードで起動するため VM に RDP 接続できない

この記事では、VM がセーフ モードで起動するように構成されているために Azure Windows 仮想マシン (VM) に接続できない問題を解決する方法を示します。

> [!NOTE]
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルが用意されています。[Resource Manager とクラシック](../../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、Resource Manager デプロイ モデルの使用方法について説明しています。最新のデプロイでは、クラシック デプロイ モデルではなくこのモデルを使用することをお勧めします。

## <a name="symptoms"></a>現象

VM がセーフ モードで起動するように構成されているため、Azure 上の VM への RDP 接続またはその他の接続 (HTTP など) を行うことができません。 Azure portal 上で[ブート診断](../troubleshooting/boot-diagnostics.md)のスクリーンショットを確認すると、VM は正常に起動しているがネットワーク インターフェイスが使用可能でないことがわかる場合があります。

![セーフ モードでのネットワーク インターフェイスに関する画像](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>原因

RDP サービスは、セーフ モードでは使用できません。 VM がセーフ モードで起動するときは、基本的なシステム プログラムとサービスだけが読み込まれます。 これは、"最低限のセーフ ブート" および "接続のあるセーフ ブート" という 2 つの異なるバージョンのセーフ モードに適用されます。


## <a name="solution"></a>解決策

これらの手順を実行する前に、バックアップとして、影響を受ける VM の OS ディスクのスナップショットを取得します。 詳細については、[ディスクのスナップショット](../windows/snapshot-copy-managed-disk.md)に関する記事を参照してください。

この問題を解決するには、シリアル コントロールを使用して通常モードで起動するように VM を構成するか、または復旧 VM を使用して [VM をオフライン修復](#repair-the-vm-offline)します。

### <a name="use-serial-control"></a>シリアル コントロールを使用する

1. [シリアル コンソールに接続し、CMD インスタンスを開きます](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
   )。 VM 上でシリアル コンソールが有効になっていない場合は、「[VM をオフライン修復する](#repair-the-vm-offline)」をご覧ください。
2. ブート構成データを確認します。

        bcdedit /enum

    VM がセーフ モードで起動するように構成されている場合、 **[Windows ブート ローダー]** セクションに **[safeboot]** という追加のフラグが表示されます。 **[safeboot]** フラグが表示されない場合、VM はセーフ モードではありません。 そのシナリオには、この記事の内容は当てはまりません。

    **[safeboot]** フラグは、次の値と共に表示されることがあります。
   - 最小限
   - ネットワーク

     この 2 つのモードのいずれでも、RDP は開始されません。 そのため、修正プログラムは同じままです。

     ![セーフ モード フラグに関する画像](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. **[safemoade]** フラグを削除すると、VM が通常モードで起動します。

        bcdedit /deletevalue {current} safeboot

4. ブート構成データをチェックして、 **[safeboot]** フラグが削除されていることを確認します。

        bcdedit /enum

5. VM を再起動し、問題が解決されているかどうかを確認します。

### <a name="repair-the-vm-offline"></a>VM をオフライン修復する

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>復旧 VM に OS ディスクを接続する

1. [復旧 VM に OS ディスクを接続します](../windows/troubleshoot-recovery-disks-portal.md)。
2. 復旧 VM へのリモート デスクトップ接続を開始します。
3. ディスクが [ディスクの管理] コンソールで **[オンライン]** になっていることを確認します。 接続された OS ディスクに割り当てられたドライブ文字をメモします。

#### <a name="enable-dump-log-and-serial-console-optional"></a>ダンプ ログとシリアル コンソールを有効にする (省略可能)

ダンプ ログとシリアル コンソールは、この記事の解決策では問題を解決できない場合にさらにトラブルシューティングを行うのに役立ちます。

ダンプ ログとシリアル コンソールを有効にするには、次のスクリプトを実行します。

1. 管理者特権のコマンド プロンプト セッション ( **[管理者として実行]** ) を開きます。
2. 次のスクリプトを実行します。

    このスクリプトでは、接続されている OS ディスクに割り当てられているドライブ文字が F であると想定しています。このドライブ文字を実際の VM の適切な値に置き換えてください。

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

#### <a name="configure-the-windows-to-boot-into-normal-mode"></a>通常モードで起動するように Windows を構成する

1. 管理者特権のコマンド プロンプト セッション ( **[管理者として実行]** ) を開きます。
2. ブート構成データを確認します。 次のコマンドでは、接続されている OS ディスクに割り当てられているドライブ文字が F であると想定しています。このドライブ文字を実際の VM の適切な値で置き換えてください。

        bcdedit /store F:\boot\bcd /enum
    **\windows** フォルダーが含まれるパーティションの識別子名をメモしておきます。 既定では、識別子名は "Default" です。

    VM がセーフ モードで起動するように構成されている場合、 **[Windows ブート ローダー]** セクションに **[safeboot]** という追加のフラグが表示されます。 **safeboot** フラグが表示されない場合、この記事の内容は目的のシナリオに当てはまりません。

    ![ブートと識別子に関する画像](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. **safeboot** フラグを削除すると、VM が通常モードで起動します。

        bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
4. ブート構成データをチェックして、 **[safeboot]** フラグが削除されていることを確認します。

        bcdedit /store F:\boot\bcd /enum
5. [OS ディスクを切断して、VM を再作成します](../windows/troubleshoot-recovery-disks-portal.md)。 その後、問題が解決されているかどうかを確認します。
