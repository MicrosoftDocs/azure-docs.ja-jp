---
title: "C# を使用して Linux で初めての Azure マイクロサービス アプリを作成する | Microsoft Docs"
description: "C# を使用して、Service Fabric アプリケーションを作成し、デプロイします"
services: service-fabric
documentationcenter: csharp
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 5a96d21d-fa4a-4dc2-abe8-a830a3482fb1
ms.service: service-fabric
ms.devlang: csharp
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/21/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: adcafaa5522fcddc0a01eb1dc8deba04ebfc38f2
ms.contentlocale: ja-jp
ms.lasthandoff: 08/24/2017

---
# <a name="create-your-first-azure-service-fabric-application"></a>最初の Azure Service Fabric アプリケーションを作成する
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Service Fabric では、.NET Core と Java の両方で Linux 上のサービスを構築するための SDK を提供しています。 このチュートリアルでは Linux 用アプリケーションを作成し、C# (.NET Core) を使用してサービスを構築する方法について説明します。

## <a name="prerequisites"></a>前提条件
作業を開始する前に、 [Linux 開発環境がセットアップ](service-fabric-get-started-linux.md)されていることを確認してください。 Mac OS X を使用している場合は、 [Vagrant を使用して仮想マシンに Linux ワンボックス環境を設定](service-fabric-get-started-mac.md)します。

また、[Service Fabric CLI](service-fabric-cli.md) をインストールしてください。

### <a name="install-and-set-up-the-generators-for-csharp"></a>CSharp のジェネレーターのインストールとセットアップ
Service Fabric には、ターミナルから Yeoman テンプレート ジェネレーターを使って Service Fabric CSharp アプリケーションを作成できるスキャフォールディング ツールが用意されています。 以下の手順に従って、ご利用のマシンに CSharp 用の Service Fabric Yeoman テンプレート ジェネレーターをセットアップしてください。
1. マシンに nodejs と NPM をインストールします。

  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```
2. NPM からマシンに [Yeoman](http://yeoman.io/) テンプレート ジェネレーターをインストールします。

  ```bash
  sudo npm install -g yo
  ```
3. NPM から Service Fabric Yeo Java アプリケーション ジェネレーターをインストールします。

  ```bash
  sudo npm install -g generator-azuresfcsharp
  ```

## <a name="create-the-application"></a>アプリケーションを作成する
Service Fabric のアプリケーションには、アプリケーションの機能を提供する際にそれぞれ特定の役割を果たすサービスを 1 つ以上含めることができます。 直前の手順でインストールした CSharp 用の Service Fabric [Yeoman](http://yeoman.io/) ジェネレーターを使用すると、初めてサービスを作成したり、後で追加したりする作業が簡単になります。 Yeoman を使用して、単一のサービスを持つアプリケーションを作成しましょう。

1. ターミナルで、コマンド「`yo azuresfcsharp`」を入力してスキャフォールディングの構築を開始します。
2. アプリケーションに名前を付けます。
3. 最初のサービスの種類を選択し、名前を付けます。 このチュートリアルでは、"Reliable Actor Service" を選択します。

   ![C# 用の Service Fabric Yeoman ジェネレーター][sf-yeoman]

> [!NOTE]
> オプションの詳細については、「 [Service Fabric プログラミング モデルの概要](service-fabric-choose-framework.md)」を参照してください。
>
>

## <a name="build-the-application"></a>アプリケーションのビルド
Service Fabric Yeoman テンプレートには、ビルド スクリプトが含まれています。(アプリケーションのフォルダーへの移動後に) これを使用してターミナルからアプリをビルドできます。

  ```sh
 cd myapp
 ./build.sh
  ```

## <a name="deploy-the-application"></a>アプリケーションのデプロイ

ビルドしたアプリケーションは、ローカル クラスターにデプロイできます。

1. ローカルの Service Fabric クラスターに接続します。

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. テンプレートに用意されているインストール スクリプトを実行してクラスターのイメージ ストアにアプリケーション パッケージをコピーし、アプリケーションの種類を登録して、アプリケーションのインスタンスを作成します。

    ```bash
    ./install.sh
    ```

ビルドしたアプリケーションは、他のすべての Service Fabric アプリケーションと同じようにデプロイできます。 詳細な手順については、[Service Fabric CLI を使用した Service Fabric アプリケーションの管理](service-fabric-application-lifecycle-sfctl.md)についてのドキュメントを参照してください。

これらのコマンドのパラメーターは、アプリケーション パッケージ内の生成されたマニフェストで確認できます。

アプリケーションのデプロイ後、ブラウザーを開いて [http://localhost:19080/Explorer](http://localhost:19080/Explorer) の [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) に移動します。 次に、**Applications** ノードを展開し、アプリケーションの種類のエントリと、その種類の最初のインスタンスのエントリができたことを確認してください。

## <a name="start-the-test-client-and-perform-a-failover"></a>テスト クライアントの起動と、フェールオーバーの実行
アクター プロジェクトは、それ自体では何も行いません。 これにメッセージを送信する別のサービスまたはクライアントが必要です。 アクター テンプレートには、アクター サービスとの対話に使用できる簡単なテスト スクリプトが含まれています。

1. ウォッチ ユーティリティを使用してスクリプトを実行し、アクター サービスの出力を確認します。

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```
2. Service Fabric Explorer で、アクター サービスのプライマリ レプリカをホストしているノードを見つけます。 次のスクリーンショットでは、ノード 3 です。

    ![Finding the primary replica in Service Fabric Explorer][sfx-primary]
3. 前の手順で見つけたノードをクリックし、[アクション] メニューの **[非アクティブにする (再起動)]** を選択します。 このアクションによって、ローカル クラスターのノードのいずれかが再起動され、別のノードで実行されているセカンダリ レプリカに強制的にフェールオーバーされます。 このアクションを行うときは、テスト クライアントからの出力に注意してください。また、フェールオーバーにかかわらず、カウンターが増加していることに注意してください。

## <a name="adding-more-services-to-an-existing-application"></a>既存アプリケーションへのサービスの追加

`yo` を使用して作成したアプリケーションにサービスを追加するには、次の手順を実行します。
1. ディレクトリを既存アプリケーションのルートに変更します。  たとえば、Yeoman で作成したアプリケーションが `MyApplication` の場合は、`cd ~/YeomanSamples/MyApplication` です。
2. `yo azuresfcsharp:AddService` を実行します。

## <a name="migrating-from-projectjson-to-csproj"></a>project.json から .csproj への移行
1. 'dotnet migrate' をプロジェクトのルート ディレクトリで実行すると、すべての project.json が csproj 形式に移行します。
2. それに応じて、プロジェクト ファイル内の csproj ファイルのプロジェクト参照を更新します。
3. build.sh でプロジェクト ファイルの名前を csproj ファイルに更新します。

## <a name="next-steps"></a>次のステップ

* [Service Fabric Reliable Actors の概要](service-fabric-reliable-actors-introduction.md)
* [Service Fabric CLI を使用した Service Fabric クラスターの対話操作](service-fabric-cli.md)
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。
* [Service Fabric CLI の概要](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png

