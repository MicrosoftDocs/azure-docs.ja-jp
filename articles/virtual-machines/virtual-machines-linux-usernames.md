---
title: "Linux に対応するユーザー名の選択 | Microsoft Docs"
description: "Azure で Linux 仮想マシンに対応するユーザー名を選択する方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 33b50c97-92f1-46c9-a623-e37f67459c5c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eaea0ce42537789278d3a0476b261e6a750dde2b


---
# <a name="selecting-user-names-for-linux-on-azure"></a>Azure 上の Linux に対応するユーザー名の選択
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Azure に Linux 仮想マシンをプロビジョニングするとき、後で VM へのログインに使用できる、ルート以外のユーザー名を指定する必要があります。 新規ユーザーの名前を選択するか、Azure クラシック ポータルでプロビジョニングする場合は既定の名前である "azureuser" を使用できます。

ほとんどの場合、このユーザーは基本イメージ上に存在せず、プロビジョニング プロセス中に作成されます。 ユーザーが基本 VM イメージ上に存在する場合、Azure Linux エージェントは VM の作成時に指定された情報に基づいて、そのユーザーのパスワードや SSH キーを構成します。

**ただし**、Linux では、使用しない方がよいユーザー名が定められています。 UID 0 ～ 99 で定義されている既存のシステム ユーザーを使用して Linux VM をプロビジョニングしようとすると、プロビジョニング プロセスは **失敗** します。 代表的な例は、UID が 0 である `root` ユーザーです。

*  [Linux 標準ベース - ユーザー ID の範囲](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)

以下に示したのは、Linux 仮想マシンを Azure にプロビジョニングするときに避けるべき CentOS と Ubuntu における一般的な組み込みシステム ユーザーの一覧です。 これはあくまで例です。ご使用のディストリビューションのドキュメントを参照し、既存のシステム ユーザーと競合しないユーザー名を使用してください。

## <a name="centos"></a>CentOS
* abrt
* adm
* audio
* bin
* cdrom
* cgred
* daemon
* dbus
* dialout
* dip
* disk
* floppy
* ftp
* ftp
* games
* gopher
* haldaemon
* halt
* kmem
* lock
* lp
* mail
* man
* mem
* nfsnobody
* nobody
* ntp
* operator
* oprofile
* postdrop
* postfix
* qpidd
* root
* rpc
* rpcuser
* saslauth
* shutdown
* slocate
* sshd
* stapdev
* stapusr
* sync
* sys
* tape
* test
* tcpdump
* tty
* users
* utempter
* utmp
* uucp
* vcsa
* video
* wheel

## <a name="ubuntu"></a>Ubuntu
* adm
* admin
* audio
* backup
* bin
* cdrom
* crontab
* daemon
* dialout
* dip
* disk
* fax
* floppy
* fuse
* games
* gnats
* irc
* kmem
* landscape
* libuuid
* list
* lp
* mail
* man
* messagebus
* mlocate
* netdev
* news
* nobody
* nogroup
* operator
* plugdev
* proxy
* root
* sasl
* shadow
* src
* ssh
* sshd
* staff
* sudo
* sync
* sys
* syslog
* tape
* tty
* users
* utmp
* uucp
* video
* voice
* whoopsie
* www-data




<!--HONumber=Nov16_HO3-->


