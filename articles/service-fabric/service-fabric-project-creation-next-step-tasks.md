---
title: "Service Fabric プロジェクトの作成の次の手順 | Microsoft Docs"
description: "この記事には、Service Fabric の中心的な開発タスク セットへのリンクが含まれています"
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: 299d1f97-1ca9-440d-9f81-d1d0dd2bf4df
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b4b2424e5efe3392b08e58ceb05ec63f15c7ad32


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
Service Fabric SDK には、スタンドアロン ASP.NET Core プロジェクトで利用できるのと同じ ASP.NET Core テンプレート セット (空、[Web API][aspnet-webapi]、および [Web Application][aspnet-webapp]) が用意されています。

## <a name="next-steps"></a>次のステップ
### <a name="create-an-azure-cluster"></a>Azure クラスターの作成
Service Fabric SDK には、開発およびテスト用のローカル クラスターが用意されています。 Azure でのクラスターの作成については、[Azure Portal からの Service Fabric クラスターのセットアップ][create-cluster-in-portal]に関する記事を参照してください。

### <a name="try-deploying-to-azure-for-free-with-party-clusters"></a>パーティ クラスターを使用した Azure へのデプロイの無料試行
独自のクラスターを設定せずに Azure でのアプリケーションのデプロイと管理を試してみる場合は、無料の [パーティ クラスター サービス](http://aka.ms/tryservicefabric)を使用できます。

### <a name="publish-your-application-to-azure"></a>Azure へのアプリケーションの発行
Visual Studio から Azure クラスターに直接アプリケーションを発行することができます。 方法については、[Azure へのアプリケーションの発行][publish-app-to-azure]に関するページを参照してください。

### <a name="use-service-fabric-explorer-to-visualize-your-cluster"></a>Service Fabric Explorer を使用したクラスターの視覚化
Service Fabric Explorer には、デプロイ済みのアプリケーションや物理的なレイアウトなど、クラスターを視覚化するための簡単な方法が用意されています。 詳細については、「[Service Fabric Explorer を使用したクラスターの視覚化]」[visualize-with-sfx]を参照してください。

### <a name="version-and-upgrade-your-services"></a>サービスのアップグレードとバージョン管理
Service Fabric では、アプリケーションにおいて、独立したサービスの個別のバージョン管理とアップグレードを実行できます。 詳細については、「[サービスのバージョン管理とアップグレード]」[app-upgrade-tutorial]を参照してください。

### <a name="configure-continuous-integration-with-visual-studio-team-services"></a>Visual Studio Team Services を使用した継続的な統合の構成
Service Fabric アプリケーション向けに継続的な統合プロセスを設定する方法については、「[Visual Studio Team Services を使用した継続的な統合の構成]」[ci-with-vso]を参照してください。

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



<!--HONumber=Nov16_HO3-->


