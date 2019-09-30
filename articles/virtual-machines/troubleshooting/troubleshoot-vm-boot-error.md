---
title: Azure 仮想マシンの起動エラー
description: 仮想マシンが復旧コンソールに入ったため、起動できませんでした
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: ''
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/28/2019
ms.author: tiag
ms.openlocfilehash: 9995b9049378a0ab4f3450ec577d034598d171e9
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984833"
---
# <a name="vm-boot-error"></a>VM の起動エラー

仮想マシン (VM) が復旧コンソールに入ったことを確認しました。 この問題は、Linux VM で最近、カーネルのアップグレードなど、カーネルの変更が適用され、起動プロセス中にカーネル エラーが発生するため、正しく起動しなくなったときに発生します。 起動プロセス中、ブート ローダーは Linux カーネルの位置を見つけ、それに起動制御を渡そうとするとき、それに失敗すると、VM は復旧コンソールに入ります。

今後、VM に接続できない場合は、Azure portal のブート診断ブレードを使用して、VM のスクリーンショットを表示できます。 これにより、問題を診断し、同様の起動エラーが原因かどうかを判断できます。

## <a name="recommended-steps"></a>推奨される手順

受け取ったエラーに合わせて下の軽減手順を実行します。

### <a name="error---unknown-filesystem"></a>エラー - 不明なファイルシステム

* **不明なファイルシステム**というエラーが表示された場合、ブート パーティション上のファイル システムが壊れているか、カーネルの構成が正しくないことがエラーの原因になっている可能性があります。

   * ファイル システムの問題については、「[Linux 回復:ファイル システムのエラーのため Linux VM に SSH 接続できない (fsck, inodes)](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/)」という記事に記載されている手順に従ってください。
   * カーネルの問題については、「[Linux 回復:カーネルの問題に関連する起動以外の問題を手動で修正する](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/)」または「[Linux 回復:chroot を使用し、カーネルの問題に関連する起動以外の問題を修正する](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/)」という記事に記載されている手順に従ってください。
   
### <a name="error---file-not-found"></a>エラー - ファイルが見つかりません

* **エラー 15:File not found or initial RAM disk** (ファイルが見つからない) または **initrd/initramfs file not found** (初期 RAM ディスクまたは initrd/initramfs ファイルが見つからない) というエラーが表示される場合、下の手順に従ってください。

    * ファイル `/boot/grub2/grub.cfg` または `initrd/initramfs` が見つからない場合、次のプロセスに進んでください。

    1. `/etc/default/grub` が存在し、設定が正しいこと、希望どおりであることを確認します。 既定の設定がわからない場合は、動作している VM で確認できます。

    2. 次に、次のコマンドを実行し、その構成を再生成します。 `grub2-mkconfig -o /boot/grub2/grub.cfg`

   * 不足しているファイルが `/boot/grub/menu.lst` の場合、このエラーは以前の OS バージョン (**RHEL 6.x**、**Centos 6.x** と **Ubuntu 14.04**) に対するものであり、コマンドが異なることがあります。 古いサーバーを起動し、正しいコマンドが入力されていることを確認する必要があります。

### <a name="error---no-such-partition"></a>エラー - そのようなパーティションはありません

* **そのようなパーティションはありません** というエラーが表示された場合、「[ケース シナリオ: OS ドライブの拡張試行後、VM を起動すると "そのようなパーティションはありません" というエラーが表示される](https://blogs.technet.microsoft.com/shwetanayak/2017/03/12/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive/)」という記事を参照してください。

### <a name="error---grubcfg-file-not-found"></a>エラー - grub.cfg ファイルが見つかりません

* **/boot/grub2/grub.cfg ファイルが見つかりません** というエラーが表示された場合、下の手順に従ってください。

    * ファイル `/boot/grub2/grub.cfg` または `initrd/initramfs` が見つからない場合、次のプロセスに進んでください。

    1. `/etc/default/grub` が存在し、設定が正しいこと、希望どおりであることを確認します。 既定の設定がわからない場合は、動作している VM で確認できます。

    2. 次に、次のコマンドを実行し、その構成を再生成します。 `grub2-mkconfig -o /boot/grub2/grub.cfg`。

   * 不足しているファイルが `/boot/grub/menu.lst` の場合、このエラーは以前の OS バージョン (**RHEL 6.x**、**Centos 6.x** と **Ubuntu 14.04**) に対するものであり、コマンドが遅れることがあります。 古いサーバーを起動し、正しいコマンドが入力されていることを確認します。

## <a name="next-steps"></a>次の手順

* [Azure 仮想マシン エージェントの概要](../extensions/agent-windows.md)
* [Windows 用の仮想マシン拡張機能とその機能](../extensions/features-windows.md)

