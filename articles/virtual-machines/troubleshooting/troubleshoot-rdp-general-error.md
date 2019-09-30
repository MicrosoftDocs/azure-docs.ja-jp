---
title: Azure 上の Windows VM に対する RDP 一般エラーのトラブルシューティング | Microsoft Docs
description: Azure 上の Windows VM に対する RDP 一般エラーのトラブルシューティング方法を学ぶ | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 7fc0fbf3362d18284ad6a80afa6396b6be1270a9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057996"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Azure VM での RDP 一般エラーのトラブルシューティング

この記事では、Azure において Windows 仮想マシン (VM) にリモート デスクトップ プロトコル (RDP) を接続する際に発生する一般エラーについて説明します。

## <a name="symptom"></a>症状

Azure の Window VM に RDP 接続を行うとき、次の一般エラー メッセージを受け取る場合があります。

**[Remote Desktop can't connect to the remote computer for one of these reasons:]\(リモート デスクトップが次のいずれかの理由のためにリモート コンピューターに接続できません。\)**

1. **[Remote access to the server is not enabled]\(サーバーへのリモートアクセスが無効です\)**

2. **[The remote Computer is turned off]\(リモート コンピューターの電源が入っていません\)**

3. **[The remote computer is not available on the network]\(リモート コンピューターがネットワーク上にありません\)**

**[Make sure the remote computer is turned on and connected to the network, and that remote access is enabled.]\(リモート コンピューターの電源が入っており、ネットワークに接続されていること、リモート アクセスが有効なことを確認してください。\)**

## <a name="cause"></a>原因

この問題は以下の原因のために発生します。

### <a name="cause-1"></a>原因 1

RDP コンポーネントが以下で無効です。

- コンポーネント レベル
- リスナー レベル
- ターミナル サーバー
- リモート デスクトップ セッション ホスト ロール

### <a name="cause-2"></a>原因 2

リモート デスクトップ サービス (TermService) が実行されていません。

### <a name="cause-3"></a>原因 3

RDP リスナーの構成が正しくありません。

## <a name="solution"></a>解決策

この問題を解決するには、[オペレーティング システム ディスクをバックアップ](../windows/snapshot-copy-managed-disk.md)し、[オペレーティング システム ディスクを復旧 VM に接続](troubleshoot-recovery-disks-portal-windows.md)し、以下の手順に従います。

### <a name="serial-console"></a>シリアル コンソール

#### <a name="step-1-open-cmd-instance-in-serial-console"></a>手順 1:Serial console で CMD インスタンスを開く

1. **[サポートとトラブルシューティング]**  >  **[Serial console (Preview)]\(シリアル コンソール (プレビュー))** を選択して [[シリアル コンソール]](serial-console-windows.md) にアクセスします。 VM で機能が有効な場合、VM を正常に接続できます。

2. CMD インスタンス用の新しいチャネルを作成します。 **CMD** と入力してチャネルを開始し、チャネル名を取得します。

3. CMD インスタンスを実行するチャネル (この場合ではチャネル 1) に切り替えます。

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>手順 2:RDP レジストリ キーの値を確認する

1. ポリシーによって RDP が無効になっていることを確認します。

      ```
      REM Get the local policy 
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      - ドメイン ポリシーが存在する場合、ローカル ポリシー上の設定は上書きされます。
      - ドメイン ポリシーによって RDP が無効 (1) と定められている場合、ドメイン コントローラーから AD ポリシーを更新します。
      - ドメイン ポリシーによって RDP が有効 (0) と定められている場合、更新は必要ありません。
      - ドメイン ポリシーが存在せず、ローカル ポリシーによって RDP が無効 (1) と定められている場合、次のコマンドを使用して RDP を有効にします。 
      
            reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
                  

2. ターミナル サーバーの現在の構成を確認します。

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
      ```

      このコマンドで 0 が返される場合、ターミナル サーバーが無効です。 そのときは、次のようにターミナル サーバーを有効にします。

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

3. ターミナル サーバー モジュールは、サーバーがターミナル サーバー ファーム (RDS または Citrix) にある場合、ドレイン モードに設定されます。 ターミナル サーバー モジュールの現在のモードを確認します。

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

      コマンドで 1 が返される場合、ターミナル サーバー モジュールはドレイン モードに設定されています。 そのときは、次のようにモジュールを動作モードに設定します。

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

4. ターミナル サーバーに接続できるかどうかを確認します。

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

      コマンドで 1 が返される場合、ターミナル サーバーに接続することはできません。 そのときは、次のように接続を有効にします。

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```
5. RDP リスナーの現在の構成を確認します。

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

      このコマンドで 0 が返される場合、RDP リスナーが無効です。 そのときは、次のようにリスナーを有効にします。

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

6. RDP リスナーに接続できるかどうかを確認します。

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   コマンドで 1 が返される場合、RDP リスナーに接続することはできません。 そのときは、次のように接続を有効にします。

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

7. VM を再起動します。

8. 「`exit`」と入力して CMD インスタンスを終了し、**Enter** を 2 回押します。

9. 「`restart`」と入力して VM を再起動してから、VM に接続します。

まだ問題が発生する場合には手順 2. に進みます。

#### <a name="step-2-enable-remote-desktop-services"></a>手順 2:リモート デスクトップ サービスを有効にする

詳しくは、「[Azure VM でリモート デスクトップ サービスが起動しない](troubleshoot-remote-desktop-services-issues.md)」をご覧ください。

#### <a name="step-3-reset-rdp-listener"></a>手順 3:RDP リスナーをリセットする

詳しくは、「[Remote Desktop disconnects frequently in Azure VM](troubleshoot-rdp-intermittent-connectivity.md)」(Azure VM でリモート デスクトップの接続が頻繁に切れる) をご覧ください。

### <a name="offline-repair"></a>オフラインでの修復

#### <a name="step-1-turn-on-remote-desktop"></a>手順 1:リモート デスクトップをオンにする

1. [復旧 VM に OS ディスクを接続します](../windows/troubleshoot-recovery-disks-portal.md)。
2. 復旧 VM へのリモート デスクトップ接続を開始します。
3. ディスクが [ディスクの管理] コンソールで **[オンライン]** になっていることを確認します。 接続された OS ディスクに割り当てられたドライブ文字をメモします。
4. 復旧 VM へのリモート デスクトップ接続を開始します。
5. 管理者特権のコマンド プロンプト セッション ( **[管理者として実行]** ) を開きます。 以下のスクリプトを実行します。 このスクリプトでは、接続されている OS ディスクに割り当てられているドライブ文字が F であると想定しています。このドライブ文字を実際の VM の適切な値に置き換えてください。

      ```
      reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv 
      reg load HKLM\BROKENSOFTWARE F:\windows\system32\config\SOFTWARE.hiv 
 
      REM Ensure that Terminal Server is enabled 

      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 

      REM Ensure Terminal Service is not set to Drain mode 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 

      REM Ensure Terminal Service has logon enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 

      REM Ensure the RDP Listener is not disabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 

      REM Ensure the RDP Listener accepts logons 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 

      REM RDP component is enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0 /f 

      reg unload HKLM\BROKENSYSTEM 
      reg unload HKLM\BROKENSOFTWARE 
      ```

6. VM がドメインに参加している場合は、次のレジストリ キーを調べて、RDP を無効にするグループ ポリシーがあるかどうか確認します。 

      ```
      HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
      ```

      このキーが 1 に設定されている場合は、RDP がポリシーによって無効化されていることを意味します。 GPO ポリシーを介してリモート デスクトップを有効にするには、ドメイン コントローラーから次のポリシーを変更します。

   
      **Computer Configuration\Policies\Administrative Templates:**

      Policy definitions\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections\Allow users to connect remotely by using Remote Desktop Services
  
1. 復旧 VM からディスクをデタッチします。
1. [そのディスクから新しい VM を作成します](../windows/create-vm-specialized.md)。

まだ問題が発生する場合には手順 2. に進みます。

#### <a name="step-2-enable-remote-desktop-services"></a>手順 2:リモート デスクトップ サービスを有効にする

詳しくは、「[Azure VM でリモート デスクトップ サービスが起動しない](troubleshoot-remote-desktop-services-issues.md)」をご覧ください。

#### <a name="step-3-reset-rdp-listener"></a>手順 3:RDP リスナーをリセットする

詳しくは、「[Remote Desktop disconnects frequently in Azure VM](troubleshoot-rdp-intermittent-connectivity.md)」(Azure VM でリモート デスクトップの接続が頻繁に切れる) をご覧ください。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
