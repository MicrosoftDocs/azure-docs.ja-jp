---
title: Azure シリアル コンソールの GRUB の事前構成 | Microsoft Docs
description: Azure 仮想マシンでのシングル ユーザー モード アクセスと回復モード アクセスを許可するように、さまざまなディストリビューションで GRUB を構成します。
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/10/2019
ms.author: mimckitt
ms.openlocfilehash: c48ef0321ece2e7e0ffcdfcb8c0907c5f839e738
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87831364"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>事前に GRUB と sysrq に確実にアクセスできるようにすることでダウンタイムを大幅に短縮する

シリアル コンソールと GRUB にアクセスできると、ほとんどの場合、IaaS Linux 仮想マシンの復旧時間が短縮されます。 GRUB には復旧オプションが用意されています。それがないと、VM の復旧にはさらに長い時間がかかります。 


VM の復旧を実行する理由は多数あり、次のようなシナリオが要因になります。

   - ファイル システム、カーネル、MBR (マスター ブート レコード) の破損
   - カーネルのアップグレードの失敗
   - 正しくない GRUB カーネル パラメーター
   - 正しくない fstab の構成
   - ファイアウォールの構成
   - パスワードの紛失
   - 破損した sshd 構成ファイル
   - ネットワークの構成

 他の多くのシナリオについて詳しくは、[こちら](./serial-console-linux.md#common-scenarios-for-accessing-the-serial-console)をご覧ください

Azure にデプロイされている VM で GRUB とシリアル コンソールにアクセスできることを確認します。 

シリアル コンソールを初めて使用する場合は、[こちらのリンク](./serial-console-linux.md)をご覧ください。

> [!TIP]
> 変更を行う前に、ファイルのバックアップを作成してください

GRUB にアクセスした後で、Linux VM をすばやく復旧する方法については、次のビデオをご覧ください

[Linux VM の復旧に関するビデオ](https://youtu.be/KevOc3d_SG4)

Linux VM の復旧に役立つ方法がいろいろあります。 クラウド環境では、このプロセスは困難なものでした。
サービスを迅速に復旧するためのツールや機能の改善が継続的に行われています。

Azure シリアルコンソールを使うと、システムのコンソールを使用しているかのように Linux VM と対話できます。

カーネルの起動方法など、多くの構成ファイルを操作できます。 

経験豊富な Linux や Unix のシステム管理者は、多くの復旧シナリオでディスクのスワップや VM の削除が冗長になる、Azure シリアル コンソールを使用してアクセスできる**シングル ユーザー モード**と**緊急モード**の価値を認めています。

復旧の方法は、発生している問題によって異なります。たとえば、紛失したり誤って設定したりしたパスワードのリセットは、Azure portal のオプション **[パスワードのリセット]** を使って行うことができます。 **[パスワードのリセット]** 機能は拡張機能と呼ばれ、Linux ゲスト エージェントと通信します。

カスタム スクリプトなどの他の拡張機能も使用できますが、これらのオプションを使うには、Linux の **waagent** が正常に稼働している必要があり、常にそうであるとは限りません。

![エージェントの状態](./media/virtual-machines-serial-console/agent-status.png)


Azure シリアル コンソールと GRUB にアクセスできるようにするということは、パスワードの変更や構成の誤りを、数時間ではなく数分で修正できることを意味します。 ディスク上に複数のカーネルがあれば、プライマリ カーネルが破損した場合に、強制的に別のカーネルから VM を起動することもできます。

![複数のカーネル](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>推奨される復旧方法の順序:

- Azure シリアル コンソール

- ディスク スワップ – 次のいずれかを使用して自動化できます。

   - [PowerShell の復旧スクリプト](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [bash の復旧スクリプト](https://github.com/sribs/azure-support-scripts)

- 従来の方法

## <a name="disk-swap-video"></a>ディスク スワップの動画:

GRUB にアクセスできない場合は、[こちら](https://youtu.be/m5t0GZ5oGAc)の動画を見て、VM を復旧するためのディスク スワップ手順を簡単に自動化する方法を確認してください

## <a name="challenges"></a>課題:

すべての Linux Azure VM が既定で GRUB アクセス用に構成されるわけではなく、sysrq コマンドで中断されるように構成されるわけでもありません。 SLES 11 などの一部の古いディストリビューションは、Azure シリアル コンソールにログイン プロンプトを表示するように構成されません

この記事では、GRUB を利用できるようにする方法について、さまざまな Linux ディストリビューションとドキュメント構成を確認します。




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>SysRq キーを受け入れるように Linux VM を構成する方法
Sysrq キーは、一部の新しい Linux ディストリビューションでは既定で有効になりますが、他のディストリビューションでは、特定の SysRq 関数に対する値のみを受け入れるように構成されている場合があります。
古いディストリビューションでは、完全に無効になっている可能性があります。

SysRq 機能は、クラッシュまたは応答しない VM を Azure シリアル コンソールから直接再起動する場合に役立ちます。また、GRUB メニューにアクセスする場合にも便利です。代わりに、別のポータル ウィンドウや SSH セッションから VM を再起動すると、現在のコンソール接続が切断され、そのために GRUB メニューの表示に使用される GRUB タイムアウトが切れる可能性があります。
VM は、カーネル パラメーターの値として、sysrq のすべての関数が有効になる 1、または再起動と電源オフのみが可能な 128 を受け入れるように、構成する必要があります


[SysRq 有効化のビデオ](https://youtu.be/0doqFRrHz_Mc)


Azure portal での SysRq コマンドによる再起動を受け入れるように VM を構成するには、カーネル パラメーター kernel.sysrq に対して値 1 を設定する必要があります

この構成が再起動時に維持されるようにするには、ファイル **sysctl.conf** にエントリを追加します

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

カーネル パラメーターを動的に構成するには

`sysctl -w kernel.sysrq=1`

**ルート** アクセスできない場合、または sudo が壊れている場合は、シェル プロンプトから sysrq を構成できません。

このシナリオでは、Azure portal を使用して sysrq を有効にすることができます。 この方法は、**sudoers.d/waagent** ファイルが破損したり削除されたりした場合に役立ちます。

Azure portal の [Operations]\(操作\) > [実行コマンド] > [RunShellScript] 機能を使用するには、waagent プロセスが正常である必要があります。その場合は、このコマンドを挿入して sysrq を有効にすることができます

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

次に例を示します。![enable sysrq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

完了したら、**sysrq** にアクセスできるようになり、再起動が可能であることがわかります。

![enable sysrq3](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

**[再起動]** および **[SysRq の送信]** コマンドを選択します

![enable sysrq4](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

システムにより次のようなリセット メッセージがログに記録される必要があります

![enable sysrq5](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Ubuntu での GRUB の構成

既定では、VM の起動中に **Esc** キーを押したままにすることで、GRUB にアクセスできます。 GRUB メニューが表示されない場合は、次のいずれかのオプションを使用して、Azure シリアル コンソールの画面に GRUB メニューを強制的に保持することができます。

**オプション 1** - GRUB を強制的に画面に表示する 

指定したタイムアウトまで GRUB メニューが画面に表示されているよう、ファイル /etc/default/grub.d/50-cloudimg-settings.cfg を更新します。
GRUB はすぐに表示されるので、**Esc** キーを押す必要はありません

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**オプション 2** - 起動前に **Esc** キーを押すことができるようにする

/etc/default/grub ファイルを変更し、3 秒間のタイムアウトを確認して **Esc** キーを押すことにより、同様の動作を発生させることができます


次の 2 行をコメントアウトします。

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
そして、次の行を追加します。

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12\.04

Ubuntu 12.04 では、シリアル コンソールにアクセスすることはできますが、対話することはできません。 **login:** プロンプトが表示されません


12.04 で **login:** プロンプトを表示するには:
1. 次のテキストを含む /etc/init/ttyS0.conf という名前のファイルを作成します。

    ```
    # ttyS0 - getty
    #
    # This service maintains a getty on ttyS0 from the point the system is
    # started until it is shut down again.
    start on stopped rc RUNLEVEL=[12345]
    stop on runlevel [!12345]
    
    respawn
    exec /sbin/getty -L 115200 ttyS0 vt102
    ```    

2. getty を開始するよう upstart に指示します     
    ```
    sudo start ttyS0
    ```
 
Ubuntu バージョン用にシリアル コンソールを構成するために必要な設定については、[こちら](https://help.ubuntu.com/community/SerialConsoleHowto)をご覧ください

## <a name="ubuntu-recovery-mode"></a>Ubuntu の回復モード

Ubuntu では GRUB を使用して追加の復旧およびクリーンアップ オプションを使用できますが、これらの設定は、カーネル パラメーターを適切に構成した場合にのみアクセスできます。
このカーネル ブート パラメーターを構成しないと、復旧メニューは、Azure シリアル コンソールではなく、Azure Diagnostics に強制的に送信されます。
Ubuntu の復旧メニューにアクセスするには、次の手順のようにします。

起動プロセスを中断し、GRUB メニューにアクセスします

[Advanced Options for Ubuntu] を選択し、Enter キーを押します

![ubunturec1](./media/virtual-machines-serial-console/ubunturec1.png)

*[(recovery mode)]* と表示されている行を選択し、Enter キーではなく e キーを押します

![ubunturec2](./media/virtual-machines-serial-console/ubunturec2.png)

カーネルを読み込む行を見つけて、最後のパラメーター **nomodeset** を宛先 **console=ttyS0** に置き換えます

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![ubunturec3](./media/virtual-machines-serial-console/ubunturec3.png)

**Ctrl + x** キーを押して、カーネルを起動して読み込みます。
すべてが正常に進むと、他の復旧オプションを実行できる追加オプションが表示されます

![ubunturec4](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Red Hat での GRUB の構成

## <a name="red-hat-74-grub-configuration"></a>Red Hat 7\.4\+ での GRUB の構成
これらのバージョンの /etc/default/grub は、既定で適切に構成されます

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_SERIAL_COMMAND="serial"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

SysRq キーを有効にします

```
sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq
```

## <a name="red-hat-72-and-73-grub-configuration"></a>Red Hat 7\.2 および 7\.3 での GRUB の構成
変更するファイルは /etc/default/grub であり、既定の構成は次の例のようになります。

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

/etc/default/grub の次の行を変更します

```
GRUB_TIMEOUT=1 

to

GRUB_TIMEOUT=5
```


```
GRUB_TERMINAL_OUTPUT="console"

to

GRUB_TERMINAL="serial console"
```

また、次の行を追加します。

```
GRUB_SERIAL_COMMAND=”serial –speed=115200 –unit=0 –word=8 –parity=no –stop=1″
```

/etc/default/grub は次の例のようになっている必要があります。

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
次のコマンドを使用して、grub の構成を完了し、更新します

`grub2-mkconfig -o /boot/grub2/grub.cfg`

SysRq カーネル パラメーターを設定します。

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

代わりに、シェルまたは実行コマンドで 1 行を使用して GRUB と SysRq を構成することもできます。 このコマンドを実行する前に、ファイルをバックアップします。


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Red Hat 6\.x での GRUB の構成
変更するファイルは /boot/grub/grub.conf です。 `timeout` の値により、GRUB が表示されている期間が決まります。

```
#boot=/dev/vda
default=0
timeout=15
splashimage=(hd0,0)/grub/splash.xpm.gz
#hiddenmenu
serial --unit=0 --speed=9600
terminal serial
terminal --timeout=5 serial console
```


最後の行 *terminal –-timeout=5 serial console* では、**Press any key to continue** プロンプトの表示時間に 5 秒追加することで、**GRUB** がタイムアウトするまでの時間をさらに長くします。

![rh6-1](./media/virtual-machines-serial-console/rh6-1.png)

Esc キーを押さなくても、GRUB メニューは構成されている timeout=15 だけ画面に表示されます。メニューをアクティブにし、必要なカーネルを選択するには、必ずブラウザーでコンソールをクリックしてください

![rh6-2](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>SuSE

## <a name="sles-12-sp1"></a>SLES 12 sp1
公式の[ドキュメント](./serial-console-grub-single-user-mode.md#grub-access-in-suse-sles)に従って、YaST ブートローダーを使用します

または、/etc/default/grub に次のパラメーターを追加または変更します。

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
GRUB_CMDLINE_LINUX または GRUB_CMDLINE_LINUX_DEFAULT で ttys0 が使用されていることを確認します

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

grub.cfg を作成し直します

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
シリアル コンソールが開いてブート メッセージは表示されますが、**login:** プロンプトは表示されません

VM に対して ssh セッションを開き、次の行をコメント解除して、ファイル **/etc/inittab** を更新します。

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

次に、コマンドを実行します 

`telinit q`

GRUB を有効にするには、/boot/grub/menu.lst に対して次の変更を行う必要があります 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 この構成により、メッセージ **Press any key to continue** がコンソールに 5 秒間表示されるようになります 

その後、さらに 5 秒間、GRUB メニューが表示されます。下方向キーを押してカウンターを中断し、起動するカーネルを選択します。ルート パスワードを設定する必要があるシングル ユーザー モードの場合は、**single** キーワードを追加します。

コマンド **init=/bin/bash** を追加すると、カーネルは読み込まれますが、init プログラムは bash シェルに置き換えられます。

パスワードを入力しなくても、シェルにアクセスできるようになります。 次に、Linux アカウントのパスワードの更新や、その他の構成の変更を行うことができます。


## <a name="force-the-kernel-to-a-bash-prompt"></a>カーネルを強制的に bash プロンプトにする
GRUB にアクセスできるようにすると、初期化プロセスを中断することができ、この対話は多くの復旧手順に役立ちます。
ルート パスワードがなく、シングル ユーザーでルート パスワードが要求されている場合は、init プログラムを bash プロンプトに置き換えてカーネルを起動できます。この割り込みは、カーネルの起動行に init=/bin/bash を追加することで実現できます

![bash1](./media/virtual-machines-serial-console/bash1.png)

次のコマンドを使用して、/(root) ファイル システムの RW を再マウントします

`mount -o remount,rw /`

![bash2](./media/virtual-machines-serial-console/bash2.png)


ルート パスワードの変更またはその他の多くの Linux 構成の変更を実行できるようになります

![bash3](./media/virtual-machines-serial-console/bash3.png)

次のコマンドで VM を再起動します 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>シングル ユーザー モード

または、シングル ユーザー モードまたは緊急モードで VM にアクセスすることが必要になる場合もあります。 方向キーを使用して、起動または中断するカーネルを選択します。
カーネル起動行にキーワード **single** または **1** を追加して、目的のモードにします。 RHEL システムでは、**rd.break** を追加することもできます。

シングル ユーザー モードにアクセスする方法について詳しくは、[こちらのドキュメント](./serial-console-grub-single-user-mode.md#general-single-user-mode-access)をご覧ください 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>次のステップ
[Azure シリアル コンソール]( ./serial-console-linux.md)についてさらに詳しく学習します