

[Docker](https://www.docker.com/) は、最もよく利用されている仮想化アプローチの 1 つで、アプリケーション データの分離と共有リソースでのコンピューティングの手段として仮想マシンではなく [Linux コンテナー](http://en.wikipedia.org/wiki/LXC)を使用します。 [Azure Linux エージェント](../articles/virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に対して [Azure Docker VM 拡張機能](https://github.com/Azure/azure-docker-extension/blob/master/README.md)を使用すれば、Azure 上に Docker VM を作成し、アプリケーション用に任意の数のコンテナーをホストさせることができます。

このトピックの内容:

* [Docker と Linux コンテナー]
* [Azure で Docker VM 拡張機能を使用する方法]
* [Linux および Windows 向けの仮想マシン拡張機能]

Docker 対応 VM の作成をすぐに開始するには、以下を参照してください。

* [Azure コマンド ライン インターフェイス (Azure CLI) での Docker VM 拡張機能の使用方法]
* [Azure クラシック ポータルで Docker VM 拡張機能を使用する方法]
* [Azure Marketplace で Docker をすばやく開始する方法]

拡張機能の詳細と、その機能方法については、「 [Docker 拡張機能のユーザー ガイド](https://github.com/Azure/azure-docker-extension/blob/master/README.md)」を参照してください。

## <a name="docker-and-linux-containers"></a>Docker と Linux コンテナー
[Docker](https://www.docker.com/) は、最もよく利用されている仮想化アプローチの 1 つであり、データの分離と共有リソースでのコンピューティングの手段として、仮想マシンではなく [Linux コンテナー](http://en.wikipedia.org/wiki/LXC)を用います。また、Docker が提供しているサービスを利用して、アプリケーションを短時間で構築、アセンブルし、他の Docker コンテナーに配布することもできます。

Docker と Linux コンテナーは、Windows Hyper-V や Linux の [KVM](http://www.linux-kvm.org/page/Main_Page) のような (他にも例は多数あります) [ハイパーバイザー](http://en.wikipedia.org/wiki/Hypervisor)ではありません。 ハイパーバイザーでは基になるオペレーティング システムが仮想化されるため、オペレーティング システム ( *仮想マシン*と呼ばれる) 全体がハイパーバイザー内でアプリケーションのように実行されます。

Docker をはじめとする *コンテナー* アプローチでは、Linux カーネルが備えるプロセスとファイル システムの分離機能を使用し、カーネル機能の利用以外の部分では互いに分離されたコンテナーを構築することで、起動時間と処理やストレージのオーバーヘッドを大幅に軽減しています。

ハイパーバイザーと Linux コンテナーが備える機能の種類の違いに関する概要を次の表に示します。 機能の中には自社のアプリケーション ニーズによってどちらが望ましいかが異なるものがあります。

| 機能 | ハイパーバイザー | コンテナー |
|:--- | --- | --- |
| プロセスの分離 |ほぼ全部 |root が取得されると、コンテナー ホストが危害を受けるおそれがある。 |
| 必要なディスク上のメモリ |OS 全体とアプリ |アプリ要件のみ |
| 起動にかかる時間 |非常に長い: OS のブートとアプリの読み込み |非常に短い: カーネルが既に実行されているため必要なのはアプリの起動のみ |
| コンテナーの自動化 |OS やアプリによって大きく異なる |[Docker イメージ ギャラリー](https://registry.hub.docker.com/)、その他 |

コンテナーとその利点に関する概要については、「 [Docker High Level Whiteboard (Docker の概要ホワイトボード)](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard)」を参照してください。

Docker の機能やしくみの詳細については、「 [What is Docker? (Docker とは)](https://www.docker.com/whatisdocker/)

#### <a name="docker-and-linux-container-security-best-practices"></a>Docker と Linux コンテナーのセキュリティに関するベスト プラクティス
コンテナーはホスト コンピューターのカーネルへのアクセスを共有するため、悪意のあるコードが root を取得できた場合、ホスト コンピューターだけでなく他のコンテナーにもアクセスできる可能性があります。 既定の構成からさらにコンテナー システムのセキュリティを強化するために、[SELinux](http://selinuxproject.org/page/Main_Page) や [AppArmor](http://wiki.apparmor.net/index.php/Main_Page) など、グループ ポリシーや[ロール ベースのセキュリティ](http://en.wikipedia.org/wiki/Role-based_access_control)を追加したり、コンテナーに使用を許可するカーネル機能をできる限り減らしたりすることが [Docker で推奨されています](https://docs.docker.com/articles/security/)。 また、インターネット上には、Docker のようなコンテナーを使用する際のセキュリティ対策について記載されたドキュメントが多数あります。

## <a name="how-to-use-the-docker-vm-extension-with-azure"></a>Azure で Docker VM 拡張機能を使用する方法
Docker VM 拡張機能は、作成した VM インスタンスにインストールされるコンポーネントで、拡張機能自身が Docker エンジンをインストールし、VM とのリモート通信を管理します。 VM 拡張機能をインストールする方法は、2 つあります。1 つは管理ポータルを使用して VM を作成する方法、もう 1 つは Azure コマンド ライン インターフェイス (Azure CLI) から作成する方法です。

ポータルでは、互換性のある任意の Linux VM に Docker VM 拡張機能を追加できます (現時点では、7 月以降の Ubuntu 14.04 LTS イメージのみが Docker VM 拡張機能をサポートしています)。 一方、Azure CLI コマンド ラインを使用すれば、VM インスタンスを作成するときに、同時に Docker VM 拡張機能をインストールし、Docker 通信証明書を作成してアップロードできます。

Docker 対応 VM の作成をすぐに開始するには、以下を参照してください。

* [Azure コマンド ライン インターフェイス (Azure CLI) での Docker VM 拡張機能の使用方法]
* [Azure クラシック ポータルで Docker VM 拡張機能を使用する方法]

## <a name="virtual-machine-extensions-for-linux-and-windows"></a>Linux および Windows 向けの仮想マシン拡張機能
[Azure 向けの Docker VM 拡張機能](https://github.com/Azure/azure-docker-extension/blob/master/README.md)は、特殊な動作を提供する数ある VM 拡張機能のうちの 1 つにすぎません。現在、他にも多くの機能が開発中です。 たとえば、[Linux VM エージェント拡張機能](../articles/virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)の中には、セキュリティ機能やカーネルとネットワークの機能など、仮想マシンの変更と管理を実行できるものがあります。 また VMAccess 拡張機能では、管理者のパスワードまたは SSH キーをリセットできます。

詳しい一覧については、[Azure VM 拡張機能](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事を参照してください。

<!--Anchors-->
[Azure コマンド ライン インターフェイス (Azure CLI) での Docker VM 拡張機能の使用方法]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Azure クラシック ポータルで Docker VM 拡張機能を使用する方法]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/
[Azure Marketplace で Docker をすばやく開始する方法]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-ubuntu-quickstart/
[Docker と Linux コンテナー]: #Docker-and-Linux-Containers
[Azure で Docker VM 拡張機能を使用する方法]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Linux および Windows 向けの仮想マシン拡張機能]: #Virtual-Machine-Extensions-For-Linux-and-Windows
