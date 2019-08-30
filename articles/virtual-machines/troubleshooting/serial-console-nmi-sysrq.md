---
title: Azure Serial Console での SysRq および NMI 呼び出し | Microsoft Docs
description: シリアル コンソールを使用して Azure 仮想マシンで SysRq および NMI 呼び出しを行います。
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
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: d5c647bac2bc6abc85a74531e052f0f3a54b2047
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70090092"
---
# <a name="use-serial-console-for-sysrq-and-nmi-calls"></a>SysRq および NMI 呼び出しにシリアル コンソールを使用する

## <a name="system-request-sysrq"></a>システム要求 (SysRq)
SysRq は Linux オペレーション システム カーネルによって解釈されるキーのシーケンスであり、事前定義済みの一連のアクションをトリガーできます。 これらのコマンドは通常、仮想マシンのトラブルシューティングまたは復旧が従来の管理では実行できない場合 (たとえば、VM が応答していない場合など) に使用されます。 Azure Serial Console の SysRq 機能を使用することは、SysRq キーを押すことや物理キーボード上に文字が入力されることと、同等の動作になります。

SysRq シーケンスが配信されると、以降は、カーネル構成によってシステムの応答方法が制御されます。 SysRq の有効化および無効化に関する情報については、「*SysRq Admin Guide*」(SysRq 管理ガイド) の "[テキスト](https://aka.ms/kernelorgsysreqdoc) | [マークダウン](https://aka.ms/linuxsysrq)" を参照してください。  

Azure Serial Console は、以下に示すコマンド バーのキーボード アイコンを使用して、SysRq を Azure 仮想マシンに送信するために使用できます。

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg)

"Send SysRq Command"\(SysRq コマンドを送信する\) を選択するとダイアログが開き、一般的な SysRq オプションを指定したり、ダイアログに入力された SysRq コマンドのシーケンスを許可したりできます。  ｌこれにより、`REISUB` を使用して、一連の SysRq コマンドで安全な再起動などの高度な操作を実行できるようになります。

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-sysreq_UI.png)

SysRq コマンドは、停止している仮想マシン上や、カーネルが応答しない状態 (カーネル パニックなど) になった仮想マシン上では、使用できません。

### <a name="enable-sysrq"></a>SysRq の有効化 
前記の「*SysRq Admin Guide*」(SysRq 管理ガイド) の説明にあるように、すべてのコマンドまたは一部の特定のコマンドが利用可能か、またはどのコマンドも利用不可能になるように SysRq を構成できます。 以下の手順を使用してすべての SysRq コマンドを有効にできますが、再起動すると、この構成は失われます。
```
echo "1" >/proc/sys/kernel/sysrq
```
SysReq の構成を永続化するために、次の手順を実行してすべての SysRq コマンドを有効にできます。
1. 以下の行を */etc/sysctl.conf* に追加します。 <br>
    `kernel.sysrq = 1`
1. 次のコマンドを実行して、sysctl を再起動または更新します。 <br>
    `sysctl -p`

### <a name="command-keys"></a>コマンド キー 
前記の「SysRq Admin Guide」(SysRq 管理ガイド) に示されたコマンド キーは次のとおりです。

|command| Function
| ------| ----------- |
|``b``  |   ディスクの同期またはマウント解除を行わずに、システムをすぐに再起動します。
|``c``  |   NULL ポインターの逆参照によってシステム クラッシュを実行します。 構成した場合は、クラッシュ ダンプが取得されます。
|``d``  |   保持されているすべてのロックを表示します。
|``e``  |   init を除いて、SIGTERM をすべてのプロセスに送信します。
|``f``  |   oom キラーを呼び出して、メモリ ホグ プロセスを強制終了します。ただし、何も強制終了できなかった場合に、パニック状態にはなりません。
|``g``  |   kgdb によって使用されます (カーネル デバッガー)。
|``h``  |   ヘルプが表示されます (ここに示された以外のキーでもヘルプが表示されますが、覚えやすいのは ``h`` です :-)
|``i``  |    init を除いて、SIGKILL をすべてのプロセスに送信します。
|``j``  |    強制的に、FIFREEZE ioctl によって凍結されるファイルシステムを "ただ解凍します"。
|``k``  |    Secure Access Key (SAK) によって、現在の仮想コンソール上のすべてのプログラムを強制終了します。 注:SAK セクションにある重要なコメントを参照してください。
|``l``  |    すべてのアクティブな CPU に対するスタック バックトレースを表示します。
|``m``  |    コンソールに現在のメモリ情報をダンプします。
|``n``  |    RT タスクを最適化するために使用されます。
|``o``  |    システムを閉じます (構成され、サポートされている場合)。
|``p``  |    現在のレジスタとフラグをコンソールにダンプします。
|``q``  |    装備されたすべての hr タイマー (ただし、通常の timer_list のタイマーは対象外) およびすべてのクロックイベント デバイスに関する詳細情報を CPU 一覧ごとにダンプします。
|``r``  |    キーボードの raw モードをオフにし、XLATE に設定します。
|``s``  |    マウントされたすべてのファイルシステムの同期を試行します。
|``t``  |    お使いのコンソールに対する現在のタスクとその情報の一覧をダンプします。
|``u``  |    マウントされたすべてのファイルシステムの再マウントを読み取り専用で試行します。
|``v``  |    framebuffer コンソールを強制的に復元します。
|``v``  |    ETM バッファーのダンプを行います [ARM 固有]。
|``w``  |    非割り込み (ブロックされた) 状態でタスクをダンプします。
|``x``  |    ppc/powerpc プラットフォーム上で、xmon インターフェイスによって使用されます。 sparc64 上でグローバル PMU Register を表示します。 MIPS 上のすべての TLB エントリをダンプします。
|``y``  |    グローバル CPU Register を表示します [SPARC-64 固有]
|``z``  |    ftrace バッファーをダンプします。
|``0``-``9`` | コンソール ログ レベルを設定して、どのカーネル メッセージがコンソールにプリントされるかを制御します (たとえば ``0`` では、PANIC や OOPS などの緊急時のメッセージのみがコンソールに示されるように、レベルを設定します)。

### <a name="distribution-specific-documentation"></a>ディストリビューション固有のドキュメント ###
SysRq に関するディストリビューション固有のドキュメントと、Linux を構成して SysRq の "Crash" コマンドを受信した場合にクラッシュ ダンプを作成するための手順については、以下のリンクを参照してください。

#### <a name="ubuntu"></a>Ubuntu ####
 - [カーネル クラッシュ ダンプ](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

#### <a name="red-hat"></a>Red Hat ####
- [SysRq ファシリティの概要と使用方法](https://access.redhat.com/articles/231663)
- [SysRq ファシリティを使用して RHEL サーバーから情報を収集する方法](https://access.redhat.com/solutions/2023)

#### <a name="suse"></a>SUSE ####
- [カーネル コア ダンプのキャプチャを構成する](https://www.suse.com/support/kb/doc/?id=3374462)

#### <a name="coreos"></a>CoreOS ####
- [クラッシュ ログの収集](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="non-maskable-interrupt-nmi"></a>マスク不可能割り込み (NMI) 
マスク不可能割り込み (NMI) は、仮想マシン上のソフトウェアで無視されない信号を作成するために設計されています。 従来より、NMI は、特定の応答時間を要したシステム上でのハードウェアの問題を監視するために使用されてきました。  現在、プログラマーやシステム管理者は、応答していないシステムのデバッグやトラブルシューティングのためのメカニズムとして、NMI をよく使用しています。

シリアル コンソールは、以下に示すコマンド バーのキーボード アイコンを使用して、NMI を Azure 仮想マシンに送信するために使用できます。 NMI が配信されると、以降は、仮想マシン構成によってシステムの応答方法が制御されます。  Linux オペレーティング システムは、オペレーティング システムが NMI を受信した場合に、クラッシュしてメモリ ダンプを作成するように構成できます。

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg) <br>

### <a name="enable-nmi"></a>NMI を有効にする
カーネル パラーメーターを構成するための sysctl をサポートしている Linux システムの場合、次の手順を使用して、この NMI の受信時にパニック状態を有効にできます。
1. 以下の行を */etc/sysctl.conf* に追加します。 <br>
    `kernel.panic_on_unrecovered_nmi=1`
1. 次のコマンドを実行して、sysctl を再起動または更新します。 <br>
    `sysctl -p`

`unknown_nmi_panic`、`panic_on_io_nmi`、および `panic_on_unrecovered_nmi` を含む Linux カーネル構成の詳細については、以下をご覧ください:[/proc/sys/kernel/* のドキュメント](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt)。 NMI のディストリビューション固有のドキュメントと、Linux を構成して NMI を受信した場合にクラッシュ ダンプを作成する手順については、以下のリンクを参照してください。
 
### <a name="ubuntu"></a>Ubuntu 
 - [カーネル クラッシュ ダンプ](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

### <a name="red-hat"></a>Red Hat 
 - [NMI の概要とその用途](https://access.redhat.com/solutions/4127)
 - [NMI スイッチが押されるとシステムがクラッシュするように構成する方法](https://access.redhat.com/solutions/125103)
 - [クラッシュ ダンプ管理ガイド](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/pdf/kernel_crash_dump_guide/kernel-crash-dump-guide.pdf)

### <a name="suse"></a>SUSE 
- [カーネル コア ダンプのキャプチャを構成する](https://www.suse.com/support/kb/doc/?id=3374462)

### <a name="coreos"></a>CoreOS 
- [クラッシュ ログの収集](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="next-steps"></a>次の手順
* 主要なシリアル コンソールの Linux ドキュメントのページについては、[こちら](serial-console-linux.md)を参照してください。
* シリアル コンソールを使用して、[GRUB を起動し、シングル ユーザー モードに入る](serial-console-grub-single-user-mode.md)
* シリアル コンソールは、[Windows](serial-console-windows.md) VM でも使用できます。
* [ブート診断](boot-diagnostics.md)の詳細を表示する