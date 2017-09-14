---
title: "パーティ クラスターに Azure Service Fabric アプリケーションをデプロイする | Microsoft Docs"
description: "パーティ クラスターにアプリケーションをデプロイする方法を説明します。"
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: mikhegn
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: 6624d683edb548a65d07ab4012c599faaf940ed0
ms.contentlocale: ja-jp
ms.lasthandoff: 08/17/2017

---

# <a name="deploy-an-application-to-a-party-cluster-in-azure"></a>Azure でパーティ クラスターにアプリケーションをデプロイする
このチュートリアルはシリーズの第 2 部です。Azure でパーティ クラスターに Azure Service Fabric アプリケーションをデプロイする方法について説明します。

シリーズの第 2 部で学習する内容は次のとおりです。
> [!div class="checklist"]
> * Visual Studio を使用してリモート クラスターにアプリケーションをデプロイする
> * Service Fabric Explorer を使用してクラスターからアプリケーションを削除する

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [.NET Service Fabric アプリケーションを構築する](service-fabric-tutorial-create-dotnet-app.md)
> * アプリケーションをリモート クラスターにデプロイする
> * [Visual Studio Team Services を使用して CI/CD を構成する](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)

## <a name="prerequisites"></a>前提条件
このチュートリアルを開始する前に
- Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成します。
- [Visual Studio 2017 をインストール](https://www.visualstudio.com/)し、**Azure 開発**ワークロードと **ASP.NET および Web 開発**ワークロードをインストールします。
- [Service Fabric SDK をインストール](service-fabric-get-started.md)します。

## <a name="download-the-voting-sample-application"></a>投票サンプル アプリケーションをダウンロードする
[このチュートリアル シリーズの第 1 部](service-fabric-tutorial-create-dotnet-app.md)で投票サンプル アプリケーションをビルドしていない場合は、ダウンロードすることができます。 コマンド ウィンドウで、次のコマンドを実行して、サンプル アプリのリポジトリをローカル コンピューターに複製します。

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-a-party-cluster"></a>パーティ クラスターをセットアップする
パーティ クラスターは、Azure でホストされる無料の期間限定の Service Fabric クラスターであり、Service Fabric チームによって実行されます。このクラスターには、だれでもアプリケーションをデプロイして、プラットフォームについて学習することができます。 無料試用版をお試しください!

パーティ クラスターへのアクセス権を取得するには、次のサイト (http://aka.ms/tryservicefabric) を参照し、指示に従ってクラスターへのアクセス権を取得します。 パーティ クラスターへのアクセス権を取得するには、Facebook または GitHub アカウントが必要です。

> [!NOTE]
> パーティ クラスターはセキュリティで保護されないため、ご利用のアプリケーションとそれに入力するデータが他のユーザーに表示される可能性があります。 他のユーザーに見せたくないものは一切デプロイしないでください。 使用条件の詳細に必ず目を通してください。

## <a name="configure-the-listening-port"></a>リスニング ポートを構成する
VotingWeb フロントエンド サービスが作成されると、Visual Studio は、リッスンするサービスのポートをランダムに選択します。  VotingWeb サービスは、このアプリケーションのフロント エンドとして機能して外部のトラフィックを受け入れるため、このサービスを固定のウェルノウン ポートにバインドしましょう。 ソリューション エクスプローラーで、*VotingWeb/PackageRoot/ServiceManifest.xml* を開きます。  **Resources** セクションの **Endpoint** リソースを検索し、**Port** 値を 80 に変更します。

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="80" />
    </Endpoints>
  </Resources>
```

また、Voting プロジェクトの Application URL プロパティ値を更新し、F5 キーを押してデバッグするときに正しいポートに対して Web ブラウザーが開くようにします。  ソリューション エクスプローラーで、**Voting** プロジェクトを選択し、**Application URL** プロパティを更新します。

![アプリケーションの URL](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-url.png)

## <a name="deploy-the-app-to-the-azure"></a>Azure にアプリケーションをデプロイする
これで、アプリケーションの準備ができたので、Visual Studio から直接パーティ クラスターにデプロイできます。

1. ソリューション エクスプローラーで **[Voting]** を右クリックして、**[発行]** を選択します。

    ![[発行] ダイアログ](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

2. **[接続のエンドポイント]** フィールドにパーティ クラスターの接続エンドポイントを入力し、**[発行]** をクリックします。

    発行が完了した後は、ブラウザーからアプリケーションに要求を送信できます。

3. 好みのブラウザーを開き、クラスター アドレス (ポート情報を除いた接続エンドポイント。たとえば、win1kw5649s.westus.cloudapp.azure.com) を入力します。

    アプリケーションをローカルで実行するときに確認したのと同じ結果が表示されます。

    ![クラスターからの API 応答](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="remove-the-application-from-a-cluster-using-service-fabric-explorer"></a>Service Fabric Explorer を使用してクラスターからアプリケーションを削除する
Service Fabric Explorer は、Service Fabric クラスター内のアプリケーションを参照および管理するためのグラフィカル ユーザー インターフェイスです。

パーティ クラスターからアプリケーションを削除するには:

1. パーティ クラスターのサインアップ ページで提供されるリンクを使用して、Service Fabric Explorer を参照します。 たとえば、http://win1kw5649s.westus.cloudapp.azure.com:19080/Explorer/index.html にアクセスします。

2. Service Fabric Explorer で、左側にあるツリービューの **[fabric://Voting]** ノードに移動します。

3. 右側の **[基本]** ウィンドウの **[アクション]** をクリックして、**[アプリケーションの削除]** を選択します。 アプリケーション インスタンスの削除を承認すると、クラスターで実行されているアプリケーション インスタンスが削除されます。

![Service Fabric Explorer でのアプリケーションの削除](./media/service-fabric-tutorial-deploy-app-to-party-cluster/delete-application.png)

## <a name="remove-the-application-type-from-a-cluster-using-service-fabric-explorer"></a>Service Fabric Explorer を使用してクラスターからアプリケーションの型を削除する
アプリケーションは、Service Fabric クラスター内にアプリケーションの型としてデプロイされます。これにより、アプリケーションの複数のインスタンスやバージョンをクラスター内で実行できます。 アプリケーションの実行中のインスタンスを削除した後は、その型も削除でき、デプロイのクリーンアップを完了できます。

Service Fabric のアプリケーション モデルの詳細については、「[Service Fabric でのアプリケーションのモデル化](service-fabric-application-model.md)」を参照してください。

1. ツリービューの **[VotingType]** ノードに移動します。

2. 右側の **[基本]** ウィンドウの **[アクション]** をクリックして、**[Unprovision Type]\(型のプロビジョニングを解除\)** を選択します。 アプリケーションの型のプロビジョニング解除を承認します。

![Service Fabric Explorer でのアプリケーションの型のプロビジョニング解除](./media/service-fabric-tutorial-deploy-app-to-party-cluster/unprovision-type.png)

これでチュートリアルは終了します。

## <a name="next-steps"></a>次のステップ
このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Visual Studio を使用してリモート クラスターにアプリケーションをデプロイする
> * Service Fabric Explorer を使用してクラスターからアプリケーションを削除する

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [Visual Studio Team Services を使用して継続的インテグレーションを設定する](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
