---
title: Azure Dev Spaces に使用するクラスターのセットアップの実際
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Azure Dev Spaces に使用する Azure Kubernetes Service クラスターのセットアップの実際について説明します。
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
ms.openlocfilehash: 841e67b96e95aa251fa5bf1ef469b68de30f54d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91972681"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>Azure Dev Spaces に使用するクラスターのセットアップの実際

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces には、Kubernetes アプリケーションを迅速に反復処理してデバッグし、Azure Kubernetes Service (AKS) クラスターでチームと共同作業を行うための複数の方法が用意されています。 1 つの方法は、[サービスを直接クラスター上で実行][how-it-works-up]し、[追加のネットワーク機能とルーティング機能][how-it-works-routing]を使用できるよう、ご利用の AKS クラスター上で Azure Dev Spaces を有効にすることです。 この記事では、クラスターの準備と Azure Dev Spaces の有効化について説明します。

## <a name="prepare-your-aks-cluster"></a>AKS クラスターを準備する

Dev Spaces に使用する AKS クラスターを準備するには、AKS クラスターが [Azure Dev Spaces でサポート][supported-regions]されるリージョンに存在すること、また Kubernetes 1.10.3 以降が実行されていることを確認します。 Azure CLI から `az aks use-dev-spaces` を実行して、Azure Dev Spaces を有効にできます。

AKS クラスターで Azure Dev Spaces を有効にすると、クラスター用のコントローラーがインストールされます。 コントローラーは、AKS クラスターの外部に存在します。 これは、クライアント側ツールと AKS クラスター間の動作と通信を管理します。 有効にした後は、クライアント側ツールを使用して、コントローラーを操作できます。

コントローラーでは次のアクションが実行されます。

* 開発スペースの作成と選択を管理する。
* アプリケーションの Helm チャートをインストールし、Kubernetes オブジェクトを作成する。
* アプリケーションのコンテナー イメージをビルドする。
* アプリケーションを AKS にデプロイする。
* インクリメンタル ビルドを行い、ソース コードが変更されたら再起動する。
* ログと HTTP トレースを管理する。
* stdout と stderr をクライアント側ツールに転送する。
* スペース内および親スペースと子スペースにまたがるアプリケーションのルーティングを構成する。

このコントローラーは、クラスターの外部にある個別の Azure リソースであり、クラスター内のリソースに対して次のことを行います。

* 開発スペースとして使用する Kubernetes 名前空間を作成または指定する。
* *azds* という名前の Kubernetes 名前空間が存在する場合は、それを削除し、新しい名前空間を作成する。
* Kubernetes webhook の構成をデプロイします。
* webhook 受付サーバーをデプロイします。

AKS クラスターが他の Azure Dev Spaces コンポーネントにサービスの呼び出しを行うために使用するものと同じサービス プリンシパルが使用します。

![Azure Dev Spaces がクラスターを準備する](media/how-dev-spaces-works/prepare-cluster.svg)

Azure Dev Spaces を使用するには、開発スペースが少なくとも 1 つ必要です。 Azure Dev Spaces では、AKS クラスター内の Kubernetes 名前空間を開発スペースとして使用します。 コントローラーのインストール時には、最初の開発スペースとして使用する新しい Kubernetes 名前空間の作成または既存の名前空間の選択を求められます。 既定では、*default* という既存の Kubernetes 名前空間が、コントローラーによって最初の開発スペースにアップグレードされます。

名前空間が開発スペースとして指定されると、コントローラーでは *azds.io/space=true* ラベルをその名前空間に追加し、開発スペースとして認定します。 作成または指定する最初の開発スペースは、クラスターの準備が完了した後に既定で選択されます。 選択されたスペースは、Azure Dev Spaces で新しいワークロードを作成するために使用されます。

クライアント側ツールを使用すると、新しい開発スペースを作成して既存の開発スペースを削除できます。 Kubernetes における制限があるため、*default* 開発スペースを削除することはできません。 また、コントローラーでは、*azds* という名前の既存の Kubernetes 名前空間を削除して、クライアント側ツールで使用される `azds` コマンドとの競合を回避します。

Kubernetes webhook 受付サーバーは、インストルメンテーション用のデプロイ時に 3 つのコンテナー (devspaces-proxy コンテナー、devspaces-proxy-init コンテナー、devspaces-build コンテナー) をポッドに挿入するために使用します。 **これらの 3 つのコンテナーはすべて、AKS クラスターでルート アクセスを使用して実行されます。** また、AKS クラスターが他の Azure Dev Spaces コンポーネントにサービスの呼び出しを行うために使用するものと同じサービス プリンシパルも使用します。

![Azure Dev Spaces Kubernetes webhook 受付サーバー](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

devspaces-proxy コンテナーは、アプリケーション コンテナーに出入りするすべての TCP トラフィックを処理し、ルーティングの実行に役立つサイドカー コンテナーです。 devspaces-proxy コンテナーでは、特定のスペースが使用されている場合に HTTP メッセージを再ルーティングします。 たとえば、親スペースと子スペースのアプリケーション間で HTTP メッセージをルーティングできます。 HTTP 以外のすべてのトラフィックは、未変更の状態で devspaces-proxy を通過します。 また、devspaces-proxy container コンテナーでは、受信と送信の HTTP メッセージをすべてログに記録し、クライアント側ツールにそれらをトレースとして送信します。 これらのトレースは、アプリケーションの動作を調べるために開発者が表示できます。

devspaces-proxy-init コンテナーは、アプリケーションのコンテナーに対するスペースの階層に基づいてルーティング規則を追加する[初期化コンテナー](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)です。 起動の前にアプリケーションのコンテナーの */etc/resolv.conf* ファイルと iptables 構成を更新することでルーティング規則が追加されます。 */etc/resolv.conf* に対する更新によって、親スペースにおけるサービスの DNS 解決が可能になります。 iptables 構成の更新によって、アプリケーションのコンテナーに出入りするすべての TCP トラフィックが devspaces-proxy を経由するようになります。 devspaces-proxy-init からのすべての更新は、Kubernetes で追加される規則に加えて発生します。

devspaces-build コンテナーは初期化コンテナーであり、プロジェクトのソース コードと Docker ソケットがマウントされています。 プロジェクトのソース コードと Docker へのアクセスによって、アプリケーション コンテナーをポッドから直接ビルドできます。

> [!NOTE]
> Azure Dev Spaces では、同じノードを使用して、アプリケーションのコンテナーをビルドおよび実行します。 そのため、Azure Dev Spaces には、アプリケーションをビルドして実行するための外部のコンテナー レジストリが必要ありません。

Kubernetes webhook 受付サーバーは AKS クラスターで作成された新しいポッドをリッスンします。 *azds.io/space=true* ラベル付きの名前空間にそのポッドがデプロイされる場合は、コンテナーを追加してポッドが挿入されます。 devspaces-build コンテナーが挿入されるのは、アプリケーションのコンテナーがクライアント側ツールを使用して実行される場合のみです。

AKS クラスターの準備が完了したら、クライアント側ツールを使用して、開発スペースでコードを準備および実行できます。

## <a name="client-side-tooling"></a>クライアント側ツール

ユーザーはクライアント側ツールを使用して次の操作を行うことができます。
* アプリケーション用の Dockerfile、Helm チャート、および Azure Dev Spaces 構成ファイルを生成する。
* 親開発スペースと子開発スペースを作成する。
* アプリケーションをビルドして起動するようコントローラーに通知する。

クライアント側ツールでは、アプリケーションの実行中に次の操作を行うこともできます。
* AKS で実行されているアプリケーションから stdout と stderr を受信して表示する。
* [port-forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) を使用して、http:\//localhost によるアプリケーションへの Web アクセスを許可する。
* AKS で実行中のアプリケーションにデバッガーをアタッチする。
* インクリメンタル ビルドに対する変更が検出された場合に、ソース コードを開発スペースと同期して、迅速なイテレーションを可能にする。
* 開発用マシンを直接 AKS クラスターに接続できるようにする。

コマンド ラインから `azds` コマンドの一部としてクライアント側ツールを使用することができます。 クライアント側ツールを以下のものと共に使用することもできます。

* [Azure Dev Spaces 拡張機能](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)を使用する Visual Studio Code。
* Visual Studio と Azure 開発ワークロード。

## <a name="next-steps"></a>次のステップ

クライアント側ツールを使用してコードを準備し、開発スペースで実行する方法について詳しくは、「[Azure Dev Spaces のためにプロジェクトを準備する方法][how-it-works-prep]」を参照してください。


[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service