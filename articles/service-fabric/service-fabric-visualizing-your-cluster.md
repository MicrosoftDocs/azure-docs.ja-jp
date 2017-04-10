---
title: "Service Fabric Explorer を使用したクラスターの視覚化 | Microsoft Docs"
description: "Service Fabric Explorer は、Microsoft Azure Service Fabric クラスター内のクラウド アプリケーションとノードを検査および管理するための Web ベースのツールです。"
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: c875b993-b4eb-494b-94b5-e02f5eddbd6a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: cc61146e0353455c5c763297a002afb86b76887a
ms.lasthandoff: 03/10/2017


---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Service Fabric Explorer を使用したクラスターの視覚化
Service Fabric Explorer は、Azure Service Fabric クラスター内のアプリケーションとノードを検査および管理するための Web ベースのツールです。 Service Fabric クラスター内で直接ホストされているので、ローカル クラスターが実行されている場所に関係なく、いつでも使用できます。

## <a name="video-tutorial"></a>ビデオ チュートリアル

Service Fabric Explorer の使用方法については、次の Microsoft Virtual Academy のビデオをご覧ください。

[<center><img src="./media/service-fabric-visualizing-your-cluster/SfxVideo.png" WIDTH="360" HEIGHT="244"></center>](https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=bBTFg46yC_9806218965)

## <a name="connect-to-service-fabric-explorer"></a>Service Fabric Explorer への接続
[開発環境を準備する](service-fabric-get-started.md)手順に従った場合は、http://localhost:19080/Explorer に移動することで、ローカル クラスター上で Service Fabric Explorer を起動できます。

> [!NOTE]
> Internet Explorer と Service Fabric Explorer を併用してリモート クラスターを管理する場合は、Internet Explorer の一部の設定を構成する必要があります。 すべての情報が正しく読み込まれるように、**[ツール]** > **[互換表示設定]** に移動し、**[イントラネット サイトを互換表示で表示する]** チェック ボックスをオフにします。
>
>

## <a name="understand-the-service-fabric-explorer-layout"></a>Service Fabric Explorer のレイアウトについて
左側のツリーを使用して、Service Fabric Explorer 内を移動できます。 ツリーのルートでは、クラスター ダッシュボードにクラスターの概要 (アプリケーションとノードの正常性の概要など) が表示されます。

![Service Fabric Explorer のクラスター ダッシュボード][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>クラスターのレイアウトを表示する
Service Fabric クラスターのノードは、障害ドメインとアップグレード ドメインの&2; 次元グリッドにわたって配置されます。 このように配置することで、ハードウェアの障害やアプリケーションのアップグレード時にもアプリケーションを継続して利用できます。 現在のクラスターの配置を表示するには、クラスター マップを使用します。

![Service Fabric Explorer のクラスター マップ][sfx-cluster-map]

### <a name="view-applications-and-services"></a>アプリケーションとサービスを表示する
クラスターには、2 つのサブツリーが含まれています。1 つはアプリケーション、もう&1; つはノードのサブツリーです。

アプリケーション ビューでは、Service Fabric の論理階層 (アプリケーション、サービス、パーティション、レプリカ) 内を移動できます。

次の例では、**MyApp** アプリケーションは、**MyStatefulService** と **WebService** の&2; つのサービスで構成されています。 **MyStatefulService** はステートフルであるため、1 つのプライマリ レプリカと&2; つのセカンダリ レプリカがあるパーティションが&1; つ含まれています。 これに対して、WebSvcService はステートレスであり、インスタンスが&1; つだけ含まれています。

![Service Fabric Explorer のアプリケーション ビュー][sfx-application-tree]

ツリーの各レベルでは、メイン ウィンドウにその項目の関連情報が表示されます。 たとえば、特定のサービスの正常性状態とバージョンを確認できます。

![Service Fabric Explorer のメイン ウィンドウ][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>クラスターのノードを表示する
ノード ビューには、クラスターの物理的なレイアウトが表示されます。 特定のノードについて、そのノードでコードがデプロイされているアプリケーション、 正確に言うと、そこで現在実行されているレプリカを調べることができます。

## <a name="actions"></a>アクション
Service Fabric Explorer では、クラスター内のノード、アプリケーション、サービスに対する操作を簡単に呼び出すことができます。

たとえば、アプリケーション インスタンスを削除するには、左側のツリーでアプリケーションを選択し、**[アクション]** > **[アプリケーションの削除]** の順に選択します。

![Service Fabric Explorer でのアプリケーションの削除][sfx-delete-application]

> [!TIP]
> 同じ操作を実行するには、各要素の横にある省略記号をクリックします。
>
>

次の表に、各エンティティに対して実行できるアクションを示します。

| **エンティティ** | **アクション** | **説明** |
| --- | --- | --- |
| アプリケーションの種類 |種類のプロビジョニング解除 |アプリケーション パッケージをクラスターのイメージ ストアから削除します。 その種類のすべてのアプリケーションを先に削除する必要があります。 |
| アプリケーション |アプリケーションの削除 |アプリケーションを削除します。サービスと状態もすべて削除されます (存在する場合)。 |
| サービス |サービスの削除 |サービスと状態を削除します (存在する場合)。 |
| ノード |アクティブ化 |ノードをアクティブ化します。 |
| ノード | 非アクティブ化 (一時停止) | 現在の状態でノードを一時停止します。 サービスは引き続き実行されますが、Service Fabric は、障害やデータの不整合を回避する必要がない限り、移動を積極的に行いません。 通常、このアクションは、特定のノードでのデバッグ サービスを有効にして、検査中に移動が発生しないようにするために使用されます。 | |
| ノード | 非アクティブ化 (再開) | ノードからメモリ内のすべてのサービスを安全に移動し、永続的なサービスを終了します。 通常は、ホスト プロセスまたはコンピューターの再起動が必要な場合に使用されます。 | |
| ノード | 非アクティブ化 (データを削除) | 十分なレプリカを作成した後、ノードで実行されているすべてのサービスを安全に閉じます。 通常は、ノード (または少なくともそのストレージ) が完全にコミットの対象から除外された場合に使用されます。 | |
| ノード | ノードの状態の削除 | ノードのレプリカの知識をクラスターから削除します。 通常は、既に障害が発生したノードを回復不能と判断したときに使用されます。 | |
| ノード | 再起動 | ノードを再起動することによって、ノード障害をシミュレートします。 詳細情報は[こちら](https://docs.microsoft.com/en-us/powershell/servicefabric/vlatest/Restart-ServiceFabricNode)です | |

多くの操作は破壊的であるため、アクションを実行する前に確認を求められることがあります。

> [!TIP]
> Service Fabric Explorer を使用して実行できる操作はすべて、PowerShell または REST API を使用して実行し、自動化することもできます。
>
>

Service Fabric Explorer を使用して、特定のアプリケーションの種類とバージョンのアプリケーション インスタンスを生成することもできます。 ツリー ビューでアプリケーションの種類を選択し、右側のウィンドウでバージョンの横にある **[Create app instance (アプリ インスタンスの作成)]** リンクをクリックします。

![Service Fabric Explorer でのアプリケーション インスタンスの作成][sfx-create-app-instance]

> [!NOTE]
> Service Fabric Explorer を使って作成されたアプリケーション インスタンスは現在パラメーター化できません。 これらは、既定のパラメーター値を使用して作成されます。
>
>

## <a name="connect-to-a-remote-service-fabric-cluster"></a>リモート Service Fabric クラスターへの接続
クラスターのエンドポイントがわかっていて、かつ十分なアクセス許可があれば、任意のブラウザーから Service Fabric Explorer にアクセスできます。 これは、Service Fabric Explorer がクラスター内で実行されている別のサービスであるからです。

### <a name="discover-the-service-fabric-explorer-endpoint-for-a-remote-cluster"></a>リモート クラスターの Service Fabric Explorer エンドポイントを検出する
特定のクラスターの Service Fabric Explorer にアクセスするには、ブラウザーで次の場所にアクセスします。

http://&lt;your-cluster-endpoint&gt;:19080/Explorer

Azure クラスターの場合、完全な URL は Azure Portal の [Cluster Essentials] ウィンドウにもあります。

### <a name="connect-to-a-secure-cluster"></a>セキュリティ保護されたクラスターに接続する
証明書または Azure Active Directory (AAD) を使用して、クライアントによる Service Fabric クラスターへのアクセスを制御できます。

セキュリティで保護されたクラスターで Service Fabric Explorer に接続する場合、クラスターの構成に応じて、クライアント証明書を提示するか、AAD を使用してログインする必要があります。

## <a name="next-steps"></a>次のステップ
* [Testability の概要](service-fabric-testability-overview.md)
* [Visual Studio での Service Fabric アプリケーションの管理](service-fabric-manage-application-in-visual-studio.md)
* [PowerShell を使用した Service Fabric アプリケーションのデプロイメント](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/SfxCreateAppInstance.png

