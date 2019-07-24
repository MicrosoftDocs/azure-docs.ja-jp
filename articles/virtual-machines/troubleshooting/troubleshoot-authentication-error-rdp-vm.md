---
title: RDP を使用して Azure VM に接続するときの認証エラーのトラブルシューティング | Microsoft Docs
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
ms.openlocfilehash: 47d3b827099d3a4a7520ac66765d2928795b6e49
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52967982"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>RDP を使用して Azure VM に接続するときの認証エラーのトラブルシューティングについて説明します。

この記事は、Azure 仮想マシン (VM) への接続にリモート デスクトップ プロトコル (RDP) 接続を使用する場合に、発生した認証エラーのトラブルシューティングを行うのに役立ちます。

## <a name="symptoms"></a>現象

ようこそ画面が表示された、オペレーティング システムが実行中であることを示している Azure VM のスクリーンショットをキャプチャします。 しかし、リモート デスクトップ接続を使用して VM に接続しようとすると、次のいずれかのエラー メッセージが表示されます。

### <a name="error-message-1"></a>エラー メッセージ 1

**認証エラーが発生しました。ローカル セキュリティ機関にアクセスできません。**

### <a name="error-message-2"></a>エラー メッセージ 2

**接続しようとしているリモート コンピューターには、ネットワーク レベル認証 (NLA) が必要ですが、お使いの Windows のドメイン コントローラーに接続して NLA を実行することができません。リモート コンピューターの管理者であれば、[システムのプロパティ] ダイアログ ボックスの [リモート] タブにあるオプションを使用して、NLA を無効にすることができます。**

### <a name="error-message-3-generic-connection-error"></a>エラー メッセージ 3 (一般的な接続エラー)

**このコンピューターはリモート コンピューターに接続できません。接続を再試行してください。問題が解決しない場合は、リモート コンピューターの所有者またはネットワーク管理者にお問い合わせください。**

## <a name="cause"></a>原因

NLA によって VM への RDP アクセスがブロックされる場合は、複数の理由があります。

### <a name="cause-1"></a>原因 1

VM とドメイン コントローラー (DC) の通信ができません。 この問題があると、RDP セッションからドメイン資格情報を使用して VM にアクセスすることができません。 ただし、その場合もローカル管理者の資格情報を使用してログオンすることはできます。 この問題は次の状況で発生する場合があります。

1. VM と DC の間の Active Directory セキュリティ チャネルが破損している。

2. VM 上にアカウントのパスワードの古いコピーが残っており、DC 上にそれよりも新しいコピーがある。

3. この VM の接続先 DC が正常な状態ではない。

### <a name="cause-2"></a>原因 2

VM の暗号化レベルが、クライアント コンピューターで使用されている暗号化レベルよりも上です。

### <a name="cause-3"></a>原因 3

TLS 1.0、1.1、または 1.2 (サーバー) プロトコルが VM 上で無効になっています。

### <a name="cause-4"></a>原因 4

VM が、ドメイン資格情報を使用したログオンを無効にするように設定されていて、ローカル セキュリティ機関 (LSA) が正しく設定されていません。

### <a name="cause-5"></a>原因 5

VM は Federal Information Processing Standards (FIPS) 準拠アルゴリズムによる接続のみを受け付けるように設定されています。 これは通常、Active Directory ポリシーを使用して行われます。 これはまれな構成ですが、FIPS はリモート デスクトップ接続のみに適用できます。

## <a name="before-you-troubleshoot"></a>トラブルシューティングを行う前に

### <a name="create-a-backup-snapshot"></a>バックアップ スナップショットの作成

バックアップ スナップショットを作成するには、「[ディスクのスナップショットの作成](../windows/snapshot-copy-managed-disk.md)」の手順に従います。

### <a name="connect-to-the-vm-remotely"></a>VM へのリモート接続

VM にリモート接続するには、[リモート ツールを使用して Azure VM に関する問題のトラブルシューティングを行う方法](remote-tools-troubleshoot-azure-vm-issues.md)に関する記事を参照してください。

### <a name="group-policy-client-service"></a>グループ ポリシー クライアント サービス

これがドメインに参加している VM である場合は、まず、Active Directory ポリシーによって変更が上書きされないように、グループ ポリシー クライアント サービスを停止します。 そのためには、次のコマンドを実行します。

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

問題を解決した後、ドメインから最新の GPO を取得するために、ドメインに接続するこの VM の機能を復元します。 これを行うには、次のコマンドを実行します。

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

変更が元に戻された場合、それは Active Directory ポリシーが問題の原因であることを意味します。 

### <a name="workaround"></a>対処法

この問題を回避するには、コマンド ウィンドウで、NLA を無効にする次のコマンドを実行します。

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

その後、VM を再起動します。

NLA を再度有効にするには、次のコマンドを実行し、VM を再起動します。

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="for-domain-joined-vms"></a>ドメインに参加している VM の場合

この問題のトラブルシューティングを行うには、VM が DC に接続できるかどうかと、DC が "正常" な状態にあり VM からの要求を処理できるかどうかを最初に確認します。

>[!Note] 
>DC の正常性をテストするには、同じ VNET の同じサブネット上にあって同じログオン サーバーを共有している、別の VM を使用できます。

「VM へのリモート接続」のセクションにある手順に従い、シリアル コンソール、リモート CMD、またはリモート PowerShell を使用して、問題のある VM に接続します。

VM の接続先 DC がどれであるかを確認するには、コンソールで次のコマンドを実行します。 

```cmd
set | find /i "LOGONSERVER"
```

次に、VM と DC の間の、セキュリティで保護されたチャネルの正常性を確認します。 これを行うには、管理者特権の PowerShell インスタンスで次のコマンドを実行します。 このコマンドからは、セキュリティで保護されたチャネルが有効かどうかを示すブール値のフラグが返されます。

```powershell
Test-ComputerSecureChannel -verbose
```

チャネルが破損している場合は、次のコマンドを実行して修復します。

```powershell
Test-ComputerSecureChannel -repair
```

VM と DC 上で、Active Directory コンピューター アカウントのパスワードが更新されていることを確認します。

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

DC と VM の間の通信に異常がないにもかかわらず、DC が完全に機能していないために RDP セッションを開くことができない場合は、DC の再起動を試してみてください。

上記のコマンドによってドメインとの通信の問題が解決しなかった場合は、この VM をドメインに再度参加させることをお勧めします。 そのためには、次の手順に従います。

1. 次の内容を使用して Unjoin.ps1 という名前のスクリプトを作成し、Azure portal 上で、カスタム スクリプト拡張機能としてスクリプトをデプロイします。

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    このスクリプトにより、VM がドメインから強制的に離脱させられ、10 秒後に再起動します。 その後、ドメイン側にあるコンピューター オブジェクトをクリーンアップする必要があります。

2.  クリーンアップが完了したら、この VM をドメインに再参加させます。 次の内容を使用して JoinDomain.ps1 という名前のスクリプトを作成し、Azure portal 上で、カスタム スクリプト拡張機能としてスクリプトをデプロイします。 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >これにより、指定した資格情報を使用して VM をドメインに参加させます。

Active Directory チャネルが正常であり、コンピューターのパスワードが更新されていて、ドメイン コントローラーが想定どおりに動作している場合は、次の手順を行います。

問題が解決しない場合は、ドメインの資格情報が無効になっていないかどうかを確認します。 これを行うには、管理者特権でコマンド プロンプト ウィンドウを開き、次のコマンドを実行します。このコマンドでは、VM が、VM にログオンするためのドメイン アカウントを無効にするように設定されているかどうかを確認します。

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

キーが **1** に設定されている場合は、サーバーがドメイン資格情報を許可するように設定されていることを意味します。 このキーを **0** に変更します。

### <a name="for-standalone-vms"></a>スタンドアロン VM の場合

#### <a name="check-minencryptionlevel"></a>MinEncryptionLevel の確認

CMD インスタンス上で次のコマンドを実行し、**MinEncryptionLevel** レジストリ値のクエリを実行します。

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

レジストリ値に基づいて、以下の手順に従います。

* 4 (FIPS):「[FIPS 準拠アルゴリズムによる接続の確認](#fips-compliant)」に進みます。

* 3 (128 ビット暗号化):次のコマンドを実行して重大度を **2** に設定します。

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2 (クライアントによって指示された可能な最大の暗号化):次のコマンドを実行して暗号化を最小値の **1** に設定します。

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
レジストリに対する変更が反映されるように、VM を再起動します。

#### <a name="tls-version"></a>TLS のバージョン

システムに応じて、RDP では TLS 1.0、1.1、1.2 (サーバー) のいずれかのプロトコルが使用されます。 これらのプロトコルが VM 上でどのように設定されているかに関するクエリを実行するには、CMD インスタンスを開き、次のコマンドを実行します。

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

返された値が全部 **1** でない限り、プロトコルは無効になっています。 これらのプロトコルを有効にするには、次のコマンドを実行します。

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
```

その他のプロトコルのバージョンでは、次のコマンドを実行できます。

<pre lang="bat">
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
</pre>

> [!Note]
> SSH/TLS バージョン x.x は、SCHANNEL エラーに関するゲスト OS ログから取得します。

#### <a name="fips-compliant"></a> FIPS 準拠アルゴリズムによる接続の確認

リモート デスクトップで FIPS 準拠アルゴリズムの接続のみを使用するように指定できます。 これはレジストリ キーを使用して設定できます。 これを行うには、管理者特権でコマンド プロンプト ウィンドウを開きし、次のキーのクエリを実行します。

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

コマンドから **1** が返された場合は、レジストリ値を **0** に変更します。

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

VM 上の現在の MinEncryptionLevel が何であるかを確認します。

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

コマンドから **4** が返された場合は、レジストリ値を **2** に変更します。

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

レジストリに対する変更が反映されるように、VM を再起動します。

## <a name="next-steps"></a>次の手順

[Win32_TSGeneralSetting クラスの SetEncryptionLevel メソッド](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting-setencryptionlevel)

[サーバー認証と暗号化レベルの構成](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770833(v=ws.11))

[Win32_TSGeneralSetting クラス](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting)
