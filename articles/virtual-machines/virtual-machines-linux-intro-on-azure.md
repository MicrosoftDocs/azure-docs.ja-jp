---
title: "Azure での Linux 入門 | Microsoft Docs"
description: "Azure での Linux 仮想マシンの使用に関する詳細です。"
services: virtual-machines-linux
documentationcenter: python
author: szarkos
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: b13bf305-87bf-4df3-815e-e8f6337aa6ea
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 554d1d504205767a287c690d82f03808047b1961
ms.lasthandoff: 03/27/2017


---
# <a name="introduction-to-linux-on-azure"></a>Azure での Linux 入門
このトピックでは、Azure クラウドで Linux 仮想マシンを使用するさまざまな局面について概説します。 Linux 仮想マシンのデプロイは、ギャラリー内に存在するイメージを使用する際に簡単に使用できるプロセスです。

## <a name="authentication-usernames-passwords-and-ssh-keys"></a>認証: ユーザー名、パスワード、SSH 鍵。
Azure クラシック ポータルを使用して Linux 仮想マシンを作成すると、ユーザー名、パスワードまたは SSH 公開キーの入力が求められます。 Azure で Linux 仮想マシンをデプロイするユーザー名を選択する場合、root など、既に仮想マシン内に存在するシステム アカウント (UID <100) の名前は許可されない、という制約があります。

* 参照: [Linux を実行する仮想マシンの作成](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* 参照: [Azure 上の Linux における SSH の使用方法](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="obtaining-superuser-privileges-using-sudo"></a>`sudo`
Azure での仮想マシン インスタンスをデプロイする際に指定したユーザー アカウントは、特権を持つアカウントです。 このアカウントは、Azure Linux エージェントによって `sudo` ユーティリティを使用して権限を root (スーパーユーザー アカウント) に昇格できるように構成されます。 このユーザー アカウントを使用してログインすると、コマンド構文を使用して、root としてコマンドを実行できるようになります。

    # sudo <COMMAND>

**sudo -s**を使用して root シェルを取得することもできます。

* 参照: [Azure 上の Linux 仮想マシンでの root 権限の使用](virtual-machines-linux-use-root-privileges.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="firewall-configuration"></a>ファイアウォールの構成
Azure では、Azure クラシック ポータルで指定されたポートに接続を制限する受信パケット フィルターが用意されています。 既定では、許可されている唯一のポートは SSH です。 Azure クラシック ポータルでエンドポイントを構成することで、Linux 仮想マシンの追加ポートへのアクセスを設定できます。

* 参照: [Azure 上でクラシック Windows 仮想マシンにエンドポイントをセットアップする方法](windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

既定では、Azure ギャラリー内の Linux のイメージを使用して *iptables* ファイアウォールを有効にすることはできません。 必要があれば、このファイアウォールにフィルターを追加するように構成することはできます。

## <a name="hostname-changes"></a>ホスト名の変更
最初に Linux イメージのインスタンスをデプロイするときに、仮想マシンのホスト名を提供するように求められます。 仮想マシンが実行されると、このホスト名はプラットフォーム DNS サーバーに公開されます。相互に接続された複数の仮想マシンがホスト名を使用して IP アドレス検索を実行できるようになります。

仮想マシンがデプロイされた後でホスト名を変更する場合は、次のコマンドを使用します。

    # sudo hostname <newname>

Azure Linux エージェントには、この名前の変更を自動的に検出する機能、この変更を保持するように仮想マシンを正しく構成する機能、この変更をプラットフォーム DNS サーバーに公開する機能があります。

* [Azure Linux エージェント ユーザー ガイド](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="cloud-init"></a>Cloud-Init
**Ubuntu** イメージと **CoreOS** イメージでは、仮想マシンをブートストラップするための追加機能を提供する Azure の cloud-init を使用します。

* [カスタム データを挿入する方法](windows/classic/inject-custom-data.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Microsoft Azure のカスタム データと Cloud-Init](https://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)
* [Cloud-Init を使用した Azure スワップ パーティションの作成](https://wiki.ubuntu.com/AzureSwapPartitions)
* [Azure で CoreOS を使用する方法](https://coreos.com/os/docs/latest/booting-on-azure.html)

## <a name="virtual-machine-image-capture"></a>仮想マシン イメージのキャプチャ
Azure には、既存の仮想マシンの状態をイメージにキャプチャする機能があります。このイメージは、後で追加の仮想マシンのインスタンスをデプロイするために使用できます。 Azure Linux エージェントは、プロビジョニング中に実行したカスタマイズをロールバックするために使用することもできます。 仮想マシンをイメージとしてキャプチャする手順は次のとおりです。

1. **waagent -deprovision** を実行してプロビジョニング中のカスタマイズを元に戻します。 または、 **waagent -deprovision+user** を実行して、プロビジョニング中に指定したユーザー アカウントとすべての関連データを削除します。
2. 仮想マシンをシャットダウン/電源オフします。
3. Azure クラシック ポータルで *[キャプチャ]* をクリックするか、Powershell ツールまたは CLI ツールを使用して仮想マシンをイメージとしてキャプチャします。
   
   * 参照: [Linux を実行する仮想マシンのイメージをキャプチャする方法](linux/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## <a name="attaching-disks"></a>ディスクの接続
各仮想マシンには、一時的なローカル *リソース ディスク* が接続されています。 リソース ディスク上のデータには、再起動を行った場合の持続性がないため、仮想マシン上で実行されているアプリケーションやプロセスは、多くの場合、このディスクを過渡的および **一時的な** ストレージとして使用します。 また、オペレーティング システムがページ ファイルやスワップ ファイルを格納する場合にも使用されます。

Linux では通常、リソース ディスクは Azure Linux エージェントによって管理され、**/mnt/resource** (または Ubuntu イメージでは **/mnt**) に自動的にマウントされます。

> [!NOTE]
> リソース ディスクは **一時ディスク** であるため、仮想マシンが再起動されると削除され再フォーマットされることに注意してください。
> 
> 

Linux では、データ ディスクはカーネルによって `/dev/sdc`という名前が付けられる場合があり、ユーザーはこのリソースをパーティション分割し、フォーマットした上で、マウントする必要があります。 ディスクの接続については、チュートリアル「[データ ディスクを Linux 仮想マシンに接続する方法](linux/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」で詳しく説明しています。

* **関連項目:** 「[Linux でのソフトウェア RAID の構成」](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) & 「[Azure で Linux VM の LVM を構成する](virtual-machines-linux-configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」


