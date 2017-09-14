---
title: "Service Fabric プロジェクトの作成の次の手順 | Microsoft Docs"
description: "この記事には、Service Fabric の中心的な開発タスク セットへのリンクが含まれています"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 299d1f97-1ca9-440d-9f81-d1d0dd2bf4df
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: rwike77
ms.translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 8208a1a41388a8cc36f3702bd0cad2bb82e16403
ms.contentlocale: ja-jp
ms.lasthandoff: 03/31/2017


---
# <a name="your-service-fabric-application-and-next-steps"></a>Service Fabric アプリケーションと次の手順
Azure Service Fabric アプリケーションが作成されました。 この記事では、プロジェクトの構成と潜在的な次の手順について説明します。

## <a name="your-application"></a>アプリケーション
すべての新しいアプリケーションには、アプリケーション プロジェクトが含まれています。 選択したサービスのタイプに応じて、さらに 1 つか 2 つのプロジェクトがある場合があります。

### <a name="the-application-project"></a>アプリケーション プロジェクト
アプリケーション プロジェクトは次で構成されます。

* アプリケーションを構成するサービスへの一連の参照。
* クラスター エンドポイントや既定でデプロイメントのアップグレードを実行するかどうかなど、異なる環境で作業するための設定を管理する 3 つの発行プロファイル (1 ノード ローカル、5 ノード ローカル、およびクラウド)。
* サービス用に作成するパーティション数などの環境に固有のアプリケーション構成を維持するために使用する 3 つのアプリケーション パラメーター ファイル (上記と同じ)。
* コマンドラインまたは自動化された継続的インテグレーションおよびデプロイ パイプラインの一環としての、アプリケーションをデプロイするために使用するデプロイ スクリプト。
* アプリケーションを説明するアプリケーション マニフェスト。 マニフェストは、ApplicationPackageRoot フォルダーにあります。

### <a name="stateless-service"></a>ステートレス サービス
新しいステートレス サービスを追加すると、Visual Studio は `StatelessService`から継承した型を含むソリューションにサービス プロジェクトを追加します。 サービスは、カウンター内のローカル変数をインクリメントします。

### <a name="stateful-service"></a>ステートフル サービス
新しいステートフル サービスを追加すると、Visual Studio は `StatefulService`から継承した型を含むソリューションにサービス プロジェクトを追加します。 サービスは、`RunAsync` メソッドでカウンターをインクリメントし、`ReliableDictionary` に結果を格納します。

### <a name="actor-service"></a>アクター サービス
新しい Reliable Actor を追加すると、Visual Studio はソリューションにアクター プロジェクトとインターフェイス プロジェクトを追加します。

アクター プロジェクトは、アクターの状態に確実に保持されるカウンターの値を取得および設定するメソッドを提供します。 インターフェイス プロジェクトは、その他のサービスでアクターを呼び出すために使用できるインターフェイスを提供します。

### <a name="stateless-web-api"></a>ステートレス Web API
ステートレス Web API プロジェクトでは、外部クライアントに対してアプリケーションを開くときに使用できる基本的な Web サービスを提供します。 プロジェクトの構造の詳細については、「 [はじめに: OWIN 自己ホストによる Service Fabric Web API サービス](service-fabric-reliable-services-communication-webapi.md)」をご覧ください。


### <a name="aspnet-core"></a>ASP.NET Core
Service Fabric SDK には、スタンドアロン ASP.NET Core プロジェクトで利用できるのと同じ ASP.NET Core テンプレート セット (空、[Web API][aspnet-webapi]、および [Web アプリケーション][aspnet-webapp]) が用意されています。

### <a name="guest-executables-and-guest-containers"></a>ゲストの実行可能ファイルおよびゲスト コンテナー

Service Fabric 'guest' は、プラットフォームのプログラミング モデルに組み込まれていないサービスです。 ゲスト用にバイナリを[直接アプリケーション パッケージに入れる](service-fabric-deploy-existing-app.md)か、[コンテナー イメージを使用](service-fabric-deploy-container.md)してパッケージできます。 どちらの場合も、Visual Studio によってアプリケーション プロジェクトの **ApplicationPackageRoot** フォルダー内に必要な成果物が作成されます。 コードは既に別の場所に存在するため、Visual Studio は新しいサービス プロジェクトを作成しません。 ゲスト プロジェクトを Service Fabric アプリケーション プロジェクトと一緒に管理する場合は、それらを同じ Visual Studio ソリューションに追加できます。

## <a name="next-steps"></a>次のステップ
### <a name="create-an-azure-cluster"></a>Azure クラスターの作成
Service Fabric SDK には、開発およびテスト用のローカル クラスターが用意されています。 Azure でのクラスターの作成については、[Azure Portal からの Service Fabric クラスターのセットアップ][create-cluster-in-portal]に関する記事を参照してください。

### <a name="publish-your-application-to-azure"></a>Azure へのアプリケーションの発行
Visual Studio から Azure クラスターに直接アプリケーションを発行することができます。 詳細については、[Azure へのアプリケーションの発行][publish-app-to-azure]に関するページを参照してください。

### <a name="use-service-fabric-explorer-to-visualize-your-cluster"></a>Service Fabric Explorer を使用したクラスターの視覚化
Service Fabric Explorer には、デプロイ済みのアプリケーションや物理的なレイアウトなど、クラスターを視覚化するための簡単な方法が用意されています。 詳細については、「[Service Fabric Explorer を使用したクラスターの視覚化]」[visualize-with-sfx]を参照してください。

### <a name="version-and-upgrade-your-services"></a>サービスのアップグレードとバージョン管理
Service Fabric では、アプリケーションにおいて、独立したサービスの個別のバージョン管理とアップグレードを実行できます。 詳細については、「[サービスのバージョン管理とアップグレード][app-upgrade-tutorial]」を参照してください。

### <a name="configure-continuous-integration-with-visual-studio-team-services"></a>Visual Studio Team Services を使用した継続的な統合の構成
Service Fabric アプリケーション向けに継続的な統合プロセスを設定する方法については、「[Visual Studio Team Services を使用した継続的な統合の構成][ci-with-vso]」を参照してください。

<!-- Links -->
[add-web-frontend]: service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: service-fabric-publish-app-remote-cluster.md
[visualize-with-sfx]: service-fabric-visualizing-your-cluster.md
[ci-with-vso]: service-fabric-set-up-continuous-integration.md
[reliable-services-webapi]: service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: service-fabric-application-upgrade-tutorial.md
[aspnet-webapi]: https://docs.asp.net/en/latest/tutorials/first-web-api.html
[aspnet-webapp]: https://docs.asp.net/en/latest/tutorials/first-mvc-app/index.html

