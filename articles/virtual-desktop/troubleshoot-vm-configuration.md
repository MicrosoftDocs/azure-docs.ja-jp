---
title: Windows Virtual Desktop セッション ホストのトラブルシューティング - Azure
description: Windows Virtual Desktop セッション ホスト仮想マシンを構成しているときに発生する問題を解決する方法。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f1f2caa194972bb870dc58cfa0bd6ccb2f8db053
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100099916"
---
# <a name="session-host-virtual-machine-configuration"></a>セッション ホスト仮想マシンの構成

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトを含む Windows Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトなしで Windows Virtual Desktop (classic) を使用している場合は、[こちらの記事](./virtual-desktop-fall-2019/troubleshoot-vm-configuration-2019.md)を参照してください。

この記事は、Windows Virtual Desktop セッション ホスト仮想マシン (VM) の構成中に発生する問題を解決するときにご利用ください。

## <a name="provide-feedback"></a>フィードバックの提供

Windows Virtual Desktop サービスに関して製品チームや活発なコミュニティのメンバーと話をしたいときは、[Windows Virtual Desktop の技術コミュニティ](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)にアクセスしてください。

## <a name="vms-are-not-joined-to-the-domain"></a>VM がドメインに参加していません

仮想マシン (VM) をドメインに参加させることができない場合は、次の手順に従ってください。

- 「[Join a Windows Server virtual machine to a managed domain](../active-directory-domain-services/join-windows-vm.md)」(Windows Server 仮想マシンのマネージド ドメインへの参加) のプロセスを利用するか、[ドメイン参加テンプレート](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)を利用し、VM を手動で参加させます。
- VM のコマンド ラインからドメイン名を ping してみます。
- 「[Troubleshooting Domain Join Error Messages](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx)」(ドメイン参加エラー メッセージのトラブルシューティング) でドメイン参加エラー メッセージの一覧を確認します。

### <a name="error-incorrect-credentials"></a>エラー:資格情報が間違っています

**原因:** Azure Resource Manager テンプレート インターフェイスの修正で資格情報が入力されたとき、入力ミスがありました。

**解決策:** 解決するには、次のいずれかの操作を行ってください。

- ドメインに VM を手動で追加します。
- 資格情報が確認されたらテンプレートを再デプロイします。 「[PowerShell を使用してホスト プールを作成する](create-host-pools-powershell.md)」を参照してください。
- 「[Joins an existing Windows VM to AD Domain](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)」 (既存の Windows VM を AD ドメインに参加させる) のテンプレートで VM をドメインに参加させます。

### <a name="error-timeout-waiting-for-user-input"></a>エラー:ユーザー入力の待機中にタイムアウトになりました

**原因:** ドメイン参加の完了に使用するアカウントに多要素認証 (MFA) が設定されている可能性があります。

**解決策:** 解決するには、次のいずれかの操作を行ってください。

- アカウントの MFA を一時的に削除します。
- サービス アカウントを使用します。

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>エラー:プロビジョニング中に使用されたアカウントに、操作を完了するためのアクセス許可が与えられていません。

**原因:** 使用中のアカウントには、コンプライアンスと規則により、ドメインに VM を参加させるためのアクセス許可が与えられていません。

**解決策:** 解決するには、次のいずれかの操作を行ってください。

- 管理者グループに属するアカウントを使用します。
- 必要なアクセス許可を使用中のアカウントに与えます。

### <a name="error-domain-name-doesnt-resolve"></a>エラー:ドメイン名が解決されません。

**原因 1:** ドメインが置かれている仮想ネットワーク (VNET) に関連しない仮想ネットワークに VM が入っています。

**解決策 1:** VM がプロビジョニングされた VNET と、ドメイン コントローラー (DC) が実行されている VNET の間に VNET ピアリングを作成します。 「[仮想ネットワーク ピアリングを作成する - Resource Manager、異なるサブスクリプション](../virtual-network/create-peering-different-subscriptions.md)」を参照してください。

**原因 2:** Azure Active Directory Domain Services (Azure AD DS) を使用する場合、仮想ネットワークの DNS サーバーの設定が、管理対象のドメイン コントローラーを指すように更新されません。

**解決策 2:** Azure AD DS を含む仮想ネットワークの DNS 設定を更新するには、「[Azure 仮想ネットワークの DNS 設定を更新する](../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)」を参照してください。

**原因 3:** ネットワーク インターフェイスの DNS サーバー設定が、仮想ネットワーク上の適切な DNS サーバーを指していません。

**解決策 3:** 次のいずれかの操作を実行して、[DNS サーバーの変更] の手順に従って解決します。
- [[DNS サーバーの変更]](../virtual-network/virtual-network-network-interface.md#change-dns-servers) の手順に従って、ネットワーク インターフェイスの DNS サーバー設定を **[カスタム]** に変更し、仮想ネットワーク上の DNS サーバーのプライベート IP アドレスを指定します。
- [[DNS サーバーの変更]](../virtual-network/virtual-network-network-interface.md#change-dns-servers) の手順に従って、ネットワーク インターフェイスの DNS サーバー設定を **[仮想ネットワークから継承する]** に変更し、その後、[[DNS サーバーの変更]](../virtual-network/manage-virtual-network.md#change-dns-servers) の手順に従って、仮想ネットワークの DNS サーバーの設定を変更します。

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Windows Virtual Desktop Agent と Windows Virtual Desktop Boot Loader がインストールされていません

推奨される VM のプロビジョニング方法は、Azure portal 作成テンプレートを使用することです。 このテンプレートにより、Windows Virtual Desktop Agent と Windows Virtual Desktop Agent Boot Loader が自動的にインストールされます。

次の手順でコンポーネントがインストールされていることを確認し、エラー メッセージがないか確認します。

1. **[コントロール パネル]**  > 、 **[プログラム]**  > 、 **[プログラムと機能]** の順に選択し、2 つのコンポーネントがインストールされていることを確認します。 **Windows Virtual Desktop Agent** と **Windows Virtual Desktop Agent Boot Loader** がない場合、VM にインストールされていません。
2. **エクスプローラー** を開き、**C:\Windows\Temp\ScriptLog.log** に移動します。 ファイルがない場合、2 つのコンポーネントをインストールした PowerShell DSC が、指定されたセキュリティ状況の中で実行できなかったことを示します。
3. ファイル **C:\Windows\Temp\ScriptLog.log** がある場合、それを開き、エラー メッセージがないか確認します。

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>エラー:Windows Virtual Desktop Agent と Windows Virtual Desktop Agent Boot Loader がありません C:\Windows\Temp\ScriptLog.log もありません

**原因 1:** Azure Resource Manager テンプレートに入力中に指定された資格情報が間違っているか、アクセス許可が足りません。

**解決策 1:** 「[PowerShell を使用してホスト プールを作成する](create-host-pools-powershell.md)」の手順で VM に足りないコンポーネントを手動追加します。

**原因 2:** PowerShell DSC は起動し、実行できましたが、Windows Virtual Desktop にサインインして必要な情報を得ることができないため、実行を完了できませんでした。

**解決策 2:** 次のリストにある項目を確認します。

- アカウントに MFA が指定されていないことを確認します。
- ホスト プールの名前が正しく、ホスト プールが Windows Virtual Desktop に存在することを確認します。
- 少なくとも Azure サブスクリプションまたはリソース グループに対する共同作成者アクセス許可がアカウントに与えられていることを確認します。

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>エラー:認証失敗、C:\Windows\Temp\ScriptLog.log にエラー

**原因:** PowerShell DSC は実行できましたが、Windows Virtual Desktop に接続できませんでした。

**解決策:** 次のリストにある項目を確認します。

- VM を Windows Virtual Desktop サービスに手動で登録します。
- Azure サブスクリプションまたはリソース グループでホスト プールを作成するためのアクセス許可が、Windows Virtual Desktop への接続に使用されるアカウントに与えられていることを確認します。
- アカウントに MFA が指定されていないことを確認します。

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Windows Virtual Desktop Agent が Windows Virtual Desktop サービスに登録されません

(手動か、Azure Resource Manager テンプレートと PowerShell DSC によって) Windows Virtual Desktop Agent が最初にセッション ホスト VM にインストールされたとき、登録トークンが与えられます。 次のセクションでは、Windows Virtual Desktop Agent とトークンに適用される問題のトラブルシューティングについて説明します。

### <a name="error-the-status-filed-in-get-azwvdsessionhost-cmdlet-shows-status-as-unavailable"></a>エラー:AzWvdSessionHost コマンドレットの状態フィールドに、状態が Unavailable (利用不可) と示されています

> [!div class="mx-imgBorder"]
> ![Get-AzWvdSessionHost コマンドレットを実行すると状態が Unavailable (利用不可) と示される。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**原因:** エージェントを新しいバージョンに更新できません。

**解決策:** 次の手順でエージェントを手動更新します。

1. セッション ホスト VM に新しいバージョンのエージェントをダウンロードします。
2. タスク マネージャーを起動し、[サービス] タブで RDAgentBootLoader サービスを停止します。
3. 新しいバージョンの Windows Virtual Desktop Agent のインストーラーを実行します。
4. 登録トークンを求められたら、エントリ INVALID_TOKEN を削除し、[次へ] を押します (新しいトークンは要求されません)。
5. インストール ウィザードを完了します。
6. タスク マネージャーを開き、RDAgentBootLoader サービスを開始します。

## <a name="error-windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>エラー:Windows Virtual Desktop Agent のレジストリ エントリ IsRegistered に値 0 が表示されます

**原因:** 登録トークンの有効期限が切れています。

**解決策:** 次の手順でエージェント レジストリ エラーを修正します。

1. 登録トークンが既に存在する場合は、Remove-AzWvdRegistrationInfo を使用してそれを削除します。
2. **New-AzWvdRegistrationInfo** コマンドレットを実行して、新しいトークンを生成します。
3. *-ExpriationTime* パラメーターが 3 日に設定されていることを確認します。

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-azwvdsessionhost"></a>エラー:Get-AzWvdSessionHost を実行したとき、Windows Virtual Desktop Agent からハートビートが報告されません

**原因 1:** RDAgentBootLoader サービスが停止しました。

**解決策 1:** タスク マネージャーを起動し、[サービス] タブで RDAgentBootLoader サービスの状態が停止になっている場合、サービスを起動します。

**原因 2:** ポート 443 が閉じられている可能性があります。

**解決策 2:** 次の手順でポート 443 を開きます。

1. [Sysinternal ツール](/sysinternals/downloads/psping/)から PSPing ツールをダウンロードし、ポート 443 が開いていることを確認します。
2. エージェントが実行されているセッション ホスト VM に PSPing をインストールします。
3. 管理者としてコマンド プロンプトを開き、下のコマンドを実行します。

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. PSPing で RDBroker から返された情報が受け取られることを確認します。

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Windows Virtual Desktop サイドバイサイド スタックで問題を解決する

Windows Virtual Desktop サイドバイサイド スタックが Windows Server 2019 で自動的にインストールされます。 Microsoft Installer (MSI) を使用し、Microsoft Windows Server 2016 または Windows Server 2012 R2 にサイドバイサイド スタックをインストールします。 Microsoft Windows 10 の場合、Windows Virtual Desktop サイドバイサイド スタックは **enablesxstackrs.ps1** で有効になります。

サイドバイサイド スタックは主に 3 とおりの方法でセッション ホスト プール VM にインストールされるか、有効化されます。

- Azure portal 作成テンプレートを使用する
- マスター イメージに含まれ、有効になっている
- 各 VM に手動でインストールし、有効にする (あるいは、extensions/PowerShell を使用する)

Windows Virtual Desktop サイドバイサイド スタックに問題がある場合、コマンド プロンプトから **qwinsta** コマンドを入力し、サイドバイサイド スタックがインストールされ、有効になっていることを確認します。

サイドバイサイド スタックがインストールされ、有効になっている場合、**qwinsta** の出力には、**rdp-sxs** が一覧表示されます。

> [!div class="mx-imgBorder"]
> ![サイドバイサイド スタックは、出力に rdp-sxs として記載されている qwinsta でインストールされ、有効化されました。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

下に記載されているレジストリ エントリを調べ、その値が一致することを確認します。 レジストリ キーがないか、値が一致しない場合は、[サポートされているオペレーティング システム](troubleshoot-agent.md#error-operating-a-pro-vm-or-other-unsupported-os)を実行していることを確認してください。 これに該当する場合は、「[PowerShell を使用してホスト プールを作成する](create-host-pools-powershell.md)」にあるサイドバイサイド スタックの再インストール方法を実行してください。

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>エラー:O_REVERSE_CONNECT_STACK_FAILURE

> [!div class="mx-imgBorder"]
> ![O_REVERSE_CONNECT_STACK_FAILURE エラー コード。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**原因:** サイドバイサイド スタックがセッション ホスト VM にインストールされていません。

**解決策:** 次の手順で、セッション ホスト VM にサイドバイサイド スタックをインストールします。

1. リモート デスクトップ プロトコル (RDP) を使用し、ローカル管理者としてセッション ホスト VM に直接入ります。
2. 「[PowerShell を使用してホスト プールを作成する](create-host-pools-powershell.md)」を参照してサイドバイサイド スタックをインストールします。

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>誤作動する Windows Virtual Desktop サイドバイサイド スタックの修正方法

サイドバイサイド スタックに誤作動を引き起こす状況が確認されています。

- 正しい手順でサイドバイサイド スタックを有効にしていない
- Windows 10 Enhanced Versatile Disc (EVD) の自動更新
- リモート デスクトップ セッション ホスト (RDSH) ロールがない
- enablesxsstackrc.ps1 を複数回実行
- ローカル管理特権のないアカウントで enablesxsstackrc.ps1 を実行

このセクションの手順は、Windows Virtual Desktop サイドバイサイド スタックをアンインストールする際に参考になります。 サイドバイサイド スタックをアンインストールしたら、「[PowerShell を使用してホスト プールを作成する](create-host-pools-powershell.md)」の「Windows Virtual Desktop ホスト プールに VM を登録する」に進み、サイドバイサイド スタックを再インストールします。

修復に使用される VM は、誤作動するサイドバイサイド スタックがある VM と同じサブネットならびにドメインに置かれている必要があります。

同じサブセットならびにドメインから次の手順で修復します。

1. 標準のリモート デスクトップ プロトコル (RDP) で、修正プログラムを適用する VM に接続します。
2. [https://docs.microsoft.com/sysinternals/downloads/psexec](https://docs.microsoft.com/sysinternals/downloads/psexec ) から PsExec をダウンロードします。
3. ダウンロードしたファイルを解凍します。
4. ローカル管理者としてコマンド プロンプトを起動します。
5. PsExec が解凍されたフォルダーに移動します。
6. コマンド プロンプトから次のコマンドを実行します。

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!NOTE]
    >VMname は、サイドバイサイド スタックが誤作動する VM のマシン名です。

7. [同意する] をクリックし、PsExec の使用許諾契約に同意します。

    > [!div class="mx-imgBorder"]
    > ![ソフトウェア使用許諾契約のスクリーンショット。](media/SoftwareLicenseTerms.png)

    >[!NOTE]
    >このダイアログは、PsExec の初回実行時にのみ表示されます。

8. サイドバイサイド スタックが誤作動する VM でコマンド プロンプト セッションが開いたら、qwinsta を実行し、rdp-sxs という名前のエントリがあることを確認します。 エントリがない場合、サイドバイサイド スタックは VM 上にありません。そのため、問題はサイドバイサイド スタックに関連したものではありません。

    > [!div class="mx-imgBorder"]
    > ![管理者コマンド プロンプト](media/AdministratorCommandPrompt.png)

9. 次のコマンドを実行します。このコマンドによって、サイドバイサイドが誤作動する VM にインストールされている Microsoft コンポーネントが一覧表示されます。

    ```cmd
        wmic product get name
    ```

10. 上記の手順から得られた製品名を指定し、下のコマンドを実行します。

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. "リモート デスクトップ" で始まるすべての製品をアンインストールします。

12. Windows Virtual Desktop コンポーネントがすべてアンインストールされたら、お使いのオペレーション システム向けの手順を実行します。

13. お使いのオペレーティング システムが Windows Server であれば、(Azure portal か PsExec ツールから) サイドバイサイド スタックが誤作動した VM を再起動します。

お使いのオペレーティング システムが Microsoft Windows 10 であれば、下の手順を実行します。

14. PsExec を実行する VM からエクスプローラーを起動し、サイドバイサイドが誤作動した VM のシステム ドライブに disablesxsstackrc.ps1 をコピーします。

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname は、サイドバイサイド スタックが誤作動する VM のマシン名です。

15. 推奨プロセス: PsExec ツールから、PowerShell を起動し、前の手順のフォルダーに移動し、disablesxsstackrc.ps1 を実行します。 あるいは、次のコマンドレットを実行できます。

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. コマンドレットの実行が終わると、サイドバイサイドが誤作動する VM を再起動します。

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>リモート デスクトップ ライセンス モードが構成されていない

管理者アカウントを使用して Windows 10 Enterprise マルチセッションにサインインしている場合、「Remote Desktop licensing mode is not configured, Remote Desktop Services will stop working in X days. On the Connection Broker server, use Server Manager to specify the Remote Desktop licensing mode. （リモート デスクトップ ライセンス モードが構成されていません。リモート デスクトップ サービスはあと X 日で動作を停止します。Connection Broker サーバーで、サーバー マネージャーを使用してリモート デスクトップ ライセンス モードを指定してください。）」と書かれた通知を受信する可能性があります。

制限時間が経過すると、「このコンピューターで利用できるリモート デスクトップ クライアント アクセス ライセンスがないため、リモート セッションは切断されました」というエラー メッセージが表示されます。

これらのメッセージのいずれかが表示された場合は、イメージに最新の Windows 更新プログラムがインストールされていないか、グループ ポリシーでリモート デスクトップ ライセンス モードを設定していることを意味します。 次のセクションの手順に従って、グループ ポリシーの設定を確認し、Windows 10 Enterprise マルチセッションのバージョンを特定して、対応する更新プログラムをインストールしてください。

>[!NOTE]
>Windows Virtual Desktop では、ホスト プールに Windows Server セッション ホストが含まれている場合は、RDS クライアント アクセス ライセンス (CAL) のみが必要です。 RDS CAL を構成する方法については、「[クライアント アクセス ライセンス (CAL) を使用して RDS 展開をライセンスする](/windows-server/remote/remote-desktop-services/rds-client-access-license/)」を参照してください。

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>リモート デスクトップ ライセンス モードのグループ ポリシーの設定を無効にする

グループ ポリシーの設定を確認するには、VM でグループ ポリシーエディターを開いて、 **[管理用テンプレート]**  >  **[Windows コンポーネント]**  >  **[リモート デスクトップ サービス]**  >  **[リモート デスクトップ セッション ホスト]**  >  **[ライセンス]**  >  **[リモート デスクトップ ライセンス モードの設定]** の順に移動します。 グループポリシーの設定が **[有効]** の場合は、 **[無効]** に変更します。 既に無効の場合は、そのままにしておきます。

>[!NOTE]
>ご自身のドメインを使用してグループ ポリシーを設定する場合は、これらの Windows 10 Enterprise マルチセッション VM を対象としたポリシーでこの設定を無効にします。

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>使用している Windows 10 Enterprise マルチセッションのバージョンを特定する

使用している Windows 10 Enterprise マルチセッションのバージョンを特定するには、次のようにします。

1. 管理者アカウントを使用してサインインします。
2. [スタート] メニューの横にある検索バーに「About」と入力します。
3. **[PC 情報]** を選択します。
4. "バージョン" の横の数字を確認します。 次の図に示すように、この数字は "1809" または "1903" のいずれかになります。

    > [!div class="mx-imgBorder"]
    > ![Windows の仕様ウィンドウのスクリーンショット。 バージョン番号が青色で強調表示されています。](media/windows-specifications.png)

バージョン番号がわかったら、該当するセクションに進んでください。

### <a name="version-1809"></a>バージョン 1809

バージョン番号が "1809" の場合は、[KB4516077 更新プログラム](https://support.microsoft.com/help/4516077)をインストールします。

### <a name="version-1903"></a>バージョン 1903

Azure ギャラリーから、最新バージョンの Windows 10 バージョン 1903 イメージを使用して、ホスト オペレーティング システムを再デプロイします。

## <a name="we-couldnt-connect-to-the-remote-pc-because-of-a-security-error"></a>We couldn't connect to the remote PC because of a security error (セキュリティ エラーのため、リモート PC に接続できませんでした)

「We couldn't connect to the remote PC because of a security error. (セキュリティ エラーのため、リモート PC に接続できませんでした。) If this keeps happening, ask your admin or tech support for help (この問題が引き続き発生する場合は、管理者または技術サポートにお問い合わせください)」というエラーがユーザーに表示された場合は、既定の RDP アクセス許可を変更する既存のポリシーを確認してください。 このエラーを発生させる可能性があるポリシーの 1 つは、"リモート デスクトップ サービスを使ったログオンを許可" セキュリティ ポリシーです。

このポリシーの詳細については、「[リモート デスクトップ サービスを使ったログオンを許可](/windows/security/threat-protection/security-policy-settings/allow-log-on-through-remote-desktop-services)」を参照してください。

## <a name="next-steps"></a>次のステップ

- Windows Virtual Desktop トラブルシューティングの概要とエスカレーション トラックについては、「[トラブルシューティングの概要、フィードバック、サポート](troubleshoot-set-up-overview.md)」を参照してください。
- Windows Virtual Desktop 環境でホスト プールを作成しているときに発生した問題のトラブルシューティングを行うには、[環境とホスト プールの作成](troubleshoot-set-up-issues.md)に関するページを参照してください。
- Windows Virtual Desktop で仮想マシン (VM) の構成中に発生した問題を解決するには、[Session host virtual machine configuration (セッション ホスト仮想マシンの構成)](troubleshoot-vm-configuration.md) に関する記事を参照してください。
- Windows Virtual Desktop エージェントまたはセッション接続に関連した問題をトラブルシューティングするには、「[Windows Virtual Desktop エージェントに関する一般的な問題をトラブルシューティングする](troubleshoot-agent.md)」を参照してください。
- Windows Virtual Desktop クライアント接続の問題をトラブルシューティングするには、[Windows Virtual Desktop サービスの接続](troubleshoot-service-connection.md)に関するページを参照してください。
- リモート デスクトップ クライアントの問題をトラブルシューティングするには、[リモート デスクトップ クライアントのトラブルシューティング](troubleshoot-client.md) に関するページを参照してください
- Windows Virtual Desktop で PowerShell を使用しているときに発生した問題を解決するには、「[Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照してください。
- サービスの詳細については、[Windows Virtual Desktop 環境](environment-setup.md)に関するページを参照してください。
- トラブルシューティング チュートリアルについては、「[Tutorial:Resource Manager テンプレート デプロイのトラブルシューティング](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)」を参照してください。
- 監査アクションについては、「 [リソース マネージャーの監査操作](../azure-resource-manager/management/view-activity-logs.md)」をご覧ください。
- デプロイ時にエラーが発生した場合の対応については、 [デプロイ操作の確認](../azure-resource-manager/templates/deployment-history.md)に関するページを参照してください。
