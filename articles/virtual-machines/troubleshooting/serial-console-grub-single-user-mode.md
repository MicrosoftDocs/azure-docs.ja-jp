---
title: GRUB とシングル ユーザー モード用の Azure シリアル コンソール | Microsoft Docs
description: この記事では、Azure 仮想マシンで GRUB のシリアル コンソールを使用する方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: alsin
ms.openlocfilehash: 06cb3fe5d551ddfc95fcbd37cd9620adebd825c5
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883927"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>シリアル コンソールを使用して GRUB とシングル ユーザー モードにアクセスする
GRand Unified Bootloader (GRUB) は、仮想マシン (VM) を起動したときにおそらく最初に表示されるものです。 GRUB は、オペレーティング システムが起動する前に表示されるため、SSH からはアクセスできません。 GRUB ではさまざまな設定ができ、シングル ユーザー モードで起動するようにブート構成を変更することができます。

シングル ユーザー モードは、最小限の機能を持つ最小限の環境です。 ブートの問題やファイル システムの問題、ネットワークの問題を調査するのに役立ちます。 バックグラウンドで実行されるサービスが少なくなり、実行レベルによっては、ファイル システムが自動的にマウントされないこともあります。

シングル ユーザー モードはまた、サインインに SSH キーのみを受け入れるように VM が構成される状況でも役立ちます。 この場合は、パスワード認証を使用するアカウントを作成するためにシングル ユーザー モードを使用することができます。 

> [!NOTE]
> シリアル コンソール サービスでは、"*共同作成者*" レベル以上のアクセス許可を持つユーザーのみが、VM のシリアル コンソールにアクセスできます。

シングル ユーザー モードを開始するには、VM の起動時に GRUB に入り、GRUB でブート構成を変更します。 次のセクションで、GRUB に入る詳細な手順を説明します。 一般的に、VM が GRUB を表示するように構成されている場合は、VM のシリアル コンソール内の再起動ボタンを使用して VM を再起動し、GRUB を表示できます。

![Linux シリアル コンソールの再起動ボタン](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>GRUB の一般的なアクセス
GRUB にアクセスするには、シリアル コンソール ウィンドウが開いている状態で VM を再起動します。 一部のディストリビューションでは GRUB を表示するのにキーボード入力が必要ですが、それ以外のディストリビューションでは数秒間 GRUB が自動的に表示されるので、ユーザーがキーボード入力を行うと、タイムアウトをキャンセルできます。

シングル ユーザー モードにアクセスできるようにするには、VM 上で GRUB を確実に有効にする必要があります。 ディストリビューションによっては、GRUB を確実に有効にするために設定作業が必要になる場合があります。 ディストリビューション固有の情報については、次のセクションと、[Azure での Linux のサポート](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)に関するページを参照してください。

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>シリアル コンソールで VM を再起動して GRUB にアクセスする
シリアル コンソール内で VM を再起動するには、 **[再起動]** ボタンをポイントし、 **[VM の再起動]** を選択します。 再起動に関する通知がウィンドウの下部に表示されます。

[SysRq](./serial-console-nmi-sysrq.md) が有効になっている場合は、SysRq "b" コマンドを実行して VM を再起動することもできます。 再起動したときに GRUB でどのようなことができるかについては、以降のディストリビューションに固有の手順のセクションを参照してください。

![Linux シリアル コンソールでの再起動](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>シングル ユーザー モードの一般的なアクセス
パスワード認証を使用してアカウントを構成していない場合は、シングル ユーザー モードへの手動アクセスが必要になる可能性があります。 シングル ユーザー モードを手動で開始するには、GRUB 構成を変更します。 これを完了したら、その後の手順については「シングル ユーザー モードを使用してパスワードをリセットまたは追加する」セクションを参照してください。

VM を起動できない場合、ユーザーを自動的にシングル ユーザー モードまたは緊急モードにするディストリビューションが多くあります。 しかしながら、それ以外のディストリビューションでは、ユーザーを自動的にシングル ユーザー モードまたは緊急モードにするにはその前に root パスワードの設定など、追加のセットアップが必要です。

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>シングル ユーザー モードを使用してパスワードをリセットまたは追加する
シングル ユーザー モードになったら、以下の手順に従って sudo 特権を持つ新しいユーザーを追加します。
1. `useradd <username>` を実行してユーザーを追加します。
1. `sudo usermod -a -G sudo <username>` を実行して新しいユーザーに root 特権を付与します。
1. `passwd <username>` を使用して新しいユーザーのパスワードを設定します。 これで、新しいユーザーとしてサインインできます。


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Red Hat Enterprise Linux (RHEL) へのアクセス
RHEL を正常に起動できない場合、自動的にシングル ユーザー モードに切り替わります。 しかしながら、シングル ユーザー モード用に root アクセス権を設定していないと、root パスワードがないため、サインインできません。 回避策はあります (「RHEL のシングル ユーザー モードを手動で開始する」セクションを参照してください)。ただし、最初に root アクセス権を設定することをお勧めします。

### <a name="grub-access-in-rhel"></a>RHEL での GRUB アクセス
RHEL には GRUB が付属しており、すぐに使用できます。 GRUB に入るには、`sudo reboot` を実行して VM を再起動し、任意のキーを押します。 GRUB ウィンドウが表示されるはずです。 表示されない場合は、次の行が確実に GRUB ファイル (`/etc/default/grub`) に存在しているようにします。

**RHEL 8 の場合**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
```

**RHEL 7 の場合**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL_OUTPUT="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 net.ifnames=0"
```

> [!NOTE]
> また、Red Hat には、レスキュー モード、緊急モード、またはデバッグ モードでの起動、および root パスワードのリセットを実行するためのドキュメントが用意されています。 手順については、[ブート中のターミナル メニューの編集](https://aka.ms/rhel7grubterminal)に関するページを参照してください。

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>RHEL でシングル ユーザー モードの root アクセス権を設定する
ルート ユーザーは既定では無効です。 RHEL のシングル ユーザー モードを使用するには、ルート ユーザーを有効にする必要があります。 シングル ユーザー モードを有効にする必要がある場合は、次の手順のようにします。

1. SSH を使って Red Hat システムにサインインします。
1. ルートに切り替えます。
1. 次の手順を行って、ルート ユーザーのパスワードを有効にします。
    * `passwd root` を実行します (強力な root パスワードを設定します)。
1. 次の手順を行って、ルート ユーザーが確実に ttyS0 経由でのみサインインできるようにします。  
    a. `edit /etc/ssh/sshd_config` を実行し、PermitRootLogIn を確実に `no` に設定します。  
    b. `edit /etc/securetty file` を実行して、ttyS0 経由のサインインのみを許可します。

これで、システムがシングル ユーザー モードで起動した場合、root パスワードを使用してサインインできます。

RHEL 7.4 以降または 6.9 以降の場合、代わりの方法として、GRUB プロンプトでシングル ユーザー モードを有効にできます。[シングル ユーザー モードでブートする](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)に関するページを参照してください。

### <a name="manually-enter-single-user-mode-in-rhel"></a>RHEL でシングル ユーザー モードを手動で開始する
前述の手順を使用して GRUB と root アクセス権を設定した場合は、次の手順に従ってシングル ユーザー モードにすることができます。

1. GRUB に入るには、VM を再起動するときに Esc キーを押します。
1. GRUB で E キーを押して、ブート先の OS を編集します。 OS は通常、最初の行に表示されます。
1. カーネル行を見つけます。 Azure では、"*linux16*" で始まります。
1. Ctrl キーを押しながら E キーを押して行末に移動します。
1. 行の末尾に、"*systemd.unit=rescue.target*" を追加します。
    
    この操作により、シングル ユーザー モードで起動されます。 緊急モードを使用する場合は、"*systemd.unit=emergency.target*" ("*systemd.unit=rescue.target*" ではなく) を行の末尾に追加します。

1. Ctrl キーを押しながら X キーを押して終了し、適用された設定で再起動します。

   シングル ユーザー モードを開始するには、管理者パスワードの入力を求められます。 このパスワードは、前の手順で作成したものです。

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>RHEL で root アカウントを有効にせずにシングル ユーザー モードを開始する
前の手順に従ってルート ユーザーを有効にしなかった場合でも、次の手順に従って root パスワードをリセットできます。

> [!NOTE]
> SELinux を使用している場合は、root パスワードをリセットするときに、[Red Hat のドキュメント](https://aka.ms/rhel7grubterminal)に記載されている追加の手順に従ってください。

1. GRUB に入るには、VM を再起動するときに Esc キーを押します。

1. GRUB で E キーを押して、ブート先の OS を編集します。 OS は通常、最初の行に表示されます。
1. カーネル行を見つけます。 Azure では、"*linux16*" で始まります。
1. 行の末尾に "*rd.break*" を追加します。 カーネル行と "*rd.break*" の間にスペースを入れます。

    この操作により、[Red Hat のドキュメント](https://aka.ms/rhel7rootpassword)で説明されているように、コントロールが `initramfs` から `systemd` に渡される前に起動プロセスが中断されます。
1. Ctrl キーを押しながら X キーを押して終了し、適用された設定で再起動します。

   再起動すると、読み取り専用のファイル システムで緊急モードが開始されます。 
   
1. シェルで `mount -o remount,rw /sysroot` と入力して、読み取り/書き込みアクセス許可でルート ファイル システムを再マウントします。
1. シングル ユーザー モードで起動したら、`chroot /sysroot` と入力して `sysroot` jail に切り替えます。
1. これで、ルートになりました。 `passwd` を入力して root パスワードをリセットしてから、前述の手順を使用してシングル ユーザー モードを開始することができます。 
1. 完了したら、`reboot -f` と入力して再起動します。

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> [!NOTE]
> 前述の手順を行うと、緊急シェルが開始されるので、`fstab` の編集などのタスクも実行できます。 しかしながら通常は、root パスワードをリセットしてシングル ユーザー モードの開始に使用することをお勧めします。

## <a name="access-for-centos"></a>CentOS へのアクセス
Red Hat Enterprise Linux と同様に、CentOS でシングル ユーザー モードを使用するには、GRUB が必要であり、ルート ユーザーを有効にする必要があります。

### <a name="grub-access-in-centos"></a>CentOS での GRUB アクセス
CentOS には GRUB が付属しており、すぐに使用できます。 GRUB に入るには、`sudo reboot` を入力して VM を再起動し、任意のキーを押します。 この操作により、GRUB ウィンドウが表示されます。

### <a name="single-user-mode-in-centos"></a>CentOS でのシングル ユーザー モード
CentOS でシングル ユーザー モードを有効にするには、前述の RHEL の手順を行います。

## <a name="access-for-ubuntu"></a>Ubuntu へのアクセス
Ubuntu イメージでは、root パスワードは必要ありません。 システムがシングル ユーザー モードで起動した場合、資格情報を追加指定しなくても使用できます。

### <a name="grub-access-in-ubuntu"></a>Ubuntu での GRUB アクセス
GRUB にアクセスするには、VM の起動中に Esc キーを長押しします。

既定では、Ubuntu イメージは GRUB ウィンドウに自動的に表示されません。 設定を変更するには、次の手順を行います。
1. テキスト エディターで、" */etc/default/grub.d/50-cloudimg-settings.cfg*" ファイルを開きます。

1. `GRUB_TIMEOUT` 値を 0 以外の値に変更します。
1. テキスト エディターで、" */etc/default/grub*" を開きます。
1. `GRUB_HIDDEN_TIMEOUT=1` の行をコメント アウトします。
1. `GRUB_TIMEOUT_STYLE=menu` の行があることを確認します。
1. `sudo update-grub` を実行します。

### <a name="single-user-mode-in-ubuntu"></a>Ubuntu でのシングル ユーザー モード
Ubuntu を正常に起動できない場合、自動的にシングル ユーザー モードに切り替わります。 シングル ユーザー モードを手動で開始するには、次の手順を行います。

1. GRUB で、E キーを押してブート エントリ (Ubuntu エントリ) を編集します。
1. "*linux*" で始まる行を探し、次に "*ro*" を探します。
1. "*ro*" の後に "*single*" を追加し、"*single*" の前後にスペースがあるようにします。
1. Ctrl キーを押しながら X キーを押してこれらの設定で再起動し、シングル ユーザー モードを開始します。

### <a name="use-grub-to-invoke-bash-in-ubuntu"></a>GRUB を使用して Ubuntu で bash を呼び出す
前述の手順を試してみた後でも、Ubuntu VM でシングル ユーザー モードにまだアクセスできない場合があります (root パスワードを忘れた場合など)。 また、init として、システム init ではなく `/bin/bash` が実行されるようにカーネルに指示することもできます。 この操作により、bash シェルを利用して、システムのメンテナンスができるようになります。 次の手順を実行してください。

1. GRUB で、E キーを押してブート エントリ (Ubuntu エントリ) を編集します。

1. "*linux*" で始まる行を探し、次に "*ro*" を探します。
1. "*ro*" を "*rw init=/bin/bash*" に置き換えます。

    この操作により、ファイル システムが読み取り/書き込み可能としてマウントされ、init プロセスとして `/bin/bash` が使用されます。
1. Ctrl キーを押しながら X キーを押してこれらの設定で再起動します。

## <a name="access-for-coreos"></a>CoreOS へのアクセス
CoreOS でシングル ユーザー モードを使用するには、GRUB を有効にする必要があります。

### <a name="grub-access-in-coreos"></a>CoreOS での GRUB アクセス
GRUB にアクセスするには、VM の起動中に任意のキーを押します。

### <a name="single-user-mode-in-coreos"></a>CoreOS でのシングル ユーザー モード
CoreOS を正常に起動できない場合、自動的にシングル ユーザー モードに切り替わります。 シングル ユーザー モードを手動で開始するには、次の手順を行います。

1. GRUB で、E キーを押してブート エントリを編集します。

1. "*linux$* " で始まる行を探します。 この行の 2 つのインスタンスが存在し、それぞれが、異なる "*if...else*" 句の中に含まれている必要があります。
1. "*coreos.autologin=ttyS0*" を各 "*linux$* " 行の末尾に追加します。
1. Ctrl キーを押しながら X キーを押してこれらの設定で再起動し、シングル ユーザー モードを開始します。

## <a name="access-for-suse-sles"></a>SUSE SLES へのアクセス
SLES 12 SP3 以降の新しいイメージでは、システムが緊急モードで起動している場合、シリアル コンソールを使ってアクセスできます。

### <a name="grub-access-in-suse-sles"></a>SUSE SLES での GRUB アクセス
SLES での GRUB アクセスには、YaST を使用してブートローダーを構成する必要があります。 この構成を作成するには、次の操作を行います。

1. SSH を使用して SLES VM にサインインし、`sudo yast bootloader` を実行します。 Tab キーを押して Enter キーを押し、方向キーを使用してメニュー内を移動します。

1. **[カーネル パラメーター]** に移動し、 **[Use serial console]\(シリアル コンソールを使用する\)** チェック ボックスを選択します。
1. **Console** 引数に `serial --unit=0 --speed=9600 --parity=no` を追加します。
1. F10 キーを押して設定を保存し、終了します。
1. GRUB に入るには、VM を再起動し、起動シーケンス中に任意のキーを押して GRUB ウィンドウを表示したままにします。

    GRUB の既定のタイムアウトは **1 秒**です。 この設定を変更するには、" */etc/default/grub*" ファイルの `GRUB_TIMEOUT` 変数を変更します。

![ブートローダーの構成を初期化する](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>SUSE SLES でのシングル ユーザー モード
SLES が正常に起動できない場合は、自動的に緊急シェルに切り替わります。 緊急シェルを手動で開始するには、次の手順を行います。

1. GRUB で、E キーを押してブート エントリ (SLES エントリ) を編集します。

1. "*linux$* " で始まるカーネル行を探します。
1. "*systemd.unit=emergency.target*" をカーネル行の末尾に追加します。
1. Ctrl キーを押しながら X キーを押してこれらの設定で再起動し、緊急シェルを開始します。

   > [!NOTE]
   > この操作により、読み取り専用のファイル システムで緊急シェルが開始されます。 ファイルを編集するには、読み取り/書き込みアクセス許可を使用してファイル システムを再マウントします。 これを行うには、シェルに `mount -o remount,rw /` と入力します。

## <a name="access-for-oracle-linux"></a>Oracle Linux へのアクセス
Red Hat Enterprise Linux と同様に、Oracle Linux でシングル ユーザー モードを使用するには、GRUB が必要であり、ルート ユーザーを有効にする必要があります。

### <a name="grub-access-in-oracle-linux"></a>Oracle Linux での GRUB アクセス
Oracle Linux には GRUB が付属しており、すぐに使用できます。 GRUB に入るには、`sudo reboot` を実行して VM を再起動し、Esc キーを押します。この操作により、GRUB ウィンドウが表示されます。 GRUB ウィンドウが表示されない場合は、`GRUB_TERMINAL` 行の値に "*serial console*" (`GRUB_TERMINAL="serial console"`) が含まれることを確保します。 `grub2-mkconfig -o /boot/grub/grub.cfg` を使用して GRUB を再構築します。

### <a name="single-user-mode-in-oracle-linux"></a>Oracle Linux でのシングル ユーザー モード
Oracle Linux でシングル ユーザー モードを有効にするには、前述の RHEL の手順を行います。

## <a name="next-steps"></a>次の手順
シリアル コンソールの詳細については、以下を参照してください。
* [Linux シリアル コンソールのドキュメント](serial-console-linux.md)
* [シリアル コンソールを使用してさまざまなディストリビューションで GRUB を有効にする](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* [NMI および SysRq 呼び出しにシリアル コンソールを使用する](serial-console-nmi-sysrq.md)
* [Windows VM 用シリアル コンソール](serial-console-windows.md)
* [ブート診断](boot-diagnostics.md)
