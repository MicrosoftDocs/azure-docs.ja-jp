---
title: Windows で Azure Service Fabric Linux クラスターを設定する | Microsoft Docs
description: この記事では、Windows 開発用マシンで実行される Service Fabric Linux クラスターを設定する方法について説明します。 これは、クロス プラットフォーム開発で特に便利です。
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: e700250a6ebcdb82f99c1b460a510811d7ceb96c
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819737"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Windows 開発用マシンで Linux Service Fabric クラスターを設定する

このドキュメントでは、Windows 開発用マシンでローカル Service Fabric を設定する方法について説明します。 ローカル Linux クラスターを設定すると、Linux クラスターを対象としているが、Windows マシンで開発されたアプリケーションをすばやくテストするときに便利です。

## <a name="prerequisites"></a>前提条件
Linux ベースの Service Fabric クラスターは、Windows ではネイティブに実行されません。 ローカル Service Fabric クラスターを実行するために、事前構成済みの Docker コンテナー イメージが用意されています。 作業を開始する前に、以下を行う必要があります。

* 少なくとも 4 GB の RAM
* 最新バージョンの [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Docker は Linux モードで実行されている必要があります

>[!TIP]
> * Docker の公式[ドキュメント](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions)に記載されている手順に従って Windows に Docker をインストールしてください。 
> * インストールが完了したら、[ここ](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine)に記載されている手順に従って、Docker が正しくインストールされているかどうかを確認します。


## <a name="create-a-local-container-and-setup-service-fabric"></a>ローカル コンテナーを作成し、Service Fabric をセットアップする
ローカル Docker コンテナーをセットアップし、そこでサービス ファブリック クラスターを実行するには、PowerShell で次の手順を実行します。


1. ホスト上の Docker デーモン構成を以下を使用して更新し、Docker デーモンを再起動します。 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    Docker アイコン、[設定]、[デーモン]、[詳細設定] の順に選択し、そこで更新することをお勧めします。 次に、Docker デーモンを再起動し、変更を反映させます。 

2. 新しいディレクトリに、Service Fabric イメージを構築する `Dockerfile` というファイルを作成します。

    ```Dockerfile
    FROM microsoft/service-fabric-onebox
    WORKDIR /home/ClusterDeployer
    RUN ./setup.sh
    #Generate the local
    RUN locale-gen en_US.UTF-8
    #Set environment variables
    ENV LANG=en_US.UTF-8
    ENV LANGUAGE=en_US:en
    ENV LC_ALL=en_US.UTF-8
    EXPOSE 19080 19000 80 443
    #Start SSH before running the cluster
    CMD /etc/init.d/ssh start && ./run.sh
    ```

    >[!NOTE]
    >実際のコンテナーには、このファイルを応用して他のプログラムや依存関係を追加することができます。
    >たとえば、「`RUN apt-get install nodejs -y`」を追加すれば、ゲスト実行可能ファイルとして `nodejs` アプリケーションに対応することができます。
    
    >[!TIP]
    > 既定では、最新バージョンの Service Fabric を含んだイメージがプルされます。 特定のリビジョンについては、[Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/) のページをご覧ください。

3. 再利用可能なイメージを `Dockerfile` から構築するには、ターミナルを開き、`cd` で `Dockerfile` の格納場所に移動して次のコマンドを実行します。

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >この操作にはしばらく時間がかかりますが、実行するのは 1 回でかまいません。

4. これで、Service Fabric のローカル コピーを、必要なときにいつでも、次のコマンドを実行することですぐに起動することができます。

    ```powershell 
    docker run --name sftestcluster -d -v //var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >操作の際にわかりやすくするために、コンテナー インスタンスの名前を指定します。 
    >
    >アプリケーションが特定のポートでリッスンしている場合は、追加の `-p` タグを使用してポートを指定する必要があります。 たとえば、アプリケーションがポート 8080 でリッスンしている場合は、次の `-p` タグを追加します。
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox`
    >

5. 少しするとクラスターが起動され、次のコマンドを使ってログを確認したり、ダッシュボードに切り替えてクラスターの正常性を確認したりすることができます ([http://localhost:19080](http://localhost:19080))。

    ```powershell 
    docker logs sftestcluster
    ```

6. 手順 5. が正常に完了したら、Windows から ``http://localhost:19080`` にアクセスします。Service Fabric Explorer が表示されます。 この時点で、Windows 開発者用マシンから任意のツールを使用してこのクラスターに接続し、Linux Service Fabric クラスターを対象としたアプリケーションを配置します。 

    > [!NOTE]
    > Eclipse プラグインは、現在、Windows でサポートされていません。 

7. 完了したら、コンテナーを停止し、次のコマンドを使用してコンテナーをクリーンアップします。

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>既知の制限事項 
 
 Mac 用のコンテナーで実行されているローカル クラスターの既知の制限は、次のとおりです。 
 
 * DNS サービスが実行されず、サポートされていない [問題 #132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="next-steps"></a>次の手順
* [Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse) の概要
* その他の [Java サンプル](https://github.com/Azure-Samples/service-fabric-java-getting-started)を確認する


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
