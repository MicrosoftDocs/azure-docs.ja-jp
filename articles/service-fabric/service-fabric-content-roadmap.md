---
title: "Azure Service Fabric について | Microsoft Docs"
description: "Service Fabric では、アプリケーションは多数のマイクロサービスで構成されており、拡張性と復元性を提供します。ここでは Service Fabric の概要とファースト ステップ ガイドを紹介します。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/17/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 47b3fffb2d5c24b7473884e490be19ff17b61b61
ms.openlocfilehash: 9742523c0a1743ff5982e746aa3c99aed8934499
ms.lasthandoff: 02/21/2017


---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Service Fabric に興味をお持ちでしょうか。
このラーニング ロードマップでは、Service Fabric でスケーラブルかつ信頼性が高く、管理しやすいアプリケーションを開発する方法について説明します。

## <a name="the-five-minute-overview"></a>5 分間の概説
Azure Service Fabric は、スケーラブルで信頼性の高いマイクロサービスのパッケージ化、デプロイ、管理を簡単に実現する分散システム プラットフォームで、クラウド アプリケーションの開発や管理での大きな課題に対処します。 Service Fabric を使用することで、開発者と管理者は複雑なインフラストラクチャの問題を解決する必要がなくなり、代わりにミッション クリティカルで要求の厳しいワークロードの実装に重点を置くことができます。こうしたワークロードは、スケーラブルで信頼性が高く管理しやすいからです。 Service Fabric は、これらのエンタープライズ クラスの Tier-1 クラウド スケール アプリケーションを構築して管理するための次世代のミドルウェア プラットフォームの代表例です。  

次の Channel9 の短いビデオでは、Service Fabric とマイクロサービスを紹介しています。<center><a target="_blank" href="https://aka.ms/servicefabricvideo">  
<img src="./media/service-fabric-content-roadmap/OverviewVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="the-detailed-overview"></a>概要の詳細
Service Fabric を使用すると、マシンの共有プール (クラスターと呼ばれる) 上で超高密度で実行されるマイクロサービスで構成された、スケーラブルで信頼性の高いアプリケーションを構築して管理することができます。 分散型のスケーラブルなステートレスおよびステートフル マイクロサービスを構築するための高度なランタイムを提供します。 また、デプロイ済みのアプリケーションをプロビジョニング、デプロイ、監視、アップグレード/修正、削除するための包括的なアプリケーション管理機能も提供します。  詳しくは、「[Service Fabric の概要](service-fabric-overview.md)」をご覧ください。

なぜ設計にマイクロサービスを採用しているのでしょうか。 すべてのアプリケーションは時間の経過に伴って進化します。 成功したアプリケーションは便利であることで進化します。 現在の要件についておよびその要件が将来どうなるかについてどれほどのことをご存じでしょうか。 (アプリケーションは後で再設計可能であると承知した上で) 簡単なアプリケーションを概念実証として完成させることが推進要因となることがあります。 一方で、企業がクラウドの構築について話すとき、成長と使用状況に対する期待があります。 問題はその成長と規模が予想できないことです。 そのアプリケーションをスケールして予測できない成長や使用状況に対応できることを把握しながら、迅速にプロトタイプを構築する必要があります。  「[マイクロサービスとは](service-fabric-overview-microservices.md)」では、マイクロサービス設計アプローチがこれらの課題をどのようにして克服できるか、およびマイクロサービスを作成して個別にスケールアップ/スケールダウン、テスト、デプロイ、管理する方法について説明します。

Service Fabric はさまざまな種類のビジネス アプリケーションやサービスを作成し実行できる信頼性の高い柔軟なプラットフォームを提供します。  また、(あらゆる言語で記述された) 任意の既存のアプリケーションを実行できます。  これらのアプリケーションとマイクロサービスはステートレスまたはステートフルが考えられますが、最大限に効率化するために仮想マシン間でリソース分散されます。 Service Fabric 独自のアーキテクチャでは、リアルタイムに近いデータ分析、メモリ内のコンピューティング、並列トランザクション、アプリケーションでのイベント処理を実行できます。 リソース要件の変化に応じて、アプリケーションを簡単にスケール アップまたはスケール ダウン (実際にはスケール インまたはスケール アウト) できます。 作成できるアプリケーションやサービスのカテゴリおよびお客様導入事例については、[アプリケーションのシナリオ](service-fabric-application-scenarios.md)に関する記事を参照してください。

次の Microsoft Virtual Academy のビデオでは、Service Fabric の主要概念について説明しています。<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">  
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="get-started-and-create-your-first-app"></a>導入および最初のアプリの作成 
Service Fabric SDK とツールを使用して Windows、Linux、または MacOS の各環境でアプリを開発し、それらのアプリを Windows または Linux で実行されているクラスターにデプロイできます。  次のガイドに従ってアプリをデプロイします。デプロイにかかる時間は数分です。  最初のアプリケーションを実行後、既製の[サンプル アプリ](http://aka.ms/servicefabricsamples)をいくつかダウンロードして実行します。 具体的には、[入門サンプル](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)から始めてください。

### <a name="on-windows"></a>Windows の場合
Service Fabric SDK には、Service Fabric アプリケーションの作成、デプロイ、およびデバッグのためのテンプレートとツールを提供する Visual Studio 用アドインが含まれます。 これらのトピックでは、Visual Studio で最初のアプリケーションを作成し、それをお使いの開発用コンピューターで実行するプロセスについて説明します。

[開発環境のセットアップ](service-fabric-get-started.md)
[最初のアプリの作成 (C#)](service-fabric-create-your-first-application-in-visual-studio.md)

#### <a name="practical-hands-on-labs"></a>実践的なハンズオン ラボ
この広範な[ハンズオン ラボ (パート 1)](https://msdnshared.blob.core.windows.net/media/2016/07/SF-Lab-Part-I.docx) で Service Fabric のエンド ツー エンドの開発フローに慣れることができます。  ステートレス サービスの作成、監視と正常性レポートの構成、アプリケーションのアップグレードを実行する方法を学習します。 その後、[ハンズオン ラボ (パート 2)](http://aka.ms/sflab2) を実行すると、ステートフル サービスについて学習できます。


次の Channel9 のビデオでは、Visual Studio で C# アプリケーションを作成するプロセスについて説明します。  
<center><a target="_blank" href="https://channel9.msdn.com/Blogs/Azure/Creating-your-first-Service-Fabric-application-in-Visual-Studio">  
<img src="./media/service-fabric-content-roadmap/first-app-vid.png" WIDTH="360" HEIGHT="244">  
</a></center>

### <a name="on-linux"></a>Linux の場合
Service Fabric では、.NET Core と Java の両方で Linux 上のサービスを構築するための SDK を提供しています。 これらのトピックでは、Linux で最初の Java または C# アプリケーションを作成し、それをお使いの開発用コンピューターで実行するプロセスについて説明します。
[開発環境のセットアップ](service-fabric-get-started-linux.md)
[最初のアプリの作成 (Java)](service-fabric-create-your-first-linux-application-with-java.md)
[最初のアプリの作成 (C#)](service-fabric-create-your-first-linux-application-with-csharp.md)

次の Microsoft Virtual Academy ビデオで、Linux で Java アプリを作成する手順について説明しています。  
<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-content-roadmap/LinuxVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

### <a name="on-macos"></a>MacOS の場合
Linux クラスターで実行する Service Fabric アプリケーションを MacOS X で構築できます。 これらの記事では、お使いの Mac を開発用にセットアップする方法と、MacOS で最初の Java アプリケーションを作成して Ubuntu 仮想マシンで実行するプロセスについて説明します。
[開発環境のセットアップ](service-fabric-get-started-mac.md)
[最初のアプリの作成 (Java)](service-fabric-create-your-first-linux-application-with-java.md)

## <a name="core-concepts"></a>主要な概念
「[Service Fabric の用語](service-fabric-technical-overview.md)」、「[アプリケーション モデル](service-fabric-application-model.md)」、および「[サポートされるプログラミング モデル](service-fabric-choose-framework.md)」では詳細な概念や説明が提供されていますが、ここでは基本的な概念について説明します。

<table><tr><th>主要な概念</th><th>設計時</th><th>実行時</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965"><img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">
<img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td></tr>
</table>

### <a name="design-time-app-type-service-type-app-package-and-manifest-service-package-and-manifest"></a>設計時: アプリの種類、サービスの種類、アプリのパッケージとマニフェスト、サービス パッケージとマニフェスト
アプリケーションの種類は、一連のサービスの種類に割り当てられる名前/バージョンです。 ApplicationManifest.xml ファイル内に定義され、アプリケーション パッケージ ディレクトリに組み込まれた後、Service Fabric クラスターのイメージ ストアにコピーされます。 このアプリケーションの種類に基づいて名前付きアプリケーションを作成できます。作成したアプリケーションはクラスター内で実行します。 

サービスの種類は、サービスのコード パッケージとデータ パッケージ、構成パッケージに割り当てられる名前/バージョンです。 ServiceManifest.xml ファイル内に定義され、サービス パッケージ ディレクトリに組み込まれた後、このサービス パッケージ ディレクトリがアプリケーション パッケージの ApplicationManifest.xml ファイルによって参照されます。 クラスターには、名前付きアプリケーションを作成した後、そのアプリケーションの種類を構成するいずれかのサービスの種類から名前付きサービスを作成することができます。 サービスの種類はその ServiceManifest.xml によって記述され、実行時に読み込まれる実行コードのサービス構成設定と、サービスによって消費される静的データで構成されます。

![Service Fabric アプリケーションの種類とサービスの種類][cluster-imagestore-apptypes]

アプリケーション パッケージは、アプリケーションの種類の ApplicationManifest.xml ファイルが含まれるディスクのディレクトリで、アプリケーションの種類を構成するサービスの種類ごとに、対応するサービス パッケージを参照します。 たとえば電子メール アプリケーション タイプのアプリケーション パッケージであれば、Queue サービス パッケージやフロントエンド サービス パッケージ、データベース サービス パッケージへの参照が含まれることが考えられます。 アプリケーション パッケージ ディレクトリ内のファイルは、Service Fabric クラスターのイメージ ストアにコピーされます。 

サービス パッケージは、サービスの種類の ServiceManifest.xml ファイルが含まれるディスクのディレクトリで、そのサービスの種類のコード、静的データ、および構成パッケージを参照します。 サービス パッケージ ディレクトリ内のファイルは、アプリケーションの種類を定義した ApplicationManifest.xml ファイルから参照されます。 たとえばサービス パッケージは、データベース サービスを構成するコードや静的データ、構成パッケージを参照します。

### <a name="run-time-clusters-and-nodes-named-apps-named-services-partitions-and-replicas"></a>実行時: クラスターとノード、名前付きアプリ、名前付きサービス、パーティション、およびレプリカ
[Service Fabric クラスター](service-fabric-deploy-anywhere.md)は、ネットワークで接続された一連の仮想マシンまたは物理マシンで、マイクロサービスがデプロイおよび管理されます。 クラスターは多数のマシンにスケールできます。

クラスターに属しているコンピューターまたは VM をノードといいます。 それぞれのノードには、ノード名 (文字列) が割り当てられます。 ノードには、配置プロパティなどの特性があります。 それぞれのコンピューターまたは VM には、自動的に開始される Windows サービス (FabricHost.exe) が存在します。このサービスがコンピューターまたは VM の起動時に開始され、Fabric.exe と FabricGateway.exe の&2; つの実行可能ファイルを起動します。 ノードは、この&2; つの実行可能ファイルから成ります。 開発およびテストのシナリオでは、Fabric.exe と FabricGateway.exe の複数のインスタンスを実行することによって、1 台のコンピューターまたは VM で複数のノードをホストできます。

名前付きアプリケーションは、名前付きサービスのコレクションで、特定または複数の関数を実行します。 サービスは完全なスタンドアロンの機能を実行し (他のサービスから独立して開始、実行できる)、コード、構成、データで構成されます。 アプリケーション パッケージがイメージ ストアにコピーされた後、アプリケーション パッケージのアプリケーションの種類を (その名前/バージョンで) 指定することにより、そのアプリケーションのインスタンスをクラスター内に作成します。 アプリケーションの種類のインスタンスにはそれぞれ、URI 名が割り当てられます (例: *fabric:/MyNamedApp*)。 1 つのアプリケーションの種類から複数の名前付きアプリケーションをクラスター内で作成することが可能です。 異なるアプリケーションの種類から名前付きアプリケーションを作成することもできます。 個々の名前付きアプリケーションは別々に管理され、バージョニングされます。

名前付きアプリケーションを作成した後、そのいずれかのサービスの種類を (対応する名前/バージョンで) 指定することによって、特定のサービスの種類 (名前付きサービス) のインスタンスをクラスターに作成できます。 サービスの種類のインスタンスにはそれぞれ、対応する名前付きアプリケーションの URI に従属する URI 名が割り当てられます。 たとえば名前付きサービス "MyDatabase" を名前付きアプリケーション "MyNamedApp" 内に作成した場合、*fabric:/MyNamedApp/MyDatabase* のような URI が割り当てられます。 1 つの名前付きアプリケーションに複数の名前付きサービスを作成することができます。 名前付きサービスにはそれぞれ固有のパーティション構成とインスタンス/レプリカ数を割り当てることができます。 

ステートレスとステートフルの&2; 種類のサービスがあります。  ステートレス サービスの永続的な状態は、外部ストレージ サービス (Azure Storage、Azure SQL Database、Azure DocumentDB など) に保存されます。 永続的なストレージがサービスにまったく存在しない場合は、ステートレス サービスを使用します。 ステートフル サービスは Service Fabric を使用して、その Reliable Collection や Reliable Actors のプログラミング モデルを介してサービスの状態を管理します。  

名前付きサービスを作成するときは、パーティション構成を指定します。 さまざまな状態を伴うサービスでは、データが複数のパーティションに分割されてクラスターのノードに分散されます。 これによって名前付きサービスの状態を拡張することができます。 パーティションには、ステートレスの名前付きサービスの場合はインスタンスが、ステートフルの名前付きサービスの場合はレプリカが格納されます。 通常、ステートレスの名前付きサービスは内部的な状態を持たないため、割り当てられるパーティションは&1; つだけです。 パーティションのインスタンスによってもたらされるのは可用性です。つまり&1; つのインスタンスで障害が発生しても他のインスタンスは正常動作を保ち、Service Fabric によって新しいインスタンスが作成されます。 ステートフルの名前付きサービスでは、その状態がレプリカ内で管理されます。それぞれのパーティションが固有のレプリカ セットを持ち、すべての状態が同期されます。 万一レプリカに障害が発生した場合は、Service Fabric が既存のレプリカから新しいレプリカを構築します。

次の図は、アプリケーションとサービス インスタンス、パーティション、レプリカ間のリレーションシップを示しています。

![サービス内のパーティションとレプリカ][cluster-application-instances]

## <a name="supported-programming-models"></a>サポートされるプログラミング モデル
Service Fabric には、サービスの記述と管理に使用できる複数の方法が用意されています。 サービスは、Service Fabric API を使用して、プラットフォームの機能とアプリケーション フレームワークをフルに活用することができます。また、サービスは、任意の言語で記述され、Service Fabric クラスターでホストされているだけのコンパイル済み実行可能プログラムにすることもできます。 詳しくは、[サポートされるプログラミング モデル](service-fabric-choose-framework.md)に関する記事をご覧ください。

### <a name="guest-executables"></a>ゲスト実行可能ファイル
[ゲスト実行可能ファイル](service-fabric-deploy-existing-app.md)は、任意の言語で記述された任意の実行可能ファイルです。そのため、既存のアプリケーションを利用し、他のサービスとともに Service Fabric クラスターでホストすることができます。 ただし、ゲスト実行ファイルは Service Fabric API と直接統合されていないため、プラットフォームに用意されているフルセットの機能 (カスタムの正常性、負荷のレポート、サービス エンドポイントの登録、ステートフル コンピューティングなど) を利用できません。

### <a name="containers"></a>コンテナー
既定では、Service Fabric はサービスをプロセスとしてデプロイし、アクティブ化します。 さらに、Service Fabric では、[コンテナー イメージ](service-fabric-containers-overview.md)内のサービスもデプロイできます。 重要なこととして、プロセスとしてのサービスとコンテナー内のサービスを同じアプリケーション内で混在させることができます。  現在、Service Fabric では、Linux での Docker コンテナーのデプロイと、Windows Server 2016 での Windows Server コンテナーのデプロイをサポートしています。 Service Fabric のアプリケーション モデルでは、コンテナーは複数のサービス レプリカが配置されたアプリケーション ホストを表します。  Service Fabric を使用して、既存のアプリケーション、ステートレス サービス、またはステートフル サービスをコンテナーにデプロイできます。  

### <a name="reliable-services"></a>Reliable Service
[Reliable Services](service-fabric-reliable-services-introduction.md) は、Service Fabric プラットフォームと統合されたサービス作成用の軽量のフレームワークで、プラットフォームの全機能を活用できます。 Reliable Services は、Web サーバー、Azure Cloud Services の worker ロールなど、ほとんどのサービス プラットフォームと同様にステートレスになる場合があり、状態は Azure DB や Azure Table Storage など、外部のソリューションに維持されます。 Reliable Services はステートフルになる場合もあり、状態は Reliable Collection を使用してサービスに直接維持されます。 状態はレプリケーションによって高可用になり、パーティションによって分散されます。いずれも Service Fabric で自動的に管理されます。

### <a name="reliable-actors"></a>Reliable Actor
[Reliable Actors](service-fabric-reliable-actors-introduction.md) フレームワークは Reliable Services 上に構築され、アクター設計パターンに基づいて、Virtual Actor パターンを実装するアプリケーション フレームワークです。 Reliable Actors フレームワークは、独立したコンピューティングのユニットと、アクターという単一スレッドの実行を含む状態を使用します。 Reliable Actors フレームワークには、アクターとプリセットされた状態の永続性とスケールアウト構成に対応する組み込みの通信が用意されています。

## <a name="next-steps"></a>次のステップ
* [Azure でのクラスター](service-fabric-cluster-creation-via-portal.md)または [Windows でのスタンドアロン クラスター](service-fabric-cluster-creation-for-windows-server.md)を作成する方法を学びます。
* [Reliable Services](service-fabric-reliable-services-quick-start.md) または [Reliable Actors](service-fabric-reliable-actors-get-started.md) プログラミング モデルを使用してサービスを作成してみます。
* [アプリケーションのライフサイクル](service-fabric-application-lifecycle.md)について学びます。
* [アプリケーションとクラスターの正常性を確認する](service-fabric-health-introduction.md)方法を学びます。
* [サービスを監視および診断する](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)方法を学びます。 
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png

