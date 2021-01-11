---
title: Windows Azure ゲスト エージェントのトラブルシューティング
description: Windows Azure ゲスト エージェントが機能しない問題のトラブルシューティングを行います
services: virtual-machines-windows
ms.service: virtual-machines-windows
author: dkdiksha
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/07/2020
ms.author: genli
ms.openlocfilehash: 38bf652ccefe98c2bd0fb28e8018d41df21651dc
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88659468"
---
# <a name="troubleshooting-windows-azure-guest-agent"></a>Windows Azure ゲスト エージェントのトラブルシューティング

Windows Azure ゲスト エージェントは、仮想マシン (VM) エージェントです。 これを使用すると、VM が IP アドレス 168.63.129.16 でファブリック コントローラー (VM がホストされている基盤の物理サーバー) と通信できるようになります。 これは、通信を容易にする仮想パブリック IP アドレスです。 詳しくは、「[IP アドレス 168.63.129.16 とは](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16)」を参照してください。

 オンプレミスから Azure に移行された VM や、カスタマイズされたイメージを使用して作成された VM には、Windows Azure ゲスト エージェントがインストールされていません。 これらのシナリオでは、手動で VM エージェントをインストールする必要があります。 VM エージェントをインストールする方法の詳細については、「[Azure 仮想マシン エージェントの概要](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)」を参照してください。

Windows Azure ゲスト エージェントが正常にインストールされると、次のサービスが VM の services.msc に一覧表示されます。
 
- Windows Azure ゲスト エージェント サービス
- テレメトリ サービス
- RDAgent サービス

**Windows Azure ゲスト エージェント サービス**:このサービスは、WAppAgent 内のすべてのログ記録を担当するサービスです。 このサービスは、さまざまな拡張機能を構成し、ゲストからホストへの通信を行います。 

**テレメトリ サービス**:このサービスは、VM のテレメトリ データをバックエンド サーバーに送信する役割を担います。

**RD エージェント サービス**:このサービスは、ゲスト エージェントのインストールを担当します。 Transparent Installer も RDAgent のコンポーネントであり、ゲスト エージェントの他のコンポーネントやサービスをアップグレードするのに役立ちます。 RDAgent は、ゲスト VM から物理サーバー上のホスト エージェントにハートビートを送信する役割も担います。

> [!NOTE]
> VM ゲスト エージェントのバージョン 2.7.41491.971 以降では、テレメトリ コンポーネントが RDAgent サービスに含まれているため、新しく作成された VM でこのテレメトリ サービスが表示されない場合があります。

## <a name="checking-agent-status-and-version"></a>エージェントの状態とバージョンの確認

Azure portal の VM プロパティ ページに移動して、 **[エージェントの状態]** を確認します。 Windows Azure ゲスト エージェントが正常に機能している場合は、状態が **[準備完了]** になっています。 VM エージェントが **[準備未完了]** 状態になっている場合、Azure portal の拡張機能と**実行コマンド**は機能しません。

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>準備未完了状態の VM エージェントのトラブルシューティング

### <a name="step-1-check-whether-the-windows-azure-guest-agent-service-is-installed"></a>手順 1 Windows Azure ゲスト エージェント サービスがインストールされているかどうかを確認する

- パッケージを確認する

    C:\WindowsAzure フォルダーを見つけます。 バージョン番号を示す GuestAgent フォルダーがある場合は、Windows Azure ゲスト エージェントが VM にインストールされていることを意味します。 さらに、インストールされているパッケージを検索します。  VM に Windows Azure ゲスト エージェントがインストールされている場合は、次の場所にパッケージが保存されています: `C:\windows\OEM\GuestAgent\VMAgentPackage.zip`。
    
    次の PowerShell コマンドを実行すると、VM エージェントが VM にデプロイされているかどうかを確認できます。
    
    `Get-Az VM -ResourceGroup “RGNAME” – Name “VMNAME” -displayhint expand`
    
    出力で、**ProvisionVMAgent** プロパティを見つけ、値が **True** に設定されているかどうかを確認します。 設定されている場合は、エージェントが VM にインストールされていることを意味します。
    
- サービスとプロセスを確認する

   サービス コンソール (services.msc) に移動し、次のサービスの状態を確認します:Windows Azure ゲスト エージェント サービス、RDAgent サービス、Windows Azure テレメトリ サービス、Windows Azure ネットワーク エージェント サービス。
 
    また、タスク マネージャーで以下のプロセスを調べて、これらのサービスが実行されているかどうかを確認することもできます。
       
    - WindowsAzureGuestAgent.exe:Windows Azure ゲスト エージェント サービス
    - WaAppAgent.exe:RDAgent サービス
    - WindowsAzureNetAgent.exe:Windows Azure ネットワーク エージェント サービス
    - WindowsAzureTelemetryService.exe:Windows Azure テレメトリ サービス

   これらのプロセスやサービスが見つからない場合は、Windows Azure ゲスト エージェントがインストールされていないことを示しています。

- プログラムと機能を確認する

    コントロール パネルで、 **[プログラムと機能]** に移動し、Windows Azure ゲスト エージェント サービスがインストールされているかどうかを確認します。

実行中のパッケージ、サービス、プロセスが見つからず、[プログラムと機能] で Windows Azure ゲスト エージェント サービスがインストールされていない場合は、[Windows Azure ゲスト エージェント サービスのインストール](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)を実行してみます。 ゲスト エージェントが正常にインストールされない場合は、[オフラインで VM エージェントをインストール](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline)できます。

サービスが表示され、実行されている場合は、サービスを再起動して問題が解決されているかどうかを確認します。 サービスが停止している場合は、サービスを開始して数分待ちます。 その後、 **[エージェントの状態]** が **[準備完了]** として報告されるかどうかを確認します。 これらのサービスがクラッシュしている場合は、一部のサードパーティ プロセスが原因でこれらのサービスがクラッシュする場合があります。 これらの問題のさらなるトラブルシューティングを行う場合は、[Microsoft サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)にお問い合わせください。

### <a name="step-2-check-whether-auto-update-is-working"></a>手順 2 自動更新が機能しているかどうかを確認する

Windows Azure ゲスト エージェントは、自動更新機能を備えています。 新しい更新プログラムを自動的に確認し、インストールします。 自動更新機能が正常に機能しない場合は、次の手順に従って、Windows Azure ゲスト エージェントをアンインストールしてからインストールしてみてください。

1. Windows Azure ゲスト エージェント サービスが **[プログラムと機能]** に表示される場合は、Windows Azure ゲスト エージェントをアンインストールします。

2. 管理者特権があるコマンド プロンプト ウィンドウを開きます。

3. ゲスト エージェント サービスを停止します。 サービスが停止しない場合は、サービスを**手動スタートアップ**に設定してから、VM を再起動する必要があります。
    ```
    net stop rdagent
    net stop WindowsAzureGuestAgent
    net stop WindowsAzureTelemetryService
    ```
1. ゲスト エージェント サービスを削除します。
    ```
    sc delete rdagent
    sc delete WindowsAzureGuestAgent
    sc delete WindowsAzureTelemetryService
    ```
1. `C:\WindowsAzure` の下に、OLD という名前のフォルダーを作成します。

1. Packages または GuestAgent という名前のフォルダーを OLD フォルダーに移動します。

1. [ここ](https://go.microsoft.com/fwlink/?linkid=394789&clcid=0x409)からエージェント インストール パッケージの最新バーションをダウンロードしてインストールします。 インストールを実行するには、管理者権限が必要です。

1. 次のコマンドを使用してゲスト エージェントをインストールします。

    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log
    ```
    その後、ゲスト エージェント サービスが正常に開始しているかどうかを確認します。
 
    まれに、ゲスト エージェントが正常にインストールされない場合は、[オフラインで VM エージェントをインストール](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline)できます。
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>手順 3 VM がファブリック コントローラーに接続できるかどうかを確認する

PsPing などのツールを使用して、VM がポート 80、32526、443 で 168.63.129.16 に接続できるかどうかをテストします。 VM が想定どおりに接続されない場合は、ポート 80、443、32526 を介した送信方向の通信が VM のローカル ファイアウォールで開いているかどうかを確認します。 この IP アドレスがブロックされると、VM エージェントがさまざまなシナリオで予期しない動作を示す可能性があります。

## <a name="advanced-troubleshooting"></a>高度なトラブルシューティング

Windows Azure ゲスト エージェントのトラブルシューティングに役立つイベントは、以下のログ ファイルに記録されます。

- C:\WindowsAzure\Logs\WaAppAgent.log
- C:\WindowsAzure\Logs\TransparentInstaller.log

Windows Azure ゲスト エージェントが**準備未完了**状態に移行するか、想定どおりに機能しなくなる一般的なシナリオを次に示します。

### <a name="agent-stuck-on-starting"></a>エージェントが「開始」中にスタックする

WaAppAgent ログを見ると、エージェントが開始プロセスでスタックし、開始できないことがわかる場合があります。

**ログ情報**
 
[00000007] [05/28/2019 12:58:50.90] [INFO] WindowsAzureGuestAgent starting. Version 2.7.41491.901

**分析**
 
VM で以前のバージョンの Windows Azure ゲスト エージェントがまだ実行されています。 C:\WindowsAzure フォルダーを見ると、複数の Windows Azure ゲスト エージェント インスタンスがインストールされていることがわかります。これには、複数の同じバージョンも含まれます。 複数のエージェント インスタンスがインストールされているため、VM は最新バージョンの Windows Azure ゲスト エージェントを起動しません。

**解決方法**

次の手順に従って、Windows Azure ゲスト エージェントを手動でアンインストールしてから再インストールします。

1. [コントロール パネル]、[プログラムと機能] の順に開き、Windows Azure ゲスト エージェントをアンインストールします。
1. タスク マネージャーを開き、次のサービスを手動で停止します:Windows Azure ゲスト エージェント サービス、RDAgent サービス、Windows Azure テレメトリ サービス、Windows Azure ネットワーク エージェント サービス。
1. C:\WindowsAzure の下に、OLD という名前のフォルダーを作成します。
1. Packages または GuestAgent という名前のフォルダーを OLD フォルダーに移動します。 さらに、C:\WindowsAzure\logs 内の GuestAgent_x.x.xxxxx で始まるすべての GuestAgent フォルダーを OLD フォルダーに移動します。
1. 最新バージョンの MSI エージェントをダウンロードしてインストールします。 インストールを実行するには、管理者権限が必要です。
1. 次の MSI コマンドを使用してゲスト エージェントをインストールします。
    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log`
    ```
1. RDAgent、Windows Azure ゲスト エージェント、Windows Azure テレメトリ サービスが現在実行中であることを確認します。
 
1. WaAppAgent.exe を調べて、最新バージョンの Windows Azure ゲスト エージェントが実行されていることを確認します。
 
1. C:\WindowsAzure の下にある OLD フォルダーを削除します。
  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>WireServer IP (ホスト IP) に接続できない 

WaAppAgent.log と Telemetry.log に次のエラー エントリがあることがわかります。

**ログ情報**

```Log sample
[ERROR] GetVersions() failed with exception: Microsoft.ServiceModel.Web.WebProtocolException: Server Error: Service Unavailable (ServiceUnavailable) ---> 
System.Net.WebException: The remote server returned an error: (503) Server Unavailable.
```
```Log sample
[00000011] [12/11/2018 06:27:55.66] [WARN]  (Ignoring) Exception while fetching supported versions from HostGAPlugin: System.Net.WebException: Unable to connect to the remote server 
---> System.Net.Sockets.SocketException: An attempt was made to access a socket in a way forbidden by its access permissions 168.63.129.16:32526
at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
at System.Net.ServicePoint.ConnectSocketInternal(Boolean connectFailure, Socket s4, Socket s6, Socket& socket, IPAddress& address, ConnectSocketState status, IAsyncResult asyncResult, Exception& exception)
--- End of inner exception stack trace ---
at System.Net.WebClient.DownloadDataInternal(Uri address, WebRequest& request)
at System.Net.WebClient.DownloadString(Uri address)
at Microsoft.GuestAgentHostPlugin.Client.GuestInformationServiceClient.GetVersions()
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.HostGAPluginUtility.UpdateHostGAPluginAvailability()`
```
**分析**

VM が WireServer ホスト サーバーに到達できません。

**解決方法**

1. WireServer に到達できないため、リモート デスクトップを使用して VM に接続し、インターネット ブラウザーから次の URL にアクセスしてみます: http://168.63.129.16/?comp=versions 
1. 手順 1 で指定した URL に接続できない場合は、ネットワーク インターフェイスを調べて、インターフェイスが DHCP 対応として設定され、DNS を使用しているかどうかを確認します。 ネットワーク インターフェイスの DHCP の状態を確認するには、次のコマンドを実行します: `netsh interface ip show config`。
1. DHCP が無効になっている場合は、次のコマンドを実行します (黄色の値をインターフェイスの名前に変更してください): `netsh interface ip set address name="Name of the interface" source=dhcp`。
1. ファイアウォール、プロキシ、または IP アドレス 168.63.129.16 へのアクセスをブロックするその他のソースが原因で起きる可能性がある問題を確認します。
1. Windows ファイアウォールまたはサードパーティのファイアウォールによってポート 80、443、32526 へのアクセスがブロックされているかどうかを確認します。 このアドレスをブロックしてはいけない詳しい理由については、「[IP アドレス 168.63.129.16 とは](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16)」をご覧ください。

### <a name="guest-agent-is-stuck-stopping"></a>ゲスト エージェントが「停止」中にスタックする  

WaAppAgent.log に次のエラー エントリがあることがわかります。

**ログ情報** 

```Log sample
[00000007] [07/18/2019 14:46:28.87] [WARN] WindowsAzureGuestAgent stopping.
[00000007] [07/18/2019 14:46:28.89] [INFO] Uninitializing StateExecutor with WaitForTerminalStateReachedOnEnd : True
[00000004] [07/18/2019 14:46:28.89] [WARN] WindowsAzureGuestAgent could not be stopped. Exception: System.NullReferenceException: Object reference not set to an instance of an object.
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.WaitForExtensionWorkflowComplete(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorForCloud.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentCore.Stop(Boolean waitForTerminalState)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.DoStopService()
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.<>c__DisplayClass2.<OnStopProcessing>b__1()
```
**分析**

Windows Azure ゲスト エージェントが停止プロセスでスタックしています。

**解決方法**

1. WaAppAgent.exe が VM で実行されていることを確認します。 実行されていない場合は、rdgagent サービスを再起動して 5 分間待ちます。 WaAppAgent.exe が実行されている場合は、WindowsAzureGuest.exe プロセスを終了します。
2. 手順 1 で問題が解決しない場合は、現在インストールされているバージョンを削除し、エージェントの最新バージョンを手動でインストールします。

### <a name="npcap-loopback-adapter"></a>Npcap ループバック アダプター 

WaAppAgent.log に次のエラー エントリがあることがわかります。
 
```Log sample
[00000006] [06/20/2019 07:44:28.93] [INFO]  Attempting to discover fabric address on interface Npcap Loopback Adapter.
[00000024] [06/20/2019 07:44:28.93] [WARN]  Empty DHCP option data returned
[00000006] [06/20/2019 07:44:28.93] [ERROR] Did not discover fabric address on interface Npcap Loopback Adapter
```
**分析**

Npcap ループバック アダプターは、Wireshark によって VM にインストールされます。 Wireshark は、ネットワーク トラフィックのプロファイリングとパケットの分析を行うオープンソース ツールです。 このようなツールは、多くの場合、ネットワーク アナライザー、ネットワーク プロトコル アナライザー、またはスニファーと呼ばれます。

**解決方法**

Wireshark によって Npcap ループバック アダプターがインストールされている可能性があります。 これを無効にした後で、問題が解決されているかどうかを確認します。

## <a name="next-steps"></a>次のステップ

「Windows Azure ゲスト エージェントが機能しない」問題のさらなるトラブルシューティングを行うには、[Microsoft サポートにお問い合わせください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。