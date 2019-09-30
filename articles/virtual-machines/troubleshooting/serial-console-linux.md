---
title: Linux 用 Azure シリアル コンソール | Microsoft Docs
description: Azure Virtual Machines および仮想マシン スケール セット用の双方向シリアル コンソール。
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: f6e08f113e29b44e4ec94d14624d62c1c3d48d45
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70124464"
---
# <a name="azure-serial-console-for-linux"></a>Linux 用 Azure シリアル コンソール

Azure portal のシリアル コンソールでは、Linux 仮想マシン (VM) および仮想マシン スケール セット インスタンス用のテキスト ベースのコンソールへのアクセスが提供されます。 このシリアル接続は、ネットワークやオペレーティング システムの状態には関係なく、VM または仮想マシン スケール セット インスタンスの ttys0 シリアル ポートに接続してそのポートへのアクセスを提供します。 このシリアル コンソールは Azure portal を使用してのみアクセスでき、VM または仮想マシン スケール セットへの共同作成者以上のアクセス ロールを持つユーザーに対してのみ許可されます。

シリアル コンソールは、VM と仮想マシン スケール セット インスタンスに対して同じ方法で動作します。 このドキュメントでは、特に記載のない限り、VM という記述にはすべて仮想マシン スケール セット インスタンスが暗黙的に含まれます。

Windows 用シリアル コンソールのドキュメントについては、[Windows 用シリアル コンソール](../windows/serial-console.md)に関する記事を参照してください。

> [!NOTE]
> シリアル コンソールは、グローバル Azure リージョンで一般公開されています。 Azure Government や Azure China Cloud では利用できません。


## <a name="prerequisites"></a>前提条件

- VM または仮想マシン スケール セット インスタンスは、リソース管理デプロイ モデルを使用する必要があります。 クラシック デプロイはサポートされていません。

- シリアル コンソールを使用するアカウントには、VM の[仮想マシン共同作成者ロール](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)および[ブート診断](boot-diagnostics.md)ストレージ アカウントが必要です

- VM または仮想マシン スケール セット インスタンスには、パスワード ベースのユーザーが必要です。 このアカウントは、VM アクセス拡張機能の[パスワードのリセット](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password)機能を使用して作成することができます。 **[サポート + トラブルシューティング]** セクションの **[パスワードのリセット]** を選択します。

- VM または仮想マシン スケール セット インスタンスでは、[ブート診断](boot-diagnostics.md)が有効になっている必要があります。

    ![ブート診断の設定](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Linux ディストリビューションに固有の設定については、「[シリアル コンソールの Linux ディストリビューションの可用性](#serial-console-linux-distribution-availability)」を参照してください。

- VM または仮想マシン スケール セット インスタンスは、`ttys0` でシリアル出力用に構成されている必要があります。 これが Azure イメージの既定値ですが、カスタム イメージではこれを再確認してください。 詳細は[以下](#custom-linux-images)のとおりです。


> [!NOTE]
> シリアル コンソールを使うには、パスワードが構成されたローカル ユーザーが必要です。 SSH 公開キーだけが構成された VM または仮想マシン スケール セットは、シリアル コンソールにサインインできません。 パスワードを持つローカル ユーザーを作成するには、[VM アクセス拡張機能](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension)に関する記事を参照して (Azure portal で **[パスワードのリセット]** を選択して表示することができます)、パスワードを持つローカル ユーザーを作成します。
> また、アカウントの管理者パスワードを [GRUB を使用してシングル ユーザー モードで起動](./serial-console-grub-single-user-mode.md)してリセットすることもできます。

## <a name="serial-console-linux-distribution-availability"></a>シリアル コンソールの Linux ディストリビューションの可用性
シリアル コンソールを正常に機能させるには、シリアル ポートでコンソール メッセージを読み書きするように、ゲスト オペレーティング システムを構成する必要があります。 ほとんどの [Azure での動作保証済み Linux ディストリビューション](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)は、既定で構成済みのシリアル コンソールを備えています。 Azure portal の **[サポート + トラブルシューティング]** セクションの **[シリアル コンソール]** を選択すると、シリアル コンソールにアクセスできます。

> [!NOTE]
> シリアル コンソールに何も表示されない場合、VM でそのブート診断が有効になっていることを確認してください。 多くの場合、**Enter** キーを押すと、シリアル コンソールに何も表示されない問題が解決します。

ディストリビューション      | シリアル コンソール アクセス
:-----------|:---------------------
Red Hat Enterprise Linux    | シリアル コンソール アクセスが既定で有効になっています。
CentOS      | シリアル コンソール アクセスが既定で有効になっています。
Ubuntu      | シリアル コンソール アクセスが既定で有効になっています。
CoreOS      | シリアル コンソール アクセスが既定で有効になっています。
SUSE        | Azure で利用できる新しい SLES イメージは、シリアル コンソール アクセスが既定で有効になっています。 前のバージョン (10 以前) の SLES を Azure で使っている場合は、[サポート技術情報の記事](https://www.novell.com/support/kb/doc.php?id=3456486)を参照してシリアル コンソールを有効にします。
Oracle Linux        | シリアル コンソール アクセスが既定で有効になっています。

### <a name="custom-linux-images"></a>カスタム Linux イメージ
カスタム Linux VM イメージのシリアル コンソールを有効にするには、 */etc/inittab* ファイルでコンソール アクセスを有効にして、`ttyS0` でターミナルを実行します。 (例: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`)。 また、ttyS0 上で getty の生成が必要になる場合もあります。 これは `systemctl start serial-getty@ttyS0.service` を使用して行えます。

さらに、ttys0 をシリアル出力の宛先として追加することができます。 シリアル コンソールと連動するようにカスタム イメージを構成する方法の詳細については、「[Azure 上での Linux VHD の作成とアップロード](https://aka.ms/createuploadvhd#general-linux-system-requirements)」で一般的なシステム要件を参照してください。

カスタム カーネルを構築する場合は、`CONFIG_SERIAL_8250=y` と `CONFIG_MAGIC_SYSRQ_SERIAL=y` のカーネル フラグを有効にすることを考慮してください。 構成ファイルは、通常は */boot/* パスにあります。

## <a name="common-scenarios-for-accessing-the-serial-console"></a>シリアル コンソールにアクセスする一般的なシナリオ

シナリオ          | シリアル コンソールでのアクション
:------------------|:-----------------------------------------
壊れた *FSTAB* ファイル | **Enter** キーを押して続行し、テキスト エディターを使って *FSTAB* ファイルを修正します。 そのためには、シングル ユーザー モードにすることが必要な場合があります。 詳しくは、[fstab の問題の修正方法](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors)に関するページのシリアル コンソールのセクション、および「[シリアル コンソールを使用して GRUB とシングル ユーザー モードにアクセスする](serial-console-grub-single-user-mode.md)」をご覧ください。
不適切なファイアウォール規則 |  SSH 接続をブロックするように iptables を構成した場合は、シリアル コンソールを使用して、SSH を必要とせずに VM と対話できます。 詳しくは、[iptables の man ページ](https://linux.die.net/man/8/iptables)をご覧ください。<br>同様に、firewalld によって SSH アクセスがブロックされている場合は、シリアル コンソールを通じて VM にアクセスし、firewalld を再構成できます。 詳しくは、[firewalld のドキュメント](https://firewalld.org/documentation/)をご覧ください。
ファイル システムの破損/チェック | 破損したファイル システムのシリアル コンソールを使用したトラブルシューティングについて詳しくは、「[Azure の Linux VM は、ファイル システム エラーが発生したため開始できません](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck)」のシリアル コンソールのセクションをご覧ください。
SSH 構成の問題 | シリアル コンソールにアクセスし、設定を変更します。 シリアル コンソールは、VM にネットワーク接続がなくても機能するので、VM の SSH 構成に関係なく使用できます。 トラブルシューティング ガイドは、「[Azure Linux VM に対する SSH 接続の失敗、エラー、拒否のトラブルシューティング](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection)」にあります。 詳しくは、「[Azure での Linux VM 接続問題に関する SSH の詳細なトラブルシューティングの手順](./detailed-troubleshoot-ssh-connection.md)」をご覧ください
ブートローダーの操作 | シリアル コンソール ブレードから VM を再起動して、Linux VM 上の GRUB にアクセスします。 詳細および distro 固有の情報については、「[シリアル コンソールを使用して GRUB とシングル ユーザー モードにアクセスする](serial-console-grub-single-user-mode.md)」をご覧ください。

## <a name="disable-the-serial-console"></a>シリアル コンソールを無効にする

既定では、すべてのサブスクリプションは、シリアル コンソールのアクセスが有効になっています。 サブスクリプション レベルまたは VM/仮想マシン スケール セット レベルのいずれかで、シリアル コンソールを無効にすることができます。 詳細な手順については、「[Azure シリアル コンソールの有効化と無効化](./serial-console-enable-disable.md)」を参照してください。

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

### <a name="use-serial-console-with-a-screen-reader"></a>スクリーン リーダーでシリアル コンソールを使用する
シリアル コンソールには、スクリーン リーダーのサポートが組み込まれています。 スクリーン リーダーを有効にしてナビゲートすると、現在選択されているボタンの代替テキストをスクリーン リーダーで読み上げることができます。

## <a name="known-issues"></a>既知の問題
Microsoft は、シリアル コンソールには問題がいくつかあることを認識しています。 そのような問題と軽減手順を以下に示します。 これらの問題と軽減策は、VM と仮想マシン スケール セット インスタンスの両方に適用されます。

問題                           |   対応策
:---------------------------------|:--------------------------------------------|
接続バナーの後に **Enter** キーを押しても、サインイン プロンプトが表示されない。 | 詳細については、[Enter キーを押しても何も実行されない](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)問題に関するページを参照してください。 この問題は、Linux がシリアル ポートに接続できない原因となるカスタム VM、堅牢化されたアプライアンス、または GRUB 構成を実行している場合に発生する可能性があります。
シリアル コンソールのテキストが画面サイズいっぱいに表示されない (テキスト エディターの使用後に多く発生)。 | シリアル コンソールは、ウィンドウ サイズに関するネゴシエーション ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)) をサポートしていません。つまり、画面サイズを更新するための SIGWINCH 信号が送信されないため、VM はターミナルのサイズを認識していません。 xterm または同様のユーティリティをインストールして `resize` コマンドを使用できるようにしてから、`resize` を実行します。
長い文字列を貼り付けると機能しない。 | シリアル コンソールでは、シリアル ポートの帯域幅に対する過負荷を防止するために、ターミナルに貼り付けられる文字列の長さが 2048 文字に制限されます。
シリアル コンソールは、ストレージ アカウントのファイアウォールと連動しません。 | シリアル コンソールは、ブート診断ストレージ アカウントで有効になっているストレージ アカウント ファイアウォールと設計上、連動できません。
シリアル コンソールが、階層型名前空間を持つ Azure Data Lake Storage Gen2 を使用するストレージ アカウントで機能しません。 | これは階層型名前空間の既知の問題です。 緩和するには、Azure Data Lake Storage Gen2 を使用して VM のブート診断ストレージ アカウントを作成しないようにします。 このオプションは、ストレージ アカウントの作成時にのみ設定できます。 この問題を緩和するには、Azure Data Lake Storage Gen2 を有効にせずに別個のブート診断ストレージ アカウントを作成しなければならない場合があります。
SLES BYOS イメージでキーボード入力が不安定。 キーボード入力は散発的にしか認識されません。 | これは Plymouth パッケージの問題です。 スプラッシュ スクリーンは不要なので、Plymouth を Azure で実行しないでください。Plymouth は、シリアル コンソールを使用するプラットフォームの機能の妨げになります。 `sudo zypper remove plymouth` を使用して Plymouth を削除してから再起動します。 または、行の末尾に `plymouth.enable=0` を付加して GRUB 構成のカーネル行を修正します。 これを行うには、[起動時に起動エントリを編集する](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles)か、`/etc/default/grub` の GRUB_CMDLINE_LINUX 行を編集し、`grub2-mkconfig -o /boot/grub2/grub.cfg` を使用して GRUB をリビルドしてから再起動します。


## <a name="frequently-asked-questions"></a>よく寄せられる質問

**Q.フィードバックを送信するにはどうすればよいですか?**

A. GitHub の問題を作成したフィードバックの提供 https://aka.ms/serialconsolefeedback します。 (あまりお勧めしませんが) azserialhelp@microsoft.com、または https://feedback.azure.com の仮想マシン カテゴリでフィードバックをお送りいただくこともできます。

**Q.シリアル コンソールでは、コピー/貼り付けが可能ですか。**

A. はい。 **Ctrl**+**Shift**+**C** キーでコピーし、**Ctrl**+**Shift**+**V** キーでターミナルに貼り付けます。

**Q.SSH 接続の代わりにシリアル コンソールを使用できますか?**

A. この使用は技術的には可能と思われますが、シリアル コンソールは主に、SSH での接続ができない状況でのトラブルシューティング ツールとしての使用が意図されています。 次の理由から、SSH の代わりにシリアル コンソールを使うことはお勧めしません。

- シリアル コンソールには、SSH ほどの帯域幅がありません。 テキストのみの接続であるため、GUI を多く使った対話式操作は難しくなります。
- 現在、シリアル コンソール アクセスは、ユーザー名とパスワードを使用することによってのみ可能です。 ユーザー名/パスワードの組み合わせよりも SSH キーの方がはるかに安全なため、サインイン セキュリティの観点から、シリアル コンソールよりも SSH を使うことをお勧めします。

**Q.私のサブスクリプションのシリアル コンソールは誰が有効にしたり、無効にしたりできますか。**

A. あるサブスクリプション レベルでシリアル コンソールを有効または無効にするには、そのサブスクリプションの書き込み権限が必要です。 書き込み権限が与えられているロールには、管理者ロールと所有者ロールが含まれます。 カスタム ロールにも、書き込み権限が与えることができます。

**Q.私の VM/仮想マシン スケール セットのシリアル コンソールには誰がアクセスできますか。**

A. シリアル コンソールにアクセスするには、VM または仮想マシン スケール セットに対して仮想マシン共同作成者以上のロールが必要です。

**Q.シリアル コンソールに何も表示されません。どうすればよいでしょうか。**

A. シリアル コンソールのアクセスに関して、おそらく、イメージが間違って構成されています。 シリアル コンソールを有効にするようにイメージを構成する方法については、「[シリアル コンソールの Linux ディストリビューションの可用性](#serial-console-linux-distribution-availability)」を参照してください。

**Q.シリアル コンソールは仮想マシン スケール セットに利用できますか。**

A. はい。 「[仮想マシン スケール セット用のシリアル コンソール](serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)」を参照してください

**Q.SSH キー認証のみを使用して VM または仮想マシン スケール セットを設定しましたが、シリアル コンソールを使用して VM/仮想マシン スケール セット インスタンスに接続できますか。**

A. はい。 シリアル コンソールは SSH キーを必要としないので、必要な操作はユーザー名とパスワードの組み合わせを設定することのみです。 Azure portal で **[パスワードのリセット]** を選択し、これらの資格情報を使用してシリアル コンソールにサインインしてください。

## <a name="next-steps"></a>次の手順
* シリアル コンソール を使用して [GRUB とシングル ユーザー モードにアクセス](serial-console-grub-single-user-mode.md)します。
* [NMI および SysRq 呼び出し](serial-console-nmi-sysrq.md)にシリアル コンソールを使用します。
* シリアル コンソールを使用して[さまざまなディストリビューションで GRUB を有効にする](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)方法について説明します。
* シリアル コンソールは、[Windows VM](../windows/serial-console.md) でも使用可能です。
* ブート診断の詳細については、[こちら](boot-diagnostics.md)を参照してください。

