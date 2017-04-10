---
title: "Azure での Service Fabric の概要 | Microsoft Docs"
description: "拡張性と復元性を提供する多くのマイクロサービスでアプリケーションが構成されている Service Fabric の概要です。 Service Fabric とは、スケーラブルで信頼性が高く管理しやすいクラウド向けアプリケーションの構築に使用される分散型システム プラットフォームです。"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: masnider
ms.assetid: bbcc652a-a790-4bc4-926b-e8cd966587c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: mfussell
translationtype: Human Translation
ms.sourcegitcommit: 7033955fa9c18b2fa1a28d488ad5268d598de287
ms.openlocfilehash: 0e899225063e77ccef254e8aaacbf0390faa25e3


---
# <a name="overview-of-azure-service-fabric"></a>Azure Service Fabric の概要
Azure Service Fabric は、拡張性と信頼性に優れたマイクロサービスのパッケージ化とデプロイ、管理を簡単に行うことができる分散システム プラットフォームです。 Service Fabric は、クラウド アプリケーションの開発と管理における重要な課題にも対処します。 開発者と管理者は複雑なインフラストラクチャの問題を避けることができ、スケーラブルで信頼性が高く、管理しやすい、ミッション クリティカルで要求の厳しいワークロードの実装に重点を置くことができます。 Service Fabric は、これらのエンタープライズ クラスの Tier-1 クラウド スケール アプリケーションを構築して管理するための次世代のミドルウェア プラットフォームの代表例です。

次の Channel9 の短いビデオでは、Service Fabric とマイクロサービスを紹介しています。<center><a target="_blank" href="https://aka.ms/servicefabricvideo">  
<img src="./media/service-fabric-overview/OverviewVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

次の Microsoft Virtual Academy のビデオでは、Service Fabric の主要概念について説明しています。<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">  
<img src="./media/service-fabric-overview/CoreConceptsVid.png" WIDTH="360" HEIGHT="244">  
</a></center>


## <a name="applications-composed-of-microservices"></a>マイクロサービスで構成されたアプリケーション
Service Fabric を使用すると、マシンの共有プール (クラスターと呼ばれる) 上で超高密度で実行されるマイクロサービスで構成された、スケーラブルで信頼性の高いアプリケーションを構築して管理することができます。 分散型のスケーラブルなステートレスおよびステートフル マイクロサービスを構築するための高度なランタイムを提供します。 また、デプロイ済みのアプリケーションをプロビジョニング、デプロイ、監視、アップグレード/修正、削除するための包括的なアプリケーション管理機能も提供します。

マイクロサービスの手法が重要な理由 主に&2; つの理由があります。

* アプリケーションのニーズに応じてアプリケーションのさまざまな部分を拡張できます。
* 開発チームは、変更を展開するための俊敏性を高めることで、製品をお客様により早く、より高い頻度で提供できるようになります。

現在、Service Fabric は、Azure SQL Database、Azure DocumentDB、Cortana、Microsoft Power BI、Microsoft Intune、Azure Event Hubs、Azure IoT Hub、Skype for Business、さまざまなコア Azure サービスなど、多数の Microsoft サービスの機能を強化しています。

Service Fabric は、クラウド ネイティブのサービスの作成用に調整されています。これらのサービスは、必要に応じて小規模で開始し、数百または数千ものコンピューターを含むとても大きなスケールまで拡張することができます。

現在のインターネット規模のサービスは、マイクロサービスで構築されています。 マイクロサービスの例としては、プロトコル ゲートウェイ、ユーザー プロファイル、ショッピング カート、インベントリ処理、キュー、キャッシュなどが挙げられます。 Service Fabric は、すべてのマイクロサービスにステートレスまたはステートフルな一意の名前を指定するマイクロサービス プラットフォームです。

Service Fabric は、これらのマイクロサービスで構成されたアプリケーションに対して、包括的なランタイムおよびライフサイクル管理機能を提供します。 Service Fabric クラスター間にわたってデプロイされアクティブ化されるマイクロサービスを、コンテナー内にホストします。 仮想マシンからコンテナーへの移行によって密度を&1; 桁増やすことができます。 同様に、コンテナーからマイクロサービスへの移行によっても、密度を&1; 桁増やすことができます。 たとえば、単一の Azure SQL Database クラスターは、合計数十万のデータベースをホストする数万のコンテナーを実行する数百のマシンで構成されます。 各データベースは Service Fabric ステートフル マイクロサービスです。 前述のサービスの場合も同様です。Service Fabric の機能を表すのに*ハイパースケール*という語が使用されるのはこのためです。 コンテナーによって高密度が得られれば、マイクロサービスによってハイパースケールを実現できます。

マイクロサービスの手法について詳しくは、「[マイクロサービスの手法でアプリケーションを構築する理由は何ですか。](service-fabric-overview-microservices.md)」をご覧ください。

## <a name="container-deployment-and-orchestration"></a>コンテナーのデプロイとオーケストレーション
Service Fabric は、マシン クラスター全体のマイクロサービスの[オーケストレーター](service-fabric-cluster-resource-manager-introduction.md)です。 [Service Fabric プログラミング モデル](service-fabric-choose-framework.md)の使用や[ゲスト実行可能ファイル](service-fabric-deploy-existing-app.md)のデプロイなどの多くの方法で、マイクロサービスを開発することができます。 Service Fabric では、コンテナー イメージ内のサービスをデプロイできます。 重要なこととして、プロセスとしてのサービスとコンテナー内のサービスの両方を同じアプリケーション内で混在させることができます。 [コンテナー イメージのデプロイと管理](service-fabric-containers-overview.md)をマシン クラスター全体で行うなら、Service Fabric が最適な選択肢です。

## <a name="create-clusters-for-service-fabric-anywhere"></a>Service Fabric 用のクラスターの任意の場所への作成
Azure、オンプレミス、Windows Server、Linux を含むさまざまな環境で Service Fabric クラスターを作成できます。 さらに、SDK の開発環境は、エミュレーターが含まれない運用環境と同一です。 つまり、ローカルの開発クラスターで実行した場合、他の環境内の同じクラスターにデプロイされます。

オンプレミスでのクラスター作成の詳細については、[Windows Server または Linux でのクラスターの作成](service-fabric-deploy-anywhere.md)に関する記事を参照してください。また、Azure でのクラスター作成の詳細については、[Azure Portal を使用したクラスターの作成](service-fabric-cluster-creation-via-portal.md)に関する記事を参照してください。

![Service Fabric platform][Image1]

## <a name="stateless-and-stateful-micrososervices-for-service-fabric"></a>Service Fabric 用のステートレスおよびステートフル マイクロサービス
Service Fabric では、マイクロサービスで構成されるアプリケーションを構築することができます。 ステートレス マイクロサービス (プロトコル ゲートウェイや Web プロキシなど) では、要求およびそのサービスからの応答以外では変更可能な状態が維持されません。 Azure Cloud Services worker ロールは、ステートレス サービスの一例です。 ステートフル マイクロサービス (ユーザー アカウント、データベース、デバイス、ショッピング カート、キューなど) では、要求およびその応答以外でも変更可能な認証状態が維持されます。 今日のインターネット規模のアプリケーションは、ステートレス マイクロサービスとステートフル マイクロサービスの組み合わせで構成されています。

ステートレス マイクロサービスだけでなく、ステートフル マイクロサービスも必要である理由。 主に&2; つの理由があります。

* 同じコンピューター上でコードとデータを密接に保持することで、高スループット、低待機時間、エラー トレラントなオンライン トランザクション処理 (OLTP) サービスを構築できます。 例として、対話型のストアフロント、検索、モノのインターネット (IoT) システム、取引システム、クレジット カード処理、不正検出システム、個人記録管理などがあります。
* アプリケーションの設計を簡素化することができます。 完全にステートレスなマイクロサービスの可用性と待機時間の要件に対応するために、従来は追加のキューとキャッシュが必要でしたが、ステートフル マイクロサービスでは不要になります。 ステートフル サービスは高可用性と低待機時間が特徴であり、アプリケーション全体で管理すべき変動要素が少なくなります。

Service Fabric を使用したアプリケーション パターンの詳細については、[アプリケーションのシナリオ](service-fabric-application-scenarios.md)に関する記事と、サービスの[プログラミング モデル フレームワークの選択](service-fabric-choose-framework.md)に関する記事を参照してください。

ステートレス サービスとステートフル サービスの概要については、次の Microsoft Virtual Academy のビデオもご覧ください。<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=HhD9566yC_4106218965">  
<img src="./media/service-fabric-overview/ReliableServicesVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="application-lifecycle-management"></a>アプリケーション ライフサイクル管理
Service Fabric では、クラウド アプリケーションの完全なアプリケーション ライフ サイクル管理をサポートします。 このライフ サイクルには、開発、展開、日常的な管理、保守、最終的な廃棄が含まれています。

Service Fabric ライフサイクル管理機能を使用すると、アプリケーション管理者および IT オペレーターは単純なロータッチ ワークフローを使用して、アプリケーションをプロビジョニング、デプロイ、修正、および監視することができます。 これらの組み込みワークフローにより、アプリケーションを継続的に使用可能に保つために IT オペレーターにかかる負担が大幅に軽減されます。

ほとんどのアプリケーションは、ステートレス マイクロサービスとステートフル マイクロサービスの組み合わせ、および一緒にデプロイされた他の実行可能ファイル/ランタイムで構成されています。 Service Fabric は、アプリケーションの厳密な型およびパッケージ化されたマイクロサービスを備えており、複数のアプリケーション インスタンスのデプロイが可能です。 インスタンスは個別に管理およびアップグレードできます。 重要なのは、Service Fabric は *任意* の実行可能ファイルまたはランタイムをデプロイし、それらを信頼性の高いものにすることができる点です。 たとえば、Service Fabric は、アプリケーションを構成する .NET、ASP.NET Core、Node.js、Java 仮想マシン、スクリプト、Angular、その他の要素をデプロイします。

アプリケーション ライフサイクル管理の詳細については、[アプリケーションのライフサイクル](service-fabric-application-lifecycle.md)に関するページを参照してください。 任意のコードを展開する方法の詳細については、[ゲスト実行可能ファイルの展開に関するページ](service-fabric-deploy-existing-app.md)を参照してください。

アプリケーション ライフサイクル管理の概要については、次の Microsoft Virtual Academy のビデオもご覧ください。<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">  
<img src="./media/service-fabric-overview/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="key-capabilities"></a>主な機能
Service Fabric を使用すると、次の操作を実行できます。

* 自己復旧機能を備えた非常にスケーラブルなアプリケーションを開発します。
* Service Fabric プログラミング モデルを使って、マイクロサービスで構成されたアプリケーションを開発します。 または、単純に、ゲスト実行可能ファイル、およびユーザーが選んだその他のアプリケーション フレームワーク (ASP.NET Core や Node.js など) をホストすることができます。
* 信頼性の高いステートレスおよびステートフルなマイクロサービスを開発します。
* クラスター全体で、Windows コンテナーと Docker コンテナーを含むコンテナーをデプロイし、調整します。 これらのコンテナーには、ゲスト実行可能ファイルや、信頼性が高くステートレスかつステートフルなマイクロサービスを含めることができます。 いずれの場合も、コンテナー ポートからホスト ポートへのマッピングを使用し、コンテナーが探しやすくなるとともに、フェールオーバーを自動化できます。
* キャッシュとキューの代わりにステートフル マイクロサービスを使用して、アプリケーションの設計を簡略化します。
* コードを変更せずに、Azure にデプロイするか、Windows または Linux を実行しているオンプレミスのクラウドにデプロイします。 一度記述してから、任意の Service Fabric クラスターにデプロイします。
* "マシン上のデータ センター" アプローチで開発を行います。 ローカル開発環境は、Azure データ センターで実行されるのと同じコードです。
* アプリケーションを数秒間でデプロイします。
* マシンごとに数百または数千のアプリケーションをデプロイすることで、仮想マシンよりも高密度なアプリケーションのデプロイを実現します。
* それぞれ個別にアップグレード可能な、同じアプリケーションの異なるバージョンを並行してデプロイします。
* 重大なアップグレードと重大でないアップグレードを含め、ステートフル アプリケーションのライフサイクルをダウンタイムなく管理します。
* .NET API、Java (Linux)、PowerShell、Azure コマンドライン インターフェイスI (Linux) または REST インターフェイスを使用してアプリケーションを管理します。
* アプリケーション内で個別にマイクロサービスをアップグレードおよび修正します。
* アプリケーションの状態を監視および診断し、自動修復を実行するようにポリシーを設定します。
* クラスター内のノードの数をスケール アウトまたはスケール インし、各ノードのサイズをスケール アップまたはスケール ダウンします。 ノードのスケールを変更すると、アプリケーションのスケールも自動的に変更され、使用可能なリソースに基づいて配布されます。
* クラスター内でアプリケーションの再配布を調整する自己修復リソース バランサーを監視します。 Service Fabric は障害から回復し、利用可能なリソースに基づいて負荷分散を最適化します。
* 障害分析サービスを使ってサービスで混乱テストを実行し、運用環境で実行する前に問題と障害を見つけます。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>次のステップ
* 詳細:
  * [マイクロサービスの手法でアプリケーションを構築する理由は何ですか。](service-fabric-overview-microservices.md)
  * [用語の概要](service-fabric-technical-overview.md)
* Service Fabric [開発環境](service-fabric-get-started.md)  
* [プログラミング モデル フレームワークの選択](service-fabric-choose-framework.md) に関する記事を参照してください。
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png



<!--HONumber=Jan17_HO4-->


