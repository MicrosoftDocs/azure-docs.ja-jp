---
title: "Linux 上で Azure Service Fabric コンテナー アプリケーションを作成する | Microsoft Docs"
description: "Azure Service Fabric で初めての Linux コンテナー アプリケーションを作成します。  アプリケーションの Docker イメージをビルドして、そのイメージをコンテナー レジストリにプッシュし、Service Fabric コンテナー アプリケーションをビルドおよびデプロイします。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: ryanwi
ms.openlocfilehash: 42a2542b7564f1692146b3e3927792ac339f30be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-an-azure-service-fabric-linux-container-application-on-azure"></a>Azure Service Fabric Linux コンテナー アプリケーションを Azure にデプロイする
Azure Service Fabric は、スケーラブルで信頼性に優れたマイクロサービスとコンテナーのデプロイと管理を行うための分散システム プラットフォームです。 

このクイックスタートでは、Linux コンテナーを Service Fabric クラスターにデプロイする方法を説明します。 完了すると、Service Fabric クラスターで実行される Python Web フロントエンドと Redis バックエンドで構成される投票アプリケーションが作成されます。 

![quickstartpic][quickstartpic]

このクイックスタートでは、次の方法について説明します。
> [!div class="checklist"]
> * Service Fabric への Linux コンテナーのデプロイ
> * Service Fabric でのコンテナーのスケールとフェールオーバー

## <a name="prerequisite"></a>前提条件
Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/en-us/free/) を作成してください。
  
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

コマンド ライン インターフェイス (CLI) をローカルにインストールして使用する場合、Azure CLI バージョン 2.0.4 以降を実行する必要があります。 バージョンを確認するには、az --version を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)」を参照してください。

## <a name="get-application-package"></a>アプリケーション パッケージを取得する
コンテナーを Service Fabric にデプロイするには、個別のコンテナーとアプリケーションを説明する一連のマニフェスト ファイル (アプリケーション定義) が必要です。

Cloud Shell で git を使用して、アプリケーション定義のコピーを複製します。

```azurecli-interactive
git clone https://github.com/Azure-Samples/service-fabric-dotnet-containers.git

cd service-fabric-dotnet-containers/Linux/container-tutorial/Voting
```

## <a name="deploy-the-containers-to-a-service-fabric-cluster-in-azure"></a>コンテナーを Azure の Service Fabric クラスターにデプロイする
Azure 内のクラスターにアプリケーションをデプロイする場合、独自のクラスターかパーティー クラスターを使用します。

パーティー クラスターは、Azure でホストされている期間限定の無料 Service Fabric クラスターです。 Service Fabric チームによってサポートされており、誰でもアプリケーションをデプロイし、プラットフォームについて学ぶことができます。 パーティ クラスターにアクセスするには、[こちらの手順を実行します](http://aka.ms/tryservicefabric)。 

独自クラスターの作成については、「[Azure で初めての Service Fabric クラスターを作成する](service-fabric-get-started-azure-cluster.md)」を参照してください。

> [!Note]
> Web フロントエンド サービスは、ポート 80 で受信トラフィックをリッスンする構成になっています。 このポートがクラスターで開放されていることを確認してください。 パーティ クラスターを使用した場合、このポートは開放されています。
>

### <a name="deploy-the-application-manifests"></a>アプリケーション マニフェストのデプロイ 
CLI 環境内に [Service Fabric CLI (sfctl)](service-fabric-cli.md) をインストールする

```azurecli-interactive
pip3 install --user sfctl 
export PATH=$PATH:~/.local/bin
```
Azure CLI を使用して Azure の Service Fabric クラスターに接続します。 エンドポイントはクラスターの管理エンドポイントです (`http://linh1x87d1d.westus.cloudapp.azure.com:19080` など)。

```azurecli-interactive
sfctl cluster select --endpoint http://linh1x87d1d.westus.cloudapp.azure.com:19080
```

用意されているインストール スクリプトを使用してクラスターに投票アプリケーション定義をコピーし、アプリケーションの種類を登録して、アプリケーションのインスタンスを作成します。

```azurecli-interactive
./install.sh
```

ブラウザーを開いて、http://\<my-azure-service-fabric-cluster-url>:19080/Explorer (`http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer` など) で Service Fabric Explorer に移動します。 アプリケーション ノードを展開し、投票アプリケーションの種類と作成したインスタンスのエントリがあることを確認します。

![Service Fabric Explorer][sfx]

実行中のコンテナーに接続します。  クラスターの URL を指す Web ブラウザーを開きます (`http://linh1x87d1d.westus.cloudapp.azure.com:80` など)。 ブラウザーに Voting アプリケーションが表示されます。

![quickstartpic][quickstartpic]

## <a name="fail-over-a-container-in-a-cluster"></a>クラスター内のコンテナーをフェールオーバーする
障害が発生すると、Service Fabric はクラスター内の他のノードにコンテナー インスタンスを自動的に移動します。 また、コンテナーのノードを手動でドレインして、クラスター内の他のノードに適切に移動することもできます。 サービスをスケーリングするには複数の方法があります。この例では、Service Fabric Explorer を使います。

フロントエンド コンテナーのフェールオーバーは、次の手順で行います。

1. クラスターで Service Fabric Explorer を開きます (例: `http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`)。
2. ツリー ビューの **fabric:/Voting/azurevotefront** ノードをクリックし、パーティション ノード (GUID で表されます) を展開します。 ツリー ビューのノード名に注意してください。この名前は、コンテナーが現在実行しているノードを示します (例: `_nodetype_4`)。
3. ツリー ビューの **Nodes** ノードを展開します。 コンテナーを実行しているノードの横にある省略記号 (3 つのドット) をクリックします。
4. **[再起動]** を選んでそのノードを再起動し、再起動操作を確認します。 再起動により、コンテナーはクラスター内の別のノードにフェールオーバーします。

![sfxquickstartshownodetype][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>クラスター内のアプリケーションとサービスをスケールする
Service Fabric サービスは、その負荷に対応するように、クラスターで簡単にスケールすることができます。 サービスをスケールするには、クラスターで実行されるインスタンスの数を変更します。

Web フロントエンド サービスをスケールするには、次の手順に従います。

1. クラスターで Service Fabric Explorer を開きます (例: `http://linh1x87d1d.westus.cloudapp.azure.com:19080`)。
2. ツリー ビューで **fabric:/Voting/azurevotefront** ノードの横にある省略記号 (3 つの点) をクリックし、**[Scale Service]\(サービスのスケール\)** を選択します。

    ![containersquickstartscale][containersquickstartscale]

  これで Web フロントエンド サービスのインスタンス数をスケールできる状態になりました。

3. この数値を **2** に変更し、**[Scale Service]\(サービスのスケール\)** をクリックします。
4. ツリー ビューの **fabric:/Voting/azurevotefront** ノードをクリックし、パーティション ノード (GUID で表されます) を展開します。

    ![containersquickstartscaledone][containersquickstartscaledone]

    サービスに 2 つのインスタンスがあることがわかります。 ツリー ビューでは、インスタンスが実行されるノードを確認できます。

たったこれだけの管理タスクにより、フロントエンド サービスでユーザー負荷を処理するためのリソースが 2 倍になりました。 実行するサービスの信頼性を高めるために、サービスのインスタンスを複数用意する必要はないことに注目してください。 サービスで障害が発生した場合、Service Fabric によって新しいサービス インスタンスがクラスターで実行されます。

## <a name="clean-up"></a>クリーンアップ
クラスターからアプリケーション インスタンスを削除し、その種類のアプリケーションの登録を解除するには、テンプレートに指定されているアンインストール スクリプトを使用します。 このコマンドは、インスタンスをクリーンアップするのに時間がかかるため、このスクリプトの直後に install.sh コマンドを実行することはお勧めしません。 

```bash
./uninstall.sh
```

## <a name="next-steps"></a>次のステップ
このクイック スタートでは、次の方法について説明しました。
> [!div class="checklist"]
> * Linux コンテナー アプリケーションの Azure へのデプロイ
> * Service Fabric クラスターでのコンテナーのフェールオーバー
> * Service Fabric クラスターでのコンテナーのスケール

* [Service Fabric でのコンテナー](service-fabric-containers-overview.md)の実行について確認します。
* Service Fabric の[アプリケーション ライフサイクル](service-fabric-application-lifecycle.md)について確認します。
* GitHub で [Service Fabric コンテナーのコード サンプル](https://github.com/Azure-Samples/service-fabric-dotnet-containers)を確認します。

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
