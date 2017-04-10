---
title: "Linux 用 Docker VM 拡張機能の使用 | Microsoft Docs"
description: "Docker と Azure Virtual Machines の拡張機能について説明し、クラシック デプロイ モデルで Azure CLI を使用して、Docker ホストである Azure 仮想マシンを作成する方法を示します。"
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 19cf64e8-f92c-43ad-a120-8976cd9102ac
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/27/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 0197d78185cda05cec3512e6a4ad411ae0da60da
ms.lasthandoff: 03/27/2017


---
# <a name="using-the-docker-vm-extension-with-the-azure-classic-portal"></a>Azure クラシック ポータルで Docker VM 拡張機能を使用する方法
> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。

[Docker](https://www.docker.com/) は、最もよく利用されている仮想化アプローチの 1 つで、データの分離と共有リソースでのコンピューティングの手段として仮想マシンではなく [Linux コンテナー](http://en.wikipedia.org/wiki/LXC)を使用します。 [Azure Linux エージェント] で管理する Docker VM 拡張機能を使用すれば、Azure 上に Docker VM を作成し、アプリケーション用に任意の数のコンテナーをホストさせることができます。

> [!NOTE]
> このトピックでは、Azure クラシック ポータルで Docker VM を作成する方法について説明します。 コマンド ラインで Docker VM を作成する方法については、「 [Azure コマンド ライン インターフェイス (Azure CLI) での Docker VM 拡張機能の使用]」を参照してください。 コンテナーとその利点に関する概要については、「 [Docker High Level Whiteboard (Docker の概要ホワイトボード)](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard)」を参照してください。
> 
> 

## <a name="create-a-new-vm-from-the-image-gallery"></a>イメージ ギャラリーからの新しい VM の作成
最初のステップとして、Docker VM 拡張機能をサポートする Linux イメージから Azure VM を作成する必要があります。イメージ ギャラリーから取得した Ubuntu 14.04 LTS イメージをサンプルのサーバー イメージとして、Ubuntu 14.04 デスクトップをクライアントとして使用します。 ポータルで、左下の **[+ 新規]** をクリックし、新しい VM インスタンスを作成します。下図に示すように、使用可能な選択項目か、すべて表示されるイメージ ギャラリーから、Ubuntu 14.04 LTS イメージを選択します。

> [!NOTE]
> 現時点では、2014 年 7 月以降の Ubuntu 14.04 LTS イメージのみが Docker VM 拡張機能をサポートしています。
> 
> 

![新しい Ubuntu イメージの作成
](./media/portal-use-docker/ChooseUbuntu.png)

## <a name="create-docker-certificates"></a>Docker 証明書の作成
VM の作成が完了したら、クライアント コンピューターに Docker がインストールされていることを確認します (詳細については、[Docker のインストール手順](https://docs.docker.com/installation/#installation)を参照してください)。

[HTTPS を使用した Docker の実行]に関するページに記載された手順に従って Docker の通信用の証明書とキー ファイルを作成し、クライアント コンピューターの **`~/.docker`** ディレクトリに配置します。

> [!NOTE]
> 現時点では、ポータルの Docker VM 拡張機能には Base64 でエンコードした証明書が必要です。
> 
> 

コマンド ラインで **`base64`** を使用するか、別の使い慣れたエンコード ツールを使用して、Base64 でエンコードされたトピックを作成します。 証明書とキー ファイルのシンプルなセットをしてこの操作を行った場合、次のようになります。

```
 ~/.docker$ ls
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ ls
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## <a name="add-the-docker-vm-extension"></a>Docker VM 拡張機能の追加
Docker VM 拡張機能を追加するには、作成した VM インスタンスを見つけ、下図に示す **[拡張機能]** まで下へスクロールし、クリックして VM 拡張機能を表示します。

> [!NOTE]
> この機能はプレビュー ポータル (https://portal.azure.com/) でのみサポートされます。
> 
> 

![](media/portal-use-docker/ClickExtensions.png)

### <a name="add-an-extension"></a>拡張機能の追加
**[+ 新規]** をクリックすると、この VM に追加できる VM 拡張機能が表示されます。

![](media/portal-use-docker/ClickAdd.png)

### <a name="select-the-docker-vm-extension"></a>Docker VM 拡張機能の選択
Docker VM 拡張機能を選択します。この操作により、Docker の説明と重要なリンクが表示されます。下部にある **[作成]** をクリックすると、インストール手順が開始されます。

![](media/portal-use-docker/ChooseDockerExtension.png)

![](media/portal-use-docker/CreateButtonFocus.png)

### <a name="add-your-certificate-and-key-files"></a>証明書とキー ファイルの追加
下図に示すように、フォームのフィールドに、Base64 でエンコードされた CA 証明書、サーバー証明書、およびサーバー キーを入力します。

![](media/portal-use-docker/AddExtensionFormFilled.png)

> [!NOTE]
> (上のイメージに示すように) 既定で 2376 が入力されています。 ここに任意のエンドポイントを入力できますが、次のステップで、一致するエンドポイントを開く必要があります。 既定値を変更した場合は、必ず、次のステップで一致するエンドポイントを開いてください。
> 
> 

## <a name="add-the-docker-communication-endpoint"></a>Docker 通信エンドポイントの追加
作成したリソース グループを表示するとき、VM に関連付けられているネットワーク セキュリティ グループを選択し、 **[受信セキュリティ規則]** をクリックして、次に示すようにルールを表示します。

![](media/portal-use-docker/AddingEndpoint.png)

**[+ 新規]** をクリックするとルールが 1 つ追加されます。既定で、エンドポイントの名前 (この例では **Docker**) と、[宛先ポート範囲] に「2376」が入力されています。 **[TCP]** と表示されているプロトコルの値を設定し、**[OK]** をクリックして、ルールを作成します。

![](media/portal-use-docker/AddEndpointFormFilledOut.png)

## <a name="test-your-docker-client-and-azure-docker-host"></a>Docker クライアントと Azure Docker ホストのテスト
VM のドメイン名を見つけてコピーし、クライアント コンピューターのコマンド ラインで、「 `docker --tls -H tcp://`*dockerextension*`.cloudapp.net:2376 info` 」と入力します ( *dockerextension* は、使用している VM のサブドメインに置き換えます)。

結果は次のようになります。

```
$ docker --tls -H tcp://dockerextension.cloudapp.net:2376 info
Containers: 0
Images: 0
Storage Driver: devicemapper
 Pool Name: docker-8:1-131214-pool
 Pool Blocksize: 65.54 kB
 Data file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
 Data Space Used: 305.7 MB
 Data Space Total: 107.4 GB
 Metadata Space Used: 729.1 kB
 Metadata Space Total: 2.147 GB
 Library Version: 1.02.82-git (2013-10-04)
Execution Driver: native-0.2
Kernel Version: 3.13.0-36-generic
WARNING: No swap limit support
```

これまでの手順を完了すると、Azure VM で実行する Docker ホストが正常に機能し、別のクライアントからリモートで接続されるように構成された状態になります。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>次のステップ
「 [Docker ユーザー ガイド] 」にアクセスして Docker VM の使用を開始する準備が整いました。 コマンド ライン インターフェイスから Azure VM の Docker ホストを自動的に作成する方法については、「 [Azure コマンド ライン インターフェイス (Azure CLI) での Docker VM 拡張機能の使用]

<!--Anchors-->
[Create a new VM from the Image Gallery]:#createvm
[Create Docker Certificates]:#dockercerts
[Add the Docker VM Extension]:#adddockerextension
[Test Docker Client and Azure Docker Host]:#testclientandserver
[Next steps]:#next-steps

<!--Image references-->
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[6]:./media/markdown-template-for-new-articles/pretty49.png
[7]:./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Azure コマンド ライン インターフェイス (Azure CLI) での Docker VM 拡張機能の使用]:http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Azure Linux エージェント]:../../virtual-machines-linux-agent-user-guide.md
[Link 3 to another azure.microsoft.com documentation topic]:../storage-whatis-account.md

[HTTPS を使用した Docker の実行]:http://docs.docker.com/articles/https/
[Docker ユーザー ガイド]:https://docs.docker.com/userguide/

