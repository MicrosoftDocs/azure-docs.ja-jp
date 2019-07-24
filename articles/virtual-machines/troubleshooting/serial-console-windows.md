---
title: Windows の Azure 仮想マシン シリアル コンソール | Microsoft Docs
description: Azure Windows 仮想マシンの双方向シリアル コンソール。
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: harijay
ms.openlocfilehash: 57b20f9d694ae0581988762735c35cb65012fd8e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57992370"
---
# <a name="virtual-machine-serial-console-for-windows"></a>Windows の仮想マシンのシリアル コンソール

Azure portal の仮想マシン (VM) のシリアル コンソールでは、Windows 仮想マシンのテキスト ベースのコンソールにアクセスできます。 このシリアル接続では、仮想マシンの COM1 シリアル ポートに接続し、仮想マシンのネットワークまたはオペレーティング システムの状態に左右されずに仮想マシンにアクセスできます。 仮想マシンのシリアル コンソールへのアクセスは、Azure portal を使用することでのみ可能です。 仮想マシンに対する仮想マシン共同作成者以上のアクセス ロールを持つユーザーだけが使用できます。

Linux VM のシリアル コンソールのドキュメントについては、[Linux の仮想マシンのシリアル コンソール](serial-console-linux.md)に関するページをご覧ください。

> [!NOTE]
> 仮想マシンのシリアル コンソールは、グローバル Azure リージョンで一般公開されています。 Azure Government や Azure China Cloud では利用できません。


## <a name="prerequisites"></a>前提条件

* シリアル コンソールにアクセスする VM は、リソース管理デプロイ モデルを使用している必要があります。 クラシック デプロイはサポートされていません。

* シリアル コンソールにアクセスする VM では、[ブート診断](boot-diagnostics.md)が有効になっている必要があります。

    ![ブート診断の設定](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* シリアル コンソールを使用するアカウントには、VM の[仮想マシン共同作成者ロール](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)と[ブート診断](boot-diagnostics.md)のストレージ アカウントが必要です。

* シリアル コンソールにアクセスする VM には、パスワードベースのアカウントが必要です。 このアカウントは、VM アクセス拡張機能の[パスワードのリセット](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password)機能を使用して作成することができます。 **[サポート + トラブルシューティング]** セクションの **[パスワードのリセット]** を選択します。


## <a name="get-started-with-the-serial-console"></a>シリアル コンソールの概要
仮想マシンのシリアル コンソールには、Azure portal からのみアクセスできます。

  1. [Azure Portal](https://portal.azure.com)を開きます。
  1. 左側のメニューで **[Virtual Machines]** を選択します。
  1. 一覧で VM を選びます。 VM の概要ページが開きます。
  1. 下へスクロールして **[サポート + トラブルシューティング]** セクションを表示し、**[シリアル コンソール]** を選択します。 シリアル コンソールで新しいウィンドウが開き、接続が開始されます。

## <a name="enable-serial-console-functionality"></a>シリアル コンソール機能を有効にする

> [!NOTE]
> シリアル コンソールに何も表示されない場合は、VM でそのブート診断が有効になっていることを確認してください。

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>カスタム イメージまたは古いイメージでシリアル コンソールを有効にする
Azure の新しい Windows Server イメージでは、既定で [Special Administrative Console](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) が有効です。 SAC は Windows のサーバー バージョンではサポートされていますが、クライアント バージョン (Windows 10、Windows 8、Windows 7 など) では使用できません。

以前の Windows Server イメージ (2018 年 2 月より前に作成されたもの) では、Azure portal の実行コマンド機能を使用してシリアル コンソールを自動的に有効にすることができます。 Azure portal で **[実行コマンド]** を選択して、リストから **EnableEM** という名前のコマンドを選択します。

![実行コマンドの一覧](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

または、2018 年 2 月より前に作成された Windows 仮想マシンのシリアル コンソールを手動で有効にするには、次の手順を実行します。

1. リモート デスクトップを使用して Windows 仮想マシンに接続します。
1. 管理コマンド プロンプトで次のコマンドを実行します。
    - `bcdedit /ems {current} on`
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. システムを再起動して、SAC コンソールを有効にします。

    ![SAC コンソール](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

必要に応じて、SAC をオフラインでも有効にすることができます。

1. SAC を構成する Windows ディスクをデータ ディスクとして既存の VM にアタッチします。

1. 管理コマンド プロンプトで次のコマンドを実行します。
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>SAC が有効になっているかどうかを知る方法

[SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) が有効になっていない場合、シリアル コンソールに SAC プロンプトは表示されません。 VM の正常性情報が表示される場合もありますが、それ以外は空白になります。 2018 年 2 月より前に作成された Windows Server イメージを使用している場合、SAC が有効になっていない可能性があります。

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>シリアル コンソールの Windows ブート メニューの有効化

Windows ブート ローダーのプロンプトを有効にしてシリアル コンソールに表示する必要がある場合は、ブート構成データに次のオプションを追加します。 詳細については、[bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) に関するページをご覧ください。

1. リモート デスクトップを使用して Windows 仮想マシンに接続します。

1. 管理コマンド プロンプトで次のコマンドを実行します。
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. システムを再起動して、ブート メニューを有効にします。

> [!NOTE]
> ブート マネージャー メニューの表示に対して設定するタイムアウトは、OS ブート時間に影響します。 10 秒のタイムアウト値が短すぎるか、長すぎると思われる場合は、別の値に設定します。

## <a name="use-serial-console"></a>シリアル コンソールを使用する

### <a name="use-cmd-or-powershell-in-serial-console"></a>シリアル コンソールで CMD または PowerShell を使用する

1. シリアル コンソールに接続します。 正常に接続できたら、プロンプトは **SAC>** になります。

    ![SAC に接続する](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  「`cmd`」と入力して、CMD インスタンスがあるチャネルを作成します。

1.  「`ch -si 1`」と入力して、CMD インスタンスを実行しているチャネルに切り替えます。

1.  **Enter** キーを押して、管理アクセス許可を持つサインイン資格情報を入力します。

1.  有効な資格情報を入力すると、CMD インスタンスが開きます。

1.  PowerShell インスタンスを起動するには、CMD インスタンスに「`PowerShell`」と入力し、**Enter** キーを押します。

    ![PowerShell インスタンスを開く](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>NMI 呼び出しにシリアル コンソールを使用する
マスク不可能割り込み (NMI) は、仮想マシン上のソフトウェアで無視されない信号を作成するために設計されています。 従来より、NMI は、特定の応答時間を要したシステム上でのハードウェアの問題を監視するために使用されてきました。 今日、プログラマーおよびシステム管理者は、NMI をハング システムのデバッグやトラブルシューティングのためのメカニズムとして、頻繁に使用しています。

シリアル コンソールは、コマンド バーのキーボード アイコンを使用して、NMI を Azure 仮想マシンに送信するために使用できます。 NMI が配信された後は、仮想マシン構成によってシステムの応答方法が制御されます。 NMI を受信したときにメモリ ダンプ ファイルをクラッシュして作成するように Windows を構成できます。

![NMI を送信する](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

NMI を受信したときにクラッシュ ダンプ ファイルを作成するように Windows を構成する方法については、「[NMI を使用してクラッシュ ダンプ ファイルを生成する方法](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file)」を参照してください。

### <a name="use-function-keys-in-serial-console"></a>シリアル コンソールでファンクション キーを使用する
Windows VM のシリアル コンソールでファンクション キーを有効にして使用することができます。 シリアル コンソールのドロップダウンで F8 キーを押すと、詳細ブート設定メニューを簡単に表示することができますが、シリアル コンソールは他のすべてのファンクション キーと互換性があります。 シリアル コンソールを使用しているコンピューターによっては、キーボードの **Fn** + **F1** (または F2、F3 など) を押す必要があります。

### <a name="use-wsl-in-serial-console"></a>シリアル コンソールで WSL を使用する
Windows Subsystem for Linux (WSL) は Windows Server 2019 以降で有効なので、Windows Server 2019 以降を実行している場合は、シリアル コンソール内で WSL を有効にして使用することもできます。 この点は、Linux コマンドも熟知しているユーザーに役立つ可能性があります。 WSL for Windows Server を有効にする手順については、[インストール ガイド](https://docs.microsoft.com/windows/wsl/install-on-server)に関するページを参照してください。

### <a name="restart-your-windows-vm-within-serial-console"></a>シリアル コンソール内で Windows VM を再起動する
電源ボタンに移動して [Restart VM]\(VM の再起動\) をクリックすると、シリアル コンソール内で VM を再起動できます。 これにより VM の再起動が始まり、Azure portal 内に再起動に関する通知が表示されます。

これは、シリアル コンソールから離れることなく VM のブート メニューにアクセスする場合に便利です。

![Windows シリアル コンソールでの再起動](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-serial-console"></a>シリアル コンソールを無効にする
既定では、すべてのサブスクリプションは、すべての VM に対してシリアル コンソールのアクセスが有効になっています。 サブスクリプション レベルまたは VM レベルのいずれかで、シリアル コンソールを無効にすることができます。

> [!NOTE]
> あるサブスクリプションに対してシリアル コンソールを有効または無効にするには、そのサブスクリプションの書き込み権限が必要です。 これらの権限には、管理者ロールと所有者ロールがありますが、それらに限定されません。 カスタム ロールにも、書き込み権限が与えることができます。

### <a name="subscription-level-disable"></a>サブスクリプション レベルの無効化
サブスクリプション全体のシリアル コンソールは、[Disable Console REST API 呼び出し](/rest/api/serialconsole/console/disableconsole)で無効にできます。 API ドキュメントのページで使用できる**使ってみる**機能を利用して、サブスクリプションのシリアル コンソールを有効または無効にすることができます。 **subscriptionId** のサブスクリプション ID を入力し、**default** に "default" と入力して、**[実行]** を選択します。 Azure CLI コマンドはまだ利用できません。

![REST API Try It](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

または、Cloud Shell で以下の一連の bash コマンドを使用して、サブスクリプションのシリアル コンソールの無効化と有効化、および無効化状態の表示ができます。

* サブスクリプションのシリアル コンソールの無効化状態を取得するには、以下を実行します。
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* サブスクリプションのシリアル コンソールを無効にするには、以下を実行します。
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* サブスクリプションのシリアル コンソールを有効にするには、以下を実行します。
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

### <a name="vm-level-disable"></a>VM レベルの無効化
特定の VM のシリアル コンソールは、その VM のブート診断設定を無効にすることで無効にできます。 Azure portal からブート診断をオフにすると、その VM のシリアル コンソールが無効になります。

## <a name="serial-console-security"></a>シリアル コンソールのセキュリティ

### <a name="access-security"></a>アクセス セキュリティ
シリアル コンソールへのアクセスは、仮想マシンに対する[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)以上のアクセス ロールを持つユーザーだけが限定されています。 シリアル コンソールには [Azure portal](https://portal.azure.com) からアクセスするため、Azure Active Directory テナントに多要素認証が必要な場合は、シリアル コンソールへのアクセスにも MFA が必要になります。

### <a name="channel-security"></a>チャネル セキュリティ
やり取りされるすべてのデータがネットワーク上で暗号化されます。

### <a name="audit-logs"></a>監査ログ
現在、シリアル コンソールへのすべてのアクセスが、仮想マシンの[ブート診断](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics)ログに記録されます。 これらのログへのアクセスは、Azure 仮想マシン管理者が所有し、制御します。

> [!CAUTION]
> コンソールのアクセス パスワードはログに記録されません。 ただし、コンソール内で実行されるコマンドにパスワード、シークレット、ユーザー名、またはその他の形式の個人を特定できる情報 (PII) が含まれていたり、出力されたりした場合、それらの情報は VM のブート診断ログに書き込まれます。 それらは、シリアル コンソールのスクロールバック機能の実装の一部として、表示される他のすべてのテキストと共に書き込まれます。 これらのログは循環型であり、診断ストレージ アカウントに対する読み取りアクセス許可を持つユーザーだけがアクセスできます。 ただし、シークレットや PII が含まれている可能性のあるものにはリモート デスクトップを使用するというベスト プラクティスに従うことをお勧めします。

### <a name="concurrent-usage"></a>同時使用
ユーザーがシリアル コンソールに接続しているときに、別のユーザーがその同じ仮想マシンへのアクセスを要求し、その要求が成功した場合、最初のユーザーが切断され、2 番目のユーザーが同じセッションに接続されます。

> [!CAUTION]
> これは、切断されたユーザーはログアウトされないことを意味します。(SIGHUP または同様のメカニズムを使用して) 切断時に強制的にログアウトする機能は、まだロードマップにあります。 Windows では SAC で自動タイムアウトが有効になっていますが、Linux ではターミナルのタイムアウト設定を構成できます。

## <a name="accessibility"></a>アクセシビリティ
アクセシビリティは、Azure シリアル コンソールの重点事項です。 そのため、視覚や聴覚に障碍があるユーザーや、マウスを使用できない可能性があるユーザーがシリアル コンソールにアクセスできるようにしました。

### <a name="keyboard-navigation"></a>キーボード ナビゲーション
Azure portal からシリアル コンソール インターフェイスでナビゲートするには、キーボードの **Tab** キーを使用します。 現在の場所が画面上で強調表示されます。 シリアル コンソール ウィンドウのフォーカスを解除するには、キーボードの **Ctrl**+**F6** キーを押します。

### <a name="use-the-serial-console-with-a-screen-reader"></a>スクリーン リーダーでシリアル コンソールを使用する
シリアル コンソールには、スクリーン リーダーのサポートが組み込まれています。 スクリーン リーダーを有効にしてナビゲートすると、現在選択されているボタンの代替テキストをスクリーン リーダーで読み上げることができます。

## <a name="common-scenarios-for-accessing-the-serial-console"></a>シリアル コンソールにアクセスする一般的なシナリオ

シナリオ          | シリアル コンソールでのアクション
:------------------|:-----------------------------------------
不適切なファイアウォール規則 | シリアル コンソールにアクセスし、Windows ファイアウォール規則を修正します。
ファイル システムの破損/チェック | シリアル コンソールにアクセスし、ファイルシステムを復旧します。
RDP 構成の問題 | シリアル コンソールにアクセスし、設定を変更します。 詳しくは、[RDP のドキュメント](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access)をご覧ください。
システムのネットワーク ロックダウン | Azure portal からシリアル コンソールにアクセスして、システムを管理します。 一部のネットワーク コマンドについては、「[Windows コマンド - CMD と PowerShell](serial-console-cmd-ps-commands.md)」を参照してください。
ブートローダーの操作 | シリアル コンソールを使用して BCD にアクセスします。 詳細については、「[シリアル コンソールの Windows ブート メニューの有効化](#enable-the-windows-boot-menu-in-the-serial-console)」を参照してください。


## <a name="errors"></a>Errors
ほとんどのエラーは一時的なものであるため、多くの場合、接続の再試行によって解決できます。 次の表にエラーと対応策を示します。

Error                            |   対応策
:---------------------------------|:--------------------------------------------|
Unable to retrieve boot diagnostics settings for *&lt;VMNAME&gt;*. (VMNAME のブート診断設定を取得できません。) To use the serial console, ensure that boot diagnostics is enabled for this VM. (シリアル コンソールを使用するには、この VM のブート診断が有効になっていることを確認してください。) | VM の[ブート診断](boot-diagnostics.md)が有効になっていることを確認します。
The VM is in a stopped deallocated state. (VM は停止済み (割り当て解除) 状態です。) Start the VM and retry the serial console connection. (VM を起動し、シリアル コンソール接続を再試行してください。) | シリアル コンソールにアクセスするには、仮想マシンが起動済み状態である必要があります。
You do not have the required permissions to use this VM serial console.\(この VM のシリアル コンソールを使用するために必要なアクセス許可がありません。\) Ensure you have at least Virtual Machine Contributor role permissions. (少なくとも仮想マシン共同作成者ロールのアクセス許可があることを確認してください。)| シリアル コンソールにアクセスするには、特定のアクセス許可が必要です。 詳細については、「[前提条件](#prerequisites)」を参照してください。
Unable to determine the resource group for the boot diagnostics storage account *&lt;STORAGEACCOUNTNAME&gt;*. (ブート診断ストレージ アカウント STORAGEACCOUNTNAME のリソース グループを特定できません。) Verify that boot diagnostics is enabled for this VM and you have access to this storage account. (この VM のブート診断が有効になっており、このストレージ アカウントにアクセスできることを確認してください。) | シリアル コンソールにアクセスするには、特定のアクセス許可が必要です。 詳細については、「[前提条件](#prerequisites)」を参照してください。
この VM のブート診断ストレージ アカウントにアクセスする際に、"許可されていません" という応答が発生しました。 | ブート診断にアカウントのファイアウォールがないことを確認してください。 シリアル コンソールが機能するには、アクセス可能なブート診断ストレージ アカウントが必要です。
Web ソケットが閉じているか、開けませんでした。 | 場合によっては、`*.console.azure.com` をホワイトリストに登録する必要があります。 より詳しいものの時間もかかるアプローチとしては、[Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)をホワイトリストに追加するというものがありますが、この IP 範囲はかなり規則的に変化します。
Windows VM に接続したときに、正常性情報だけが表示される| このエラーは、Windows イメージ用に Special Administrative Console が有効になっていない場合に発生します。 Windows VM で SAC を手動で有効にする方法については、「[カスタム イメージまたは古いイメージでシリアル コンソールを有効にする](#enable-the-serial-console-in-custom-or-older-images)」を参照してください。 詳細については、[Windows 正常性シグナル](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)に関するページをご覧ください。

## <a name="known-issues"></a>既知の問題
Microsoft は、シリアル コンソールには問題がいくつかあることを認識しています。 そのような問題と軽減手順を以下に示します。

問題                             |   対応策
:---------------------------------|:--------------------------------------------|
接続バナーの後に **Enter** キーを押しても、サインイン プロンプトが表示されない。 | 詳細については、[Enter キーを押しても何も実行されない](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)問題に関するページを参照してください。 このエラーは、Windows がシリアル ポートに正常に接続できない原因となるカスタム VM、堅牢化されたアプライアンス、ブート構成を実行している場合に発生する可能性があります。 このエラーは Windows 10 クライアント VM を実行している場合にも発生します。Windows Server VM のみが EMS を有効にするように構成されているからです。
カーネル デバッグを有効にすると、SAC プロンプトで入力できない。 | VM に RDP で接続して、管理者特権でのコマンド プロンプトから `bcdedit /debug {current} off` を実行します。 RDP で接続できない場合は、代わりに OS ディスクを別の Azure VM に接続して、データ ディスクとしてアタッチされている間に `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off` を実行して変更し、ディスクを戻すこともできます。
元のコンテンツに反復する文字が含まれる場合、SAC の PowerShell に貼り付けると 3 文字目が生成される。 | 回避策は、`Remove-Module PSReadLine` を実行して、現在のセッションから PSReadLine モジュールをアンロードすることです。 このアクションは、モジュールを削除またはアンインストールしません。
一部のキーボード入力で、不適切な SAC 出力が生成される (例: **[A**、**[3~**)。 | [VT100](https://aka.ms/vtsequences) エスケープ シーケンスは SAC プロンプトでサポートされていません。
長い文字列を貼り付けると機能しない。 | シリアル コンソールでは、シリアル ポートの帯域幅に対する過負荷を防止するために、ターミナルに貼り付けられる文字列の長さが 2048 文字に制限されます。
シリアル コンソールは、ストレージ アカウントのファイアウォールと連動しません。 | シリアル コンソールは、ブート診断ストレージ アカウントで有効になっているストレージ アカウント ファイアウォールと設計上、連動できません。


## <a name="frequently-asked-questions"></a>よく寄せられる質問

**Q.フィードバックを送信するにはどうすればよいですか?**

A. GitHub の問題を作成したフィードバックの提供 https://aka.ms/serialconsolefeedbackします。 (あまりお勧めしませんが) azserialhelp@microsoft.com、または https://feedback.azure.com の仮想マシン カテゴリでフィードバックをお送りいただくこともできます。

**Q.シリアル コンソールでは、コピー/貼り付けが可能ですか。**

A. はい。 **Ctrl**+**Shift**+**C** キーでコピーし、**Ctrl**+**Shift**+**V** キーでターミナルに貼り付けます。

**Q.私のサブスクリプションのシリアル コンソールは誰が有効にしたり、無効にしたりできますか。**

A. あるサブスクリプション レベルでシリアル コンソールを有効または無効にするには、そのサブスクリプションの書き込み権限が必要です。 書き込み権限が与えられているロールには、管理者ロールと所有者ロールが含まれます。 カスタム ロールにも、書き込み権限が与えることができます。

**Q.私の VM のシリアル コンソールには誰がアクセスできますか。**

A. VM のシリアル コンソールにアクセスするには、VM に対して仮想マシン共同作成者以上のロールが必要です。

**Q.シリアル コンソールに何も表示されません。どうすればよいでしょうか。**

A. シリアル コンソールのアクセスに関して、おそらく、イメージが間違って構成されています。 シリアル コンソールを有効にするようにイメージを構成する方法については、「[カスタム イメージまたは古いイメージでシリアル コンソールを有効にする](#enable-the-serial-console-in-custom-or-older-images)」を参照してください。

**Q.シリアル コンソールは仮想マシン スケール セットに利用できますか。**

A. 現時点では、仮想マシン スケール セット インスタンスのシリアル コンソールにはアクセスできません。

## <a name="next-steps"></a>次の手順
* Windows SAC で使用できる CMD および PowerShell コマンドの詳細なガイドについては、「[Windows コマンド -CMD と PowerShell](serial-console-cmd-ps-commands.md)」を参照してください。
* シリアル コンソールは、[Linux](serial-console-linux.md) VM でも使用できます。
* ブート診断の詳細については、[こちら](boot-diagnostics.md)を参照してください。
