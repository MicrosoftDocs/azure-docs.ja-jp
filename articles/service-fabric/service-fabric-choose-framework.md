---
title: Service Fabric プログラミング モデルの概要 | Microsoft Docs
description: Service Fabric では、アクター フレームワークとサービス フレームワークという、サービスを構築するための 2 つのフレームワークが提供されています。 それぞれ、シンプル性とコントロールという、両極端の特性を持っています。
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: vturecek
ms.assetid: 974b2614-014e-4587-a947-28fcef28b382
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: d764cbe2df78cb9029a4109caa2998ddded5d6ff
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58665714"
---
# <a name="service-fabric-programming-model-overview"></a>Service Fabric プログラミング モデルの概要
Service Fabric には、サービスの記述と管理に使用できる複数の方法が用意されています。 サービスでは、Service Fabric API を使用するように選択して、プラットフォームの機能とアプリケーション フレームワークを最大限に活用できます。 サービスはまた、任意の言語で記述されたコンパイル済みの実行可能プログラム、または Service Fabric クラスターでホストされるコンテナー内で実行されているコードにすることもできます。

## <a name="guest-executables"></a>ゲスト実行可能ファイル
[ゲスト実行可能ファイル](service-fabric-guest-executables-introduction.md)は、アプリケーション内でサービスとして実行できる既存の任意の実行可能ファイル (任意の言語で記述されたもの) です。 ゲスト実行可能ファイルでは、Service Fabric SDK の API を直接呼び出さないでください。 それでも、Service Fabric によって公開される REST API を呼び出すことにより、サービスの検出可能性や、正常性と負荷のカスタム レポートなど、このプラットフォームが提供する機能からメリットを得られます。 また、完全なアプリケーションのライフサイクルのサポートも備えています。

ゲスト実行可能ファイルから始める場合は、最初の [ゲスト実行可能ファイル](service-fabric-deploy-existing-app.md)をデプロイしてください。

## <a name="containers"></a>Containers
既定では、Service Fabric はサービスをプロセスとしてデプロイし、アクティブ化します。 また、Service Fabric では[コンテナー](service-fabric-containers-overview.md)内のサービスもデプロイできます。 Service Fabric では、Linux コンテナーのデプロイと、Windows Server 2016 での Windows コンテナーのデプロイをサポートしています。 コンテナー イメージは、任意のコンテナー リポジトリからプルし、マシンにデプロイすることができます。 既存のアプリケーションを、ゲスト実行可能ファイルとしてデプロイすることも、コンテナー内の Service Fabric のステートレスまたはステートフルな Reliable Services または Reliable Actors としてデプロイすることもできます。また、プロセス内のサービスとコンテナー内のサービスを同じアプリケーション内で混在させることができます。

[Windows または Linux でサービスをコンテナー化することに関する詳細情報](service-fabric-deploy-container.md)

## <a name="reliable-services"></a>Reliable Service
Reliable Services は、Service Fabric プラットフォームと統合されたサービスを作成できる軽量のフレームワークです。プラットフォームの全機能を活用できます。 Reliable Services には、Service Fabric ランタイムがサービスのライフサイクルを管理し、サービスがランタイムとやり取りすることができる最小限の API のセットが用意されています。 アプリケーション フレームワークは最小限で、設計と実装の選択を細かく制御できるので、ASP.NET Core など、他のアプリケーション フレームワークのホストに使用できます。

Reliable Services は、Web サーバーなどのほとんどのサービス プラットフォームと同様にステートレス (サービスの各インスタンスは同様に作成され、状態は Azure DB や Azure Table Storage など、外部のソリューションに維持される) にすることができます。

Reliable Services は Service Fabric と排他的でステートフルの場合もあります。Service Fabric は Reliable Collection を使用してサービスに直接維持されます。 状態はレプリケーションによって高可用になり、パーティションによって分散されます。いずれも Service Fabric で自動的に管理されます。

[Reliable Services の詳細](service-fabric-reliable-services-introduction.md)、または[最初の Reliable Services を作成する](service-fabric-reliable-services-quick-start.md)場合の概要を参照してください。

## <a name="aspnet-core"></a>ASP.NET Core
ASP.NET Core は新しいオープンソースのクロスプラットフォーム フレームワークであり、Web アプリ、IoT アプリ、モバイル バックエンドなど、最新のクラウドベースのインターネット接続アプリケーションを構築するために使用されます。 Service Fabric は ASP.NET Core と統合しているので、リライアブル コレクションおよび Service Fabric の高度なオーケストレーション機能を活用するステートレスおよびステートフル両方の ASP.NET Core アプリケーションを記述できます。

[Service Fabric の ASP.NET Core の詳細](service-fabric-reliable-services-communication-aspnetcore.md)または[初めての ASP.NET Core の Service Fabric アプリケーションの記述](service-fabric-tutorial-create-dotnet-app.md)に関する記事をご覧ください。

## <a name="reliable-actors"></a>Reliable Actor
Reliable Actors フレームワークは Reliable Services 上に構築され、アクター設計パターンに基づいて、Virtual Actor パターンを実装するアプリケーション フレームワークです。 Reliable Actors フレームワークは、独立したコンピューティングのユニットと、アクターという単一スレッドの実行を含む状態を使用します。 Reliable Actors フレームワークには、アクターとプリセットされた状態の永続性とスケールアウト構成に対応する組み込みの通信が用意されています。

Reliable Actors は、Reliable Services 上に構築されたアプリケーション フレームワークであるため、Service Fabric プラットフォームと完全に統合されており、プラットフォームによって用意される機能をすべて利用できます。

[Reliable Actors の詳細](service-fabric-reliable-actors-introduction.md)と[最初の Reliable Actors サービスを作成する](service-fabric-reliable-actors-get-started.md)場合の概要を参照してください。


[ASP.NET Core を使用してフロント エンド サービスを構築する](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>次の手順
[Service Fabric とコンテナーの概要](service-fabric-containers-overview.md)

[Reliable Services の概要](service-fabric-reliable-services-introduction.md)

[Reliable Actors の概要](service-fabric-reliable-actors-introduction.md)

[Service Fabric および ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)




