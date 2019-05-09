---
title: Windows での SSH キーを使用した Linux VM への接続 | Microsoft Docs
description: Windows コンピューターで SSH キーを生成して使用し、Azure 上の Linux 仮想マシンに接続する方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: 0ac62a99f5735647f67917d441645e30444b3818
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58005678"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Azure 上の Windows で SSH キーを使用する方法

この記事では、Windows コンピューターで *Secure Shell* (SSH) キーを生成して使用し、Azure に Linux 仮想マシン (VM) を作成して接続する方法について説明します。 Linux または macOS のクライアントから SSH キーを使用する方法については、[クイック ガイダンス](mac-create-ssh-keys.md)または[詳細ガイダンス](create-ssh-keys-detailed.md)をご覧ください。

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Windows パッケージと SSH クライアント
Azure の Linux VM に接続して管理するには、*SSH クライアント*を使用します。 Linux または macOS を実行しているコンピューターには、通常、SSH キーを生成して管理し、SSH 接続を行うための、SSH コマンドのスイートがあります。 

Windows コンピューターでは、それに相当する SSH コマンドが常にインストールされることはありません。 最近のバージョンの Windows 10 には、コマンド プロンプトから SSH キーを作成および管理し、SSH 接続を行うことができる [OpenSSH クライアント コマンド](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/)が用意されています。 最近の Windows 10 バージョンには、SSH クライアントなどのユーティリティを Bash シェル内でネイティブに実行し、アクセスすることができる [Windows Subsystem for Linux](https://docs.microsoft.com/windows/wsl/about) も含まれています。 

ローカルにインストールできるその他の一般的な Windows SSH クライアントは、次のパッケージに含まれています。

* [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git For Windows](https://git-for-windows.github.io/)
* [MobaXterm](https://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

また、[Azure Cloud Shell](../../cloud-shell/overview.md) の Bash で入手できる SSH ユーティリティを使用することもできます。 

* Web ブラウザーで ([https://shell.azure.com](https://shell.azure.com))、または [Azure Portal](https://portal.azure.com) で、Cloud Shell にアクセスします。 
* [Azure Account 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)をインストールすることにより、Visual Studio Code 内からターミナルとして Cloud Shell にアクセスします。

## <a name="create-an-ssh-key-pair"></a>SSH キー ペアの作成
以下のセクションでは、Windows で SSH キー ペアを作成する 2 つのオプションについて説明します。 シェル コマンド (`ssh-keygen`) または GUI ツール (PuTTYgen) を使用できます。

### <a name="create-ssh-keys-with-ssh-keygen"></a>ssh-keygen で SSH キーを作成する

Windows で SSH クライアント ツールをサポートするコマンド シェルを実行する (または Azure Cloud Shell を使用する) 場合は、`ssh-keygen` コマンドを使用して SSH キーペアを作成します。 次のコマンドを入力して、プロンプトに答えます。 選択した場所に SSH キー ペアがある場合、それらのファイルは上書きされます。 

```bash
ssh-keygen -t rsa -b 2048
```

背景情報と詳細情報については、`ssh-keygen` を使用して SSH キーを作成する手順の[概要](mac-create-ssh-keys.md)または[詳細](create-ssh-keys-detailed.md)を参照してください。

### <a name="create-ssh-keys-with-puttygen"></a>PuTTYgen で SSH キーを作成する

GUI ベースのツールを使って SSH キーを作成する場合は、[PuTTY のダウンロード パッケージ](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)に含まれる PuTTYgen キー ジェネレーターを使うことができます。 

PuTTYgen で SSH RSA キー ペアを作成するには:

1. PuTTYgen を開始します。

2. **[Generate]\(生成\)** をクリックします。 既定では、PuTTYgen は 2048 ビットの SSH-2 RSA キーを生成します。

4. 空白領域内でマウスを動かすと、キーがランダムになります。

5. 公開キーが生成された後、必要に応じて、パスフレーズを入力して確認します。 プライベート SSH キーを使用して VM の認証を受けると、パスフレーズの入力を求められます。 パスフレーズを使用しない場合、だれかが秘密キーを入手すると、その人物はそのキーを使用するすべての VM またはサービスにサインインできるようになります。 そのため、パスフレーズを作成することをお勧めします。 ただし、パスフレーズを忘れた場合、それを回復する方法はありません。

6. 公開キーがウィンドウの上部に表示されます。 Linux VM を作成するときに、この公開キー全体をコピーし、Azure portal または Azure Resource Manager テンプレートに貼り付けます。 **[Save public key]\(公開キーの保存\)** をクリックして、公開キーを自分のコンピューターに保存することもできます。

    ![PuTTY 公開キー ファイルの保存](./media/ssh-from-windows/save-public-key.png)

7. PuTTy の秘密キー形式 (.ppk ファイル) で秘密キーを保存する必要がある場合は、**[Save private key]\(秘密キーの保存\)** を選択します。 後で PuTTY を使用して VM への SSH 接続を行うときに .ppk ファイルが必要になります。

    ![PuTTY 秘密キー ファイルを保存する](./media/ssh-from-windows/save-ppk-file.png)

    多くの SSH クライアントによって使われる秘密キーの形式である OpenSSH 形式で秘密キーを保存する場合は、**[変換]** > **[Export OpenSSH key]\(OpenSSH キーのエクスポート\)** を選択します。

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>VM をデプロイするときに SSH 公開キーを提供する

認証に SSH キーを使う Linux VM を作成するには、Azure Portal または他の方法を使って VM を作成するときに、SSH 公開キーを提供します。

次の例は、Linux VM を作成するときにこの公開キーをコピーして Azure Portal に貼り付ける方法を示しています。 通常、公開キーは新しい VM 上の ~/.ssh/authorized_key ディレクトリに格納されます。

   ![Azure Portal で VM を作成する際の公開キーの使用](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>VM に接続する

Windows から Linux VM に SSH 接続する方法の 1 つは、SSH クライアントを使うことです。 Windows システムに SSH クライアントがインストールされている場合、または Azure Cloud Shell の Bash で SSH ツールを使っている場合は、これが推奨される方法です。 GUI ベースのツールを使う場合は、PuTTY で接続することができます。  

### <a name="use-an-ssh-client"></a>SSH クライアントを使用する
公開キーを Azure VM に、秘密キーをローカル システム上に配置した状態で、VM の IP アドレスまたは DNS 名を使用して、VM に SSH 接続します。 次のコマンドの *azureuser* と *myvm.westus.cloudapp.azure.com* を、管理者のユーザー名と完全修飾ドメイン名 (または IP アドレス) に置き換えてください。

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

キー ペアを作成するときにパスフレーズを構成した場合は、サインイン プロセス中に入力を求められたら、そのパスフレーズを入力します。

VM が Just-In-Time アクセス ポリシーを使用している場合、VM に接続するにはアクセス権を要求する必要があります。 Just-In-Time ポリシーの詳細については、[Just in Time ポリシーを使用した仮想マシン アクセスの管理](../../security-center/security-center-just-in-time.md)に関するページを参照してください。

### <a name="connect-with-putty"></a>PuTTY を使用して接続する

[PuTTY のダウンロード パッケージ](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)をインストールし、前に PuTTY の秘密キー (.ppk) ファイルを生成した場合は、PuTTY を使って Linux VM に接続することができます。

1. PuTTY を起動します。

2. Azure Portal で取得した VM のホスト名と IP アドレスを入力します。

    ![新しい PuTTY 接続の開始](./media/ssh-from-windows/putty-new-connection.png)

3. **[接続]** > **[SSH]** > **[認証]** カテゴリを選択します。 PuTTY の秘密キー (.ppk ファイル) を参照して選択します。

    ![認証用の PuTTY 秘密キーを選択する](./media/ssh-from-windows/putty-auth-dialog.png)

4. **[Open]\(開く\)** をクリックして、VM に接続します。

## <a name="next-steps"></a>次の手順

* SSH キーの使用に関する詳細な手順、オプション、および高度な例については、[SSH キー ペアの詳細な作成手順](create-ssh-keys-detailed.md)に関するページを参照してください。

* Azure Cloud Shell 内で PowerShell を使用して SSH キーを生成し、Linux VM に SSH 接続することもできます。 [PowerShell のクイック スタート](../../cloud-shell/quickstart-powershell.md#ssh)に関するページをご覧ください。

* Linux VM に対する SSH 接続に問題がある場合は、[Azure Linux VM に対する SSH 接続のトラブルシューティング](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するトピックを参照してください。
