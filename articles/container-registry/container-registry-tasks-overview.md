---
title: Azure Container Registry タスク (ACR) でコンテナー イメージのビルドとパッチ適用を自動化する
description: クラウド内でのコンテナー イメージの安全で自動化されたビルド、管理、修正プログラム適用を提供する、Azure Container Registry の機能スイートである ACR タスクの概要。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 5089650996693b81e548bba8d89c0de29a8afd93
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147979"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>ACR タスクでコンテナー イメージのビルドとメンテナンスを自動化する

コンテナーは、アプリケーションと開発者の依存関係をインフラストラクチャおよび操作の要件から分離する新たなレベルの仮想化を提供します。 ただし、コンテナーのライフサイクルを通してこのアプリケーション仮想化を管理し修正プログラムを適用する方法に対処することはやはり必要です。

## <a name="what-is-acr-tasks"></a>ACR タスクとは

**ACR タスク**は、Azure Container Registry 内の一連の機能です。 Linux、Windows、および ARM 用のクラウドベースのコンテナー イメージのビルド機能があり、Docker コンテナーの [OS およびフレームワークのパッチ適用](#automate-os-and-framework-patching)を自動化できます。 ACR タスクでは、オンデマンドのコンテナー イメージのビルドによって "内部ループ" 型の開発サイクルをクラウドに拡張するだけでなく、ソース コードのコミット時またはコンテナーの基本イメージが更新されたときのビルドの自動化を実現できます。 基本イメージの更新のトリガーを使用して、OS とアプリケーション フレームワークの修正プログラム適用のワークフローを自動化することができ、不変のコンテナーの原則に従いながら、セキュリティで保護された環境を維持できます。

以下の 4 つの方法で ACR タスクを使用し、コンテナー イメージのビルドとテストを行います。

* [クイック タスク](#quick-task):ローカル Docker エンジンをインストールすることなく、Azure でコンテナー イメージをオンデマンドでビルドし、プッシュします。 クラウドでの `docker build` や `docker push` を思い浮かべてください。 ローカルのソース コードまたは Git リポジトリからビルドします。
* [ソース コードのコミット時のビルド](#automatic-build-on-source-code-commit):コードが Git リポジトリにコミットされたときにコンテナー イメージのビルドを自動的にトリガーします。
* [基本イメージ更新時のビルド](#automate-os-and-framework-patching):基本イメージが更新されたときにコンテナー イメージのビルドをトリガーします。
* [複数ステップのタスク](#multi-step-tasks): コマンドとしてイメージをビルドしてコンテナーを実行し、イメージをレジストリにプッシュするマルチ ステップ タスクを定義します。 ACR タスクのこの機能は、オンデマンドでのタスクの実行、並列でのイメージのビルド、テスト、およびプッシュの操作をサポートしています。

## <a name="quick-task"></a>クイック タスク

内部ループ型の開発サイクルは、コードの記述、ビルド、およびアプリケーションのテストの反復プロセスであり、実のところ、コンテナーのライフサイクル管理の開始点となります。

ACR タスクの[クイック タスク](container-registry-tutorial-quick-task.md)機能は、コードの 1 行目をコミットする前でさえ、コンテナー イメージのビルドを Azure にオフロードすることで、統合された開発環境を提供できます。 クイック タスクを使用すると、コードをコミットする前に、自動化されたビルド定義を検証し、潜在的な問題を知ることができます。

よく知られている `docker build` 形式を使用して、Azure CLI の [az acr build][az-acr-build] コマンドは*コンテキスト* (ビルド対象の一連のファイル) を取得して ACR タスクに送信し、既定では、完了時にビルド済みのイメージをそのレジストリにプッシュします。

概要については、Azure Container Registry での[コンテナー イメージのビルドと実行](container-registry-quickstart-task-cli.md)のクイック スタートを参照してください。  

次の表は、ACR タスクでサポートされているコンテキストの場所の例を示しています。

| コンテキストの場所 | 説明 | 例 |
| ---------------- | ----------- | ------- |
| ローカル ファイルシステム | ローカル ファイル システム上のディレクトリ内のファイル。 | `/home/user/projects/myapp` |
| GitHub master ブランチ | GitHub リポジトリの master (またはその他の既定の) ブランチ内のファイル。  | `https://github.com/gituser/myapp-repo.git` |
| GitHub ブランチ | GitHub リポジトリの特定のブランチ。| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub のサブフォルダー | GitHub リポジトリのサブフォルダー内のファイル 例では、ブランチとサブフォルダーの指定の組み合わせが示されています。 | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| リモート tarball | リモート Web サーバー上の圧縮されたアーカイブ内のファイル。 | `http://remoteserver/myapp.tar.gz` |

ACR タスクは、コンテナー ライフサイクル プリミティブとして設計されています。 たとえば、ACR タスクを CI/CD ソリューションに統合します。 [az login][az-login] を[サービス プリンシパル][az-login-service-principal]で実行することにより、CI/CD ソリューションは [az acr build][az-acr-build] コマンドを発行してイメージ ビルドを開始できます。

クイック タスクを使用する方法については、[Azure Container Registry タスクを使用してクラウドでコンテナー イメージをビルドする](container-registry-tutorial-quick-task.md)ことに関する ACR タスクの最初のチュートリアルを参照してください。

## <a name="automatic-build-on-source-code-commit"></a>ソース コードのコミット時の自動ビルド

ACR タスクを使用して、コードが Git リポジトリにコミットされたときにコンテナー イメージのビルドを自動的にトリガーします。 Azure CLI コマンド [az acr task][az-acr-task] を使用して構成できるビルド タスクにより、Git リポジトリと、必要に応じて分岐および Dockerfile を指定できます。 チームがコードをリポジトリにコミットすると、ACR タスクで作成された webhook が、リポジトリで定義されているコンテナー イメージのビルドをトリガーします。

> [!IMPORTANT]
> 以前、プレビュー期間中に `az acr build-task` コマンドを使用してタスクを作成した場合、それらのタスクは [az acr task][az-acr-task] コマンドを使用して再作成する必要があります。

ソース コードのコミット時にビルドをトリガーする方法については、[Azure Container Registry タスクを使用してコンテナー イメージのビルドを自動化する](container-registry-tutorial-build-task.md)ことに関する ACR タスクの 2 つ目のチュートリアルを参照してください。

## <a name="automate-os-and-framework-patching"></a>OS とフレームワークの修正プログラムの適用を自動化する

ACR タスクがコンテナー ビルド ワークフローを真に強化する能力は、基本イメージに対する更新を検出する機能に由来します。 更新された基本イメージがレジストリにプッシュされると、または基本イメージが Docker Hub などのパブリック リポジトリで更新されると、ACR タスクではそれに基づいてすべてのアプリケーション イメージを自動的にビルドできます。

コンテナー イメージは、"*基本*" イメージと "*アプリケーション*" イメージに大きく分類できます。 通常、基本イメージには、アプリケーションのビルドとその他のカスタマイズの基になるオペレーティング システムとアプリケーション フレームワークが含まれます。 通常、これらの基本イメージ自体はパブリック アップストリーム イメージに基づいています。たとえば、[Alpine Linux][base-alpine]、[Windows][base-windows]、[.NET][base-dotnet]、[Node.js][base-node] です。 いくつかのアプリケーション イメージは、共通の基本イメージを共有することがあります。

重要な OS セキュリティ更新プログラムなどによって OS またはアプリ フレームワークのイメージが更新された場合は、基本イメージも更新して重要な修正を組み込む必要があります。 各アプリケーション イメージもリビルドして、基本イメージに組み込まれたこれらのアップストリームの修正プログラムを組み込む必要があります。

ACR タスクはコンテナー イメージをビルドするときに基本イメージの依存関係を動的に検出するため、アプリケーション イメージの基本イメージが更新されるとそれを検出することができます。 事前に構成された 1 つの[ビルド タスク](container-registry-tutorial-base-image-update.md#create-a-task)により、ACR タスクは**すべてのアプリケーション イメージを自動的にリビルド**します。 ACR タスクはこの自動検出とリビルドによって、更新された基本イメージを参照しているすべてのアプリケーション イメージを手動で追跡し、更新するために通常は必要となる時間と労力を削減しています。

OS とフレームワークの修正プログラムの適用については、[Azure Container Registry タスクを使用して基本イメージ更新時のイメージ ビルドを自動化する](container-registry-tutorial-base-image-update.md)ことに関する ACR タスクの 3 つ目のチュートリアルを参照してください。

> [!NOTE]
> 現在、基本イメージの更新では、基本イメージとアプリケーション イメージの両方が同じ Azure コンテナー レジストリにある場合、または基本イメージがパブリック環境の Docker Hub リポジトリまたは Microsoft Container Registry リポジトリにある場合にのみ、ビルドがトリガーされます。

## <a name="multi-step-tasks"></a>複数ステップのタスク

複数ステップのタスクでは、クラウドでのコンテナー イメージのビルド、テスト、および修正プログラムの適用のために、ステップベースでタスクの定義と実行を行うことができます。 タスクのステップでは、コンテナー イメージのビルド操作とプッシュ操作を個々に定義します。 各ステップで実行環境としてコンテナーを使用するように、1 つまたは複数のコンテナーの実行を定義することもできます。

たとえば、以下を自動化するマルチ ステップ タスクを作成できます。

1. Web アプリケーション イメージをビルドする
1. Web アプリケーション コンテナーを実行する
1. Web アプリケーションのテスト イメージをビルドする
1. 実行中のアプリケーション コンテナーに対するテストを実行する Web アプリケーション テスト コンテナーを実行する
1. テストに成功した場合は Helm グラフのアーカイブ パッケージをビルドする
1. 新しい Helm グラフのアーカイブ パッケージを使用して `helm upgrade` を実行する

マルチ ステップ タスクを使用すると、イメージのビルド、実行、およびテストを、ステップ間の依存関係がサポートされる、よりコンポーザブルなステップに分割できます。 ACR タスクでマルチ ステップ タスクを使用すると、イメージのビルド、テスト、OS やフレームワークへの修正プログラム適用のワークフローをより細かく制御できます。

マルチ ステップ タスクについては、[ACR タスクでビルド、テスト、および修正プログラムの適用を行うマルチ ステップ タスクを実行する](container-registry-tasks-multi-step.md)ことに関するページを参照してください。

## <a name="view-task-logs"></a>タスク ログを表示する

それぞれのタスク実行で、タスク ステップが正常に実行されたかどうかを判別するために検査できるログ出力が生成されます。 [az acr build](/cli/azure/acr#az-acr-build)、[az acr run](/cli/azure/acr#az-acr-run)、または [az acr task run](/cli/azure/acr/task#az-acr-task-run) コマンドを実行してタスクをトリガーすると、そのタスク実行のログ出力がコンソールにストリーミングされ、さらに、後で取得するために格納されます。 タスク実行のログは Azure portal で表示するか、[az acr task logs](/cli/azure/acr/task#az-acr-task-logs) コマンドを使用します。

2019 の 7 月以降レジストリ内のタスク実行のデータとログは、既定で 30 日間保持された後、自動的に消去されます。 タスク実行のデータをアーカイブする場合は、[az acr task update-run](/cli/azure/acr/task#az-acr-task-update-run) コマンドを使用してアーカイブを有効にしてください。 次の例は、レジストリ *myregistry* 内のタスク実行 *cf11* のアーカイブを有効にします。

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>次の手順

クラウドでコンテナー イメージをビルドすることで OS とフレームワークの修正プログラム適用を自動化する準備ができたら、[ACR タスクに関する 3 部構成のチュートリアル シリーズ](container-registry-tutorial-quick-task.md)を参照してください。

Azure コンテナー レジストリを使用するには、必要に応じて [Visual Studio Code 用の Docker 拡張機能](https://code.visualstudio.com/docs/azure/docker)と [Azure アカウント](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)拡張機能をインストールします。 Azure コンテナー レジストリとの間でイメージをプルおよびプッシュしたり、ACR タスクを実行したりします。すべて Visual Studio Code 内で実行します。

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task]: /cli/azure/acr
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
