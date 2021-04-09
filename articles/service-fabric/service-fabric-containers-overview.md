---
title: Service Fabric とコンテナーの概要
description: Service Fabric に関する説明と、コンテナーを使用してマイクロサービス アプリケーションをデプロイする方法の概要を示します。 この記事では、コンテナーの使い方と、Service Fabric で利用可能な機能の概要について説明しています。
ms.topic: conceptual
ms.date: 7/9/2020
ms.openlocfilehash: cd0ec7dd2247fdd791df362fa34542178c17df4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87091659"
---
# <a name="service-fabric-and-containers"></a>Service Fabric とコンテナー

## <a name="introduction"></a>はじめに

Azure Service Fabric は、スケーラブルで信頼性に優れたマイクロサービスとコンテナーのパッケージ化とデプロイ、管理を簡単に行うことができる分散システム プラットフォームです。

Service Fabric は、マシン クラスター全体にマイクロサービスをデプロイする Microsoft の[コンテナー オーケストレーター](service-fabric-cluster-resource-manager-introduction.md)です。 Service Fabric には、長年にわたる Microsoft の大規模なサービスの提供から得た教訓が生きています。

[Service Fabric プログラミング モデル](service-fabric-choose-framework.md)や [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) の使用から[任意のコード](service-fabric-guest-executables-introduction.md)のデプロイまで、多くの方法でマイクロサービスを開発することができます。 [コンテナーのデプロイと管理](service-fabric-containers-overview.md)だけを行う場合でも、Service Fabric は優れた選択肢です。

既定では、Service Fabric はこれらのサービスをプロセスとしてデプロイし、アクティブ化します。 プロセスとしてこれらを実施することで、これまでになくアクティブ化が高速になり、クラスターにおけるリソースの使用に無駄もなくなります。 さらに、Service Fabric では、コンテナー イメージ内のサービスもデプロイできます。 プロセスとしてのサービスとコンテナー内のサービスを同じアプリケーション内で混在させることもできます。

Service Fabric をすぐに開始し、コンテナーを試してみるには、クイック スタート、チュートリアル、またはサンプルを使用してください。  

[クイック スタート: Linux コンテナー アプリケーションを Service Fabric にデプロイする](service-fabric-quickstart-containers-linux.md)  
[クイック スタート: Windows コンテナー アプリケーションを Service Fabric にデプロイする](service-fabric-quickstart-containers.md)  
[既存の .NET アプリをコンテナー化する](service-fabric-host-app-in-a-container.md)  
[Service Fabric コンテナーのサンプル](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>コンテナーとは

コンテナーは、異なるコンピューティング環境でアプリケーションを確実に実行するという問題を、アプリケーションを実行するための不変の環境を提供することで解決します。 コンテナーは、アプリケーションとそのすべての依存関係 (ライブラリや構成ファイルなど) を、コンテナー内でソフトウェアを実行するために必要なものをすべて格納する独自の分離された "ボックス" にラップします。 コンテナーの実行場所に関係なく、その中のアプリケーションは、適切なバージョンの依存するライブラリーや構成ファイルなど、実行するために必要なすべてのものを常に持っています。

コンテナーはカーネル上で直接動作し、ファイルシステムなどのリソースの分離されたビューを備えています。 コンテナー内のアプリケーションは、コンテナーの外のアプリケーションやプロセスを認識しません。 各アプリケーションとそのランタイム、依存関係、およびシステム ライブラリは、コンテナー内で動作し、オペレーティング システムのコンテナー独自の分離されたビューへの完全なプライベート アクセスが提供されます。 異なるコンピューティング環境で実行するために必要なアプリケーションのすべての依存関係を簡単に用意できることに加え、セキュリティとリソースの分離も Service Fabric でコンテナーを使用することの重要なメリットです。それ以外の場合、サービスはプロセス内で実行されます。

仮想マシンと比較して、コンテナーには次の利点があります。

* **小さい**: コンテナーは 1 つのストレージ領域を使用し、複数のバージョンと更新プログラムを階層化することで効率を高めています。
* **高速**: コンテナーはオペレーティング システム全体を再起動する必要がないため、はるかに高速に (通常は秒単位で) 起動できます。
* **移植性**: コンテナー化されたアプリケーションのイメージを、クラウドまたはオンプレミスで実行されるように移植することができます。仮想マシンへの移植または物理マシン上への直接の移植が可能です。
* **リソース ガバナンスを実現し** 、コンテナーがホスト上で利用する物理リソースを制限できるようにします。

### <a name="container-types-and-supported-environments"></a>コンテナーの種類とサポートされる環境

Service Fabric は Linux と Windows の両方でコンテナーをサポートしています。また、Windows では Hyper-V の分離モードをサポートします。

#### <a name="docker-containers-on-linux"></a>Linux 上の Docker コンテナー

Docker は、Linux カーネル コンテナー上でコンテナーを作成、管理するための API を提供します。 Docker Hub は、コンテナー イメージの格納と取得に使用する中央リポジトリを提供します。
Linux ベースのチュートリアルについては、「[Linux で初めての Service Fabric コンテナー アプリケーションを作成する](service-fabric-get-started-containers-linux.md)」を参照してください。

#### <a name="windows-server-containers"></a>Windows Server コンテナー

Windows Server 2016 以降には、分離レベルの異なる 2 種類のコンテナーが用意されています。 Windows Server コンテナーと Docker コンテナーは、名前空間とファイル システムは分離されていますが、それが実行されているホストとカーネルを共有するという点で似ています。 Linux では、cgroups や名前空間によって分離を行うのが通例でした。Windows Server コンテナーも同様に動作します。

Hyper-V をサポートする Windows コンテナーでは、各コンテナーがオペレーティング システム カーネルを他のコンテナーまたはホストと共有しないため、より多くの分離とセキュリティが提供されます。 この高度なセキュリティの分離により、Hyper-V コンテナーは、潜在的に敵対するマルチテナント シナリオで効果を発揮します。
Windows ベースのチュートリアルについては、「[Windows で初めての Service Fabric コンテナー アプリケーションを作成する](service-fabric-get-started-containers.md)」を参照してください。

次の図は、利用可能な異なる種類の仮想化と分離のレベルを示しています。
![Service Fabric プラットフォーム][Image1]

## <a name="scenarios-for-using-containers"></a>コンテナーを使用するシナリオ

コンテナーの選択が望まれる一般的な例を以下に示します。

* **IIS のリフトとシフト**: 既存の [ASP.NET MVC](https://www.asp.net/mvc) アプリを ASP.NET Core に移行する代わりにコンテナー内に配置できます。 これらの ASP.NET MVC アプリケーションは、インターネット インフォメーション サービス (IIS) に依存します。 このようなアプリケーションは、事前に作成された IIS イメージからコンテナー イメージにパッケージ化し、Service Fabric でデプロイすることができます。 Windows コンテナーの作成方法については、「[Windows Server のコンテナー イメージ](/virtualization/windowscontainers/quick-start/quick-start-windows-server)」を参照してください。

* **コンテナーと Service Fabric マイクロサービスの混在使用**: アプリケーションの一部として、既存のコンテナー イメージを使用できます。 たとえば、より多くのバックエンド計算に対応するために、アプリケーションやステートフル サービスの Web フロントエンドに [NGINX コンテナー](https://hub.docker.com/_/nginx/)を使用できます。

* **"うるさい隣人" サービスの影響の軽減**: コンテナーのリソース ガバナンス機能を使用し、ホストでサービスが使用するリソースを制約できます。 サービスが多くのリソースを使用し、他の操作のパフォーマンスに影響を及ぼす可能性がある場合 (実行時間の長いクエリのような操作など)、リソース ガバナンス機能を備えたコンテナーにそれらのサービスを配置することを検討してください。

## <a name="service-fabric-support-for-containers"></a>Service Fabric によるコンテナーのサポート

Service Fabric では、Linux での Docker コンテナーのデプロイと、Windows Server 2016 以降での Windows Server コンテナーのデプロイをサポートしています。また、Hyper-V の分離モードもサポートしています。

> [!NOTE]
> コンテナーは、ローカルの単一ノード Service Fabric クラスターではサポートされていません (OneBox の Linux クラスター、およびローカル Service Fabric インストールの Windows クラスターの両方でサポートされません)。

Service Fabric は、[アプリケーション モデル](service-fabric-application-model.md)を提供します。このモデルでは、コンテナーは、複数のレプリカがその中に配置されるアプリケーション ホストを表わします。 Service Fabric は、[ゲスト実行可能ファイル シナリオ](service-fabric-guest-executables-introduction.md)もサポートします。このシナリオでは、組み込みの Service Fabric プログラミング モデルではなく、任意の言語またはフレームワークを使用して記述されている既存のアプリケーションをコンテナー内にパッケージ化します。 このシナリオは、コンテナーの一般的なユースケースです。

[コンテナー内の Service Fabric サービス](service-fabric-services-inside-containers.md)を実行することもできます。 コンテナー内の Service Fabric サービスの実行のサポートは、現時点では限定されています。

Service Fabric には、コンテナー化されたマイクロサービスで構成されたアプリケーションの構築に役立つさまざまなコンテナー機能が用意されています。

* コンテナー イメージのデプロイとアクティブ化
* Azure クラスターでのリソース値の既定の設定を含むリソース ガバナンス
* リポジトリの認証
* コンテナー ポートからホスト ポートへのマッピング
* コンテナー間での検出と通信
* 環境変数の構成と設定の機能
* コンテナーにセキュリティ資格情報を設定する機能
* コンテナーに対する異なるネットワーク モードの選択

Azure Kubernetes Service での Kubernetes クラスターの作成方法や Azure Container Registry へのプライベート Docker レジストリの作成方法など、Azure でのコンテナーのサポートの包括的な概要については、「[Azure for Containers](../containers/index.yml)」を参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、コンテナーを実行するために Service Fabric が提供するサポートについて説明しました。 次に、各機能の例の紹介と、その使用方法の解説に進みます。

[Linux で初めての Service Fabric コンテナー アプリケーションを作成する](service-fabric-get-started-containers-linux.md)  
[Windows で初めての Service Fabric コンテナー アプリケーションを作成する](service-fabric-get-started-containers.md)  
[Windows コンテナーの詳細](/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png
