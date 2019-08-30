---
title: Azure での Linux VM 用の SSH キー ペアの作成と使用 | Microsoft Docs
description: Azure 内に Linux VM 用の SSH 公開キーと秘密キーのペアを作成し、認証プロセスのセキュリティを向上させる方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/11/2018
ms.author: cynthn
ms.openlocfilehash: e66c0d2cf16733b4350366d87e9fd67d7f645b38
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082960"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>簡単な手順: Azure 内に Linux VM 用の SSH 公開/秘密キーのペアを作成して使用する

Secure Shell (SSH) キー ペアを使用すると、認証に SSH キーを使う仮想マシン (VM) を Azure に作成でき、サインインするためのパスワードが不要になります。 この記事では、Linux VM 用の SSH 公開キー ファイルと秘密キー ファイルのペアを短時間で生成して使用する方法について説明します。 これらの手順は、Azure Cloud Shell、macOS または Linux ホスト、Windows Subsystem for Linux、および OpenSSH をサポートするその他のツールを使用して完了できます。 

> [!NOTE]
> 既定では、SSH キーを使用して作成された VM は、パスワードが無効にされます。そのため、推測によるブルート フォース攻撃が大幅に困難になります。 

詳しい背景と例については、[SSH キー ペアを作成するための詳細な手順](create-ssh-keys-detailed.md)に関するページを参照してください。

Windows コンピューター上で、SSH キーを生成して使用するその他の方法については、「[Azure 上の Windows で SSH キーを使用する方法](ssh-from-windows.md)」を参照してください。

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>SSH キー ペアの作成

`ssh-keygen` コマンドを使用して、SSH 公開キーと秘密キーのファイルを生成します。 既定では、これらのファイルは ~/.ssh ディレクトリに作成されます。 秘密キー ファイルにアクセスするには、別の場所、および任意のパスワード (*パスフレーズ*) を指定できます。 同じ名前の SSH キー ペアが指定された場所にある場合、それらのファイルは上書きされます。

次のコマンドでは、RSA 暗号化と 2048 ビット長を使用して SSH キー ペアが作成されます。

```bash
ssh-keygen -t rsa -b 2048
```

[Azure CLI](/cli/azure) を使用して [az vm create](/cli/azure/vm#az-vm-create) コマンドで VM を作成する場合は、必要に応じて `--generate-ssh-keys` オプションを使用して、SSH 公開キー ファイルと秘密キー ファイルを生成できます。 `--ssh-dest-key-path` オプションで指定されない限り、キー ファイルは ~/.ssh ディレクトリに格納されます。 `--generate-ssh-keys` オプションでは、既存のキー ファイルを上書きすることはありません。代わりにエラーを返します。 次のコマンドで、*VMname* と *RGname* を独自の値に置き換えます。

```azurecli
az vm create --name VMname --resource-group RGname --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>VM をデプロイするときに SSH 公開キーを提供する

認証するために SSH キーを使用する Linux VM を作成するには、Azure portal、Azure CLI、Azure Resource Manager テンプレート、またはその他の方法を使用して VM を作成するときに SSH 公開キーを指定します。

* [Azure Portal で Linux 仮想マシンを作成する](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLI で Linux 仮想マシンを作成する](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure テンプレートを使用して Linux VM を作成する](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

SSH 公開キーの形式になじみのない場合は、次の `cat` コマンドでご利用の公開キーを表示できます。必要に応じて、`~/.ssh/id_rsa.pub` を独自の公開キー ファイルのパスとファイル名に置き換えます。

```bash
cat ~/.ssh/id_rsa.pub
```

一般的な公開キーの値は次の例のようになります。

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

公開キー ファイルの内容をコピーし、Azure portal または Resource Manager テンプレートに貼り付けて使用する場合は、行末の空白スペースをコピーしないように注意してください。 macOS で公開キーをコピーするには、公開キー ファイルを **pbcopy** にパイプすることができます。 Linux と同様に、**xclip** などのプログラムに公開キー ファイルをパイプすることができます。

キー ペアの作成時に別の場所を指定しない限り、Azure 内の Linux VM 上に配置した公開キーは、既定で ~/.ssh/id_rsa.pub に格納されます。 [Azure CLI 2.0](/cli/azure) を使用して既存の公開キーで VM を作成するには、[az vm create](/cli/azure/vm#az-vm-create) コマンドを `--ssh-key-value` オプション付きで使用することで、この公開キーの値または任意で場所を指定します。 次のコマンドで、*VMname*、*RGname*、*keyFile* を独自の値に置き換えます。

```azurecli
az vm create --name VMname --resource-group RGname --ssh-key-value @keyFile
```

## <a name="ssh-into-your-vm"></a>VM への SSH 接続

公開キーを Azure VM に、秘密キーをローカル システム上にデプロイした状態で、ご利用の VM の IP アドレスまたは DNS 名を使用して、VM に SSH 接続します。 次のコマンドの *azureuser* と *myvm.westus.cloudapp.azure.com* を、管理者のユーザー名と完全修飾ドメイン名 (または IP アドレス) に置き換えてください。

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

キー ペアを作成するときにパスフレーズを指定した場合は、ログイン プロセス中に入力を求められたら、そのパスフレーズを入力します。 VM は ~/.ssh/known_hosts ファイルに追加されます。Azure VM にある公開キーが変更されるか、サーバー名が ~/.ssh/known_hosts から削除されるまで、再度接続を求められることはありません。

VM が Just-In-Time アクセス ポリシーを使用している場合、VM に接続するにはアクセス権を要求する必要があります。 Just-In-Time ポリシーの詳細については、[Just in Time ポリシーを使用した仮想マシン アクセスの管理](../../security-center/security-center-just-in-time.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

* SSH キー ペアを操作する詳細については、[SSH キー ペアを作成して管理する詳細な手順](create-ssh-keys-detailed.md)に関するページを参照してください。

* Azure VM への SSH 接続が困難な場合は、[Azure Linux VM への SSH 接続に関するトラブルシューティング](troubleshoot-ssh-connection.md)に関するページを参照してください。
