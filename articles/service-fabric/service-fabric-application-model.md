---
title: Service Fabric のアプリケーション モデル
description: アプリケーションとサービスのマニフェスト ファイルを使用して、Azure Service Fabric のアプリケーションとサービスをモデル化および記述する方法について説明します。
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 84e6b2309fdb206771d4ea01aa03c7f355d6ff19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85963735"
---
# <a name="model-an-application-in-service-fabric"></a>Service Fabric でのアプリケーションのモデル化
ここでは、Azure Service Fabric のアプリケーション モデルの概要と、マニフェスト ファイルを使用してアプリケーションとサービスを定義する方法について説明します。

## <a name="understand-the-application-model"></a>アプリケーション モデルを理解する
アプリケーションは、特定のまたは複数の関数を実行する構成サービスのコレクションです。 サービスは完全なスタンドアロンの機能を実行し、他のサービスから独立して開始および実行することができます。  サービスはコード、構成、データから成ります。 各サービスに対して、コードは実行可能ファイルのバイナリで構成され、構成は実行時に読み込まれるサービス設定で構成され、データはサービスが消費する任意の静的データで構成されます。 この階層的なアプリケーション モデル内の各コンポーネントは、個別にバージョン管理されてアップグレードされます。

![Service Fabric のアプリケーション モデル][appmodel-diagram]

アプリケーションの種類は、サービスの種類の集まりで構成されているアプリケーションの分類です。 サービスの種類は、サービスを分類したものです。 分類にはさまざまな設定と構成を含めることができますが、コア機能は変わりません。 サービスのインスタンスは、同じサービスの種類の別のサービス構成のバリエーションです。  

アプリケーションのクラス (または "型") およびサービスは XML ファイル (アプリケーション マニフェストおよびサービス マニフェスト) で記述されます。  マニフェストは、アプリケーションとサービスが記述されており、アプリケーションをクラスターのイメージ ストアからインスタンス化するテンプレートです。  マニフェストについて詳しくは、[アプリケーションとサービスのマニフェスト](service-fabric-application-and-service-manifests.md)に関するページをご覧ください。 ServiceManifest.xml と ApplicationManifest.xml ファイルのスキーマ定義は、Service Fabric SDK およびツールと共に *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd* にインストールされます。 XML スキーマについては、「[ServiceFabricServiceModel.xsd スキーマ ドキュメント](service-fabric-service-model-schema.md)」をご覧ください。

別のアプリケーション インスタンスのコードは、同じ Service Fabric ノードでホストされている場合でも個別のプロセスとして実行されます。 さらに、各アプリケーション インスタンスのライフサイクルを個別に管理 (たとえば、アップグレード) できます。 次の図では、コード、構成、データ パッケージで構成されるサービスの種類で、アプリケーションの種類がどのように構成されるかを示しています。 図を簡単にするために、 `ServiceType4` のコード/構成/データ パッケージのみが表示されていますが、各サービス タイプにはそのようなパッケージ タイプの一部または全部が含まれます。

![Service Fabric アプリケーションの種類とサービスの種類][cluster-imagestore-apptypes]

クラスターにはアクティブな 1 つ以上のサービスの種類のインスタンスがある可能性があります。 たとえば、ステートフル サービス インスタンスやレプリカの場合、クラスター内の別のノード上にあるレプリカ間で状態をレプリケートすることで高い信頼性を実現します。 レプリケーションは基本的に、クラスター内の 1 つのノードが失敗した場合でも使用できるように、サービスの冗長性を実現します。 [パーティション分割されたサービス](service-fabric-concepts-partitioning.md) は、クラスター内のノード間でその状態 (状態へのアクセス パターンも) をさらに分割します。

次の図は、アプリケーションとサービス インスタンス、パーティション、レプリカ間のリレーションシップを示しています。

![サービス内のパーティションとレプリカ][cluster-application-instances]

> [!TIP]
> http://&lt;yourclusteraddress&gt;:19080/Explorer で利用できる Service Fabric Explorer ツールを利用し、クラスターのアプリケーションのレイアウトを表示できます。 詳細については、「[Service Fabric Explorer を使用したクラスターの視覚化](service-fabric-visualizing-your-cluster.md)」を参照してください。
> 
> 


## <a name="next-steps"></a>次のステップ
- [アプリケーションのスケーラビリティ](service-fabric-concepts-scalability.md)について学習します。
- サービスの[状態](service-fabric-concepts-state.md)、[パーティション分割](service-fabric-concepts-partitioning.md)、[可用性](service-fabric-availability-services.md)について学習します。
- [アプリケーションとサービスのマニフェスト](service-fabric-application-and-service-manifests.md)でのアプリケーションおよびサービスの定義方法を理解します。
- [アプリケーションのホスティング モデル](service-fabric-hosting-model.md)に関するページでは、デプロイされたサービスのレプリカ (またはインスタンス) とサービス ホスト プロセスの関係について説明されています。

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


