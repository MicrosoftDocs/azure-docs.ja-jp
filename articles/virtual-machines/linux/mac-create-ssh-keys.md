---
title: "Azure での Linux VM 用の SSH キー ペアの作成と使用 | Microsoft Docs"
description: "Azure に Linux VM 用の SSH 公開キーと秘密キーのペアを作成し、認証プロセスのセキュリティを向上させる方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/07/2017
ms.author: iainfou
experimental: true
experiment_id: rasquill-ssh-20170308
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 4316f0690ac0941521f84d92f62b4fc3f42f76e0
ms.lasthandoff: 04/03/2017


---

# <a name="how-to-create-and-use-an-ssh-public-and-private-key-pair-for-linux-vms-in-azure"></a>Azure に Linux VM 用の SSH 公開キーと秘密キーのペアを作成して使用する方法
Secure Shell (SSH) キー ペアを使用すると、認証に SSH キーを使う仮想マシン (VM) を Azure に作成でき、ログインするためのパスワードが不要になります。 この記事では、Linux VM 用に SSH プロトコル バージョン 2 RSA の公開キー ファイルと秘密キー ファイルのペアを短時間で生成し、使用する方法について説明します。 証明書を生成する詳細な手順と、クラシック ポータルで使用する証明書などの例については、[SSH キー ペアと証明書を作成するための詳細な手順](create-ssh-keys-detailed.md)に関するページをご覧ください。

## <a name="create-an-ssh-key-pair"></a>SSH キー ペアの作成
`ssh-keygen` コマンドを使用して、SSH 公開キー ファイルと秘密キー ファイルを作成できます。ファイルは既定で `~/.ssh` ディレクトリに作成されますが、プロンプトが表示されたときに別の場所や追加のパスフレーズ (秘密キー ファイルにアクセスするためのパスワード) を指定できます。 これを行うには、Bash シェルから次のコマンドを実行し、使用したい情報をプロンプトで指定します。

```bash
ssh-keygen -t rsa -b 2048 
```

## <a name="use-the-ssh-key-pair"></a>SSH キー ペアの使用
Azure の Linux VM に配置した公開キーは、既定で `~/.ssh/id_rsa.pub` に格納されますが、VM の作成時にこの場所を変更することができます。 [Azure CLI 2.0](/cli/azure) を使用して VM を作成する場合は、[az vm create](/cli/azure/vm#create) を実行する際に `--ssh-key-path` オプションを使って、この公開キーの場所を指定します。 公開キー ファイルの内容をコピーし、Azure Portal または Resource Manager テンプレートに貼り付けて使用する場合は、余分な空白スペースをコピーしないように注意してください。 たとえば、OS X を使用している場合は、公開キー ファイル (既定では **~/.ssh/id_rsa.pub**) を **pbcopy** にパイプして、内容をコピーできます (`xclip` など、同じ目的を達成できる Linux プログラムが他にもあります)。 

SSH 公開キーがわからない場合は、次のように `cat` を実行して公開キーを表示できます。`~/.ssh/id_rsa.pub` の部分は実際の公開キー ファイルの場所に置き換えてください。

```bash
cat ~/.ssh/id_rsa.pub
```

公開キーを Azure VM に配置できたら、VM の IP アドレスまたは DNS 名を使用して SSH で VM に接続します (下記の `azureuser` と `myvm.westus.cloudapp.azure.com` は、管理者のユーザー名と、完全修飾ドメイン名または IP アドレスに置き換えてください)。

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

キー ペアを作成する際にパスフレーズを指定した場合は、ログイン プロセス中に入力を求められたら、そのパスフレーズを入力します  (サーバーは `~/.ssh/known_hosts` フォルダーに追加されます。Azure VM にある公開キーが変更されるかサーバー名が `~/.ssh/known_hosts` から削除されるまで、再度接続を求められることはありません)。

## <a name="next-steps"></a>次のステップ

SSH キーを使用して作成された VM は、既定ではパスワードが無効にされます。この措置により、推測によるブルート フォース攻撃はコストが非常に高くつき、実行するのが難しくなります。 このトピックでは、すぐに使えるように単純な SSH キー ペアを作成する方法について説明しました。 SSH キー ペアの作成に関する詳しい説明や、クラシック ポータルで使用する証明書については、[SSH キー ペアと証明書を作成するための詳細な手順](create-ssh-keys-detailed.md)に関するページをご覧ください。

SSH キー ペアを使用する VM の作成には、Azure Portal、CLI、およびテンプレートを使用できます。

* [Azure ポータルを使用して安全な Linux VM を作成する](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLI 2.0 を使用して安全な Linux VM を作成する](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure テンプレートを使用して安全な Linux VM を作成する](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

