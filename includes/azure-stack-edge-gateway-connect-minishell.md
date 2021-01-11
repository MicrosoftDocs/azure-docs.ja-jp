---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/12/2019
ms.author: alkohli
ms.openlocfilehash: f69f3ed4a439deec58d4e5d30cefe48c4834cd66
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89082185"
---
デバイスにリモート接続する手順は、クライアントのオペレーティング システムによって異なります。

### <a name="remotely-connect-from-a-windows-client"></a>Windows クライアントからリモートで接続する

開始する前に、Windows クライアントで Windows PowerShell 5.0 以降が実行されていることを確認してください。

Windows クライアントからリモートで接続するには、次の手順に従います。

1. Windows PowerShell セッションを管理者として実行します。
2. Windows リモート管理サービスがクライアントで実行されていることを確認します。 コマンド プロンプトで、次のコマンドを入力します。

    `winrm quickconfig`

3. デバイスの IP アドレスに変数を割り当てます。

    $ip = "<device_ip>"

    `<device_ip>` を デバイスの IP アドレスに置き換えます。

4. デバイスの IP アドレスをクライアントの信頼されたホスト一覧に追加するために、次のコマンドを入力します。

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. デバイスの Windows PowerShell セッションを開始します。

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. パスワードの入力を求められたら、入力します。 ローカル Web UI へのサインインに使用するパスワードと同じものを使用してください。 既定のローカル Web UI パスワードは *Password1* です。 リモート PowerShell を使用してデバイスに正常に接続すると、次のサンプル出力が表示されます。  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>Linux クライアントからリモートで接続する

接続に使用する Linux クライアントで以下を行います。

- SSH リモート処理機能を取得するために、GitHub から [Linux 用の最新の PowerShell Core をインストールします](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6)。 
- [NTLM モジュールから `gss-ntlmssp` パッケージのみをインストールします](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md)。 Ubuntu クライアントでは、次のコマンドを実行します。
    - `sudo apt-get install gss-ntlmssp`

詳細については、「[SSH 経由の PowerShell リモート処理](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6)」を参照してください。

NFS クライアントからリモートで接続するには、次の手順に従います。

1. PowerShell セッションを開くために、次のように入力します。

    `sudo pwsh`
 
2. リモート クライアントを使用して接続するために、次のように入力します。

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    入力を求められたら、デバイスへのサインインに使用するパスワードを指定します。
 
> [!NOTE]
> この手順は Mac OS では機能しません。
