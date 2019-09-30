---
title: Azure VM での Windows 再起動ループ | Microsoft Docs
description: Windows 再起動ループをトラブルシューティングする方法 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/15/2018
ms.author: genli
ms.openlocfilehash: d8a1d64ac8e65fd52730ee1750c0b0b1949b3512
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088469"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Azure VM での Windows 再起動ループ
この記事では、Microsoft Azure の Windows 仮想マシン (VM) で発生する可能性がある再起動ループについて説明します。

## <a name="symptom"></a>症状

[ブート診断](./boot-diagnostics.md)を使用して VM のスクリーンショットを取得すると、仮想マシンが起動しているが、ブート プロセスが中断されたために、プロセスがやり直しになっていることがわかります。

![スタート画面 1](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>原因

再起動ループは以下の原因のために発生します。

### <a name="cause-1"></a>原因 1

重大な状態としてフラグが設定されたサードパーティ サービスがあり、そのサービスが起動できません。 このためにオペレーティング システムが再起動されます。

### <a name="cause-2"></a>原因 2

オペレーティング システムにいくつかの変更が行われました。 通常、これらは更新プログラムのインストール、アプリケーションのインストール、または新しいポリシーに関連します。 次のログで詳細を確認する必要があります。

- イベント ログ
- CBS.logWindows
- Update.log

### <a name="cause-3"></a>原因 3

ファイル システムの破損によって引き起こされる可能性があります。 ただし、オペレーティング システムの破損の原因となった変更を診断して特定することは困難です。

## <a name="solution"></a>解決策

この問題を解決するには、[OS ディスクをバックアップ](../windows/snapshot-copy-managed-disk.md)し、[OS ディスクを復旧用の VM に接続](../windows/troubleshoot-recovery-disks-portal.md)してから、該当する解決方法に従うか、解決方法を 1 つずつ試してみます。

### <a name="solution-for-cause-1"></a>原因 1 の解決策

1. OS ディスクを稼働中の VM に接続したら、[ディスクの管理] コンソールでディスクが **[オンライン]** になっていることを確認して、 **\Windows** フォルダーを含むパーティションのドライブ名をメモします。

2. ディスクが **[オフライン]** に設定されている場合は、 **[オンライン]** に設定します。

3. 変更のロールバックが必要なケースに備えて、 **\Windows\System32\config** フォルダーのコピーを作成します。

4. 復旧用 VM で、Windows レジストリ エディター (regedit) を開きます。

5. "**HKEY_LOCAL_MACHINE**" キーを選択してから、 **[ファイル]**  >  **[ハイブの読み込み]** をメニューから選択します。

6. **\Windows\System32\config** フォルダーの SYSTEM ファイルを参照します。

7. **[開く]** を選択し、名前に **BROKENSYSTEM** を入力し、"**HKEY_LOCAL_MACHINE**" キーを展開すると、"**BROKENSYSTEM**" という追加のキーが表示されます。

8. コンピューターが起動される ControlSet を確認します。 次のレジストリ キーにキー番号が表示されます。

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. 次のレジストリ キーで、VM エージェント サービスの重要度を確認します。

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. レジストリ キーの値が **2** に設定されていない場合は、次の軽減策に移ります。

11. レジストリ キーの値が **2** に設定されている場合は、値を **2** から **1** に変更します。

12. 次のいずれかのキーが存在しており、値が **2** または **3** の場合は、それらの値を **1** に変更します。

    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. "**BROKENSYSTEM**" キーを選択してから、 **[ファイル]**  >  **[ハイブの読み込み]** をメニューから選択します。

14. トラブルシューティング用 VM と OS ディスクの接続を切断します。

15. トラブルシューティング用 VM からディスクを取り出し、Azure がこのディスクを解放するまで約 2 分待ちます。

16. [OS ディスクから新しい VM を作成します](../windows/create-vm-specialized.md)。

17. この問題が修正されたとき、場合によっては [RDAgent](https://blogs.msdn.microsoft.com/mast/2014/04/07/install-the-vm-agent-on-an-existing-azure-vm/) (WaAppAgent.exe) を再インストールする必要があります。

### <a name="solution-for-cause-2"></a>原因 2 の解決策

VM を前回正常起動時の構成に復元してから、「[Azure Windows VM を前回正常起動時の構成で起動する方法](https://support.microsoft.com/help/4016731/)」の手順に従います。

### <a name="solution-for-cause-3"></a>原因 3 の解決策
>[!NOTE]
>次の手順は最後の手段としてのみ使用してください。 regback から復元すると、マシンへのアクセスが復元されますが、OS は安定していないと見なされます。ハイブのタイムスタンプと現在の日付の間でレジストリに失われたデータがあるためです。 新しい VM を構築して、データを移行する計画を立てる必要があります。

1. ディスクをトラブルシューティング用 VM に接続したら、[ディスクの管理] コンソールでそのディスクが **[オンライン]** になっていることを確認します。

2. 変更のロールバックが必要なケースに備えて、 **\Windows\System32\config** フォルダーのコピーを作成します。

3. **\Windows\System32\config\regback** フォルダー内のファイルをコピーし、 **\Windows\System32\config** フォルダー内のファイルを置き換えます。

4. トラブルシューティング用 VM からディスクを取り出し、Azure がこのディスクを解放するまで約 2 分待ちます。

5. [OS ディスクから新しい VM を作成します](../windows/create-vm-specialized.md)。


