---
title: "Azure での Linux 用 Docker VM 拡張機能の使用"
description: "Docker と Azure 仮想マシン拡張機能について説明し、Azure CLI を使用してコマンド ラインから Azure 上に Docker ホストである仮想マシンをプログラムで作成する方法を示します。"
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: eaff75e3-d929-4931-a4a1-8c377a8e7302
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/29/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 96d88513eb4817b208d45883ed0c926921c7c10e
ms.lasthandoff: 03/27/2017


---
# <a name="using-the-docker-vm-extension-from-the-azure-command-line-interface-azure-cli"></a>Azure コマンド ライン インターフェイス (Azure CLI) での Docker VM 拡張機能の使用
> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 Resource Manager モデルを使用した Docker VM 拡張機能の使用方法については、[こちら](../../virtual-machines-linux-dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)をご覧ください。

このトピックでは、任意のプラットフォーム上のサービス管理 (asm) モードの Azure CLI で Docker VM 拡張機能を使用して VM を作成する方法について説明します。 [Docker](https://www.docker.com/) は、最もよく利用されている仮想化アプローチの 1 つで、データの分離と共有リソースでのコンピューティングの手段として仮想マシンではなく [Linux コンテナー](http://en.wikipedia.org/wiki/LXC)を使用します。 Docker VM 拡張機能と [Azure Linux エージェント](../../virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を使用して、Azure 上でアプリケーション用に任意の数のコンテナーをホストする Docker VM を作成することができます。 コンテナーとその利点に関する概要については、「 [Docker High Level Whiteboard (Docker の概要ホワイトボード)](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard)」を参照してください。

## <a name="how-to-use-the-docker-vm-extension-with-azure"></a>Azure で Docker VM 拡張機能を使用する方法
Azure で Docker VM 拡張機能を使用するには、バージョン 0.8.6 以降の [Azure コマンド ライン インターフェイス](https://github.com/Azure/azure-sdk-tools-xplat) (Azure CLI) をインストールする必要があります (この記事の執筆時点で最新バージョンは 0.10.0 です)。 Azure CLI は Mac、Linux、Windows のいずれにもインストールできます。

Azure 上で Docker を使用するためのプロセスはシンプルです。

* Azure CLI とその依存関係を、Azure を制御するコンピューターにインストールする (Windows の場合は、仮想マシンとして実行される Linux ディストリビューション)。
* Azure CLI Docker コマンドを使用して、Azure に VM Docker ホストを作成する。
* ローカルの Docker コマンドを使用して、Azure 上の Docker VM 内の Docker コンテナーを管理する。

### <a name="install-the-azure-command-line-interface-azure-cli"></a>Azure コマンド ライン インターフェイス (Azure CLI) のインストール
Azure CLI のインストールと構成の方法については、[Azure コマンド ライン インターフェイスのインストール方法](../../../cli-install-nodejs.md)に関するページを参照してください。 インストールを確認するために、コマンド プロンプトで「`azure`」と入力します。しばらくすると、Azure CLI の ASCII アートが表示され、使用可能な基本コマンドが一覧表示されます。 インストールが正常に行われていれば、「`azure help vm`」と入力したときに、一覧表示されたコマンドに "docker" が含まれています。

> [!NOTE]
> Docker には [Docker Machine](https://docs.docker.com/installation/windows/) という Windows 用ツールが用意されています。このプログラムでは、Docker クライアントの作成を自動化することもできます。作成された Docker クライアントは Docker ホストとして Azure VM との連携に使用できます。
> 
> 

### <a name="connect-the-azure-cli-to-to-your-azure-account"></a>Azure アカウントへの Azure CLI の接続
Azure CLI を使用する前に、プラットフォーム上で Azure アカウントの資格情報を Azure CLI に関連付ける必要があります。 [Azure サブスクリプションへの接続方法](../../../xplat-cli-connect.md) に関するセクションで、 **.publishsettings** ファイルをダウンロードしてインポートする方法と Azure CLI を組織 ID に関連付ける方法が説明されています。

> [!NOTE]
> 使用する認証方法によって動作に若干の違いがあるため、必ず前述のドキュメントをよく読んで、機能の違いを理解してください。
> 
> 

### <a name="install-docker-and-use-the-docker-vm-extension-for-azure"></a>Docker のインストールと Azure 用 Docker VM 拡張機能の使用
続いては、 [Docker のインストール手順](https://docs.docker.com/installation/#installation) に従って、Docker をコンピューターにローカルにインストールします。

Azure 仮想マシンで Docker を使用するには、VM に使用する Linux イメージに [Azure Linux VM エージェント](../../virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)がインストールされている必要があります。 現在、このエージェントを提供するイメージは次の 2 種類のみです。

* Azure イメージ ギャラリーで入手できる Ubuntu イメージ
* インストールされ、構成済みの Azure Linux VM エージェントで作成したカスタム Linux イメージ Azure VM エージェントでカスタム Linux VM を構築する方法の詳細については、「[Azure Linux エージェント ユーザー ガイド](../../virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

### <a name="using-the-azure-image-gallery"></a>Azure イメージ ギャラリーの使用
bash またはターミナル セッションから、次の Azure CLI コマンドを入力して、VM ギャラリーで最新の Ubuntu イメージを見つけます。

`azure vm image list | grep Ubuntu-14_04`

`b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB` など、イメージ名のいずれかを選び、次のコマンドを入力して、そのイメージを使用した新しい VM を作成します。

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB" <username> <password>
```

各値の説明:

* *&lt;vm-cloudservice name&gt;* は、Azure で Docker コンテナーのホスト コンピューターになる VM の名前です。
* *&lt;username&gt;* は、VM の既定の root ユーザーのユーザー名です。
* *&lt;password&gt;* は、Azure の複雑さの基準を満たした *username* アカウントのパスワードです。

> [!NOTE]
> 現時点では、パスワードは 8 文字以上で、小文字、大文字、数字、特殊文字 (`!@#$%^&+=` ) を 1 つずつ含める必要があります。ピリオドは特殊文字ではありません。
> 
> 

コマンドが正常に実行されると、使用した引数とオプションに応じて、以下のような応答が表示されます。

![](media/cli-use-docker/dockercreateresults.png)

> [!NOTE]
> 仮想マシンの作成には数分かかることがありますが、プロビジョニングが終わると (状態値は `ReadyRole`)、Docker デーモン (Docker サービス) が起動し、Docker コンテナー ホストに接続できるようになります。
> 
> 

Azure 上に作成した Docker VM をテストするために、次のコマンドを入力します。

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:2376 info`

*&lt;vm-name-you-used&gt;* は、`azure vm docker create` の呼び出しで使用した仮想マシンの名前です。 以下のような応答が表示されます。Azure 上で Docker ホスト VM が稼働し、コマンドの入力を待機していることが示されています。 

これで、Docker クライアントを使用して接続し、情報を取得しようとすることができます (Mac の場合など、一部の Docker クライアントのセットアップでは、`sudo` の使用が必要になる場合があります)。

    sudo docker --tls -H tcp://testsshasm.cloudapp.net:2376 info
    Password:
    Containers: 0
    Images: 0
    Storage Driver: devicemapper
    Pool Name: docker-8:1-131781-pool
    Pool Blocksize: 65.54 kB
    Backing Filesystem: extfs
    Data file: /dev/loop0
    Metadata file: /dev/loop1
    Data Space Used: 1.821 GB
    Data Space Total: 107.4 GB
    Data Space Available: 28 GB
    Metadata Space Used: 1.479 MB
    Metadata Space Total: 2.147 GB
    Metadata Space Available: 2.146 GB
    Udev Sync Supported: true
    Deferred Removal Enabled: false
    Data loop file: /var/lib/docker/devicemapper/devicemapper/data
    Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
    Library Version: 1.02.77 (2012-10-15)
    Execution Driver: native-0.2
    Logging Driver: json-file
    Kernel Version: 3.19.0-28-generic
    Operating System: Ubuntu 14.04.3 LTS
    CPUs: 1
    Total Memory: 1.637 GiB
    Name: testsshasm
    WARNING: No swap limit support

すべてが機能していることを確認するために、VM で Docker 拡張機能を調査できます。

    azure vm extension get testsshasm
    info: Executing command vm extension get
    + Getting virtual machines
    data: Publisher Extension name ReferenceName Version State
    data: -------------------- --------------- ------------------------- ------- ------
    data: Microsoft.Azure.E... DockerExtension DockerExtension 1.* Enable
    info: vm extension get command OK

### <a name="docker-host-vm-authentication"></a>Docker ホスト VM の認証
コマンド `azure vm docker create` では、Docker VM の作成に加えて、必要な証明書も自動的に作成されます。Docker クライアント コンピューターは、この証明書を使用することで、HTTPS を介して Azure コンテナー ホストに接続できます。証明書は必要に応じてクライアントとホスト マシンのどちらにも格納されます。 これ以降に実行しようとすると、既存の証明書が再利用され、新しいホストと共有されます。

既定では、証明書は `~/.docker` に配置されます。Docker はポート **2376** で実行するように構成されます。 別のポートやディレクトリを使用する場合は、以下の `azure vm docker create` コマンド ライン オプションのいずれかを使用して、Docker コンテナー ホスト VM がクライアントとの接続に別のポートや証明書を使用するよう構成できます。

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

ホストの Docker デーモンは、 `azure vm docker create` コマンドで生成された証明書を使用して、指定されたポートでクライアント接続をリッスンし、認証するよう構成されています。 クライアント コンピューターには、Docker ホストにアクセスできるように、これらの証明書が配置されている必要があります。

> [!NOTE]
> ネットワークに接続されたホストにこれらの証明書がない場合は、コンピューターに接続できる第三者に対して脆弱になります。 既定の構成を変更する前に、コンピューターやアプリケーションに対するリスクについてよく理解してください。
> 
> 

## <a name="next-steps"></a>次のステップ
* 「 [Docker ユーザー ガイド] 」にアクセスして Docker VM の使用を開始する準備が整いました。 新しいポータルで Docker 対応 VM を作成する方法については、「 [Using the Docker VM Extension with the Azure Portal (Azure ポータルでの Docker VM 拡張機能の使用)]」を参照してください。
* Azure の Docker VM 拡張機能は、Docker Compose もサポートします。Docker Compose は、宣言型 YAML ファイルを使用して、開発者が構築したアプリケーションを環境全体で有効にし、一貫性のあるデプロイを生成します。 「[Docker と Compose を使用して Azure 仮想マシン上で複数コンテナー アプリケーションを定義して実行する]」をご覧ください。  

<!--Anchors-->
[Subheading 1]:#subheading-1
[Subheading 2]:#subheading-2
[Subheading 3]:#subheading-3
[Next steps]:#next-steps

[How to use the Docker VM Extension with Azure]:#How-to-use-the-Docker-VM-Extension-with-Azure
[Virtual Machine Extensions for Linux and Windows]:#Virtual-Machine-Extensions-For-Linux-and-Windows
[Container and Container Management Resources for Azure]:#Container-and-Container-Management-Resources-for-Azure



<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]:../../virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]:../../../app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]:../storage-whatis-account.md
[Using the Docker VM Extension with the Azure Portal (Azure ポータルでの Docker VM 拡張機能の使用)]:http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[Docker ユーザー ガイド]:https://docs.docker.com/userguide/

[Docker と Compose を使用して Azure 仮想マシン上で複数コンテナー アプリケーションを定義して実行する]:../../virtual-machines-linux-docker-compose-quickstart.md

