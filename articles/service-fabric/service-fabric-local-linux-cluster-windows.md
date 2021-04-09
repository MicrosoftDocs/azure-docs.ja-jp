---
title: Windows で Azure Service Fabric Linux クラスターを設定する
description: この記事では、Windows 開発用マシンで実行される Service Fabric Linux クラスターを設定する方法について説明します。 このアプローチは、クロスプラットフォームの開発に役立ちます。
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 7b25a84e76773baea9f17430df1b7ba13aa661aa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93087079"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Windows 開発用マシンで Linux Service Fabric クラスターを設定する

このドキュメントでは、Windows 開発用マシン上でローカル Linux Service Fabric クラスターを設定する方法について説明します。 ローカル Linux クラスターを設定すると、Linux クラスターを対象としているが、Windows マシンで開発されたアプリケーションをすばやくテストするときに便利です。

## <a name="prerequisites"></a>前提条件
Linux ベースの Service Fabric クラスターは Windows 上では実行されませんが、クロスプラットフォームでのプロトタイプ作成を可能にするために、Linux Service Fabric ワンボックス クラスター Docker コンテナーを用意してあります。これは Docker for Windows を介してデプロイすることができます。

作業を開始する前に、以下を行う必要があります。

* 少なくとも 4 GB の RAM
* [Docker for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows) の最新バージョン
* Docker は Linux コンテナー モードで実行する必要があります

>[!TIP]
> ご利用の Windows コンピューターに Docker をインストールには、[Docker のドキュメント](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions)の手順に従ってください。 インストールの完了後、[インストールを確認](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine)します。
>

## <a name="create-a-local-container-and-setup-service-fabric"></a>ローカル コンテナーを作成し、Service Fabric をセットアップする
ローカル Docker コンテナーを設定し、そこで Service Fabric クラスターを実行するには、次の手順を行います。


1. ホスト上の Docker デーモン構成を以下を使用して更新し、Docker デーモンを再起動します。 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    更新するには、次の順に移動することをお勧めします。 

    * [Docker] アイコン > [設定] > [Docker エンジン]
    * 上にリストされた新しいフィールドを追加する
    * 適用して再起動 - Docker デーモンを再起動し、変更を反映させます。

2. PowerShell を介してクラスターを起動します。<br/>
    <b>Ubuntu 18.04 LTS:</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16.04 LTS:</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > 既定では、最新バージョンの Service Fabric を含んだイメージがプルされます。 特定のリビジョンについては、Docker Hub の [Service Fabric Onebox](https://hub.docker.com/_/microsoft-service-fabric-onebox) ページを参照してください。



3. 省略可能: 拡張 Service Fabric イメージをビルドします。

    新しいディレクトリに、`Dockerfile` という名前のファイルを作成して、カスタマイズしたイメージをビルドします。

    >[!NOTE]
    >Dockerfile を使用して上記のイメージを調整すれば、さらにプログラムまたは依存関係をコンテナーに追加することができます。
    >たとえば、「`RUN apt-get install nodejs -y`」を追加すれば、ゲスト実行可能ファイルとして `nodejs` アプリケーションに対応することができます。
    ```Dockerfile
    FROM mcr.microsoft.com/service-fabric/onebox:u18
    RUN apt-get install nodejs -y
    EXPOSE 19080 19000 80 443
    WORKDIR /home/ClusterDeployer
    CMD ["./ClusterDeployer.sh"]
    ```
    
    >[!TIP]
    > 既定では、最新バージョンの Service Fabric を含んだイメージがプルされます。 特定のリビジョンについては、[Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/) のページをご覧ください。

    再利用可能なイメージを `Dockerfile` から構築するには、ターミナルを開き、`cd` で `Dockerfile` の格納場所に移動して次を実行します。

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >この操作にはしばらく時間がかかりますが、実行するのは 1 回でかまいません。

    これで、Service Fabric のローカル コピーを、必要なときにいつでも、次を実行することですぐに起動することができます。

    ```powershell 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >操作の際にわかりやすくするために、コンテナー インスタンスの名前を指定します。 
    >
    >アプリケーションが特定のポートでリッスンしている場合は、追加の `-p` タグを使用してポートを指定する必要があります。 たとえば、アプリケーションがポート 8080 でリッスンしている場合は、次の `-p` タグを追加します。
    >
    >`docker run -itd -p 19000:19000 -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:u18`
    >


4. 少しするとクラスターが起動され、次のコマンドを使ってログを確認したり、ダッシュボードに切り替えてクラスターの正常性を確認したりすることができます (`http://localhost:19080`)。

    ```powershell 
    docker logs sftestcluster
    ```

5. 手順 4 で確認したとおりにクラスターが正常にデプロイされたら、Windows コンピューターから ``http://localhost:19080`` にアクセスして、Service Fabric Explorer ダッシュボードを見つけることができます。 この時点で、Windows 開発者用マシンからツールを使用してこのクラスターに接続し、Linux Service Fabric クラスターを対象としたアプリケーションを配置します。 

    > [!NOTE]
    > Eclipse プラグインは、現在、Windows でサポートされていません。 

6. 完了したら、コンテナーを停止し、次のコマンドを使用してコンテナーをクリーンアップします。

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>既知の制限事項 
 
 Mac 用のコンテナーで実行されているローカル クラスターの既知の制限は、次のとおりです。 
 
 * DNS サービスは実行されません。現在、コンテナー内ではサポートされていません。 [問題 #132](https://github.com/Microsoft/service-fabric/issues/132)
 * コンテナーベースのアプリを実行するには、Linux ホスト上で SF を実行する必要があります。 入れ子になったコンテナー アプリは現在サポートされていません。

## <a name="next-steps"></a>次のステップ
* [Yeoman を使用して Linux で最初の Service Fabric Java アプリケーションを作成してデプロイする](service-fabric-create-your-first-linux-application-with-java.md)
* [Eclipse](./service-fabric-get-started-eclipse.md) の概要
* その他の [Java サンプル](https://github.com/Azure-Samples/service-fabric-java-getting-started)を確認する
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
