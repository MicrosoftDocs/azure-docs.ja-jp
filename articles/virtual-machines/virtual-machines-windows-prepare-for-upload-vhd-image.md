---
title: "Windows VHD の Azure へのアップロードの準備 | Microsoft Docs"
description: "Azure にアップロードする前に Windows VHD または VHDX を準備する方法"
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 1/11/2017
ms.author: glimoli;genli
translationtype: Human Translation
ms.sourcegitcommit: 5d8274f61c3de178c9d418adb9be1efe0fe62bc1
ms.openlocfilehash: 6fbfc74cb1cce744b51345c0732b40b95be21c94
ms.lasthandoff: 01/14/2017


---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Azure にアップロードする Windows VHD または VHDX を準備する
Windows VM をオンプレミスから Azure にアップロードするには、仮想ハード ディスク (VHD または VHDX) を準備する必要があります。 Azure では、VHD ファイル形式で容量固定ディスクの第 1 世代の仮想マシンのみがサポートされています。 VHD のサイズの上限は、1,023 GB です。 第 1 世代の仮想マシンを、VHDX ファイル形式から VHD ファイル形式に、容量可変から容量固定ディスクに変換できます。 ただし、仮想マシンの世代を変更することはできません。 詳細については、「[Should I create a generation 1 or 2 virtual machine in Hyper-V? (Hyper-V で第 1 世代または第 2 世代の仮想マシンを作成する必要はありますか?)](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)」を参照してください。

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>仮想ディスクを VHD および容量固定ディスクに変換する 
仮想ディスクを Azure に必要な形式に変換する必要がある場合は、このセクションのいずれかのメソッドを使用します。 仮想ディスクの変換プロセスを実行する前に仮想マシンをバックアップして、Windows VHD がローカル サーバー上で正しく動作するかどうかを確認します。 Azure に変換またはアップロードする前に、VM 自体に発生しているすべてのエラーを解決します。

ディスクを変換した後は、変換したディスクを使用する VM を作成します。 VM を起動してサインインし、VM アップロードの準備を完了します。

### <a name="convert-disk-using-hyper-v-manager"></a>Hyper-V マネージャーを使用したディスクの変換
1. Hyper-V マネージャーを開いて、左側のローカル コンピューターを選択します。 その上にあるメニューで、**[アクション]**、 > **[ディスクの編集]** の順にクリックします。
2. **[仮想ハード ディスクの場所]** 画面でお使いの仮想ディスクを参照し、選択します。
3. **[操作の選択]** 画面で、**[変換]** を選択して **[次へ]** をクリックします。
4. VHDX から変換する場合は、**[VHD]** を選択し、**[次へ]** をクリックします。
5. 容量可変ディスクから変換する必要がある場合は、**[容量固定]** を選択し、**[次へ]** をクリックします。
6. 新しい VHD ファイルを保存するためのパスを参照して選択します。
7. **[完了]** をクリックして閉じます。

### <a name="convert-disk-using-powershell"></a>PowerShell を使用したディスクの変換
仮想ディスクを変換するには、Windows PowerShell で [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) コマンドレットを使用します。 PowerShell の起動時に、**[管理者として実行]** を選択します。 次の例では、VHDX から VHD に、および容量可変ディスクから容量固定ディスクに変換する方法を示します。

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
-Path の値を変換する仮想ハードディスクへのパスで置き換え、-DestinationPath を新しいパスと変換されたディスクの名前で置き換えます。

### <a name="convert-from-vmware-vmdk-disk-format"></a>VMware VMDK ディスク フォーマットからの変換
[VMDK ファイル形式](https://en.wikipedia.org/wiki/VMDK)の Windows VM イメージがある場合は、[Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497) を使用して VHD に変換できます。 詳細については、ブログ記事「[How to Convert a VMware VMDK to Hyper-V VHD (VMware VMDK から Hyper-V VHD への変換方法)](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)」を参照してください。

## <a name="set-windows-configurations-for-azure"></a>Azure 用の Windows 構成を設定する

Azure にアップロードする予定の仮想マシンで、コマンド プロンプト ウィンドウから[管理者特権](https://technet.microsoft.com/library/cc947813.aspx)で次のすべてのコマンドを実行します。

1. ルーティング テーブルの静的な固定ルートを削除します。
   
   * ルート テーブルを表示するには、コマンド プロンプト ウィンドウから `route print` を実行します。
   * **[Persistence Routes (固定ルート)]** セクションを確認します。 固定ルートがある場合は、 [ルートの削除](https://technet.microsoft.com/library/cc739598.apx) を使ってルートを削除します。
2. WinHTTP プロキシを削除します。
   
    ```CMD
    netsh winhttp reset proxy
    ```
3. ディスク SAN ポリシーを [Onlineall](https://technet.microsoft.com/library/gg252636.aspx) に設定します。 
   
    ```CMD
    diskpart 
    san policy=onlineall
    exit
    ```
    

4. Windows を世界協定時刻 (UTC) の時間に設定して、Windows タイム (w32time) サービスのスタートアップの種類を**自動**に設定します。
   
    ```CMD
    REG ADD HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
    ```
    ```CMD
    sc config w32time start= auto
    ```

## <a name="set-services-startup-to-windows-default-values"></a>サービスのスタートアップを Windows の既定値に設定する
次の各 Windows サービスが **Windows の既定値**に設定されていることを確認します。 スタートアップの設定をリセットするには、次のコマンドを実行します。
   
```CMD
sc config bfe start= auto
   
sc config dcomlaunch start= auto
   
sc config dhcp start= auto
   
sc config dnscache start= auto
   
sc config IKEEXT start= auto
   
sc config iphlpsvc start= auto
   
sc config PolicyAgent start= demand
   
sc config LSM start= auto
   
sc config netlogon start= demand
   
sc config netman start= demand
   
sc config NcaSvc start= demand
   
sc config netprofm start= demand
   
sc config NlaSvc start= auto
   
sc config nsi start= auto
   
sc config RpcSs start= auto
   
sc config RpcEptMapper start= auto
   
sc config termService start= demand
   
sc config MpsSvc start= auto
   
sc config WinHttpAutoProxySvc start= demand
   
sc config LanmanWorkstation start= auto
   
sc config RemoteRegistry start= auto
```

## <a name="update-remote-desktop-registry-settings"></a>リモート デスクトップのレジストリ設定を更新する
1. リモート デスクトップ プロトコル (RDP) リスナーに関連付けられている自己署名証明書がある場合は削除します。
   
    ```CMD
    REG DELETE "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp\SSLCertificateSHA1Hash”
    ```
   
    RDP リスナー用の証明書を構成する方法については、「 [Listener Certificate Configurations in Windows Server ](https://blogs.technet.microsoft.com/askperf/2014/05/28/listener-certificate-configurations-in-windows-server-2012-2012-r2/)
2. RDP サービスの [KeepAlive](https://technet.microsoft.com/library/cc957549.aspx) 値を構成します。
   
    ```CMD
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveEnable /t REG_DWORD  /d 1 /f
    ```
    ```CMD
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveInterval /t REG_DWORD  /d 1 /f
    ```
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v KeepAliveTimeout /t REG_DWORD /d 1 /f
    ```
3. RDP サービスの認証モードを構成します。
   
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD  /d 1 /f
   ```
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD  /d 1 /f
   ```
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD  /d 1 /f
    ```
4. 次のサブキーをレジストリに追加して、RDP サービスを有効にします。
   
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD  /d 0 /f
    ```

## <a name="configure-windows-firewall-rules"></a>Windows ファイアウォール規則の構成
1. PowerShell で次のコマンドを実行して、WinRM に&3; つのファイアウォール プロファイル (ドメイン、プライベート、パブリック) の通過を許可し、PowerShell リモート サービスを有効にします。
   
   ```powershell
   Enable-PSRemoting -force
   ```
2. コマンド プロンプト ウィンドウで次のコマンドを実行し、次のゲスト オペレーティング システムのファイアウォール規則が満たされていることを確認します。
   
   * 受信
   
   ```CMD
   netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (LLMNR-UDP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Datagram-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Name-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (Pub-WSD-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (SSDP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (UPnP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (WSD EventsSecure-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   ```
   
   * 受信および送信
   
   ```CMD
   netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
   
   netsh advfirewall firewall set rule group="Core Networking" new enable=yes
   ```
   
   * 送信
   
   ```CMD
   netsh advfirewall firewall set rule dir=out name="Network Discovery (LLMNR-UDP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Datagram-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Name-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (Pub-WSD-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (SSDP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnPHost-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD Events-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD EventsSecure-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD-Out)" new enable=yes
   ```

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>VM が正常で安全であり、RDP でアクセスできることを確認する 
1. コマンド プロンプト ウィンドウで `winmgmt /verifyrepository` を実行して、Windows Management Instrumentation (WMI) リポジトリに一貫性があることを確認します。 リポジトリが破損している場合は、ブログ投稿「[WMI: Repository Corruption, or Not? (WMI: リポジトリの破損かどうかの確認)](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not)」を参照してください。
2. ブート構成データ (BCD) を設定します。
   
   ```CMD
   bcdedit /set {bootmgr} integrityservices enable
   
   bcdedit /set {default} device partition=C:
   
   bcdedit /set {default} integrityservices enable
   
   bcdedit /set {default} recoveryenabled Off
   
   bcdedit /set {default} osdevice partition=C:
   
   bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
   ```
3. TCP パケットを分析するソフトウェアなど、余分な Transport Driver Interface フィルターを削除します。
4. ディスクが正常で一貫性があることを確認するには、コマンド プロンプト ウィンドウで `CHKDSK /f` コマンドを実行します。 "Y" を入力してチェックをスケジュールし、VM を再起動します。
5. 物理コンポーネントまたはその他の仮想化テクノロジに関連する、すべてのサードパーティ ソフトウェアとドライバーをアンインストールします。
6. サード パーティのアプリケーションでポート 3389 を使用していないことを確認します。 このポートは、Azure の RDP サービスに使用します。 コマンド プロンプト ウィンドウで `netstat -anob` を実行し、アプリケーションによって使用されているポートを参照することができます。
7. アップロードする Windows VHD がドメイン コントローラーの場合は、 [追加の手順](https://support.microsoft.com/kb/2904015) に従ってディスクを準備します。
8. VM を再起動して、Windows がまだ正常で、アクセスできることを RDP 接続を使用して確認します。
9. 現在の管理者パスワードをリセットし、このアカウントを使用して RDP 接続を介して Windows にサインインできることを確認します。 このアクセス許可は、"リモート デスクトップ サービスを使ったログオンを許可" グループ ポリシー オブジェクトによって制御されています。 このオブジェクトは、グループ ポリシー エディターの Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment にあります。

## <a name="install-windows-updates"></a>Windows 更新プログラムのインストール
Windows の最新の更新プログラムをインストールします。 これを実行できない場合は、次の更新プログラムがインストールされていることを確認します。
   
   * [KB3137061](https://support.microsoft.com/kb/3137061) Microsoft Azure VMs don't recover from a network outage and data corruption issues occur (Microsoft Azure VM がネットワークの停止およびデータ破損の問題の発生から回復しない)
   * [KB3115224](https://support.microsoft.com/kb/3115224) Reliability improvements for VMs that are running on a Windows Server 2012 R2 or Windows Server 2012 host (Windows Server 2012 R2 または Windows Server 2012 ホストで実行される VM の信頼性に関する機能強化)
   * [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: 特権の昇格に対処する Microsoft Windows のセキュリティ更新プログラム (2016 年 3 月 8 日)
   * [KB3063075](https://support.microsoft.com/kb/3063075) Many ID 129 events are logged when you run a Windows Server 2012 R2 virtual machine in Microsoft Azure (Windows Server 2012 R2 仮想マシンを Microsoft Azure で実行すると多数の ID 129 イベントがログに記録される)
   * [KB3137061](https://support.microsoft.com/kb/3137061) Microsoft Azure VMs don't recover from a network outage and data corruption issues occur (Microsoft Azure VM がネットワークの停止およびデータ破損の問題の発生から回復しない)
   * [KB3114025](https://support.microsoft.com/kb/3114025) Slow performance when you access Azure files storage from Windows 8.1 or Server 2012 R2 (Windows 8.1 または Server 2012 R2 から Azure ファイル ストレージにアクセスしたときパフォーマンスが低下する)
   * [KB3033930](https://support.microsoft.com/kb/3033930) Hotfix fix increases the 64K limit on RIO buffers per process for Azure service in Windows (修正プログラムにより Windows での Azure サービスのプロセスごとの RIO バッファーの 64K の制限が向上)
   * [KB3004545](https://support.microsoft.com/kb/3004545) You cannot access virtual machines that are hosted on Azure hosting services through a VPN connection in Windows (Windows の VPN 接続を介して Azure ホスティング サービスでホストされている仮想マシンにアクセスできない)
   * [KB3082343](https://support.microsoft.com/kb/3082343) Cross-Premises VPN connectivity is lost when Azure site-to-site VPN tunnels use Windows Server 2012 R2 RRAS (Azure サイト間 VPN トンネルで Windows Server 2012 R2 RRAS を使用したときクロスプレミス VPN 接続が失われる)
   * [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: 特権の昇格に対処する Microsoft Windows のセキュリティ更新プログラム (2016 年 3 月 8 日)
   * [KB3146723](https://support.microsoft.com/kb/3146723) MS16-048: Description of the security update for CSRSS: April 12, 2016 (CSRSS 用のセキュリティ更新プログラムの説明: 2016 年 4 月 12 日)
   * [KB2904100](https://support.microsoft.com/kb/2904100) System freezes during disk I/O in Windows (Windows でディスク I/O 時にシステムがフリーズする)
     
## <a name="run-sysprep--a-idstep23a"></a>Sysprep を実行する<a id="step23"></a>    
複数の仮想マシンにデプロイするためのイメージを作成するには、VHD を Azure にアップロードする前に [Sysprep を実行してイメージを一般化する](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)必要があります。 特殊化した VHD を使用するために Sysprep を実行する必要はありません。 詳細については、次の記事を参照してください。
   
   * [Sysprep を使用した Windows 仮想マシンの一般化](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
   * [Sysprep Support for Server Roles (Sysprep でのサーバー ロールのサポート)](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

## <a name="complete-recommended-configurations"></a>推奨される構成を完了する
次の設定は、VHD のアップロードに影響しません。 ただし、これらを構成しておくことを強くお勧めします。

* [Azure Virtual Machines エージェント](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)をインストールします。 エージェントをインストールしたら、VM 拡張機能を有効にすることができます。 VM 拡張機能では、パスワードのリセットや RDP の構成など、VM で使用する重要な機能のほとんどを実装します。
* Dump ログは、Windows のクラッシュの問題をトラブルシューティングするのに役立つ場合があります。 Dump ログ コレクションを有効にします。
  
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f`
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpFolder /t REG_EXPAND_SZ /d "c:\CrashDumps" /f
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpCount /t REG_DWORD /d 10 /f
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpType /t REG_DWORD /d 2 /f
  
    sc config wer start= auto
    ```
* VM を Azure に作成したら、システムで定義されたサイズのページファイルをドライブ D: に構成します。
  
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /t REG_MULTI_SZ /v PagingFiles /d "D:\pagefile.sys 0 0" /f
    ```

## <a name="next-steps"></a>次のステップ
* [Resource Manager デプロイメント向けに Windows VM イメージを Azure にアップロードする](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


