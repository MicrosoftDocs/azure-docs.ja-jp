---
title: SSH キーを使用して Linux VM に接続する
description: SSH キーを生成し、それを使用して Windows コンピューターから、Azure 上の Linux 仮想マシンに接続する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.date: 10/31/2020
ms.topic: how-to
ms.author: cynthn
ms.openlocfilehash: f018f591052050431996e3017335ab003973d25a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771007"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Azure 上の Windows で SSH キーを使用する方法

この記事は、*secure shell* (SSH) キーを [作成](#create-an-ssh-key-pair)し、それを使用して Azure の Linux 仮想マシン (VM) に [接続](#connect-to-your-vm)する Windows ユーザーを対象としています。 また、[Azure portal で SSH キーを生成および保存](../ssh-keys-portal.md)して、このポータルで VM を作成するときに使用することもできます。


Linux または macOS クライアントから SSH キーを使用するには、[簡単な手順](mac-create-ssh-keys.md)を参照してください。 SSH の詳細については、「[詳細な手順: Azure の Linux VM に対する認証用に SSH キーを作成して管理する](create-ssh-keys-detailed.md)」を参照してください。

## <a name="overview-of-ssh-and-keys"></a>SSH とキーの概要

[SSH](https://www.ssh.com/ssh/) は、セキュリティで保護されていない接続においてセキュリティで保護されたサインインを可能にする、暗号化された接続プロトコルです。 SSH は、Azure でホストされる Linux VM の既定の接続プロトコルです。 SSH 自体は暗号化された接続を提供しますが、SSH でパスワードを使用すると、VM はブルートフォース攻撃に対して脆弱になります。 SSH を介して VM に接続する場合は、公開/秘密キーの組を使用することをお勧めします。これは "*SSH キー*" とも呼ばれています。 

公開/秘密キーの組は、ご利用のフロント ドアのロックに似ています。 ロックは **パブリック** に公開され、適切なキーを持つユーザーがドアを開くことができます。 キーは **非公開** であり、ドアのロックを解除するために使用できるため、信頼できるユーザーにのみ与えられます。 

- "*公開キー*" は、Linux VM の作成時にその VM 上に配置されます。 

- *秘密キー* は、ローカル システム上に残ります。 このキーは安全に保管してください。 このキーは共有しないようにしてください。

ご利用の Linux VM に接続する場合、その VM によって SSH クライアントのテストが行われ、適切な秘密キーを持っているかどうかが確認されます。 クライアントに秘密キーがある場合、VM へのアクセス権が付与されます。 

組織のセキュリティ ポリシーに応じて、単一のキーの組を再利用して複数の Azure VM およびサービスにアクセスできます。 VM ごとに異なるキーの組を用意する必要はありません。 

ご自分の公開キーはだれとでも共有できますが、ご自分の秘密キーへのアクセス権を所有するのは自分 (またはローカル セキュリティ インフラストラクチャ) のみとする必要があります。

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-clients"></a>SSH クライアント

最近のバージョンの Windows 10 には、[OpenSSH クライアント コマンド](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/)が用意されています。これを使用すれば、PowerShell またはコマンド プロンプトから SSH キーを作成および使用して、SSH 接続を行うことができます。 Windows コンピューターから Linux VM への SSH 接続を作成する方法としては、これが最も簡単です。 

また、[Azure Cloud Shell](../../cloud-shell/overview.md) 内の Bash を使用して VM に接続することもできます。 Cloud Shell は、[Azure portal](https://portal.azure.com) から、[Web ブラウザー](https://shell.azure.com/bash)内で使用することも、[Azure アカウント拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)を使用して Visual Studio Code のターミナルとして使用することもできます。

また、[Linux 用 Windows サブシステム](/windows/wsl/about)をインストールして、SSH 経由で VM に接続し、Bash シェル内で他のネイティブ Linux ツールを使用することもできます。

## <a name="create-an-ssh-key-pair"></a>SSH キー ペアの作成

`ssh-keygen` コマンドを使用して、SSH キーの組を作成します。 ファイル名を入力するか、かっこで示されている既定値を使用します (`C:\Users\username/.ssh/id_rsa` など)。  ファイルのパスフレーズを入力します。パスフレーズを使用しない場合はパスフレーズを空白のままにします。 

```powershell
ssh-keygen -m PEM -t rsa -b 4096
```

## <a name="create-a-vm-using-your-key"></a>独自のキーを使用して VM を作成する

認証で SSH キーを使用する Linux VM を作成するには、VM を作成するときに、自分の SSH 公開キーを指定してください。

Azure CLI を使用する場合は、`az vm create` および `--ssh-key-value` パラメーターを使用して、公開キーのパスとファイル名を指定します。

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVM \
   --image UbuntuLTS\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa
```

PowerShell では、`New-AzVM` を使用し、' を使用して SSH キーを VM 構成に追加します。 例については、「[クイックスタート: PowerShell を使用して Azure に Linux 仮想マシンを作成する](quick-create-powershell.md)」を参照してください。

ポータルを使用して多数のデプロイを実行する場合は、Azure に自分の公開キーをアップロードすることをお勧めします。そうすれば、ポータルから VM を作成するときに簡単に選択できます。 詳細については、「[SSH キーのアップロード](../ssh-keys-portal.md#upload-an-ssh-key)」を参照してください。


## <a name="connect-to-your-vm"></a>VM に接続する

公開キーを Azure VM に、秘密キーをローカル システム上に配置した状態で、VM の IP アドレスまたは DNS 名を使用して、VM に SSH 接続します。 次のコマンドの *azureuser* と *10.111.12.123* を、管理者のユーザー名、IP アドレス (または完全修飾ドメイン名)、および自分の秘密キーへのパスに置き換えてください。

```bash
ssh -i ~/.ssh/id_rsa.pub azureuser@10.111.12.123
```

キーの組を作成したときにパスフレーズを構成してある場合、入力を求められたら、そのパスフレーズを入力します。

VM が Just-In-Time アクセス ポリシーを使用している場合、VM に接続するにはアクセス権を要求する必要があります。 Just-In-Time ポリシーの詳細については、[Just in Time ポリシーを使用した仮想マシン アクセスの管理](../../security-center/security-center-just-in-time.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

- Azure portal での SSH キーの詳細については、ポータルで VM を作成するときに使用する「[Azure portal で SSH キーを生成して保存する](../ssh-keys-portal.md)」を参照してください。

- SSH キーの使用に関する詳細な手順、オプション、および高度な例については、[SSH キー ペアの詳細な作成手順](create-ssh-keys-detailed.md)に関するページを参照してください。

- Azure Cloud Shell 内で PowerShell を使用して SSH キーを生成し、Linux VM に SSH 接続することもできます。 [PowerShell のクイック スタート](../../cloud-shell/quickstart-powershell.md#ssh)に関するページをご覧ください。

- Linux VM に対する SSH 接続に問題がある場合は、[Azure Linux VM に対する SSH 接続のトラブルシューティング](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するトピックを参照してください。
