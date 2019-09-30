---
title: DHCP が無効になっているために Azure 仮想マシンにリモート接続できない | Microsoft Docs
description: Microsoft Azure において DHCP クライアント サービスが無効になっているために発生する RDP の問題をトラブルシューティングする方法について学習します。 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: ef44931cc3b36bcab64a2de840d9264c1b8fdedb
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058018"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>DHCP クライアント サービスが無効になっているために Azure 仮想マシンに RDP で接続できない

この記事では、Azure Windows 仮想マシン (VM) に DHCP クライアント サービスを構成した後でその VM にリモート デスクトップ接続できない問題について説明します。

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="symptoms"></a>現象
Azure 内の VM で DHCP クライアント サービスが無効になっているために、その VM に対して RDP 接続を行うことができません。 Azure portal で[ブート診断](../troubleshooting/boot-diagnostics.md)のスクリーンショットを確認すると、VM は正常に起動し、ログイン画面で資格情報を待っていることがわかります。 イベント ビューアーを使用して、VM でイベント ログをリモートで表示します。 DHCP クライアント サービスが開始していないか、または開始に失敗していることがわかります。 ログのサンプルを次に示します。

**ログ名**:システム </br>
**ソース**:サービス コントロール マネージャー </br>
**日付**:12/16/2015 11:19:36 AM </br>
**イベント ID**:7022 </br>
**タスク カテゴリ**:なし </br>
**レベル**:Error </br>
**キーワード**:クラシック</br>
**ユーザー**:該当なし </br>
**コンピューター**: myvm.cosotos.com</br>
**説明**:DHCP クライアント サービスが開始時にハングしました。</br>

Resource Manager VM では、シリアル アクセス コンソール機能を使って次のコマンドでイベント ログ 7022 のクエリを行うことができます。

    wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more

クラシック VM の場合は、オフライン モードで作業して、手動でログを収集する必要があります。

## <a name="cause"></a>原因

DHCP クライアント サービスが VM で実行されていません。

> [!NOTE]
> この記事は、DHCP クライアント サービスに対してのみ当てはまり、DHCP サーバーには当てはまりません。

## <a name="solution"></a>解決策

これらの手順を実行する前に、バックアップとして、影響を受ける VM の OS ディスクのスナップショットを取得します。 詳細については、[ディスクのスナップショット](../windows/snapshot-copy-managed-disk.md)に関する記事を参照してください。

この問題を解決するには、シリアル コントロールを使用して DHCP を有効にするか、または VM に対する[ネットワーク インターフェイスをリセット](reset-network-interface.md)します。

### <a name="use-serial-control"></a>シリアル コントロールを使用する

1. [シリアル コンソールに接続し、CMD インスタンスを開きます](serial-console-windows.md#use-cmd-or-powershell-in-serial-console)。
バージョンを参照)。 VM でシリアル コンソールが有効になっていない場合は、「[ネットワーク インターフェイスをリセットする](reset-network-interface.md)」をご覧ください。
2. ネットワーク インターフェイスで DHCP が無効になっているかどうかを確認します。

        sc query DHCP
3. DHCP が停止されている場合は、サービスの開始を試みます

        sc start DHCP

4. サービスが正常に開始されているかどうかを確認するために、サービスに対してもう一度クエリを実行します。

        sc query DHCP

    VM への接続を試みて、問題が解決されるかどうかを確認します。
5. サービスが開始しない場合は、受け取ったエラー メッセージに基づいて、以下の適切な解決策を使用します。

    | Error  |  解決策 |
    |---|---|
    | 5 - ACCESS DENIED  | 「[アクセス拒否エラーのために DHCP クライアント サービスが停止される](#dhcp-client-service-is-stopped-because-of-an-access-denied-error)」をご覧ください。  |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT   | 「[DHCP クライアント サービスがクラッシュまたはハングする](#dhcp-client-service-crashes-or-hangs)」をご覧ください。  |
    | 1058 - ERROR_SERVICE_DISABLED  | 「[DHCP クライアント サービスが無効になっている](#dhcp-client-service-is-disabled)」をご覧ください。  |
    | 1059 - ERROR_CIRCULAR_DEPENDENCY  |問題を迅速に解決するには、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。   |
    | 1067 - ERROR_PROCESS_ABORTED |「[DHCP クライアント サービスがクラッシュまたはハングする](#dhcp-client-service-crashes-or-hangs)」をご覧ください。   |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL   | 問題を迅速に解決するには、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。  |
    |1069 - ERROR_SERVICE_LOGON_FAILED   |  「[ログオンの失敗が原因で DHCP クライアント サービスが失敗する](#dhcp-client-service-fails-because-of-logon-failure)」をご覧ください |
    | 1070 - ERROR_SERVICE_START_HANG  | 「[DHCP クライアント サービスがクラッシュまたはハングする](#dhcp-client-service-crashes-or-hangs)」をご覧ください。  |
    | 1077 - ERROR_SERVICE_NEVER_STARTED  | 「[DHCP クライアント サービスが無効になっている](#dhcp-client-service-is-disabled)」をご覧ください。  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   | 問題を迅速に解決するには、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。  |
    |1053 | 問題を迅速に解決するには、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>アクセス拒否エラーのために DHCP クライアント サービスが停止される

1. [シリアル コンソール](serial-console-windows.md)に接続し、PowerShell インスタンスを開きます。
2. 次のスクリプトを実行して、プロセス モニター ツールをダウンロードします。

   ```powershell
   remove-module psreadline
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip"
   $destination = "c:\temp\ProcessMonitor.zip"
   $wc = New-Object System.Net.WebClient
   $wc.DownloadFile($source,$destination)
   ```
3. ここで、**procmon** トレースを開始します。

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML
   ```
4. **アクセス拒否**メッセージを生成しているサービスを開始して、問題を再現します。

   ```
   sc start DHCP
   ```

   失敗した場合は、プロセス モニター トレースを終了します。

   ```
   procmon /Terminate
   ```
5. **c:\temp\ProcMonTrace.PML** ファイルを収集します。

    1. [データ ディスクを VM に接続します](../windows/attach-managed-disk-portal.md
)。
    2. 新しいドライブにファイルをコピーできるシリアル コンソールを使用します。 たとえば、「 `copy C:\temp\ProcMonTrace.PML F:\` 」のように入力します。 このコマンドの F は、接続されたデータ ディスクのドライブ文字です。 文字を適切な正しい値に置き換えてください。
    3. データ ドライブを切断してから、プロセス モニターがインストールされている作業用 VM に接続します。

6. 作業用 VM でプロセス モニターを使用して、**ProcMonTrace.PML** を開きます。 次に、以下のスクリーンショットに示すように、 **Result is ACCESS DENIED** でフィルター処理します。

    ![プロセス モニターでの結果によるフィルター処理](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. 出力されるレジストリ キー、フォルダー、ファイルを修正します。 通常、この問題は、サービスで使用されるサインイン アカウントに、これらのオブジェクトにアクセスするための ACL アクセス許可がない場合に発生します。 サインイン アカウントの適切な ACL アクセス許可は、正常な VM で確認できます。

#### <a name="dhcp-client-service-is-disabled"></a>DHCP クライアント サービスが無効になっている

1. サービスを既定のスタートアップ値に復元します。

   ```
   sc config DHCP start= auto
   ```

2. サービスを開始します。

   ```
   sc start DHCP
   ```

3. サービスの状態のクエリを再び行って、サービスが実行されていることを確認します。

   ```
   sc query DHCP
   ```

4. リモート デスクトップを使用して VM に接続してみます。

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>ログオンの失敗が原因で DHCP クライアント サービスが失敗する

1. この問題は、このサービスの開始アカウントが変更された場合に発生するので、アカウントを既定の状態に戻します。

        sc config DHCP obj= 'NT Authority\Localservice'
2. サービスを開始します。

        sc start DHCP
3. リモート デスクトップを使用して VM に接続してみます。

#### <a name="dhcp-client-service-crashes-or-hangs"></a>DHCP クライアント サービスがクラッシュまたはハングする

1. サービスの状態が**開始中**または**停止中**のままになっている場合は、サービスを停止してみます。

        sc stop DHCP
2. サービスをそれ自体の 'svchost' コンテナーで切り離します。

        sc config DHCP type= own
3. サービスを開始します。

        sc start DHCP
4. それでもサービスが開始されない場合は、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

### <a name="repair-the-vm-offline"></a>VM をオフライン修復する

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>復旧 VM に OS ディスクを接続する

1. [復旧 VM に OS ディスクを接続します](../windows/troubleshoot-recovery-disks-portal.md)。
2. 復旧 VM へのリモート デスクトップ接続を開始します。 接続したディスクが [ディスクの管理] コンソールで **[オンライン]** になっていることを確認します。 接続された OS ディスクに割り当てられたドライブ文字をメモします。
3.  管理者特権でのコマンド プロンプト インスタンス ( **[管理者として実行]** ) を開きます。 次に、以下のスクリプトを実行します。 このスクリプトでは、接続された OS ディスクに割り当てられたドライブ文字が **F** であるものと想定しています。お使いの VM の適切な値に文字を置き換えてください。

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

    REM Set default values back on the broken service
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v type /t REG_DWORD /d 16 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v type /t REG_DWORD /d 16 /f

    reg unload HKLM\BROKENSYSTEM
    ```

4. [OS ディスクを切断して、VM を再作成します](../windows/troubleshoot-recovery-disks-portal.md)。 その後、問題が解決されているかどうかを確認します。

## <a name="next-steps"></a>次の手順

まだ支援が必要な場合は、問題を解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。