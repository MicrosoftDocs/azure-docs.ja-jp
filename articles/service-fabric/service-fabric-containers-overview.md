---
title: "Service Fabric とコンテナーの概要 | Microsoft Docs"
description: "Service Fabric に関する説明と、コンテナーを使用してマイクロサービス アプリケーションをデプロイする方法の概要を示します。 この記事では、コンテナーの使い方と、Service Fabric で利用可能な機能の概要について説明しています。"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/20/2017
ms.author: msfussell
ms.openlocfilehash: f47a855b94a29a2e9bbf4ca509e68612423aa65d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-and-containers"></a>Service Fabric とコンテナー
> [!NOTE]
> Windows 10 での Service Fabric クラスターへのコンテナーのデプロイは、まだサポートされていません。 
>   

## <a name="introduction"></a>はじめに
Azure Service Fabric は、マシンのクラスター全体にわたるサービスの[オーケストレーター](service-fabric-cluster-resource-manager-introduction.md)であり、Microsoft の大規模なサービスで長年にわたって使用され、最適化されてきました。 [Service Fabric プログラミング モデル](service-fabric-choose-framework.md)の使用や[ゲスト実行可能ファイル](service-fabric-deploy-existing-app.md)のデプロイなどの多くの方法で、サービスを開発することができます。 既定では、Service Fabric はこれらのサービスをプロセスとしてデプロイし、アクティブ化します。 プロセスとしてこれらを実施することで、これまでになくアクティブ化が高速になり、クラスターにおけるリソースの使用に無駄もなくなります。 さらに、Service Fabric では、コンテナー イメージ内のサービスもデプロイできます。 重要なこととして、プロセスとしてのサービスとコンテナー内のサービスを同じアプリケーション内で混在させることができます。   

## <a name="what-are-containers"></a>コンテナーとは
コンテナーとは、カプセル化された、個別にデプロイが可能なコンポーネントです。同じカーネル上でそれぞれが分離されたインスタンスとして動作し、オペレーティング システムが提供する仮想化を活かすことができます。 そのため、各アプリケーションとそのランタイム、依存関係、システム ライブラリはコンテナー内で動作し、オペレーティング システム構成のコンテナー独自の分離されたビューへの完全なプライベート アクセスが提供されます。 移植性のみならず、この高度なセキュリティおよびリソースの分離は、Service Fabric でコンテナーを利用するうえでの主なメリットとなります。この方法でなければ、サービスはプロセスとして実行されるためです。

コンテナーは、システムの基礎となるオペレーティング システムをアプリケーションで仮想化する、仮想化テクノロジです。 コンテナーは、さまざまなレベルで分離されたアプリケーションを実行するための変更不可能な環境を提供します。 コンテナーはカーネル上で直接動作し、ファイルシステムなどのリソースの分離されたビューを備えています。 仮想マシンと比較して、コンテナーには次の利点があります。

* **小さい**: コンテナーは 1 つのストレージ領域を使用し、複数のバージョンと更新プログラムを階層化することで効率を高めています。
* **高速**: コンテナーはオペレーティング システム全体を再起動する必要がないため、はるかに高速に (通常は秒単位で) 起動できます。
* **移植性**: コンテナー化されたアプリケーションのイメージを、クラウドまたはオンプレミスで実行されるように移植することができます。仮想マシンへの移植または物理マシン上への直接の移植が可能です。
* **リソース ガバナンスを実現し** 、コンテナーがホスト上で利用する物理リソースを制限できるようにします。

## <a name="container-types-and-supported-environments"></a>コンテナーの種類とサポートされる環境
Service Fabric は Linux と Windows の両方でコンテナーをサポートしています。また、Windows では Hyper-V の分離モードもサポートしています。 

> [!NOTE]
> Windows 10 での Service Fabric クラスターへのコンテナーのデプロイは、まだサポートされていません。 
> 

### <a name="docker-containers-on-linux"></a>Linux 上の Docker コンテナー
Docker は、Linux カーネル コンテナー上でコンテナーを作成、管理するための上位レベルの API を提供します。 Docker Hub は、コンテナー イメージの格納と取得に使用する中央リポジトリです。
チュートリアルについては、「[Service Fabric への Linux コンテナーのデプロイ](service-fabric-get-started-containers-linux.md)」を参照してください。

### <a name="windows-server-containers"></a>Windows Server コンテナー
Windows Server 2016 には、分離レベルの異なる 2 種類のコンテナーが用意されています。 Windows Server コンテナーと Docker コンテナーは、名前空間とファイル システムが分離されていても、カーネルをコンテナーの実行されているホストと共有するという点で似ています。 Linux では、`cgroups` や `namespaces` によって分離を行うのが通例でした。Windows Server コンテナーも同様に動作します。

Hyper-V をサポートする Windows コンテナーでは、各コンテナーがオペレーティング システム カーネルを他のコンテナーまたはホストと共有しないため、より多くの分離とセキュリティが提供されます。 この高度なセキュリティの分離により、Hyper-V コンテナーは、悪意のあるマルチテナント シナリオで効果を発揮します。
チュートリアルについては、「[Service Fabric への Windows コンテナーのデプロイ](service-fabric-get-started-containers.md)」を参照してください。

次の図は、オペレーティング システムで利用可能なさまざまな種類の仮想化と分離のレベルを示しています。
![Service Fabric プラットフォーム][Image1]

## <a name="scenarios-for-using-containers"></a>コンテナーを使用するシナリオ
コンテナーの選択が望まれる一般的な例を以下に示します。

* **IIS リフト アンド シフト**: 既存の [ASP.NET MVC](https://www.asp.net/mvc) アプリを使い続ける場合、ASP.NET Core に移行せず、コンテナーに入れます。 これらの ASP.NET MVC アプリケーションは、インターネット インフォメーション サービス (IIS) に依存します。 このようなアプリケーションは、事前に作成された IIS イメージからコンテナー イメージにパッケージ化し、Service Fabric でデプロイすることができます。 Windows コンテナーの作成方法については、「[Windows Server のコンテナー イメージ](https://docs.microsoft.com/en-us/virtualization/windowscontainers/quick-start/quick-start-windows-server)」を参照してください。
* **コンテナーと Service Fabric マイクロサービスの混在使用**: アプリケーションの一部として、既存のコンテナー イメージを使用できます。 たとえば、より多くのバックエンド計算に対応するために、アプリケーションやステートフル サービスの Web フロントエンドに [NGINX コンテナー](https://hub.docker.com/_/nginx/)を使用できます。
* **"うるさい隣人" サービスの影響の軽減**: コンテナーのリソース ガバナンス機能を使用し、ホストでサービスが使用するリソースを制約できます。 サービスが多くのリソースを使用し、他の操作のパフォーマンスに影響を及ぼす可能性がある場合 (実行時間の長いクエリのような操作など)、リソース ガバナンス機能を備えたコンテナーにそれらのサービスを配置することを検討してください。

## <a name="service-fabric-support-for-containers"></a>Service Fabric によるコンテナーのサポート
Service Fabric では、Linux での Docker コンテナーのデプロイと、Windows Server 2016 での Windows Server コンテナーのデプロイをサポートしています。また、Hyper-V の分離モードもサポートしています。 

Service Fabric の [アプリケーション モデル](service-fabric-application-model.md)では、コンテナーは複数のサービス レプリカが配置されたアプリケーション ホストを表します。 Service Fabric は任意のコンテナーを実行できます。このシナリオは、コンテナー内に既存のアプリケーションをパッケージ化する[ゲスト実行可能ファイルのシナリオ](service-fabric-deploy-existing-app.md)と似ています。 このシナリオは、コンテナーによく見られるユース ケースです。たとえば、組み込みの Service Fabric プログラミング モデルを使用せずに、任意の言語またはフレームワークを使用して作成されたアプリケーションの実行などです。

また、[コンテナー内で Service Fabric サービスを実行する](service-fabric-services-inside-containers.md)こともできます。 コンテナー内で Service Fabric サービスを実行するためのサポートは現時点では限定されていますが、今後のリリースで強化される予定です。

Service Fabric には、コンテナー化されたマイクロサービスで構成されたアプリケーションの構築に役立つ、いくつかのコンテナー機能が用意されています。 Service Fabric は、コンテナー化されたサービスについて次の機能を提供しています。

* コンテナー イメージのデプロイとアクティブ化
* Azure クラスターでのリソース値の既定の設定を含むリソース ガバナンス
* リポジトリの認証
* コンテナー ポートからホスト ポートへのマッピング
* コンテナー間での検出と通信
* 環境変数の構成と設定の機能
* コンテナーにセキュリティ資格情報を設定する機能
* コンテナーに対する異なるネットワーク モードの選択

## <a name="next-steps"></a>次のステップ
この記事では、Service Fabric がコンテナー オーケストレーターとなるコンテナーについて説明したほか、Service Fabric にはコンテナーをサポートする機能があることについても説明しました。 次のステップとして、各機能の例の紹介と、その使用方法の解説に進みます。

[Windows で初めての Service Fabric コンテナー アプリケーションを作成する](service-fabric-get-started-containers.md)

[Linux で初めての Service Fabric コンテナー アプリケーションを作成する](service-fabric-get-started-containers-linux.md)

[Windows コンテナーの詳細](https://docs.microsoft.com/en-us/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png
