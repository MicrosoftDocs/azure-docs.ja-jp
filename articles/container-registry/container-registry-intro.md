---
title: Azure のプライベート Docker コンテナー レジストリ - 概要
description: クラウド ベースの管理されたプライベート Docker レジストリを提供する Azure Container Registry サービスの紹介です。
services: container-registry
author: stevelas
ms.service: container-registry
ms.topic: overview
ms.date: 04/03/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: ba75d196bdb53fab104ab6c01391e762b4a3841b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270525"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Azure のプライベート Docker コンテナー レジストリの概要

Azure コンテナー レジストリは、オープンソースの Docker Registry 2.0 に基づいた、管理された [Docker レジストリ](https://docs.docker.com/registry/) サービスです。 プライベート [Docker コンテナー](https://www.docker.com/what-docker) イメージを保存および管理する Azure コンテナー レジストリを作成および管理します。

既存のコンテナーの開発とデプロイのパイプラインと共に Azure のコンテナー レジストリを使用するか、[ACR タスク](#azure-container-registry-tasks)を使用して Azure にコンテナー イメージをビルドします。 オンデマンドでビルドしたり､ソースコード コミットやベース イメージ アップデート ビルド トリガーを使用してビルドを完全自動化したりできます｡

Docker およびコンテナーに関する背景情報は､[Docker概要](https://docs.docker.com/engine/docker-overview/)を参照してください｡

## <a name="use-cases"></a>ユース ケース

Azure コンテナー レジストリからさまざまなデプロイ ターゲットにイメージをプルできます。

* [Kubernetes](https://kubernetes.io/docs/)、[DC/OS](https://docs.mesosphere.com/)、[Docker Swarm](https://docs.docker.com/swarm/) など、ホストから成るクラスターにわたってコンテナー化されたアプリケーションを管理する**スケーラブルなオーケストレーション システム**。
* [Azure Kubernetes Service (AKS)](../aks/index.yml) や  [App Service](../app-service/index.yml)､[Batch](../batch/index.yml)､[Service Fabric](/azure/service-fabric/) など､大規模なアプリケーション のビルドと実行をサポートしている **Azure サービス**｡

開発者は、コンテナー開発ワークフローの一環としてコンテナー レジストリにプッシュすることもできます。 たとえば、[Azure DevOps Services](https://docs.microsoft.com/azure/devops/) や [Jenkins](https://jenkins.io/) などの継続的インテグレーションとデプロイのツールからコンテナー レジストリを対象にします。

ベース イメージが更新されたときに自動的にアプリケーション イメージをリビルドするように ACR タスクを構成するか、チームがコードを Git リポジトリにコミットしたときのイメージ ビルドを自動化します。 複数のコンテナー イメージのビルド、テスト、および修正プログラム適用をクラウドで並列で自動的に行うために、マルチステップ タスクを作成します。

Azure には、Azure コンテナー レジストリを管理するために、Azure コマンド ライン インターフェイス、Azure portal、API サポートなどのツールが用意されています。 Azure コンテナー レジストリを操作するには、必要に応じて [Visual Studio Code 用の Docker 拡張機能](https://code.visualstudio.com/docs/azure/docker)と [Azure アカウント](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)拡張機能をインストールします。 Azure コンテナー レジストリとの間でイメージをプルおよびプッシュしたり、ACR タスクを実行したりします。すべて Visual Studio Code 内で実行します。

## <a name="key-concepts"></a>主要な概念

* **レジストリ** - Azure サブスクリプションに 1 つ以上のコンテナー レジストリを作成できます。 レジストリには、[Basic、Standard、Premium](container-registry-skus.md) の 3 つの SKU があり、それぞれ Webhook 統合、Azure Active Directory によるレジストリ認証、および削除機能がサポートされます。 デプロイと同じ Azure の場所にレジストリを作成することで、ネットワーク上の近い場所にローカルで保存されたコンテナー イメージを活用します。 高度なレプリケーションとコンテナー イメージの配布に対応するには、Premium レジストリの [geo レプリケーション](container-registry-geo-replication.md)機能を使用してください。 レジストリの完全修飾名は `myregistry.azurecr.io` という形式になります。

  コンテナー レジストリへの[アクセスを制御](container-registry-authentication.md)するには、Azure ID、Azure Active Directory でサポートされている[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md)、または提供された管理者アカウントを使用します。 Azure CLI または標準の `docker login` コマンドを使用して、レジストリにログインします。

* **リポジトリ** - レジストリには、1 つ以上のリポジトリが含まれています。リポジトリはコンテナー イメージの仮想グループであり、名前は同じですが、タグやダイジェストは異なります。 Azure Container Registry では、複数レベルのリポジトリ名前空間をサポートしています。 複数レベルの名前空間により、特定のアプリまたはアプリのコレクションに関連するイメージのコレクションを特定の開発チームや運用チーム別にグループ化できます。 例: 

  * `myregistry.azurecr.io/aspnetcore:1.0.1`  は、会社全体のイメージを表します
  * `myregistry.azurecr.io/warrantydept/dotnet-build`  は、保証部門全体で共有される、.NET アプリを構築するために使用するイメージを表します
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web`  は、保証部門が所有する、customer submissions アプリでグループ化された Web イメージを表します

* **イメージ** - リポジトリに格納されます。各イメージは、Docker 互換コンテナーの読み取り専用のスナップショットです。 Azure コンテナー レジストリには、Windows と Linux の両方のイメージを含めることができます。 すべてのコンテナーのデプロイのイメージ名を制御できます。 イメージをリポジトリにプッシュしたり、イメージをリポジトリからプルしたりするには、標準の [Docker コマンド](https://docs.docker.com/engine/reference/commandline/)を使用します。 コンテナー イメージの他に、Azure Container Registry は、Kubernetes にアプリケーションをデプロイするために使用される [Helm チャート](container-registry-helm-repos.md)などの[関連コンテンツの形式](container-registry-image-formats.md)を格納します。

* **コンテナー** - コンテナーでは、ソフトウェア アプリケーションと、コード、ランタイム、システム ツール、ライブラリを含む完全なファイル システム内にラップされた依存関係を定義します。 コンテナー レジストリからプルした Windows または Linux イメージに基づいて Docker コンテナーを実行します。 1 台のコンピューターで実行されているすべてのコンテナーでは、オペレーティング システムのカーネルを共有します。 Docker コンテナーは、主要なすべての Linux ディストリビューション、macOS、および Windows に完全に移植できます。

## <a name="azure-container-registry-tasks"></a>Azure Container Registry タスク

[Azure Container Registry タスク](container-registry-tasks-overview.md) (ACR タスク) は､Azure における Docker コンテナーのイメージ ビルドを簡素化し、効率的に行えるようにする、Azure Container Registry 内の一連の機能です。 ACR タスクを利用して､`docker build` オペレーションを Azure にオフロードすることで、開発の社内ループをクラウドへと拡張します。 ビルド タスクの設定では､コンテナーの OS およびフレームワーク パッチ適用 パイプラインを自動化し､ソース コントロールにコードがコミットされたときに自動的にイメージがビルドされるよう指定できます｡

[マルチステップ タスク](container-registry-tasks-overview.md#multi-step-tasks)では、クラウドでのコンテナー イメージのビルド、テスト、および修正プログラムの適用のために、ステップベースでタスクの定義と実行を行うことができます。 タスクのステップでは、コンテナー イメージのビルド操作とプッシュ操作を個々に定義します。 各ステップで実行環境としてコンテナーを使用するように、1 つまたは複数のコンテナーの実行を定義することもできます。

## <a name="next-steps"></a>次の手順

* [Azure Portal を使用したコンテナー レジストリの作成](container-registry-get-started-portal.md)
* [Azure CLI を使用したコンテナー レジストリの作成](container-registry-get-started-azure-cli.md)
* [ACR タスクを使用して OS とフレームワークの修正プログラムの適用を自動化する](container-registry-tasks-overview.md)
