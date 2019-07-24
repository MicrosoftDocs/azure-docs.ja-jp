---
title: イベント ID を使用した、Azure VM の RDP 接続に関する問題のトラブルシューティング | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: 4c783c70217a84bbe5ccf15accc4a2bec0b7cca8
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52959684"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>イベント ID を使用した、Azure VM の RDP 接続に関する問題のトラブルシューティング 

この記事では、Azure 仮想マシン (VM) へのリモート デスクトップ プロトコル (RDP) 接続を妨げる問題のトラブルシューティングにイベント ID を使用する方法を説明します。

## <a name="symptoms"></a>現象

リモート デスクトップ プロトコル (RDP) セッションを使用して Azure VM に接続しようとします。 資格情報を入力した後、接続が失敗し、次のようなエラー メッセージを受け取ります。

**このコンピューターはリモート コンピューターに接続できません。接続を再試行してください。問題が解決しない場合は、リモート コンピューターの所有者またはネットワーク管理者に問い合わせてください。**

この問題をトラブルシューティングするには、VM でイベント ログを確認した後、以下のシナリオを参照してください。

## <a name="before-you-troubleshoot"></a>トラブルシューティングを行う前に

### <a name="create-a-backup-snapshot"></a>バックアップ スナップショットの作成

バックアップ スナップショットを作成するには、「[ディスクのスナップショットの作成](../windows/snapshot-copy-managed-disk.md)」の手順に従います。

### <a name="connect-to-the-vm-remotely"></a>VM にリモート接続する

VM にリモート接続するには、「[How to use remote tools to troubleshoot Azure VM issues](remote-tools-troubleshoot-azure-vm-issues.md)」(リモート ツールを使用して Azure VM の問題をトラブルシューティングする) で説明されているいずれかの方法を使用します。

## <a name="scenario-1"></a>シナリオ 1

### <a name="event-logs"></a>イベント ログ

CMD インスタンスで次のコマンドを実行して、過去 24 時間以内にイベント 1058 またはイベント 1057 がシステム ログに記録されているかどうかを確認します。

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**ログ名:**    システム <br />
**ソース:**      Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**日付:**          *時刻* <br />
**イベント ID:**    1058 <br />
**タスク カテゴリ:** なし <br />
**レベル:**       Error <br />
**キーワード:**    クラシック <br />
**ユーザー:**        該当なし <br />
**コンピューター:**      *コンピューター* <br />
**説明:** RD セッション ホスト サーバーで、SSL 接続時に RD セッション ホスト サーバー認証に使用する、期限切れの自己署名証明書を置き換えられませんでした。 関連する状態コードは "アクセスが拒否されました" でした。

**ログ名:**    システム <br />
**ソース:**      Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**日付:**          *時刻* <br />
**イベント ID:**    1058 <br />
**タスク カテゴリ:** なし <br />
**レベル:**       Error <br />
**キーワード:**    クラシック <br />
**ユーザー:**        該当なし <br />
**コンピューター:**      *コンピューター* <br />
**説明:** RD セッション ホスト サーバーで、SSL 接続時に RD セッション ホスト サーバー認証に使用する、新規の自己署名証明書を生成できませんでした。関連する状態コードは "オブジェクトは既に存在します" でした。

**ログ名:**    システム <br />
**ソース:**      Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**日付:**          *時刻* <br />
**イベント ID:**    1057 <br />
**タスク カテゴリ:** なし <br />
**レベル:**       Error <br />
**キーワード:**    クラシック <br />
**ユーザー:**        該当なし <br />
**コンピューター:**      *コンピューター* <br />
**説明:** RD セッション ホスト サーバーで、SSL 接続時に RD セッション ホスト サーバー認証に使用する、新規の自己署名証明書を生成できませんでした。 関連する状態コードは "キー セットがありません" でした。

次のコマンドを実行して、SCHANNEL エラー イベント 36872 および 36870 を確認することもできます。

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**ログ名:**    システム <br />
**ソース:**      Schannel <br />
**日付:**          — <br />
**イベント ID:**    36870 <br />
**タスク カテゴリ:** なし <br />
**レベル:**       Error <br />
**キーワード:**       <br />
**ユーザー:**        SYSTEM <br />
**コンピューター:**      *コンピューター* <br />
**説明:** SSL サーバー資格証明の秘密キーにアクセスしようとしているときに致命的なエラーが発生しました。 暗号化モジュールから返されたエラー コードは 0x8009030D です。  <br />
内部エラーの状態は 10001 です。

### <a name="cause"></a>原因
この問題は、VM 上の MachineKeys フォルダー内にあるローカルの RSA 暗号化キーにアクセスできないために発生します。 この問題は、次のいずれかの理由で発生することがあります。

1. Machinekeys フォルダーまたは RSA ファイルでのアクセス許可の構成が正しくない。

2. RSA キーが壊れているか、ない。

### <a name="resolution"></a>解決策

この問題をトラブルシューティングするには、以下の手順を使用して、RDP 証明書への適切なアクセス許可を設定する必要があります。

#### <a name="grant-permission-to-the-machinekeys-folder"></a>MachineKeys フォルダーへのアクセス許可を付与する

1. 次の内容を使用してスクリプトを作成します。

   ```powershell
   remove-module psreadline 
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
   takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt
   Restart-Service TermService -Force
   ```

2.  このスクリプトを実行して、MachineKey フォルダーのアクセス許可をリセットし、RSA ファイルを既定値にリセットします。

3.  再び VM にアクセスしてみます。

スクリプトを実行した後は、アクセス許可の問題が発生している次のファイルを確認できます。

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>RDP 自己署名証明書を更新する

問題が解決しない場合は、次のスクリプトを実行して、RDP の自己署名証明書が更新されることを確認します。

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

証明書を更新できない場合は、以下の手順に従って、証明書の削除を試みます。

1. 同じ VNET 内の別の VM で、**[ファイル名を指定して実行]** ボックスを開き、「**mmc**」と入力して、**[OK]** をクリックします。 

2. **[ファイル]** メニューの **[スナップインの追加と削除]** を選択します。

3. **[利用できるスナップイン]** ボックスの一覧で **[証明書]** を選択し、**[追加]** を選択します。

4. **[コンピューター アカウント]** を選択し、**[次へ]** を選択します。

5. **[別のコンピューター]** を選択してから、問題が発生している VM の IP アドレスを追加します。
   >[!Note]
   >内部ネットワークを使用して、仮想 IP アドレスの使用を回避します。

6. **[完了]** を選択し、**[OK]** を選択します。

   ![コンピューターを選択する](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. 証明書を展開し、Remote Desktop\Certificates フォルダーに移動し、証明書を右クリックして、**[削除]** を選択します。

8. リモート デスクトップ構成サービスを再起動します。

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >この時点で、mmc からストアを更新した場合は、証明書が表示されます。 

RDP を使用して、VM へのアクセスを再び試みます。

#### <a name="update-secure-sockets-layer-ssl-certificate"></a>Secure Sockets Layer (SSL) 証明書を更新する

SSL 証明書を使用するように VM を設定した場合は、次のコマンドを実行して拇印を取得します。 そして、証明書の拇印と同じかどうかを確認します。

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

同じでない場合は、拇印を変更します。

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

キーを削除し、RDP が RDP に自己署名証明書を使用するようにしてみることもできます。

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>シナリオ 2

### <a name="event-log"></a>イベント ログ

CMD インスタンスで次のコマンドを実行して、過去 24 時間以内に SCHANNEL エラー イベント 36871 がシステム ログに記録されているかどうかを確認します。

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**ログ名:**    システム <br />
**ソース:**      Schannel <br />
**日付:**          — <br />
**イベント ID:**    36871 <br />
**タスク カテゴリ:** なし <br />
**レベル:**       Error <br />
**キーワード:**       <br />
**ユーザー:**        SYSTEM <br />
**コンピューター:**      *コンピューター* <br />
**説明:** TLS サーバー資格証明を作成しているときに致命的なエラーが発生しました。 内部エラーの状態は 10013 です。
 
### <a name="cause"></a>原因

この問題は、セキュリティ ポリシーによって発生します。 古いバージョンの TLS (1.0 など) が無効になっていると、RDP アクセスが失敗します。

### <a name="resolution"></a>解決策

RDP では、既定のプロトコルとして TLS 1.0 が使用されます。 ただし、プロトコルが新しい TLS 1.1 に変更されている場合があります。

この問題のトラブルシューティングについては、「[RDP を使用して Azure VM に接続するときの認証エラーのトラブルシューティング](troubleshoot-authentication-error-rdp-vm.md#tls-version)」をご覧ください。

## <a name="scenario-3"></a>シナリオ 3

**リモート デスクトップ接続ブローカー** ロールを VM にインストールしてある場合は、過去 24 時間以内にイベント 2056 またはイベント 1296 が発生しているかどうかを確認します。 CMD インスタンスで、次のコマンドを実行します。 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**ログ名:**    Microsoft-Windows-TerminalServices-SessionBroker/Operational <br />
**ソース:**      Microsoft-Windows-TerminalServices-SessionBroker <br />
**日付:**          *時刻* <br />
**イベント ID:**    2056 <br />
**タスク カテゴリ:**(109) <br />
**レベル:**       Error <br />
**キーワード:**       <br />
**ユーザー:**        NETWORK SERVICE <br />
**コンピューター:**      *コンピューターの FQDN* <br />
**説明:** ソース "Microsoft-Windows-TerminalServices-SessionBroker" からのイベント ID 2056 の説明が見つかりません。 このイベントを発生させるコンポーネントがローカル コンピューターにインストールされていないか、インストールが壊れています。 ローカル コンピューターにコンポーネントをインストールするか、コンポーネントを修復してください。 <br />
イベントが別のコンピューターから発生している場合、イベントと共に表示情報を保存する必要があります。 <br />
イベントには次の情報が含まれています: <br />
NULL <br />
NULL <br />
データベースにログオンできませんでした。

**ログ名:**    Microsoft-Windows-TerminalServices-SessionBroker-Client/Operational <br />
**ソース:**      Microsoft-Windows-TerminalServices-SessionBroker-Client <br />
**日付:**          *時刻* <br />
**イベント ID:**    1,296 <br />
**タスク カテゴリ:**(104) <br />
**レベル:**       Error <br />
**キーワード:**       <br />
**ユーザー:**        NETWORK SERVICE <br />
**コンピューター:**      *コンピューターの FQDN* <br />
**説明:** ソース "Microsoft-Windows-TerminalServices-SessionBroker-Client" からのイベント ID 1296 の説明が見つかりません。 このイベントを発生させるコンポーネントがローカル コンピューターにインストールされていないか、インストールが壊れています。 ローカル コンピューターにコンポーネントをインストールするか、コンポーネントを修復してください。
イベントが別のコンピューターから発生している場合、イベントと共に表示情報を保存する必要があります。
イベントには次の情報が含まれています:  <br />
*text* <br />
*text* <br />
リモート デスクトップ接続ブローカーで RPC 通信の準備ができていません。

### <a name="cause"></a>原因

この問題は、リモート デスクトップ接続ブローカー サーバーのホスト名が変更されたために発生します。これは、サポートされている変更ではありません。 

ホスト名には Windows Internal Database に対するエントリと依存関係があり、リモート デスクトップ サービス ファームが動作するために必要です。 ファームが既に構築された後でホスト名を変更すると、多くのエラーが発生し、ブローカー サーバーの動作が停止する可能性があります。

### <a name="resolution"></a>解決策 

この問題を解決するには、リモート デスクトップ接続ブローカー ロールと Windows Internal Database を再インストールする必要があります。

## <a name="next-steps"></a>次の手順

[Schannel イベント](https://technet.microsoft.com/library/dn786445(v=ws.11).aspx)

[Schannel SSP の技術概要](https://technet.microsoft.com/library/dn786429(v=ws.11).aspx)

[リモート デスクトップ セッション ホスト証明書と SSL 通信を使用するとイベント ID 1058 およびイベント 36870 で RDP が失敗する](https://blogs.technet.microsoft.com/askperf/2014/10/22/rdp-fails-with-event-id-1058-event-36870-with-remote-desktop-session-host-certificate-ssl-communication/)

[ドメイン コントローラーでの Schannel 36872 または Schannel 36870](https://blogs.technet.microsoft.com/instan/2009/01/05/schannel-36872-or-schannel-36870-on-a-domain-controller/)

[イベント ID 1058 — リモート デスクトップ サービスの認証と暗号化](https://technet.microsoft.com/library/ee890862(v=ws.10).aspx)

