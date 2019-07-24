---
title: Azure Dev Spaces のしくみと構成方法
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 03/04/2019
ms.topic: conceptual
description: Azure Dev Spaces の使用を開始するためのプロセスおよび azds.yaml 構成ファイルでのそれらの構成方法について説明します
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
ms.openlocfilehash: 494dd3774ec47598a95c6e20de6283abc2e4ff94
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544925"
---
# <a name="how-azure-dev-spaces-works-and-is-configured"></a>Azure Dev Spaces のしくみと構成方法

Kubernetes アプリケーションの開発には課題が生じる可能性があります。 Docker と Kubernetes の構成ファイルが必要です。 アプリケーションをローカルでテストし、その他の依存サービスとやり取りする方法を理解する必要があります。 開発者のチームを採用して、開発とテストを複数のサービスで同時に処理しなければならない場合があります。

Azure Dev Spaces を使用すると、Kubernetes アプリケーションを Azure Kubernetes Service (AKS) で直接開発、デプロイ、およびデバッグできます。 また、Azure Dev Spaces を使用して開発スペースをチームで共有することもできます。 開発スペースをチームで共有すると、個々のチーム メンバーが単独で開発を行うことができます。クラスター内の依存関係やその他のアプリケーションのレプリケートまたはモックアップは必要ありません。

Azure Dev Spaces では、AKS で Kubernetes アプリケーションをデプロイ、実行、およびデバッグするための構成ファイルを作成して使用します。 この構成ファイルはアプリケーションのコードと共に存在し、バージョン コントロール システムに追加することができます。

この記事では、Azure Dev Spaces の使用を開始するためのプロセスおよび Azure Dev Spaces 構成ファイルでのそれらのプロセスの構成方法について説明します。 Azure Dev Spaces を迅速に実行して実際に確認するために、次に示すいずれかのクイック スタートを実行してください。

* [Java (CLI と Visual Studio Code を使用)](quickstart-java.md)
* [.NET Core (CLI と Visual Studio Code を使用)](quickstart-netcore.md)
* [.NET Core (Visual Studio 2017 を使用)](quickstart-netcore-visualstudio.md)
* [Node.js (CLI と Visual Studio Code を使用)](quickstart-nodejs.md)

## <a name="how-azure-dev-spaces-works"></a>Azure Dev Spaces のしくみ

Azure Dev Spaces では 2 つの異なるコンポーネント (コントローラーとクライアント側ツール) をユーザーが操作します。

![Azure Dev Spaces のコンポーネント](media/how-dev-spaces-works/components.svg)

コントローラーでは次のアクションが実行されます。

* 開発スペースの作成と選択を管理する。
* アプリケーションの Helm チャートをインストールし、Kubernetes オブジェクトを作成する。
* アプリケーションのコンテナー イメージをビルドする。
* アプリケーションを AKS にデプロイする。
* インクリメンタル ビルドを行い、ソース コードが変更されたら再起動する。
* ログと HTTP トレースを管理する。
* stdout と stderr をクライアント側ツールに転送する。
* 親開発スペースから派生した子開発スペースの作成をチーム メンバーに許可する。
* スペース内および親スペースと子スペースにまたがるアプリケーションのルーティングを構成する。

コントローラーは AKS の外部に存在します。 これは、クライアント側ツールと AKS クラスター間の動作と通信を管理します。 クラスターで Azure Dev Spaces を使用するための準備を行う際に、Azure CLI を使用してコントローラーを有効にします。 クライアント側ツールを使用すると、有効したコントローラーを操作できます。

ユーザーはクライアント側ツールを使用して次の操作を行うことができます。
* アプリケーション用の Dockerfile、Helm チャート、および Azure Dev Spaces 構成ファイルを生成する。
* 親開発スペースと子開発スペースを作成する。
* アプリケーションをビルドして起動するようコントローラーに通知する。

クライアント側ツールでは、アプリケーションの実行中に次の操作を行うこともできます。
* AKS で実行されているアプリケーションから stdout と stderr を受信して表示する。
* [port-forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) を使用して、http:\//localhost によるアプリケーションへの Web アクセスを許可する。
* AKS で実行中のアプリケーションにデバッガーをアタッチする。
* インクリメンタル ビルドに対する変更が検出された場合に、ソース コードを開発スペースと同期して、迅速なイテレーションを可能にする。

コマンド ラインから `azds` コマンドの一部としてクライアント側ツールを使用することができます。 クライアント側ツールを以下のものと共に使用することもできます。

* [Azure Dev Spaces 拡張機能](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)を使用する Visual Studio Code。
* Visual Studio 2017 および [Visual Studio Tools for Kubernetes](https://aka.ms/get-vsk8stools)。

Azure Dev Spaces の設定と使用の基本的なフローは次のとおりです。
1. Azure Dev Spaces 用に AKS クラスターを準備する
1. Azure Dev Spaces で実行するためのコードを準備する
1. 開発スペースでコードを実行する
1. 開発スペースでコードをデバッグする
1. 開発スペースを共有する

以降の各セクションでは、Azure Dev Spaces のしくみについて詳しく説明します。

## <a name="prepare-your-aks-cluster"></a>AKS クラスターを準備する

AKS クラスターの準備には以下が含まれます。
* AKS クラスターが [Azure Dev Spaces でサポートされている](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams)リージョン内にあることを確認する。
* Kubernetes 1.10.3 以降が実行されていることを確認する。
* `az aks use-dev-spaces` を使用して Azure Dev Spaces をクラスターで有効にする。

Azure Dev Spaces 用の AKS クラスターを作成して構成する方法の詳細については、次に示すいずれかのファースト ステップ ガイドを参照してください。
* [Azure Dev Spaces での Java の使用](get-started-java.md)
* [Azure Dev Spaces での .NET Core と Visual Studio の使用](get-started-netcore-visualstudio.md)
* [Azure Dev Spaces での .NET Core の使用](get-started-netcore.md)
* [Azure Dev Spaces での Node.js の使用](get-started-nodejs.md)

AKS クラスターで Azure Dev Spaces を有効にすると、クラスター用のコントローラーがインストールされます。 このコントローラーは、クラスターの外部にある個別の Azure リソースであり、クラスター内のリソースに対して次のことを行います。

* 開発スペースとして使用する Kubernetes 名前空間を作成または指定する。
* *azds* という名前の Kubernetes 名前空間が存在する場合は、それを削除し、新しい名前空間を作成する。
* Kubernetes webhook の構成をデプロイします。
* webhook 受付サーバーをデプロイします。
    

また、AKS クラスターが他の Azure Dev Spaces コンポーネントにサービスの呼び出しを行うために使用するものと同じサービス プリンシパルも使用します。

![Azure Dev Spaces がクラスターを準備する](media/how-dev-spaces-works/prepare-cluster.svg)

Azure Dev Spaces を使用するには、開発スペースが少なくとも 1 つ必要です。 Azure Dev Spaces では、AKS クラスター内の Kubernetes 名前空間を開発スペースとして使用します。 コントローラーのインストール時には、最初の開発スペースとして使用する新しい Kubernetes 名前空間の作成または既存の名前空間の選択を求められます。 名前空間が開発スペースとして指定されると、コントローラーでは *azds.io/space=true* ラベルをその名前空間に追加し、開発スペースとして認定します。 作成または指定する最初の開発スペースは、クラスターの準備が完了した後に既定で選択されます。 選択されたスペースは、Azure Dev Spaces で新しいワークロードを作成するために使用されます。

既定では、コントローラーが既存の *default* Kubernetes 名前空間をアップグレードして、*default* という名前の開発スペースを作成します。 クライアント側ツールを使用すると、新しい開発スペースを作成して既存の開発スペースを削除できます。 Kubernetes における制限があるため、*default* 開発スペースを削除することはできません。 また、コントローラーでは、*azds* という名前の既存の Kubernetes 名前空間を削除して、クライアント側ツールで使用される `azds` コマンドとの競合を回避します。

Kubernetes webhook 受付サーバーは、インストルメンテーション用のデプロイ時に 3 つのコンテナー (devspaces-proxy コンテナー、devspaces-proxy-init コンテナー、devspaces-build コンテナー) をポッドに挿入するために使用します。 **これらの 3 つのコンテナーはすべて、AKS クラスターでルート アクセスを使用して実行されます。** また、AKS クラスターが他の Azure Dev Spaces コンポーネントにサービスの呼び出しを行うために使用するものと同じサービス プリンシパルも使用します。

![Azure Dev Spaces Kubernetes webhook 受付サーバー](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

devspaces-proxy コンテナーは、アプリケーション コンテナーに出入りするすべての TCP トラフィックを処理し、ルーティングの実行に役立つサイドカー コンテナーです。 devspaces-proxy コンテナーでは、特定のスペースが使用されている場合に HTTP メッセージを再ルーティングします。 たとえば、親スペースと子スペースのアプリケーション間で HTTP メッセージをルーティングできます。 HTTP 以外のすべてのトラフィックは、未変更の状態で devspaces-proxy を通過します。 また、devspaces-proxy container コンテナーでは、受信と送信の HTTP メッセージをすべてログに記録し、クライアント側ツールにそれらをトレースとして送信します。 これらのトレースは、アプリケーションの動作を調べるために開発者が表示できます。

devspaces-proxy-init コンテナーは、アプリケーションのコンテナーに対するスペースの階層に基づいてルーティング規則を追加する[初期化コンテナー](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)です。 起動の前にアプリケーションのコンテナーの */etc/resolv.conf* ファイルと iptables 構成を更新することでルーティング規則が追加されます。 */etc/resolv.conf* に対する更新によって、親スペースにおけるサービスの DNS 解決が可能になります。 iptables 構成の更新によって、アプリケーションのコンテナーに出入りするすべての TCP トラフィックが devspaces-proxy を経由するようになります。 devspaces-proxy-init からのすべての更新は、Kubernetes で追加される規則に加えて発生します。

devspaces-build コンテナーは初期化コンテナーであり、プロジェクトのソース コードと Docker ソケットがマウントされています。 プロジェクトのソース コードと Docker へのアクセスによって、アプリケーション コンテナーをポッドから直接ビルドできます。

> [!NOTE]
> Azure Dev Spaces では、同じノードを使用して、アプリケーションのコンテナーをビルドおよび実行します。 そのため、Azure Dev Spaces には、アプリケーションをビルドして実行するための外部のコンテナー レジストリが必要ありません。

Kubernetes webhook 受付サーバーは AKS クラスターで作成された新しいポッドをリッスンします。 *azds.io/space=true* ラベル付きの名前空間にそのポッドがデプロイされる場合は、コンテナーを追加してポッドが挿入されます。 devspaces-build コンテナーが挿入されるのは、アプリケーションのコンテナーがクライアント側ツールを使用して実行される場合のみです。

AKS クラスターの準備が完了したら、クライアント側ツールを使用して、開発スペースでコードを準備および実行できます。

## <a name="prepare-your-code"></a>コードを準備する

開発スペースでアプリケーションを実行するには、アプリケーションをコンテナー化する必要があります。また、アプリケーションを Kubernetes にデプロイする方法を定義する必要があります。 アプリケーションをコンテナー化するには、Dockerfile が必要です。 アプリケーションを Kubernetes にデプロイする方法を定義するには、[Helm チャート](https://docs.helm.sh/)が必要です。 アプリケーション用の Dockerfile と Helm チャートの作成を支援するため、クライアント側ツールには `prep` コマンドが用意されています。

```cmd
azds prep --public
```

`prep` コマンドは、プロジェクト内のファイルを確認して、アプリケーションを Kubernetes で実行するための Dockerfile と Helm チャートの作成を試行します。 現時点では、`prep` コマンドは次の言語で Dockerfile と Helm チャートを生成します。

* Java
* Node.js
* .NET Core

ソース コードを含むディレクトリから `prep` コマンドを実行する "*必要があります*"。 正しいディレクトリから `prep` コマンドを実行すると、クライアント側ツールで言語を識別し、アプリケーションのコンテナー化に適した Dockerfile を作成できます。 Java プロジェクト用の *pom.xml* ファイルを含むディレクトリから `prep` コマンドを実行することもできます。

ソース コードが含まれないディレクトリから `prep` コマンドを実行すると、クライアント側ツールでは Dockerfile が生成されません。 また、次のようなエラーが表示されます。"*Dockerfile could not be generated due to unsupported language\(サポートされていない言語が原因で Dockerfile を生成できませんでした\)*"。 このエラーは、クライアント側ツールでプロジェクト タイプが認識されない場合にも発生します。

`prep` コマンドの実行時には、`--public` フラグを指定することができます。 このフラグは、インターネットにアクセス可能なエンドポイントをこのサービス用に作成するようコントローラーに指示します。 このフラグを指定しない場合は、クラスター内から、またはクライアント側ツールで作成された localhost トンネルを使用してサービスにアクセスする必要があります。 生成された Helm チャートを更新することで、`prep` コマンドの実行後にこの動作を有効または無効にすることができます。

`prep` コマンドでは、プロジェクト内の既存の Dockerfile または Helm チャートが置き換えられません。 既存の Dockerfile または Helm チャートにおいて `prep` コマンドで生成されたファイルと同じ名前付け規則が使用される場合、`prep` コマンドはこれらのファイルの生成をスキップします。 それ以外の場合、`prep` コマンドは独自の Dockerfile または Helm チャートを既存のファイルと並行して生成します。

また、`prep` コマンドではプロジェクトのルートに `azds.yaml` ファイルも生成されます。 Azure Dev Spaces では、このファイルを使用してアプリケーションをビルド、インストール、構成、および実行します。 この構成ファイルでは、Dockerfile と Helm チャートの場所が示されており、それらの成果物に加えて追加の構成も提供されます。

[.NET Core サンプル アプリケーション](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend)を使用して作成する azds.yaml ファイルの例を次に示します。

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
        # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
        # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
        # For more information see https://aka.ms/devspaces/routing
        - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
```

`prep` コマンドで生成される `azds.yaml` ファイルは、シンプルな単一のプロジェクト開発シナリオに適しています。 特定のプロジェクトの複雑さが増大した場合は、`prep` コマンドの実行後にこのファイルの更新が必要になる可能性があります。 たとえば、開発やデバッグのニーズに基づいて、プロジェクトでビルド プロセスや起動プロセスを調整しなければならない場合などです。 また、複数のビルド プロセスまたは別のビルド コンテンツが必要な複数のアプリケーションがプロジェクトに存在する場合などです。

## <a name="run-your-code"></a>コードを実行する

開発スペースでコードを実行するには、`azds.yaml` ファイルと同じディレクトリで `up` コマンドを実行します。

```cmd
azds up
```

`up` コマンドでは、プロジェクトのビルドと実行に必要なアプリケーションのソース ファイルとその他の成果物が開発スペースにアップロードされます。 開発スペース内のコントローラーは次の処理を実行します。

1. アプリケーションにデプロイする Kubernetes オブジェクトを作成する。
1. アプリケーション用のコンテナーをビルドする。
1. アプリケーションを開発スペースにデプロイする。
1. アプリケーション エンドポイント (構成済みの場合) 用に、パブリックにアクセス可能な DNS 名を作成する。
1. *port-forward* を使用して、 http://locahost によるアプリケーション エンドポイントへのアクセスを提供する。
1. stdout と stderr をクライアント側ツールに転送する。


### <a name="starting-a-service"></a>サービスを開始する

開発スペースでサービスを開始する場合は、クライアント側ツールとコントローラーが連携して、ソース ファイルを同期し、コンテナーと Kubernetes オブジェクトを作成し、アプリケーションを実行します。

より詳細なレベルでは、`azds up` の実行時には次の処理が行われます。

1. ユーザーのコンピューターから、ユーザーの AKS クラスターに固有の Azure ファイル ストレージにファイルが同期されます。 ソース コード、Helm チャート、および構成ファイルがアップロードされます。 同期プロセスの詳細については、次のセクションで説明します。
1. 新しいセッションを開始する要求がコントローラーによって作成されます。 この要求には、一意の ID、スペース名、ソース コードのパス、デバッグ フラグなどの複数のプロパティが含まれます。
1. コントローラーによって、Helm チャート内の *$(tag)* プレースホルダーが一意のセッション ID に置き換えられ、サービス用の Helm チャートがインストールされます。 一意のセッション ID への参照を Helm チャートに追加すると、この特定のセッション用に AKS クラスターにデプロイされたコンテナーをセッション要求と関連情報に再び関連付けることができます。
1. Helm チャートのインストール時には、Kubernetes webhook 受付サーバーによって、インストルメンテーションおよびプロジェクトのソース コードにアクセスするためのコンテナーがアプリケーションのポッドに追加されます。 HTTP トレースとスペースのルーティングを提供するために、devspaces-proxy コンテナーと devspaces-proxy-init コンテナーが追加されます。 アプリケーションのコンテナーのビルドに必要な Docker インスタンスとプロジェクトのソース コードへのアクセスをポッドに提供するために、devspaces-build コンテナーが追加されます。
1. アプリケーションのポッドが開始されると、devspaces-build コンテナーと devspaces-proxy-init コンテナーを使用してアプリケーション コンテナーがビルドされます。 次に、アプリケーション コンテナーと devspaces-proxy コンテナーが開始されます。
1. アプリケーション コンテナーの開始後、クライアント側の機能で Kubernetes の *port-forward* 機能が使用され、 http://localhost を経由してアプリケーションへの HTTP アクセスが提供されます。 このポート フォワーディングでは、開発スペース内のサービスに開発用コンピューターを接続します。
1. ポッド内のすべてのコンテナーが開始されると、サービスが実行されています。 この時点で、クライアント側の機能は HTTP トレース、stdout、および stderr のストリーミングを開始します。 この情報は、クライアント側の機能によって開発者向けに表示されます。

### <a name="updating-a-running-service"></a>実行中のサービスを更新する

Azure Dev Spaces では、いずれかのプロジェクトのソース コードが変更された場合に、実行中のサービスを更新することができます。 また、Dev Spaces では、変更されたファイルの種類に応じてサービスの更新をさまざまな方法で処理します。 Dev Spaces では、次に示す 3 つの方法で実行中のサービスを更新できます。

* ファイルを直接更新する
* 実行中のアプリケーションのコンテナー内でアプリケーションのプロセスをリビルドして再起動する
* アプリケーションのコンテナーをリビルドして再デプロイする

![Azure Dev Spaces のファイル同期](media/how-dev-spaces-works/file-sync.svg)

静的な資産である特定のプロジェクト ファイル (html、css、cshtml ファイルなど) をアプリケーションのコンテナー内で直接更新できます。何かを再起動する必要はありません。 静的な資産が変更されると、新しいファイルが開発スペースに同期され、実行中のコンテナーで使用されます。

ソース コードやアプリケーションの構成ファイルなどに対する変更は、実行中のコンテナー内でアプリケーションのプロセスを再起動すると適用されます。 これらのファイルが同期されると、*devhostagent* プロセスを使用して、実行中のコンテナー内でアプリケーションのプロセスが再起動されます。 アプリケーションのコンテナーを最初に作成する際は、コントローラーによって、アプリケーションのスタートアップ コマンドが別のプロセス (*devhostagent*) に置き換えられます。 続いて、アプリケーションの実際のプロセスが子プロセスとして *devhostagent* で実行され、その出力が *devhostagent* の出力を使用して行われます。 また、*devhostagent* プロセスは Dev Spaces の一部であり、Dev Spaces の代わりに実行中のコンテナーでコマンドを実行できます。 再起動の実行時には、*devhostagent* によって次の処理が行われます。

* アプリケーションに関連付けられている現在のプロセスを停止する
* アプリケーションをリビルドする
* アプリケーションに関連付けられているプロセスを再起動する

*devhostagent* による先行手順の実行方法は `azds.yaml` 構成ファイルで構成されます。 この構成の詳細については後述します。

プロジェクト ファイル (Dockerfile、csproj ファイル、Helm チャートの一部など) に対する更新を行うには、アプリケーションのコンテナーをリビルドおよび再デプロイする必要があります。 これらのいずれかのファイルが開発スペースに同期されると、コントローラーによって [helm upgrade](https://helm.sh/docs/helm/#helm-upgrade) コマンドが実行され、アプリケーションのコンテナーがリビルドおよび再デプロイされます。

### <a name="file-synchronization"></a>ファイルの同期

開発スペースで初めてアプリケーションを起動するときには、アプリケーションのソース ファイルがすべてアップロードされます。 アプリケーションの実行中および以降の再起動の際は、変更されたファイルのみアップロードされます。 このプロセスを調整するために 2 つのファイル (クライアント側ファイルとコントローラー側ファイル) が使用されます。

クライアント側ファイルは一時ディレクトリに格納され、開発スペースで実行中のプロジェクト ディレクトリのハッシュに基づいて命名されます。 たとえば、Windows におけるプロジェクト用のファイルは *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* のようになります。 Linux では、クライアント側ファイルは */tmp* ディレクトリに格納されます。 `echo $TMPDIR` コマンドを実行すると、macOS でこのディレクトリを探すことができます。

このファイルは JSON 形式であり、次のものが含まれています。

* 開発スペースと同期される各プロジェクト ファイルのエントリ
* 同期 ID
* 最後の同期操作のタイムスタンプ

プロジェクト ファイルの各エントリには、ファイルのパスとファイルのタイムスタンプが含まれています。

コントローラー側ファイルは、AKS クラスターに格納されます。 このファイルには、同期 ID と最後の同期のタイムスタンプが含まれています。

同期は、クライアント側ファイルとコントローラー側ファイルの間で同期のタイムスタンプが一致しない場合に発生します。 同期の際は、クライアント側ツールによってクライアント側ファイル内のファイル エントリが反復処理されます。 ファイルのタイムスタンプが同期のタイムスタンプより後である場合は、そのファイルが開発スペースに同期されます。 同期が完了すると、クライアント側とコントローラー側の両方のファイルで同期のタイムスタンプが更新されます。

クライアント側ファイルが存在しない場合は、すべてのプロジェクト ファイルが同期されます。 この動作では、クライアント側ファイルを削除することによって完全同期を適用できます。

### <a name="how-routing-works"></a>ルーティングのしくみ

開発スペースは AKS 上に構築され、同じ[ネットワークの概念](../aks/concepts-network.md)を使用します。 Azure Dev Spaces には一元化された *ingressmanager* サービスがあり、独自のイングレス コントローラーを AKS クラスターにデプロイします。 *ingressmanager* サービスでは、AKS クラスターが開発スペースと共に監視され、クラスター内の Azure Dev Spaces イングレス コントローラーをイングレス オブジェクトで強化してアプリケーション ポッドへのルーティングを実現します。 各ポッド内の devspaces-proxy コンテナーでは、HTTP トラフィック用の `azds-route-as` HTTP ヘッダーが URL に基づいて開発スペースに追加されます。 たとえば、URL *http://azureuser.s.default.serviceA.fedcba09...azds.io* に対する要求では、`azds-route-as: azureuser` を含む HTTP ヘッダーが取得されます。 `azds-route-as` ヘッダーが既に存在する場合は、devspaces-proxy コンテナーによってヘッダーが追加されません。

クラスタの外部からサービスに対して行われた HTTP 要求はイングレス コントローラーに進みます。 イングレス コントローラーでは、そのイングレス オブジェクトと規則に基づいて要求を適切なポッドに直接ルーティングします。 ポッド内の devspaces-proxy コンテナーが要求を受信し、URLに基づいて `azds-route-as` ヘッダーを追加して、要求をアプリケーション コンテナーにルーティングします。

クラスター内のあるサービスに対して別のサービスから行われた HTTP 要求は、呼び出し元のサービスの devspaces-proxy コンテナーを最初に通過します。 devspaces-proxy コンテナーでは、HTTP 要求を調べて `azds-route-as` ヘッダーをチェックします。 devspaces-proxy コンテナーでは、ヘッダーに基づいて、そのヘッダー値に関連付けられているサービスの IP アドレスを検索します。 IP アドレスが見つかった場合、devspaces-proxy コンテナーは要求をその IP アドレスに再ルーティングします。 IP アドレスが見つからなかった場合、devspaces-proxy コンテナーは要求を親アプリケーション コンテナーにルーティングします。

たとえば、アプリケーション *serviceA* および *serviceB* が*default* という名前の親開発スペースにデプロイされるとします。 *serviceA* は *serviceB* に依存しており、そのサービスに対する HTTP 呼び出しを行います。 Azure ユーザーは、*default* スペースに基づいて、*azureuser* という子開発スペースを作成します。 また、Azure ユーザーは、独自のバージョンの *serviceA* をその子スペースにデプロイします。 *http://azureuser.s.default.serviceA.fedcba09...azds.io* に対する要求は以下のような手順で行われます。

![Azure Dev Spaces のルーティング](media/how-dev-spaces-works/routing.svg)

1. イングレス コントローラーが URL に関連付けられているポッド (*serviceA.azureuser*) の IP を確認します。
1. イングレス コントローラーが Azure ユーザーの開発スペースでポッドの IP を検索し、要求を *serviceA.azureuser* ポッドにルーティングします。
1. *serviceA.azureuser* ポッド内の devspaces-proxy コンテナーが要求を受信し、HTTP ヘッダーとして `azds-route-as: azureuser` を追加します。
1. *serviceA.azureuser* ポッド内の devspaces-proxy コンテナーが *serviceA.azureuser* ポッド内の *serviceA* アプリケーション コンテナーに要求をルーティングします。
1. *serviceA.azureuser* ポッド内の *serviceA* アプリケーションが *serviceB* の呼び出しを行います。 また、*serviceA* アプリケーションには、既存の `azds-route-as` ヘッダー (ここでは `azds-route-as: azureuser`) を保持するためのコードが含まれています。
1. *serviceA.azureuser* ポッド内の devspaces-proxy コンテナーが要求を受信し、`azds-route-as` ヘッダーの値に基づいて *serviceB* の IP を確認します。
1. *serviceA.azureuser* ポッド内の devspaces-proxy コンテナーは *serviceB.azureuser* の IP を検索しません。
1. *serviceA.azureuser* ポッド内の devspaces-proxy コンテナーが親スペース内の *serviceB* (*serviceB.default*) の IP を検索します。
1. *serviceA.azureuser* ポッド内の devspaces-proxy コンテナーが *serviceB.default* の IP を検索し、要求を *serviceB.default* ポッドにルーティングします。
1. *serviceB.default* ポッド内の devspaces-proxy コンテナーが要求を受信し、*serviceB.default* ポッド内の *serviceB* アプリケーション コンテナーに要求をルーティングします。
1. *serviceB.default* ポッド内の *serviceB* アプリケーションが *serviceA.azureuser* ポッドに応答を返します。
1. *serviceA.azureuser* ポッド内の devspaces-proxy コンテナーが応答を受信し、*serviceA.azureuser* ポッド内の *serviceA* アプリケーション コンテナーに応答をルーティングします。
1. *serviceA* アプリケーションが応答を受信し、その独自の応答を返します。
1. *serviceA.azureuser* ポッド内の devspaces-proxy コンテナーが *serviceA* アプリケーション コンテナーから応答を受信し、クラスターの外部に存在する元の呼び出し元に応答をルーティングします。

HTTP ではないその他の TCP トラフィックはすべて、未変更の状態でイングレス コントローラーと devspaces-proxy コンテナーを通過します。

### <a name="how-running-your-code-is-configured"></a>コード実行の構成方法

Azure Dev Spaces では、`azds.yaml` ファイルを使用してサービスをインストールおよび構成します。 コントローラーでは、`azds.yaml` ファイル内の `install` プロパティを使用して、Helm チャートをインストールし、Kubernetes オブジェクトを作成します。

```yaml
...
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
      # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
      # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
      # For more information see https://aka.ms/devspaces/routing
      - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
...
```

既定では、Helm チャートは `prep` コマンドによって生成されます。 また、*install.chart* プロパティは Helm チャートのディレクトリに設定されます。 Helm チャートを別の場所で使用する場合は、その場所を使用するようにこのプロパティを更新できます。

Helm チャートのインストール時には、Helm チャート内の値をオーバーライドする方法が Azure Dev Spaces から提供されます。 Helm チャートの既定値は `charts/APP_NAME/values.yaml` に含まれています。

*install.values* プロパティを使用すると、Helm チャートで置き換える値を定義する 1 つ以上のファイルを表示できます。 たとえば、開発スペースでアプリケーションを実行する場合にホスト名またはデータベース構成が必要な場合は、このオーバーライド機能を使用できます。 また、*?* を ファイル名の末尾に追加して、オプションとして設定することもできます。

*install.set* プロパティを使用すると、Helm チャートで置き換える 1 つ以上の値を構成できます。 *install.set* で構成された値によって、*install.values* に表示されているファイルで構成された値がオーバーライドされます。 *install.set* に属するプロパティは、Helm チャート内の値に依存し、生成される Helm チャートによって異なる場合があります。

上記の例では、*install.set.replicaCount* プロパティは、開発スペースで実行するアプリケーションのインスタンス数をコントローラーに通知します。 シナリオによっては、この値を増やすことができますが、これはアプリケーションのポッドへのデバッガーのアタッチに影響を及ぼします。 詳細については、[トラブルシューティングの記事](troubleshooting.md)を参照してください。

生成される Helm チャートでは、コンテナー イメージが *{{ .Values.image.repository }}:{{ .Values.image.tag }}* に設定されます。 `azds.yaml` ファイルでは、*install.set.image.tag* プロパティが既定で *$(tag)* として定義されます。これは、*{{ .Values.image.tag }}* の値として使用されます。 この方法で *install.set.image.tag* プロパティを設定すると、Azure Dev Spaces の実行時に独自の方法でアプリケーションのコンテナー イメージにタグ付けできます。 この特定のケースでは、イメージに *<value from image.repository>:$(tag)* というタグが付けられます。 Dev Spaces が AKS クラスター内のコンテナーを認識して特定できるようにするには、*$(tag)* 変数を *install.set.image.tag* の値として使用する必要があります。

上記の例では、`azds.yaml` によって *install.set.ingress.hosts* が定義されます。 *install.set.ingress.hosts* プロパティでは、パブリック エンドポイントのホスト名の形式が定義されます。 また、このプロパティでは *$(spacePrefix)*、*$(rootSpacePrefix)*、および *$(hostSuffix)* を使用します。これらはコントローラーから提供される値です。 

*$(spacePrefix)* は子開発スペースの名前であり、*SPACENAME.s* という形式になります。 *$(RootSpacePrefix)* は親スペースの名前です。 たとえば、*azureuser* が *default* の子スペースである場合、*$(rootSpacePrefix)* の値は *default*、*$(spacePrefix)* の値は *azureuser.s* になります。 スペースが子スペースでない場合、*$(spacePrefix)* は空白になります。 たとえば、*default* スペースに親スペースがない場合、*$(rootSpacePrefix)* の値は *default*、*$(spacePrefix)* の値は空白になります。 *$(hostSuffix)* は、AKS クラスターで実行する Azure Dev Spaces イングレス コントローラーを指す DNS サフィックスです。 この DNS サフィックスは、Azure Dev Spaces コントローラーが AKS クラスターに追加されたときに作成されたワイルドカードの DNS エントリ (たとえば、*\*.RANDOM_VALUE.eus.azds.io*) に対応しています。

上記の `azds.yaml` ファイルでは、アプリケーションのホスト名を変更するように *install.set.ingress.hosts* を更新することも可能でした。 たとえば、アプリケーションのホスト名を *$(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)* から *$(spacePrefix)$(rootSpacePrefix)web$(hostSuffix)* に簡略化する場合などです。

アプリケーションのコンテナーをビルドするために、コントローラーでは、`azds.yaml` 構成ファイルの以下のセクションを使用します。

```yaml
build:
  context: .
  dockerfile: Dockerfile
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
...
```

コントローラーでは、Dockerfile を使用してアプリケーションをビルドおよび実行します。

*build.context* プロパティでは、Dockerfile が存在するディレクトリを表示します。 *build.dockerfile* プロパティでは、運用バージョンのアプリケーションをビルドするための Dockerfile の名前を定義します。 *configurations.develop.build.dockerfile* プロパティでは、開発バージョンのアプリケーション用の Dockerfile の名前を構成します。

開発と運用に異なる Dockerfile を使用すると、開発時に特定の項目を有効にする一方で、運用環境ではそれらの項目を無効にすることができます。 たとえば、デバッグや詳細なログを開発時には有効にして、運用環境では無効にすることができます。 また、Dockerfile の名前が異なる場合や、Dockerfile が別の場所にある場合にこれらのプロパティを更新することもできます。

開発時に迅速に反復処理を行えるようにするために、Azure Dev Spaces ではローカル プロジェクトから変更を同期して、アプリケーションを増分更新します。 `azds.yaml` 構成ファイルの以下のセクションを使用して、同期と更新を構成します。

```yaml
...
configurations:
  develop:
    ...
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
...
```

変更を同期するファイルとディレクトリは *configurations.develop.container.sync* プロパティで示されます。 これらのディレクトリは、`up` コマンドの実行時および変更が検出されたときに最初に同期されます。 開発スペースと同期させる追加のディレクトリまたは別のディレクトリがある場合は、このプロパティを変更できます。

*configurations.develop.container.iterate.buildCommands* プロパティでは、開発シナリオでアプリケーションをビルドする方法を指定します。 *configurations.develop.container.command* プロパティでは、開発シナリオでアプリケーションを実行するためのコマンドを提供します。 開発時に使用するビルドまたは実行時の追加のフラグやパラメーターがある場合は、これらのプロパティのどちらかの更新が必要になることがあります。

*configurations.develop.container.iterate.processesToKill* では、アプリケーションを停止するために中止するプロセスを表示します。 開発時にアプリケーションの再起動の動作を変更する場合は、このプロパティの更新が必要になることがあります。 たとえば、アプリケーションのビルドや起動の方法を変更するように *configurations.develop.container.iterate.buildCommands* プロパティまたは *configurations.develop.container.command* プロパティを更新した場合は、停止するプロセスの変更が必要になることがあります。

`azds prep` コマンドを使用してコードを準備する場合は、`--public` フラグを追加することができます。 `--public` フラグを追加すると、アプリケーションのパブリックにアクセス可能な URL が作成されます。 このフラグを省略した場合は、クラスター内から、または localhost トンネルを使用してアプリケーションにアクセスする必要があります。 `azds prep` コマンドの実行後、`charts/APPNAME/values.yaml` で *ingress.enabled* プロパティを変更して、この設定を変更することができます。

```yaml
ingress:
  enabled: true
```

## <a name="debug-your-code"></a>コードをデバッグする

Java、.NET、および Node.js アプリケーションの場合、Visual Studio Code または Visual Studio 2017 を使用して、実行中のアプリケーションを開発スペースで直接デバッグできます。 Visual Studio Code と Visual Studio 2017 には、開発スペースに接続してアプリケーションを起動し、デバッガーをアタッチするためのツールが用意されています。 `azds prep` の実行後、Visual Studio Code または Visual Studio 2017 でプロジェクトを開くことができます。 Visual Studio Code または Visual Studio 2017 では、`azds prep` の実行とは別の接続用の独自の構成ファイルが生成されます。 Visual Studio Code または Visual Studio 2017 内から、ブレークポイントを設定し、開発スペースに対してアプリケーションを起動できます。

![コードのデバッグ](media/get-started-node/debug-configuration-nodejs2.png)

Visual Studio Code または Visual Studio 2017 を使用してデバッグ用にアプリケーションを起動すると、`azds up` の実行と同じ方法で起動および開発スペースへの接続が処理されます。 また、Visual Studio Code および Visual Studio 2017 のクライアント側ツールには、デバッグ用の特定の情報を含む追加のパラメーターが用意されています。 このパラメーターには、デバッガー イメージの名前、デバッガー イメージ内のデバッガーの場所、およびアプリケーションのコンテナー内でのデバッガー フォルダーのマウント先が含まれています。 

デバッガー イメージは、クライアント側ツールによって自動的に指定されます。 `azds prep` の実行時に Dockerfile と Helm チャートの生成に使用したのと同じ方法が使用されます。 アプリケーションのイメージにマウントされたデバッガーは `azds exec` を使用して実行されます。

## <a name="sharing-a-dev-space"></a>開発スペースを共有する

チームで作業している場合は、[チーム全体で開発スペースを共有](how-to/share-dev-spaces.md)したり、派生の開発スペースを作成したりできます。 開発スペースは、その開発スペースのリソース グループへの共同作成者アクセス権を持つユーザーが使用できます。

別の開発スペースから派生した新しい開発スペースを作成することもできます。 派生の開発スペースを作成すると、その開発スペースの名前空間に *azds.io/parent-space=PARENT-SPACE-NAME* ラベルが追加されます。 また、親開発スペースからのすべてのアプリケーションが派生の開発スペースと共有されます。 更新バージョンのアプリケーションを派生の開発スペースにデプロイする場合、そのアプリケーションは派生の開発スペースにのみ存在し、親開発スペースには影響しません。 最大で 3 レベルの派生の開発スペースまたは "*親の親*" であるスペースを使用できます。

また、派生の開発スペースでは、その独自のアプリケーションと親から共有されるアプリケーションの間で要求がインテリジェントにルーティングされます。 ルーティングの際は、派生の開発スペースでアプリケーションに対する要求のルーティングが試行され、親開発スペースから共有アプリケーションにフォールバックされます。 アプリケーションが親スペースにない場合は、親の親であるスペース内の共有アプリケーションにルーティングがフォールバックします。

例: 
* 開発スペース *default* にアプリケーション *serviceA* および *serviceB* があります。
* 開発スペース *azureuser* は *default* から派生したものです。
* *serviceA* の更新バージョンは *azureuser* にデプロイされます。

*azureuser* の使用時には、*serviceA* に対するすべての要求が *azureuser* 内の更新バージョンにルーティングされます。 *serviceB* に対する要求は、最初に *azureuser* バージョンの *serviceB* へのルーティングが試行されます。 これが存在しないため、*default* バージョンの *serviceB* にルーティングされます。 *azureuser* バージョンの *serviceA* が削除されると、*serviceA* に対するすべての要求は *default* バージョンの *serviceA* を使用するようにフォールバックされます。

## <a name="next-steps"></a>次の手順

Azure Dev Spaces の使用を開始するには、以下のクイック スタートを参照してください。

* [Java (CLI と Visual Studio Code を使用)](quickstart-java.md)
* [.NET Core (CLI と Visual Studio Code を使用)](quickstart-netcore.md)
* [.NET Core (Visual Studio 2017 を使用)](quickstart-netcore-visualstudio.md)
* [Node.js (CLI と Visual Studio Code を使用)](quickstart-nodejs.md)

チーム開発を開始するには、以下のハウツー記事を参照してください。

* [チーム開発 - Java (CLI と Visual Studio Code を使用)](team-development-java.md)
* [チーム開発 - .NET Core (CLI と Visual Studio Code を使用)](team-development-netcore.md)
* [チーム開発 - .NET Core (Visual Studio 2017 を使用)](team-development-netcore-visualstudio.md)
* [チーム開発 - Node.js (CLI と Visual Studio Code を使用)](team-development-nodejs.md)
