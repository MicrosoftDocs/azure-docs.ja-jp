---
title: "Windows での SSH キーを使用した Linux VM への接続 | Microsoft Docs"
description: "Windows コンピューターで SSH キーを生成して使用し、Azure 上の Linux 仮想マシンに接続する方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 88351f0c3333fb06b1949a8ab9f63deea7259a41
ms.lasthandoff: 03/21/2017


---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Azure 上の Windows で SSH キーを使用する方法
> [!div class="op_single_selector"]
> * [Windows](virtual-machines-linux-ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> * [Linux と Mac](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
>
>

Azure で Linux 仮想マシン (VM) に接続するときは、[公開キー暗号化](https://wikipedia.org/wiki/Public-key_cryptography)を使用して、より安全な方法で Linux VM にログインできるようにする必要があります。 このプロセスでは、ユーザー名とパスワードを使用する代わりに、SSH (Secure Shell) コマンドを使用して公開キーと秘密キーを交換して、自分を認証します。 パスワードは、ブルートフォース攻撃に対して脆弱です。これは、特に Web サーバーなどのインターネットに接続された仮想マシンに当てはまります。 この記事では、SSH キーの概要と、Windows コンピューターで適切なキーを生成する方法について示します。

## <a name="overview-of-ssh-and-keys"></a>SSH とキーの概要
公開キーと秘密キーを使用することで、セキュリティで保護された方法で Linux VM にログインできます。

* **公開キー**は、Linux VM か、公開キー暗号化で使用する他のサービスに配置します。
* **秘密キー**は、Linux VM にログインするときに自分の身元を証明するために渡すキーです。 このキーは安全に保管してください。 このキーは共有しないようにしてください。

これらの公開キーと秘密キーは複数の VM とサービスで使用できます。 アクセスする VM またはサービスごとにキーのペアを用意する必要はありません。 詳細については、[公開キー暗号化](https://wikipedia.org/wiki/Public-key_cryptography)に関するページを参照してください。

SSH は、セキュリティで保護されていない接続においてセキュリティで保護されたログインを可能にする、暗号化された接続プロトコルです。 SSH は、Azure でホストされる Linux VM の既定の接続プロトコルです。 SSH 自体は暗号化された接続を提供しますが、SSH 接続でパスワードを使用すると、VM はブルートフォース攻撃やパスワードの推測に対して脆弱になります。 SSH を使用して VM に接続するためのより安全で推奨される方法は、これらの公開キーと秘密キー (SSH キーとも呼ばれます) を使用する方法です。

SSH キーを使用しない場合でも、パスワードを使用して Linux VM にログインできます。 VM がインターネットに公開されない場合は、パスワードを使用するだけで十分なことがあります。 ただし、Linux VM ごとにパスワードを管理し、パスワードの最小の長さ、定期的な更新など、優良なパスワードのポリシーと運用を維持する必要があります。 SSH キーを使用すると、複数の VM で別個に資格情報を管理する複雑さが軽減されます。

## <a name="windows-packages-and-ssh-clients"></a>Windows パッケージと SSH クライアント
Azure の Linux VM に接続して管理するには、**SSH クライアント**を使用します。 通常、Windows コンピューターには SSH クライアントがインストールされていません。 自分でインストールできる一般的な Windows SSH クライアントは次のパッケージに含まれています。

* [Git For Windows](https://git-for-windows.github.io/)
* [puTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

> [!NOTE]
> 最新の Windows 10 Anniversary Update には Bash for Windows が含まれています。 この機能を使用すると、Windows Subsystem for Linux を実行して、SSH クライアントなどのユーティリティにアクセスできます。 Bash for Windows は開発段階であり、ベータ リリースとして扱われています。 Bash for Windows の詳細については、「[Bash on Ubuntu on Windows](https://msdn.microsoft.com/commandline/wsl/about)」を参照してください。
>
>

## <a name="which-key-files-do-you-need-to-create"></a>作成する必要があるキー ファイル
Azure では、長さ 2,048 ビット以上の **ssh-rsa** 形式の公開キーと秘密キーを必須としています。 クラシック デプロイメント モデルを使用して Azure リソースを管理している場合は、PEM (`.pem` ファイル) も生成する必要があります。

次にデプロイメント シナリオとそれぞれのシナリオで使用されるファイルの種類を紹介します。

1. **ssh-rsa** キーは、[Azure Portal](https://portal.azure.com) を使用するすべてのデプロイと、[Azure CLI](../cli-install-nodejs.md) を使用する Resource Manager デプロイメントに必要です。
   * これらのキーは通常、ほとんどすべての人が必要とします。
2. `.pem` ファイルはクラシック デプロイで VM を作成する場合に必要です。 [Azure Portal](https://portal.azure.com) または [Azure CLI](../cli-install-nodejs.md) を使用する場合、クラシック デプロイでこれらのキーがサポートされます。
   * これらの追加のキーと証明書は、クラシック デプロイメント モデルを使用して作成されたリソースを管理している場合にのみ作成する必要があります。

## <a name="install-git-for-windows"></a>Git for Windows のインストール
前のセクションで、Windows 用の `openssl` ツールを含むいくつかのパッケージを紹介しました。 このツールは、公開キーと秘密キーを作成するために必要です。 任意のパッケージを選択できますが、ここでは **Git for Windows** をインストールして使用する方法を次の例に示します。 **Git for Windows** では、Linux VM を使用する際に役立つ追加のオープンソース ソフトウェア ([OSS](https://en.wikipedia.org/wiki/Open-source_software)) のツールとユーティリティにアクセスできます。

1. [https://git-for-windows.github.io/](https://git-for-windows.github.io/) から **Git for Windows** をダウンロードしてインストールします。
2. インストール プロセスでは、既定のオプションを使用します (ただし、変更する必要がある場合を除きます)。
3. **[スタート] メニュー** > **[Git]** > **[Git Bash]** を順に選択して **Git Bash** を実行します。 次のようなコンソールが表示されます。

    ![Git for Windows Bash シェル](./media/virtual-machines-linux-ssh-from-windows/git-bash-window.png)

## <a name="create-a-private-key"></a>秘密キーの作成
1. **Git Bash** ウィンドウで、`openssl.exe` を使用して秘密キーを作成します。 次の例では、`myPrivateKey` という名前のキーと `myCert.pem` という名前の証明書を作成しています。

    ```bash
    openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout myPrivateKey.key -out myCert.pem
    ```

    出力は次の例のようになります。

    ```bash
    Generating a 2048 bit RSA private key
    .......................................+++
    .......................+++
    writing new private key to 'myPrivateKey.key'
    -----
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:
    ```

   Bash によってエラーが報告された場合は、昇格された特権で新しい **Git Bash** ウィンドウを開いてみてください。 その後、`openssl` コマンドを再実行します。

2. プロンプトに対して、国の名前、場所、組織名などを入力します。
3. 新しい秘密キーと証明書が現在の作業ディレクトリに作成されます。 セキュリティのために、秘密キーにアクセス許可を設定して、自分だけがアクセスできるようにする必要があります。

    ```bash
    chmod 0600 myPrivateKey.key
    ```

4. [次のセクション](#create-a-private-key-for-putty)で、PuTTYgen を使用して公開キーの表示と使用を行う方法、および PuTTY を使用して SSH から Linux VM にログインするための秘密キーを作成する方法について詳しく説明します。 次のコマンドは、すぐに使用できる `myPublicKey.key` という名前の公開キー ファイルを生成します。

    ```bash
    openssl.exe rsa -pubout -in myPrivateKey.key -out myPublicKey.key
    ```

5. また、クラシック リソースを管理する必要がある場合は、`myCert.pem` を `myCert.cer` (DER でエンコードされた X509 証明書) に変換します。 このオプションの手順は、以前のクラシック リソースを管理する必要がある場合にのみ実行します。

    次のコマンドを使用して証明書を変換します。

    ```bash
    openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
    ```

## <a name="create-a-private-key-for-putty"></a>PuTTY 用の秘密キーの作成
PuTTY は、Windows 用の一般的な SSH クライアントです。 SSH クライアントには任意のクライアントを使用できます。 PuTTY を使用するには、PuTTY 秘密キー (PPK) という追加の種類のキーを作成する必要があります。 PuTTY を使用しない場合は、このセクションをスキップしてください。

次の例では、PuTTY を使用するためのこの追加の秘密キーを作成します。

1. **Git Bash** を使用して、秘密キーを PuTTYgen で認識できる RSA 秘密キーに変換します。 次の例では、既存のキー `myPrivateKey` から `myPrivateKey_rsa` という名前のキーを作成しています。

    ```bash
    openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
    ```

    セキュリティのために、秘密キーにアクセス許可を設定して、自分だけがアクセスできるようにする必要があります。

    ```bash
    chmod 0600 myPrivateKey_rsa
    ```
2. [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) から PuTTYgen をダウンロードして実行します。
3. **[File (ファイル)]** > **[Load a Private Key (秘密キーの読み込み)]** の順にクリックします。
4. 秘密キー (前の例の `myPrivateKey_rsa`) を見つけます。 **Git Bash** を起動したときの既定のディレクトリは `C:\Users\%username%` です。 ファイル フィルターを **[All Files (\*.\*) (すべてのファイル (*.*))]** に変更します。

    ![PuTTYgen に対する既存の秘密キーの読み込み](./media/virtual-machines-linux-ssh-from-windows/load-private-key.png)
5. **[開く]**をクリックします。 キーが正常にインポートされたことを示すプロンプトが表示されます。

    ![PuTTYgen に対するキーのインポートの成功](./media/virtual-machines-linux-ssh-from-windows/successfully-imported-key.png)
6. **[OK]** をクリックしてプロンプトを閉じます。
7. 公開キーは **PuTTYgen** ウィンドウの上部に表示されます。 Linux VM を作成するときに、この公開キーをコピーし、Azure Portal または Azure Resource Manager テンプレートに貼り付けます。 **[Save public key (公開キーを保存)]** をクリックして、公開キーを自分のコンピューターに保存することもできます。

    ![PuTTY 公開キー ファイルの保存](./media/virtual-machines-linux-ssh-from-windows/save-public-key.png)

    次の例は、Linux VM を作成するときにこの公開キーをコピーして Azure Portal に貼り付ける方法を示しています。 通常、公開キーは新しい VM の `~/.ssh/authorized_keys` に格納します。

    ![Azure Portal で VM を作成する際の公開キーの使用](./media/virtual-machines-linux-ssh-from-windows/use-public-key-azure-portal.png)
8. **PuTTYgen** で、**[Save private Key (秘密キーを保存)]** をクリックします。

    ![PuTTY 秘密キー ファイルの保存](./media/virtual-machines-linux-ssh-from-windows/save-ppk-file.png)

   > [!WARNING]
   > キーのパスフレーズを入力せずに続行するかどうかを確認するプロンプトが表示されます。 パスフレーズは、秘密キーに付属するパスワードのようなものです。 だれかが秘密キーを入手したとしても、キーだけでは認証されません。 パスフレーズも必要になります。 パスフレーズを使用しない場合、だれかが秘密キーを入手すると、その人物はそのキーを使用するすべての VM またはサービスにログインできるようになります。 そのため、パスフレーズを作成することをお勧めします。 ただし、パスフレーズを忘れた場合、それを回復する方法はありません。
   >
   >

    パスフレーズを入力する場合は、**[No (いいえ)]** をクリックした後、PuTTYgen のメイン ウィンドウにパスフレーズを入力し、もう一度 **[Save private Key (秘密キーを保存)]** をクリックします。 それ以外の場合は、**[Yes (はい)]** をクリックして、オプションのパスフレーズを指定せずに続行します。
9. PPK ファイルの保存場所と名前を入力します。

## <a name="use-putty-to-ssh-to-a-linux-machine"></a>Putty を使用した Linux マシンへの SSH 接続
前に説明したように、PuTTY は、Windows 用の一般的な SSH クライアントです。 SSH クライアントには任意のクライアントを使用できます。 次の手順では、秘密キーを使い、SSH を使用して Azure VM に対する認証を行う方法を示します。 他の SSH キー クライアントを使用する場合でも、SSH 接続を認証するために秘密キーを読み込む必要があるという点で、手順は似ています。

1. [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) から PuTTY をダウンロードして実行します。
2. Azure Portal で取得した VM のホスト名と IP アドレスを入力します。

    ![新しい PuTTY 接続の開始](./media/virtual-machines-linux-ssh-from-windows/putty-new-connection.png)
3. **[Open (開く)]** を選択する前に、**[Connection (接続)]** > **[SSH]** > **[Auth]** タブをクリックします。 秘密キーを参照して選択します。

    ![認証用の PuTTY 秘密キーの選択](./media/virtual-machines-linux-ssh-from-windows/putty-auth-dialog.png)
4. **[Open]** をクリックして、仮想マシンに接続します。

## <a name="next-steps"></a>次のステップ
公開キーと秘密キーは [OS X と Linux を使用して](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)生成することもできます。

Bash for Windows の詳細および Windows コンピューターで OSS ツールを使用できるようにしておくメリットについては、「[Bash on Ubuntu on Windows](https://msdn.microsoft.com/commandline/wsl/about)」を参照してください。

Linux VM に対する SSH 接続に問題がある場合は、[Azure Linux VM に対する SSH 接続のトラブルシューティング](virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するトピックを参照してください。

