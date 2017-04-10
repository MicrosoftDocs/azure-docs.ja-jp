---
title: "Jupyter/IPython Notebook を作成する | Microsoft Docs"
description: "Azure でクラシック デプロイ モデルを使用して作成した Linux 仮想マシンに Jupyter/IPython Notebook をデプロイする方法について説明します。"
services: virtual-machines-linux
documentationcenter: python
author: crwilcox
manager: wpickett
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 519f36dd-865e-4c1d-abe7-b87037796aa7
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: python
ms.topic: article
ms.date: 11/10/2015
ms.author: crwilcox
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 652c4c51d67b8914885406e631e7233694a8a1d8
ms.openlocfilehash: ad53cb9bb58064aeed49cb05fdcb936e408346d2
ms.lasthandoff: 03/01/2017

---
# <a name="creating-an-azure-vm-installing-jupyter-and-running-a-jupyter-notebook-on-azure"></a>Azure 上で Azure VM を作成し、Jupyter をインストールして、Jupyter Notebook を実行する
[Jupyter プロジェクト](http://jupyter.org) (旧 [IPython プロジェクト](http://ipython.org)) は、コードの実行と計算ドキュメントのライブ作成を組み合わせた強力な対話型シェルを使用する科学計算用ツールのコレクションを提供します。 作成されるノートブック ファイルには、任意のテキスト、数式、入力コード、結果、グラフィックス、ビデオ、その他の種類のメディアなど、最新の Web ブラウザーで表示できるデータを含めることができます。 Python を初めて利用し、対話形式の環境で楽しく Python を学習する場合でも、本格的な並列計算や技術計算をする場合でも、Jupyter Notebook はお勧めの選択肢です。

![スクリーンショット](./media/virtual-machines-linux-jupyter-notebook/ipy-notebook-spectral.png)SciPy パッケージと Matplotlib パッケージを使用した録音の構造分析。

## <a name="jupyter-two-ways-azure-notebooks-or-custom-deployment"></a>Jupyter を利用する&2; つの方法: Azure Notebook またはカスタム デプロイ
Azure には、 [Jupyter の使用をすぐに開始する ](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx)ために使用できるサービスが用意されています。  Azure Notebook サービスを使用すると、Web でアクセスできる Jupyter のインターフェイスにアクセスして、スケーラブルなコンピューティング リソース、Jupyter のすべての機能、および多数のライブラリを簡単に利用できます。  インストールはサービスによって処理されるため、ユーザーは、自分で構成や管理を実行することなく、これらのリソースにアクセスできます。

Notebook サービスが自分のシナリオに合っていない場合でも、この記事を読み進めてください。仮想マシン (VM) を使用して Jupyter Notebook を Microsoft Azure にデプロイする方法の説明があります。

[!INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="create-and-configure-a-vm-on-azure"></a>Azure での VM の作成と構成
最初の手順は Azure で動作する仮想マシン (VM) を作成することです。
この VM は、クラウド内の完全なオペレーティング システムであり、Jupyter Notebook を実行するために使用されます。 Azure では Linux と Windows のどちらの仮想マシンも実行できます。ここでは両方の仮想マシン上での Jupyter のセットアップについて説明します。

### <a name="create-a-linux-vm-and-open-a-port-for-jupyter"></a>Linux VM を作成し、Jupyter 用のポートを開く
[ここ][portal-vm-linux]に記載されている手順に従って、*Ubuntu* ディストリビューションの仮想マシンを作成します。 このチュートリアルでは、Ubuntu Server 14.04 LTS を使用します。 ユーザー名は *azureuser*を想定しています。

仮想マシンをデプロイした後、ネットワーク セキュリティ グループに関するセキュリティ規則を開く必要があります。  Azure Portal から **[ネットワーク セキュリティ グループ]** に移動し、VM に対応するセキュリティ グループのタブを開きます。 次の設定の受信セキュリティ規則を追加する必要があります。プロトコル: **TCP**、ソース (パブリック) ポート: **\***、および宛先 (プライベート) ポート: **9999**。

![スクリーンショット](./media/virtual-machines-linux-jupyter-notebook/azure-add-endpoint.png)

ネットワーク セキュリティ グループの表示中に、**[ネットワーク インターフェイス]** をクリックして、**[パブリック IP アドレス]** を書き留めます。これは、次の手順で VM に接続するために必要です。

## <a name="install-required-software-on-the-vm"></a>VM への必要なソフトウェアのインストール
VM で Jupyter Notebook を実行するには、まず Jupyter とそれに依存するソフトウェアをインストールする必要があります。 ssh と、VM の作成時に選択したユーザー名とパスワードを使用して、Linux VM に接続します。 このチュートリアルでは、PuTTY を使用して Windows から接続します。

### <a name="installing-jupyter-on-ubuntu"></a>Ubuntu への Jupyter のインストール
一般的なデータ サイエンス向けの Python ディストリビューションである Anaconda を、次のいずれかのリンクを使用して [Continuum Analytics](https://www.continuum.io/downloads)からインストールします。  このドキュメントの執筆時点では、次に示すリンクで最新バージョンを入手できます。

#### <a name="anaconda-installs-for-linux"></a>Linux 用の Anaconda のインストール
<table>
  <th>Python 3.4</th>
  <th>Python 2.7</th>
  <tr>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh'>64 ビット</href>
    </td>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86_64.sh'>64 ビット</href>
    </td>
  </tr>
  <tr>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86.sh'>32 ビット</href>
    </td>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86.sh'>32 ビット</href>
    </td>  
  </tr>
</table>


#### <a name="installing-anaconda3-230-64-bit-on-ubuntu"></a>Ubuntu への Anaconda3 2.3.0 64 ビット版のインストール
例として、Anaconda を Ubuntu にインストールする方法を次に示します。

    # install anaconda
    cd ~
    mkdir -p anaconda
    cd anaconda/
    curl -O https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh
    sudo bash Anaconda3-2.3.0-Linux-x86_64.sh -b -f -p /anaconda3

    # clean up home directory
    cd ..
    rm -rf anaconda/

    # Update Jupyter to the latest install and generate its config file
    sudo /anaconda3/bin/conda install jupyter -y
    /anaconda3/bin/jupyter-notebook --generate-config


![スクリーンショット](./media/virtual-machines-linux-jupyter-notebook/anaconda-install.png)

### <a name="configuring-jupyter-and-using-ssl"></a>Jupyter の構成と SSL の使用
インストールした後、Jupyter 用の構成ファイルをセットアップする必要があります。 Jupyter の構成で問題が発生した場合は、 [Notebook サーバーを実行するための Jupyter ドキュメント](http://jupyter-notebook.readthedocs.org/en/latest/public_server.html)を参照してください。

次に、プロファイル ディレクトリに移動して ( `cd` コマンドの使用)、SSL 証明書を作成し、プロファイルの構成ファイルを編集します。

Linux で次のコマンドを使用します。

    cd ~/.jupyter

次のコマンドを使用し、SSL 証明書を作成します (Linux と Windows)。

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

ここでは自己署名 SSL 証明書を作成しているため、ノートブックに接続するときに、ブラウザーでセキュリティに関する警告が表示されます。  長期間にわたって製品を使用するには、組織に関連付けられている適切に署名された証明書を使用する必要があります。  証明書の管理はこのデモの範囲外のため、ここでは自己署名証明書を引き続き使用します。

証明書を使用するだけでなく、許可されてない使用からノートブックを保護するためにパスワードを指定する必要もあります。  セキュリティ上の理由から、Jupyter は暗号化されたパスワードを構成ファイルで使用するため、最初にパスワードを暗号化する必要があります。  IPython には暗号化のためのユーティリティが用意されています。コマンド プロンプトで次のコマンドを実行します。

    /anaconda3/bin/python -c "import IPython;print(IPython.lib.passwd())"

パスワードを入力して確認した後、パスワードが出力されます。 次の手順のために、これを書き留めてください。

    Enter password:
    Verify password:
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

次に、プロファイルの構成ファイルを編集します。このファイルは、現在のディレクトリにある `jupyter_notebook_config.py` ファイルです。  このファイルは存在しないことがあります。その場合は作成してください。  このファイルにはさまざまなフィールドが含まれ、既定ではいずれもコメント アウトされています。  このファイルは、よく使用するテキスト エディターで開くことができます。少なくとも次の内容が含まれていることを確認してください。 **構成ファイル内の c.NotebookApp.password は、前の手順で取得した sha1 に必ず置き換えてください**。

    c = get_config()

    # You must give the path to the certificate file.
    c.NotebookApp.certfile = u'/home/azureuser/.jupyter/mycert.pem'

    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '

    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed traffic on that port
    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### <a name="run-the-jupyter-notebook"></a>Jupyter Notebook を実行する
この時点で、Jupyter Notebook を開始する準備が整いました。 Jupyter Notebook を実行するには、ノートブックを保存するディレクトリに移動し、次のコマンドを使用して Jupyter Notebook サーバーを起動します。

    /anaconda3/bin/jupyter-notebook

これで、アドレス `https://[PUBLIC-IP-ADDRESS]:9999` で Jupyter Notebook にアクセスできます。

ノートブックに初めてアクセスするとき、ログイン ページでパスワードが要求されます。 ログインすると、Jupyter Notebook ダッシュボードが表示されます。これは、ノートブックに関するすべての操作のコントロール センターとなります。  このページから、新しいノートブックを作成し、既存のノートブックを開くことができます。

![スクリーンショット](./media/virtual-machines-linux-jupyter-notebook/jupyter-tree-view.png)

### <a name="using-the-jupyter-notebook"></a>Jupyter Notebook の使用
**[New]** ボタンをクリックすると、次のオープニング ページが表示されます。

![スクリーンショット](./media/virtual-machines-linux-jupyter-notebook/jupyter-untitled-notebook.png)

`In []:` プロンプトがある領域に適切な Python コードを入力します。`Shift-Enter` キーを押すか、[実行] アイコン (ツール バーにある右向き三角形) をクリックすると、ここに入力したコードが実行されます。

## <a name="a-powerful-paradigm-live-computational-documents-with-rich-media"></a>強力なパラダイム: リッチ メディアを含んだリアルタイムなコンピューティング ドキュメント
ノートブック自体は、Python とワード プロセッサを使用したことがあるユーザーにとっては違和感のないドキュメントです。これは、Python とワード プロセッサの両方の特徴を持っているためです。ノートブックでは、Python コードのブロックを実行できます。また、ツール バーのドロップダウン メニューでセルの形式を [Code] から [Markdown] に変更して、メモなどのテキストを保存することもできます。

Jupyter はワード プロセッサよりも優れています。Jupyter では、計算処理とリッチ メディア (テキスト、グラフィックス、ビデオなど、最新の Web ブラウザーで表示できるほぼすべてのデータ) を混在させることができます。 テキスト、コード、ビデオなどを混在させることができます。

![スクリーンショット](./media/virtual-machines-linux-jupyter-notebook/jupyter-editing-experience.png)

Python に関連する多数の優れた科学技術計算用ライブラリを使用することで、次のスクリーンショットのように、1 つの環境で複雑なネットワーク分析も単純な計算も同じように簡単に実行することができます。

最新の Web の機能とリアルタイムな計算処理を混在させるというパラダイムは、多くの可能性を示すものです。このパラダイムは、クラウドに適した理想的なパラダイムです。Notebook の用途には次のようなものがあります。

* 問題に対する調査作業を記録するためのコンピューティング スクラッチパッドとして使用。
* "リアルタイムな" コンピューティング フォームまたはハードコピーの形式 (HTML や PDF) で、同僚と作業結果を共有するために使用。
* 計算処理を含んだライブ教材の配布や提示のために使用。これにより、生徒はすぐに実際のコードを試行したり、変更したり、あるいは対話形式で再実行したりすることができます。
* 直ちに再現したり、検証したり、他のユーザーが拡張したりできる方法で調査結果を示す "実行可能な用紙" を提供するために使用。
* コラボレーション コンピューティング用のプラットフォームとして使用。複数のユーザーが 同じ Notebook サーバーにログインして、ライブのコンピューティング セッションを共有できます。

IPython のソース コード [リポジトリ][repository]にアクセスすると、ノートブックのサンプルが保存されているディレクトリ全体を確認できます。これらのサンプルをダウンロードして、ユーザー独自の Azure Jupyter VM で試用することができます。  このサイトから `.ipynb` ファイルをダウンロードし、それらのファイルをユーザーのノートブックに関する Azure VM のダッシュボードにアップロードしてください (直接 VM にダウンロードしてもかまいません)。

## <a name="conclusion"></a>まとめ
Jupyter Notebook には、Azure 上で Python エコシステムの機能に対話的にアクセスするための強力なインターフェイスが用意されています。  このインターフェイスは幅広い用途 (簡単な調査、Python の学習、データの分析と表示、シミュレーション、並列コンピューティングなど) に対応しています。 作成される Notebook のドキュメントには実行された計算処理の完全な記録が含まれており、このドキュメントは他の Jupyter ユーザーと共有できます。  Jupyter Notebook はローカル アプリケーションとして使用できますが、Azure でのクラウドのデプロイに最適です。

Jupyter の主要機能は、[Python Tools for Visual Studio][Python Tools for Visual Studio] (PTVS) を介して Visual Studio 内部で使用することもできます。 PTVS は、Microsoft が提供する無料のオープン ソース プラグインです。このプラグインによって、Visual Studio を Python の高度な開発環境として利用することができます。この開発環境には、IntelliSense を備えた高度なエディター、デバッグ、プロファイル、並列コンピューティング統合の機能が含まれています。

## <a name="next-steps"></a>次のステップ
詳細については、 [Python デベロッパー センター](/develop/python/)を参照してください。

[portal-vm-linux]: https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-tutorial-portal-rm/
[repository]: https://github.com/ipython/ipython
[Python Tools for Visual Studio]: http://aka.ms/ptvs

