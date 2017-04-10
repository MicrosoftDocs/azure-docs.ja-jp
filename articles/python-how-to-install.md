---
title: "Python と SDK のインストール - Azure"
description: "Azure で使用するための Python と SDK のインストール方法について説明します。"
services: 
documentationcenter: python
author: lmazuel
manager: wpickett
editor: 
ms.assetid: f36294be-daeb-4caf-9129-fce18130f552
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 09/06/2016
ms.author: lmazuel
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: c2d6999c6c6e8d9168e2417309207dd14bf4f6f2
ms.lasthandoff: 03/29/2017


---
# <a name="installing-python-and-the-sdk"></a>Python と SDK のインストール
Python は、Windows でのセットアップが容易であり、Mac、Linux、および [Bash for Windows](https://msdn.microsoft.com/commandline/wsl/about)ではプレインストールされています。 このガイドでは、インストール方法と Azure で使用するためのコンピューターの準備について説明します。

## <a name="whats-in-the-python-azure-sdk"></a>Python Azure SDK の機能
Azure SDK for Python には、Azure 向けの Python アプリケーションを開発、デプロイ、管理するためのコンポーネントが用意されています。 Azure SDK for Python には次のコンポーネントが用意されています。

* **管理ライブラリ**。 このクラス ライブラリには、ストレージ アカウントや仮想マシンなどの Azure リソースを管理するインターフェイスが用意されています。
* **ランタイム ライブラリ**。 このクラス ライブラリには、ストレージやサービス バスなどの Azure 機能にアクセスするためのインターフェイスが用意されています。

## <a name="which-python-and-which-version-to-use"></a>使用する Python とそのバージョン
利用可能な Python インタープリターにはいくつかの種類があります。以下に例を示します。

* CPython - 最も一般的に使用されている標準的な Python インタープリター
* PyPy - CPython に対応する高速な代替実装
* IronPython - .Net/CLR 上で実行する Python インタープリター
* Jython - Java 仮想マシン上で実行する Python インタープリター

**CPython** v2.7 または v3.3 以降および PyPy 5.4.0 が、Python Azure SDK に対してテストされ、サポートされています。

## <a name="where-to-get-python"></a>Python を入手するには
CPython はいくつかの方法で入手できます。

* [www.python.org][www.python.org] から直接入手
* [www.continuum.io][www.continuum.io]、[www.enthought.com][www.enthought.com]、[www.activestate.com][www.activestate.com] などの信頼できるディストリビューターから入手
* ソースからビルド

特定のニーズがない限り、最初の 2 つの入手方法をお勧めします。

## <a name="sdk-installation-on-windows-linux-and-macos-client-libraries-only"></a>Windows、Linux、および MacOS (クライアント ライブラリのみ) での SDK のインストール
既に Python をインストールしている場合は、pip を使用して、既存の Python 2.7 または Python 3.3 以降の環境ですべてのクライアント ライブラリのバンドルをインストールできます。 これにより、[Python Package Index][Python Package Index] (PyPI) からパッケージがダウンロードされます。

管理者権限が必要になる場合があります。

* Linux および MacOS で `sudo` コマンド `sudo pip install azure-mgmt-compute` を使用します。
* Windows: 管理者として PowerShell/コマンド プロンプトを開きます

Azure サービスごとに個別にインストールできます。

```console
   $ pip install azure-batch          # Install the latest Batch runtime library
   $ pip install azure-mgmt-scheduler # Install the latest Storage management library
```

プレビュー パッケージをインストールするには、`--pre` フラグを使用します。

```console
   $ pip install --pre azure-mgmt-compute # will install only the latest Compute Management library
```

`azure` メタ パッケージを使用して、一連の Azure ライブラリを 1 行でインストールすることもできます。 このメタ パッケージ内の一部のパッケージはまだ安定版として発行されていません。したがって、`azure` メタ パッケージはまだプレビュー段階です。
コア パッケージについては、コード品質/完全性の観点から、現時点で "安定版" と考えることができますが、

* できるだけ早く、他の言語と同期するように、このメタ パッケージを正式に "安定版" として発表する予定です。
  それまで、大きな変更を行う予定はありません。

これはプレビュー リリースであるため、 `--pre` フラグを使用する必要があります。

```console
   $ pip install --pre azure
```

または、直接指定します

```console
   $ pip install azure==2.0.0rc6
```

## <a name="getting-more-packages"></a>その他のパッケージの入手
「[Python Package Index][Python Package Index] (PyPI)」には、Python ライブラリの豊富な選択肢があります。  ディストリビューションをインストールすると、Web 開発から技術計算にいたるさまざまなシナリオのうち、興味のある多くのシナリオを実施できます。

## <a name="python-tools-for-visual-studio"></a>Python Tools for Visual Studio
[Python Tools for Visual Studio][Python Tools for Visual Studio] (PTVS) は Microsoft が提供する無料の OSS プラグインで、VS を本格的な Python IDE として使用できます。

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

PTVS の使用はオプションですが、使用することをお勧めします。この PTVS によって、Python と Web のプロジェクト/ソリューション サポート、デバッグ、プロファイル、対話型ウィンドウ、テンプレートの編集、Intellisense を利用できるためです。

PTVS では、[Cloud Services](cloud-services/cloud-services-python-ptvs.md) と [Websites](app-service-web/web-sites-python-ptvs-django-mysql.md) へのデプロイをサポートしており、Microsoft Azure へのデプロイも容易になります。

PTVS は、既存の Visual Studio 2013 または 2015 インストールに対応しています。  ダウンロードなどの詳細については、 [Python Tools for Visual Studio]に関するページをご覧ください。  

## <a name="python-azure-scenarios-for-linux-and-macos"></a>Linux や MacOS 向けの Python Azure
Linux または MacOS の場合は、主に次の Azure シナリオがサポートされています。

1. Python クライアント ライブラリを使用して、Azure サービスを利用する
2. Linux VM でアプリケーションを実行する
3. Git を使って Azure Websites を開発して公開する

最初のシナリオでは、Azure REST API 用の Python ラッパーを介して Azure の PaaS 機能 ([BLOB ストレージ](virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)、[キュー ストレージ](storage/storage-python-how-to-use-queue-storage.md)、[テーブル ストレージ](storage/storage-python-how-to-use-table-storage.md)など) を利用する優れた Web アプリケーションを作成できます。 これらは、Windows、Mac、Linux とまったく同じように動作します。  また、ローカルの開発マシンや Linux 仮想マシンからこれらのクライアント ライブラリを使うこともできます。

VM のシナリオでは、選択した Linux VM (Ubuntu、CentOS、Suse) を起動し、必要なものを実行または管理するだけです。  たとえば、Windows、Mac、または Linux のコンピューター上で [IPython][IPython] REPL/Notebook を実行し、Azure 上で IPython エンジンを実行している Linux または Windows の複数プロセッサの VM をブラウザーで指定することができます。 詳細については、「[Azure での IPython Notebook](virtual-machines/virtual-machines-linux-jupyter-notebook.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」チュートリアルをご覧ください。

Linux 仮想マシンのセットアップの詳細については、「[Linux を実行する仮想マシンの作成](virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」チュートリアルをご覧ください。

Git のデプロイを使って Python Web アプリケーションを開発できます。また、どのオペレーティング システムからでも Azure Web サイトを公開できます。  ご利用のリポジトリを Azure にプッシュする場合、自動的に仮想環境が作られ、pip によって必要なパッケージがインストールされます。

Azure Websites を開発して公開する詳細については、[Django を使った Websites の作成](app-service-web/web-sites-python-create-deploy-django-app.md)に関するページ、[Bottle を使った Websites の作成](app-service-web/web-sites-python-create-deploy-bottle-app.md)に関するページ、および [Flask を使った Websites の作成](app-service-web/web-sites-python-create-deploy-flask-app.md)に関するページをご覧ください。 さまざまな WSGI 互換フレームワークの使用についての一般的な情報については、[Azure Websites での Python の構成](app-service-web/web-sites-python-configure.md)に関するページをご覧ください。

## <a name="additional-software-and-resources"></a>その他のソフトウェアとリソース:
* [Azure SDK for Python ReadTheDocs](http://azure-sdk-for-python.readthedocs.io/en/latest/)
* [Azure SDK for Python GitHub](https://github.com/Azure/azure-sdk-for-python)
* [Python 用の Azure 公式サンプル](https://azure.microsoft.com/documentation/samples/?platform=python)
* [Continuum Analytics Python ディストリビューション][Continuum Analytics Python Distribution]
* [Enthought の Python ディストリビューション][Enthought Python Distribution]
* [ActiveState の Python ディストリビューション][ActiveState Python Distribution]
* [SciPy - Python の科学技術計算ライブラリのパッケージ][SciPy - A suite of Scientific Python libraries]
* [NumPy - Python の数値計算ライブラリ][NumPy - A numerics library for Python]
* [Django Project - 進化した Web フレームワーク/CMS][Django Project - A mature web framework/CMS]
* [IPython - 高度な Python 用 REPL/Notebook][IPython - an advanced REPL/Notebook for Python]
* [Azure での IPython Notebook](virtual-machines/virtual-machines-linux-jupyter-notebook.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [GitHub の Python Tools for Visual Studio][Python Tools for Visual Studio on GitHub]
* [Python デベロッパー センター](/develop/python/)

[Continuum Analytics Python Distribution]: http://continuum.io
[Enthought Python Distribution]: http://www.enthought.com
[ActiveState Python Distribution]: http://www.activestate.com
[www.python.org]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy - A suite of Scientific Python libraries]: http://www.scipy.org
[NumPy - A numerics library for Python]: http://www.numpy.org
[Django Project - A mature web framework/CMS]: http://www.djangoproject.com
[IPython - an advanced REPL/Notebook for Python]: http://ipython.org
[IPython]: http://ipython.org
[IPython Notebook on Azure]: virtual-machines-linux-jupyter-notebook.md
[Cloud Services]: cloud-services-python-ptvs.md
[Websites]: web-sites-python-ptvs-django-mysql.md
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools for Visual Studio on GitHub]: https://github.com/microsoft/ptvs
[Python Package Index]: http://pypi.python.org/pypi
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[Setting up a Linux VM via the Azure portal]: create-and-configure-opensuse-vm-in-portal.md
[How to use the Azure Command-Line Interface]: crossplat-cmd-tools.md
[Create a Virtual Machine Running Linux]: virtual-machines-linux-quick-create-cli.md
[Creating Websites with Django]: web-sites-python-create-deploy-django-app.md
[Creating Websites with Bottle]: web-sites-python-create-deploy-bottle-app.md
[Creating Websites with Flask]: web-sites-python-create-deploy-flask-app.md
[Configuring Python with Azure Websites]: web-sites-python-configure.md
[table storage]: storage-python-how-to-use-table-storage.md
[queue storage]: storage-python-how-to-use-queue-storage.md
[blob storage]: storage-python-how-to-use-blob-storage.md

