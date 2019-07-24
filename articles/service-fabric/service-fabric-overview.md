---
title: Azure での Service Fabric の概要 | Microsoft Docs
description: 拡張性と復元性を提供する多くのマイクロサービスでアプリケーションが構成されている Service Fabric の概要です。 Service Fabric とは、スケーラブルで信頼性が高く管理しやすいクラウド向けアプリケーションの構築に使用される分散型システム プラットフォームです。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: masnider
ms.assetid: bbcc652a-a790-4bc4-926b-e8cd966587c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: overview
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: cd2895378f713749b3a1d0ac01986b728ceafd2b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59009005"
---
# <a name="overview-of-azure-service-fabric"></a>Azure Service Fabric の概要
Azure Service Fabric は、スケーラブルで信頼性に優れたマイクロサービスとコンテナーのパッケージ化とデプロイ、管理を簡単に行うことができる分散システム プラットフォームです。 Service Fabric は、クラウド ネイティブ アプリケーションの開発と管理における重要な課題にも対処します。 開発者と管理者は複雑なインフラストラクチャの問題を避けることができ、スケーラブルで信頼性が高く、管理しやすい、ミッション クリティカルで要求の厳しいワークロードの実装に重点を置くことができます。 Service Fabric は、コンテナーで実行するこれらのエンタープライズ クラスの Tier-1 クラウド スケール アプリケーションを構築して管理するための次世代プラットフォームの代表例です。

こちらの短いビデオでは、Service Fabric とマイクロサービスを紹介しています。
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Service-Fabric/player]

## <a name="compliance"></a>コンプライアンス
Azure Service Fabric リソース プロバイダーは、すべての Azure リージョンで利用でき、Azure が持っているすべてのコンプライアンス証明書に準拠しています。これには、SOC、ISO、PCI DSS、HIPAA、および GDRP が含まれます。 コンプライアンス証明書の完全な一覧については、「[コンプライアンス認証](https://www.microsoft.com/trustcenter/compliance/complianceofferings)」を参照してください。

## <a name="applications-composed-of-microservices"></a>マイクロサービスで構成されたアプリケーション 
Service Fabric を使用すると、マシンの共有プール (クラスターと呼ばれる) 上で高密度で実行されるマイクロサービスで構成された、スケーラブルで信頼性の高いアプリケーションを構築して管理することができます。 コンテナーで実行する分散型のスケーラブルなステートレスおよびステートフル マイクロサービスを構築するための高度な軽量ランタイムを提供します。 また、コンテナー化されたサービスを含むデプロイ済みのアプリケーションをプロビジョニング、デプロイ、監視、アップグレード/修正、削除するための包括的なアプリケーション管理機能も提供します。

現在、Service Fabric は、Azure SQL Database、Azure Cosmos DB、Cortana、Microsoft Power BI、Microsoft Intune、Azure Event Hubs、Azure IoT Hub、Dynamics 365、Skype for Business、などの Azure の核となる多くのサービスを含む、さまざまな Microsoft サービスで使用されています。

Service Fabric は、クラウド ネイティブのサービスの作成用に調整されています。これらのサービスは、必要に応じて小規模で開始し、数百または数千ものコンピューターを含むとても大きなスケールまで拡張することができます。

現在のインターネット規模のサービスは、マイクロサービスで構築されています。 マイクロサービスの例としては、プロトコル ゲートウェイ、ユーザー プロファイル、ショッピング カート、インベントリ処理、キュー、キャッシュなどが挙げられます。 Service Fabric は、すべてのマイクロサービス (またはコンテナー) にステートレスまたはステートフルな一意の名前を指定するマイクロサービス プラットフォームです。

Service Fabric は、これらのマイクロサービスで構成されたアプリケーションに対して、包括的なランタイムおよびライフサイクル管理機能を提供します。 Service Fabric クラスター間にわたってデプロイされアクティブ化されるマイクロサービスを、コンテナー内にホストします。 仮想マシンからコンテナーへの移行によって密度を 1 桁増やすことができます。 同様に、コンテナーからコンテナー内のマイクロサービスへの移行によっても、密度を 1 桁増やすことができます。 たとえば、単一の Azure SQL Database クラスターは、合計数十万のデータベースをホストする数万のコンテナーを実行する数百のマシンで構成されます。 各データベースは Service Fabric ステートフル マイクロサービスです。 

マイクロサービスの手法について詳しくは、「 [マイクロサービスの手法でアプリケーションを構築する理由は何ですか。](service-fabric-overview-microservices.md)

## <a name="container-deployment-and-orchestration"></a>コンテナーのデプロイとオーケストレーション
Service Fabric は、マシン クラスター全体にマイクロサービスをデプロイする Microsoft の[コンテナー オーケストレーター](service-fabric-cluster-resource-manager-introduction.md)です。 [Service Fabric プログラミング モデル](service-fabric-choose-framework.md)や [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) の使用から[任意のコード](service-fabric-guest-executables-introduction.md)のデプロイまで、多くの方法でマイクロサービスを開発することができます。 重要なこととして、プロセスとしてのサービスとコンテナー内のサービスの両方を同じアプリケーション内で混在させることができます。 [コンテナーのデプロイと管理](service-fabric-containers-overview.md)だけを行う場合は、Service Fabric がコンテナー オーケストレーターとして最適な選択肢です。

## <a name="any-os-any-cloud"></a>すべての OS、すべてのクラウド
Service Fabric はどこでも動きます。 Azure、オンプレミス、Windows Server、Linux を含むさまざまな環境で Service Fabric クラスターを作成できます。 他のパブリック クラウド上にクラスターを作成することさえできます。 さらに、SDK の開発環境は、エミュレーターが含まれない運用環境と**同一**です。 つまり、ローカルの開発クラスターで実行した場合、他の環境内のクラスターにデプロイされます。

![Service Fabric platform][Image1]

Windows 開発の場合、Service Fabric .NET SDK は、Visual Studio と PowerShell に統合されています。 「[開発環境を準備する](service-fabric-get-started.md)」を参照してください。 Linux 開発の場合、Service Fabric Java SDK が Eclipse に統合されており、Java、.NET Core、およびコンテナー アプリケーション用のテンプレートを生成する必要はありません 「[Linux で開発環境を準備する](service-fabric-get-started-linux.md)」を参照してください。

クラスター作成の詳細については、[Windows Server または Linux でのクラスターの作成](service-fabric-deploy-anywhere.md)に関する記事を参照してください。また、Azure でのクラスター作成の詳細については、[Azure Portal を使用したクラスターの作成](service-fabric-cluster-creation-via-portal.md)に関する記事を参照してください。

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Service Fabric 用のステートレス マイクロサービスとステートフル マイクロサービス
Service Fabric では、マイクロサービスまたはコンテナーで構成されるアプリケーションを構築することができます。 ステートレス マイクロサービス (プロトコル ゲートウェイや Web プロキシなど) では、要求およびそのサービスからの応答以外では変更可能な状態が維持されません。 Azure Cloud Services worker ロールは、ステートレス サービスの一例です。 ステートフル マイクロサービス (ユーザー アカウント、データベース、デバイス、ショッピング カート、キューなど) では、要求およびその応答以外でも変更可能な認証状態が維持されます。 今日のインターネット規模のアプリケーションは、ステートレス マイクロサービスとステートフル マイクロサービスの組み合わせで構成されています。 

Service Fabric との重要な違いは、[組み込みプログラミング モデル](service-fabric-choose-framework.md)またはコンテナー化されたステートフル サービスで、ステートフル サービスの構築に大きな重点が置かれていることです。 [アプリケーション シナリオ](service-fabric-application-scenarios.md)では、ステートフル サービスが使われるシナリオについて説明されています。


## <a name="application-lifecycle-management"></a>アプリケーション ライフサイクル管理
Service Fabric では、コンテナーを含むクラウド アプリケーションの完全なアプリケーション ライフ サイクルと CI/CD のサポートが提供されます。 このライフ サイクルには、開発、展開、日常的な管理、保守、最終的な廃棄が含まれています。

Service Fabric ライフサイクル管理機能を使用すると、アプリケーション管理者および IT オペレーターは単純なロータッチ ワークフローを使用して、アプリケーションをプロビジョニング、デプロイ、修正、および監視することができます。 これらの組み込みワークフローにより、アプリケーションを継続的に使用可能に保つために IT オペレーターにかかる負担が大幅に軽減されます。

ほとんどのアプリケーションは、ステートレスとステートフルのマイクロサービス、コンテナー、および一緒にデプロイされる他の実行可能ファイルの組み合わせで構成されます。 Service Fabric は、アプリケーションの厳密な型を備えており、複数のアプリケーション インスタンスのデプロイが可能です。 インスタンスは個別に管理およびアップグレードできます。 重要なのは、Service Fabric はコンテナーまたは任意の実行可能ファイルをデプロイし、それらを信頼性の高いものにすることができる点です。 たとえば、Service Fabric は、.NET、ASP.NET Core、node.js、Windows コンテナー、Linux コンテナー、Java 仮想マシン、スクリプト、Angular、またはアプリケーションを構成する文字通りすべてのものをデプロイできます。

Service Fabric は、[Azure Pipelines](https://www.visualstudio.com/team-services/)、[Jenkins](https://jenkins.io/index.html)、[Octopus Deploy](https://octopus.com/) などの CI/CD ツールと統合し、他のすべての一般的な CI/CD ツールで使うことができます。

アプリケーション ライフサイクル管理の詳細については、[アプリケーションのライフサイクル](service-fabric-application-lifecycle.md)に関するページを参照してください。 任意のコードをデプロイする方法について詳しくは、「[Service Fabric へのゲスト実行可能ファイルのデプロイ](service-fabric-deploy-existing-app.md)」をご覧ください。

## <a name="key-capabilities"></a>主な機能
Service Fabric を使用すると、次の操作を実行できます。

* コードを変更せずに、Azure にデプロイするか、Windows または Linux を実行しているオンプレミスのクラウドにデプロイします。 一度記述してから、任意の Service Fabric クラスターにデプロイします。
* Service Fabric プログラミング モデル、コンテナー、任意のコードを使って、マイクロサービスで構成されたスケーラブルなアプリケーションを開発します。
* 信頼性の高いステートレスおよびステートフルなマイクロサービスを開発します。 ステートフル マイクロサービスを使って、アプリケーションの設計を簡略化します。 
* 新しい Reliable Actors プログラミング モデルを使って、自己完結型のコードと状態を備えたクラウド オブジェクトを作成します。
* Windows コンテナーと Linux コンテナーを含むコンテナーをデプロイし、調整します。 Service Fabric は、データ対応でステートフルなコンテナー オーケストレーターです。
* マシンあたり数百から数千のアプリケーションまたはコンテナーという高密度で、アプリケーションを短時間でデプロイします。
* それぞれ個別にアップグレード可能な、同じアプリケーションの異なるバージョンを並行してデプロイします。
* 重大なアップグレードと重大でないアップグレードを含め、アプリケーションのライフ サイクルをダウンタイムなく管理します。
* クラスター内のノード数をスケールアウトまたはスケールインします。 ノードをスケーリングすると、アプリケーションが自動的にスケーリングします。
* アプリケーションの状態を監視および診断し、自動修復を実行するようにポリシーを設定します。
* クラスター内でアプリケーションの再配布を調整するリソース バランサーを監視します。 Service Fabric は障害から回復し、利用可能なリソースに基づいて負荷分散を最適化します。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>次の手順
* 詳細:
  * [マイクロサービスの手法でアプリケーションを構築する理由は何ですか。](service-fabric-overview-microservices.md)
  * [用語の概要](service-fabric-technical-overview.md)
* [Windows 開発環境](service-fabric-get-started.md)をセットアップする  
* [Linux 開発環境](service-fabric-get-started-linux.md)をセットアップする
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
