---
title: "Mac OS X での開発環境のセットアップ | Microsoft Docs"
description: "ランタイム、SDK、およびツールをインストールし、ローカル開発クラスターを作成します。 このセットアップを完了すると、Mac OS X でアプリケーションを構築する準備が整います。"
services: service-fabric
documentationcenter: java
author: saysa
manager: raunakp
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: e5d14eb0a656d67030f4c0d3d510aec0e9cafae7
ms.lasthandoff: 03/29/2017


---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Mac OS X で開発環境をセットアップする
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Linux クラスターで実行される Service Fabric アプリケーションを Mac OS X を使用して構築できます。この記事では、開発用に Mac をセットアップする方法について説明します。

## <a name="prerequisites"></a>前提条件
Service Fabric は、OS X ではネイティブに実行されません。Microsoft では、ローカルの Service Fabric クラスターを実行できるように、Vagrant と VirtualBox を使用して事前に構成済みの Ubuntu 仮想マシンを用意しています。 作業を開始する前に、以下を行う必要があります。

* [Vagrant (v1.8.4 以降)](http://www.vagrantup.com/downloads.html)
* [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

>[!NOTE]
> 相互にサポートされている Vagrant と VirtualBox のバージョンを使用する必要があります。 サポートされていない VirtualBox バージョンでは、Vagrant の動作が不安定になる可能性があります。
>

## <a name="create-the-local-vm"></a>ローカル VM を作成する
5 ノード構成の Service Fabric クラスターを保持するローカル VM を作成するには、次の手順を実行します。

1. `Vagrantfile` リポジトリを複製します。

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```
    この手順を実行すると、VM 構成と VM のダウンロード元の場所が含まれた `Vagrantfile` ファイルがダウンロードされます。

   
2. リポジトリのローカル クローンに移動します。

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```
3. (省略可能) 既定の VM 設定を変更します。

    既定では、ローカル VM は次のように構成されています。

   * メモリの割り当て 3 GB
   * IP 192.168.50.50 で構成されたプライベート ホスト ネットワーク。Mac ホストからのトラフィックに対してパススルーが有効化されています。

     これらの設定のどちらかを変更することも、`Vagrantfile` で VM に他の構成を追加することもできます。 構成オプションの詳細な一覧については、 [Vagrant のドキュメント](http://www.vagrantup.com/docs) をご覧ください。
4. VM の作成

    ```bash
    vagrant up
    ```

   この手順を実行すると、事前構成済みの VM イメージがダウンロードされてローカルで起動され、ローカル Service Fabric クラスターがセットアップされます。 この処理には数分かかります。 セットアップが正常に完了すると、クラスターが開始されていることを示すメッセージが出力に表示されます。

    ![Cluster setup starting following VM provisioning][cluster-setup-script]

>[!TIP]
> VM のダウンロードに長い時間がかかる場合、wget または curl を使用してダウンロードできます。そのほか、ブラウザーを使い、`Vagrantfile` ファイルの **config.vm.box_url** で指定されたリンク先に移動してダウンロードすることもできます。 VM をローカルにダウンロードした後、イメージのダウンロード先のローカル パスを指すように、`Vagrantfile` を編集します。 たとえば、イメージのダウンロード先が /home/users/test/azureservicefabric.tp8.box であった場合、**config.vm.box_url** をこのパスに設定します。
>

5. クラスターが正しくセットアップされていることをテストするために、Service Fabric Explorer (http://192.168.50.50:19080/Explorer) に移動します (プライベート ネットワーク IP アドレスが既定値のままであると仮定しています)。

    ![Service Fabric Explorer viewed from the host Mac][sfx-mac]

## <a name="install-the-service-fabric-plugin-for-eclipse-neon"></a>Eclipse Neon 用の Service Fabric プラグインをインストールする

Service Fabric には、Java サービスの作成、構築、デプロイのプロセスを簡略化できる、**Java IDE 用の Eclipse Neon** 向けのプラグインが用意されています。 Service Fabric Eclipse プラグインのインストールまたは更新については、こちらの概要の[ドキュメント](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon)に記載されたインストール手順を利用できます。

## <a name="using-service-fabric-eclipse-plugin-on-mac"></a>Mac で Service Fabric Eclipse プラグインを使用する

[Service Fabric Eclipse プラグインのドキュメント](service-fabric-get-started-eclipse.md)に記載された手順をひととおりご覧ください。 Mac ホストの Vagrant ゲスト コンテナーを使用した Service Fabric Java アプリケーションの作成、構築、デプロイの手順は、次の点を除いて概要のドキュメントとほぼ同じです。

* Service Fabric Java アプリケーションには Service Fabric ライブラリが必要なため、Eclipse プロジェクトを共有パスで作成する必要があります。 既定では、``Vagrantfile`` が存在するホストのパスの内容は、ゲストの ``/vagrant`` パスと共有されます。
* パス (たとえば ``~/home/john/allprojects/``) に ``Vagrantfile`` がある場合、Service Fabric プロジェクト ``MyActor`` は場所 ``~/home/john/allprojects/MyActor`` に作成する必要があり、Eclipse ワークスペースへのパスは ``~/home/john/allprojects`` になります。

## <a name="next-steps"></a>次のステップ
<!-- Links -->
* [Yeoman を使用して Linux で最初の Service Fabric Java アプリケーションを作成してデプロイする](service-fabric-create-your-first-linux-application-with-java.md)
* [Eclipse 用の Service Fabric プラグインを使用して Linux で最初の Service Fabric Java アプリケーションを作成してデプロイする](service-fabric-get-started-eclipse.md)
* [Azure Portal で Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-portal.md)
* [Azure Resource Manager を使用して Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-arm.md)
* [Service Fabric アプリケーション モデルを理解する](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

