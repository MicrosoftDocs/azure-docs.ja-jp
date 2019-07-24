---
title: Linux の Azure 仮想マシン シリアル コンソール | Microsoft Docs
description: Azure 仮想マシンの双方向シリアル コンソール。
services: virtual-machines-linux
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: harijay
ms.openlocfilehash: f407d87249c44ad3a4773b2cd8fc85ee09506ceb
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445654"
---
# <a name="virtual-machine-serial-console-for-linux"></a>Linux の仮想マシンのシリアル コンソール

Azure portal の仮想マシン (VM) のシリアル コンソールでは、Linux 仮想マシンのテキスト ベースのコンソールにアクセスできます。 このシリアル接続では、仮想マシンの COM1 シリアル ポートに接続し、仮想マシンのネットワークまたはオペレーティング システムの状態に左右されずに仮想マシンにアクセスできます。 仮想マシンのシリアル コンソールへのアクセスは、Azure portal を使用することでのみ可能です。 仮想マシンに対する仮想マシン共同作成者以上のアクセス ロールを持つユーザーだけが使用できます。

Windows VM のシリアル コンソールのドキュメントについては、[Windows 用の仮想マシンのシリアル コンソール](../windows/serial-console.md)に関するページを参照してください。

> [!NOTE]
> 仮想マシンのシリアル コンソールは、グローバル Azure リージョンで一般公開されています。 Azure Government や Azure China Cloud では利用できません。


## <a name="prerequisites"></a>前提条件

- シリアル コンソールにアクセスする VM は、リソース管理デプロイ モデルを使用している必要があります。 クラシック デプロイはサポートされていません。

- シリアル コンソールにアクセスする VM では、[ブート診断](boot-diagnostics.md)が有効になっている必要があります。

    ![ブート診断の設定](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- シリアル コンソールを使用するアカウントには、VM の[仮想マシン共同作成者ロール](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)と[ブート診断](boot-diagnostics.md)のストレージ アカウントが必要です。

- シリアル コンソールにアクセスする VM には、パスワードベースのアカウントが必要です。 このアカウントは、VM アクセス拡張機能の[パスワードのリセット](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password)機能を使用して作成することができます。 **[サポート + トラブルシューティング]** セクションの **[パスワードのリセット]** を選択します。

- Linux ディストリビューションに固有の設定については、「[シリアル コンソールの Linux ディストリビューションの可用性](#serial-console-linux-distribution-availability)」を参照してください。



## <a name="get-started-with-the-serial-console"></a>シリアル コンソールの概要
仮想マシンのシリアル コンソールには、Azure portal からのみアクセスできます。

  1. [Azure Portal](https://portal.azure.com)を開きます。

  1. 左側のメニューで **[Virtual Machines]** を選択します。

  1. 一覧で VM を選びます。 VM の概要ページが開きます。

  1. 下へスクロールして **[サポート + トラブルシューティング]** セクションを表示し、**[シリアル コンソール]** を選択します。 シリアル コンソールで新しいウィンドウが開き、接続が開始されます。

     ![Linux シリアル コンソール ウィンドウ](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE]
> シリアル コンソールを使うには、パスワードが構成されたローカル ユーザーが必要です。 SSH 公開キーだけが構成された VM は、シリアル コンソールにサインインできません。 パスワードを持つローカル ユーザーを作成するには、[VM アクセス拡張機能](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension)に関する記事を参照して (Azure portal で **[パスワードのリセット]** を選択して表示することができます)、パスワードを持つローカル ユーザーを作成します。
> また、アカウントの管理者パスワードを [GRUB を使用してシングル ユーザー モードで起動](./serial-console-grub-single-user-mode.md)してリセットすることもできます。

## <a name="serial-console-linux-distribution-availability"></a>シリアル コンソールの Linux ディストリビューションの可用性
シリアル コンソールを正常に機能させるには、シリアル ポートでコンソール メッセージを読み書きするように、ゲスト オペレーティング システムを構成する必要があります。 ほとんどの [Azure での動作保証済み Linux ディストリビューション](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)は、既定で構成済みのシリアル コンソールを備えています。 Azure portal の **[サポート + トラブルシューティング]** セクションの **[シリアル コンソール]** を選択すると、シリアル コンソールにアクセスできます。

ディストリビューション      | シリアル コンソール アクセス
:-----------|:---------------------
Red Hat Enterprise Linux    | シリアル コンソール アクセスが既定で有効になっています。
CentOS      | シリアル コンソール アクセスが既定で有効になっています。
Ubuntu      | シリアル コンソール アクセスが既定で有効になっています。
CoreOS      | シリアル コンソール アクセスが既定で有効になっています。
SUSE        | Azure で利用できる新しい SLES イメージは、シリアル コンソール アクセスが既定で有効になっています。 前のバージョン (10 以前) の SLES を Azure で使っている場合は、[サポート技術情報の記事](https://www.novell.com/support/kb/doc.php?id=3456486)を参照してシリアル コンソールを有効にします。
Oracle Linux        | シリアル コンソール アクセスが既定で有効になっています。
カスタム Linux イメージ     | カスタム Linux VM イメージのシリアル コンソールを有効にするには、*/etc/inittab* ファイルでコンソール アクセスを有効にして、`ttyS0` でターミナルを実行します。 (例: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`)。 カスタム イメージを適切に作成するための詳細については、[Azure 上での Linux VHD の作成とアップロード](https://aka.ms/createuploadvhd)に関する記事を参照してください。 カスタム カーネルを構築する場合は、`CONFIG_SERIAL_8250=y` と `CONFIG_MAGIC_SYSRQ_SERIAL=y` のカーネル フラグを有効にすることを考慮してください。 構成ファイルは、通常は */boot/* パスにあります。

> [!NOTE]
> シリアル コンソールに何も表示されない場合、VM でそのブート診断が有効になっていることを確認してください。

## <a name="common-scenarios-for-accessing-the-serial-console"></a>シリアル コンソールにアクセスする一般的なシナリオ

シナリオ          | シリアル コンソールでのアクション
:------------------|:-----------------------------------------
壊れた *FSTAB* ファイル | **Enter** キーを押して続行し、テキスト エディターを使って *FSTAB* ファイルを修正します。 そのためには、シングル ユーザー モードにすることが必要な場合があります。 詳しくは、[fstab の問題の修正方法](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors)に関するページのシリアル コンソールのセクション、および「[シリアル コンソールを使用して GRUB とシングル ユーザー モードにアクセスする](serial-console-grub-single-user-mode.md)」をご覧ください。
不適切なファイアウォール規則 |  SSH 接続をブロックするように iptables を構成した場合は、シリアル コンソールを使用して、SSH を必要とせずに VM と対話できます。 詳しくは、[iptables の man ページ](https://linux.die.net/man/8/iptables)をご覧ください。 同様に、firewalld によって SSH アクセスがブロックされている場合は、シリアル コンソールを通じて VM にアクセスし、firewalld を再構成できます。 詳しくは、[firewalld のドキュメント](https://firewalld.org/documentation/)をご覧ください。
ファイル システムの破損/チェック | 破損したファイル システムのシリアル コンソールを使用したトラブルシューティングについて詳しくは、「[Azure の Linux VM は、ファイル システム エラーが発生したため開始できません](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck)」のシリアル コンソールのセクションをご覧ください。
SSH 構成の問題 | シリアル コンソールにアクセスし、設定を変更します。 シリアル コンソールは、VM にネットワーク接続がなくても機能するので、VM の SSH 構成に関係なく使用できます。 トラブルシューティング ガイドは、「[Azure Linux VM に対する SSH 接続の失敗、エラー、拒否のトラブルシューティング](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection)」にあります。 詳しくは、「[Azure での Linux VM 接続問題に関する SSH の詳細なトラブルシューティングの手順](./detailed-troubleshoot-ssh-connection.md)」をご覧ください
ブートローダーの操作 | シリアル コンソール ブレードから VM を再起動して、Linux VM 上の GRUB にアクセスします。 詳細および distro 固有の情報については、「[シリアル コンソールを使用して GRUB とシングル ユーザー モードにアクセスする](serial-console-grub-single-user-mode.md)」をご覧ください。

## <a name="disable-the-serial-console"></a>シリアル コンソールを無効にする
既定では、すべてのサブスクリプションは、すべての VM に対してシリアル コンソールのアクセスが有効になっています。 VM レベルまたはサブスクリプション レベルのいずれかで、シリアル コンソールを無効にすることができます。

> [!NOTE]
> あるサブスクリプションに対してシリアル コンソールを有効または無効にするには、そのサブスクリプションの書き込み権限が必要です。 これらの権限には、管理者ロールまたは所有者ロールが含まれます。 カスタム ロールにも、書き込み権限が与えることができます。

### <a name="subscription-level-disable"></a>サブスクリプション レベルの無効化
サブスクリプション全体のシリアル コンソールは、[Disable Console REST API 呼び出し](/rest/api/serialconsole/console/disableconsole)で無効にできます。 API ドキュメントのページで使用できる**使ってみる**機能を利用して、サブスクリプションのシリアル コンソールを有効または無効にすることができます。 **subscriptionId** のサブスクリプション ID を入力し、**default** に **default** と入力して、**[実行]** を選択します。 Azure CLI コマンドはまだ利用できません。

![REST API Try It](./media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

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
> これは、切断されたユーザーはログアウトされないことを意味します。(SIGHUP または同様のメカニズムを使用して) 切断時に強制的にログアウトする機能は、まだロードマップにあります。 Windows では Special Administrative Console (SAC) で自動タイムアウトが有効になっていますが、Linux ではターミナルのタイムアウト設定を構成できます。 これを行うには、コンソールへのサインインに使用しているユーザーの *.bash_profile* または *.profile* ファイルに `export TMOUT=600` を追加します。 この設定により、10 分後にセッションがタイムアウトされます。

## <a name="accessibility"></a>アクセシビリティ
アクセシビリティは、Azure シリアル コンソールの重点事項です。 そのために、シリアル コンソールに完全にアクセスできるようにしました。

### <a name="keyboard-navigation"></a>キーボード ナビゲーション
Azure portal からシリアル コンソール インターフェイスでナビゲートするには、キーボードの **Tab** キーを使用します。 現在の場所が画面上で強調表示されます。 シリアル コンソール ウィンドウのフォーカスを解除するには、キーボードの **Ctrl**+**F6** キーを押します。

### <a name="use-the-serial-console-with-a-screen-reader"></a>スクリーン リーダーでシリアル コンソールを使用する
シリアル コンソールには、スクリーン リーダーのサポートが組み込まれています。 スクリーン リーダーを有効にしてナビゲートすると、現在選択されているボタンの代替テキストをスクリーン リーダーで読み上げることができます。

## <a name="errors"></a>Errors
ほとんどのエラーは一時的なものであるため、多くの場合、接続の再試行によって解決できます。 次の表にエラーと対応策を示します。

Error                            |   対応策
:---------------------------------|:--------------------------------------------|
Unable to retrieve boot diagnostics settings for *&lt;VMNAME&gt;*. (VMNAME のブート診断設定を取得できません。) To use the serial console, ensure that boot diagnostics is enabled for this VM. (シリアル コンソールを使用するには、この VM のブート診断が有効になっていることを確認してください。) | VM の[ブート診断](boot-diagnostics.md)が有効になっていることを確認します。
The VM is in a stopped deallocated state. (VM は停止済み (割り当て解除) 状態です。) Start the VM and retry the serial console connection. (VM を起動し、シリアル コンソール接続を再試行してください。) | シリアル コンソールにアクセスするには、VM が起動済み状態である必要があります。
You do not have the required permissions to use this VM with the serial console. (シリアル コンソールでこの VM を使用するために必要なアクセス許可がありません。) Ensure you have at least Virtual Machine Contributor role permissions. (少なくとも仮想マシン共同作成者ロールのアクセス許可があることを確認してください。)| シリアル コンソールにアクセスするには、特定のアクセス許可が必要です。 詳細については、「[前提条件](#prerequisites)」を参照してください。
Unable to determine the resource group for the boot diagnostics storage account *&lt;STORAGEACCOUNTNAME&gt;*. (ブート診断ストレージ アカウント STORAGEACCOUNTNAME のリソース グループを特定できません。) Verify that boot diagnostics is enabled for this VM and you have access to this storage account. (この VM のブート診断が有効になっており、このストレージ アカウントにアクセスできることを確認してください。) | シリアル コンソールにアクセスするには、特定のアクセス許可が必要です。 詳細については、「[前提条件](#prerequisites)」を参照してください。
Web ソケットが閉じているか、開けませんでした。 | 場合によっては、`*.console.azure.com` をホワイトリストに登録する必要があります。 より詳しいものの時間もかかるアプローチとしては、[Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)をホワイトリストに追加するというものがありますが、この IP 範囲はかなり規則的に変化します。
この VM のブート診断ストレージ アカウントにアクセスする際に、"許可されていません" という応答が発生しました。 | ブート診断にアカウントのファイアウォールがないことを確認してください。 シリアル コンソールが機能するには、アクセス可能なブート診断ストレージ アカウントが必要です。

## <a name="known-issues"></a>既知の問題
Microsoft は、シリアル コンソールには問題がいくつかあることを認識しています。 そのような問題と軽減手順を以下に示します。

問題                           |   対応策
:---------------------------------|:--------------------------------------------|
接続バナーの後に **Enter** キーを押しても、サインイン プロンプトが表示されない。 | 詳細については、[Enter キーを押しても何も実行されない](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)問題に関するページを参照してください。 この問題は、Linux がシリアル ポートに正常に接続できない原因となるカスタム VM、堅牢化されたアプライアンス、または GRUB 構成を実行している場合に発生する可能性があります。
シリアル コンソールのテキストが画面サイズいっぱいに表示されない (テキスト エディターの使用後に多く発生)。 | シリアル コンソールは、ウィンドウ サイズに関するネゴシエーション ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)) をサポートしていません。つまり、画面サイズを更新するための SIGWINCH 信号が送信されないため、VM はターミナルのサイズを認識していません。 xterm または同様のユーティリティをインストールして `resize` コマンドを使用できるようにしてから、`resize` を実行します。
長い文字列を貼り付けると機能しない。 | シリアル コンソールでは、シリアル ポートの帯域幅に対する過負荷を防止するために、ターミナルに貼り付けられる文字列の長さが 2048 文字に制限されます。
シリアル コンソールは、ストレージ アカウントのファイアウォールと連動しません。 | シリアル コンソールは、ブート診断ストレージ アカウントで有効になっているストレージ アカウント ファイアウォールと設計上、連動できません。


## <a name="frequently-asked-questions"></a>よく寄せられる質問

**Q.フィードバックを送信するにはどうすればよいですか?**

A. GitHub の問題を作成したフィードバックの提供 https://aka.ms/serialconsolefeedbackします。 (あまりお勧めしませんが) azserialhelp@microsoft.com、または https://feedback.azure.com の仮想マシン カテゴリでフィードバックをお送りいただくこともできます。

**Q.シリアル コンソールでは、コピー/貼り付けが可能ですか。**

A. はい。 **Ctrl**+**Shift**+**C** キーでコピーし、**Ctrl**+**Shift**+**V** キーでターミナルに貼り付けます。

**Q.SSH 接続の代わりにシリアル コンソールを使用できますか?**

A. この使用は技術的には可能と思われますが、シリアル コンソールは主に、SSH での接続ができない状況でのトラブルシューティング ツールとしての使用が意図されています。 次の理由から、SSH の代わりにシリアル コンソールを使うことはお勧めしません。

- シリアル コンソールには、SSH ほどの帯域幅がありません。 テキストのみの接続であるため、GUI を多く使った対話式操作は難しくなります。
- 現在、シリアル コンソール アクセスは、ユーザー名とパスワードを使用することによってのみ可能です。 ユーザー名/パスワードの組み合わせよりも SSH キーの方がはるかに安全なため、サインイン セキュリティの観点から、シリアル コンソールよりも SSH を使うことをお勧めします。

**Q.私のサブスクリプションのシリアル コンソールは誰が有効にしたり、無効にしたりできますか。**

A. あるサブスクリプション レベルでシリアル コンソールを有効または無効にするには、そのサブスクリプションの書き込み権限が必要です。 書き込み権限が与えられているロールには、管理者ロールと所有者ロールが含まれます。 カスタム ロールにも、書き込み権限が与えることができます。

**Q.私の VM のシリアル コンソールには誰がアクセスできますか。**

A. VM のシリアル コンソールにアクセスするには、VM に対して仮想マシン共同作成者以上のロールが必要です。

**Q.シリアル コンソールに何も表示されません。どうすればよいでしょうか。**

A. シリアル コンソールのアクセスに関して、おそらく、イメージが間違って構成されています。 シリアル コンソールを有効にするようにイメージを構成する方法については、「[シリアル コンソールの Linux ディストリビューションの可用性](#serial-console-linux-distribution-availability)」を参照してください。

**Q.シリアル コンソールは仮想マシン スケール セットに利用できますか。**

A. 現時点では、仮想マシン スケール セット インスタンスのシリアル コンソールにはアクセスできません。

**Q.SSH キー認証のみを使用して VM をセットアップしましたが、シリアル コンソールを使用して VM に接続できますか。**

A. はい。 シリアル コンソールは SSH キーを必要としないので、必要な操作はユーザー名とパスワードの組み合わせを設定することのみです。 Azure portal で **[パスワードのリセット]** を選択し、これらの資格情報を使用してシリアル コンソールにサインインしてください。

## <a name="next-steps"></a>次の手順
* シリアル コンソール を使用して [GRUB とシングル ユーザー モードにアクセス](serial-console-grub-single-user-mode.md)します。
* [NMI および SysRq 呼び出し](serial-console-nmi-sysrq.md)にシリアル コンソールを使用します。
* シリアル コンソールを使用して[さまざまなディストリビューションで GRUB を有効にする](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)方法について説明します。
* シリアル コンソールは、[Windows VM](../windows/serial-console.md) でも使用可能です。
* ブート診断の詳細については、[こちら](boot-diagnostics.md)を参照してください。

