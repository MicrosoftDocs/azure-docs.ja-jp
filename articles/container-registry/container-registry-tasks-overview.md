---
title: ACR タスクの概要
description: クラウド内でのコンテナー イメージの安全で自動化されたビルド、管理、修正プログラム適用を提供する、Azure Container Registry の機能スイートである ACR タスクの概要。
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: 96997f963f0bcb319d5318e2dd88a6e1e21fb36b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74840767"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>ACR タスクでコンテナー イメージのビルドとメンテナンスを自動化する

コンテナーは、アプリケーションと開発者の依存関係をインフラストラクチャおよび操作の要件から分離する新たなレベルの仮想化を提供します。 ただし、コンテナーのライフサイクルを通してこのアプリケーション仮想化を管理し修正プログラムを適用する方法に対処することはやはり必要です。

## <a name="what-is-acr-tasks"></a>ACR タスクとは

**ACR タスク**は、Azure Container Registry 内の一連の機能です。 Linux、Windows、ARM などの[プラットフォーム](#image-platforms)用のクラウドベースのコンテナー イメージのビルド機能があり、Docker コンテナーの [OS とフレームワークのパッチ適用](#automate-os-and-framework-patching)を自動化できます。 ACR タスクでは、オンデマンドのコンテナー イメージのビルドによって "内部ループ" 型の開発サイクルをクラウドに拡張するだけでなく、ソース コードの更新、コンテナーの基本イメージの更新、またはタイマーによってトリガーされるビルドの自動化を有効にすることもできます。 たとえば、基本イメージの更新のトリガーを使用することで、OS とアプリケーション フレームワークの修正プログラム適用のワークフローを自動化したり、不変のコンテナーの原則に従いながら、セキュリティで保護された環境を維持したりできます。

## <a name="task-scenarios"></a>一般的なシナリオ

ACR タスクでは、コンテナー イメージやその他の成果物をビルドし、保守管理するためのシナリオをいくつかサポートしています。 詳細については、この記事の次のセクションを参照してください。

* **[クイック タスク](#quick-task)** - ローカル Docker エンジンをインストールすることなく、Azure で 1 つのコンテナー イメージをオンデマンドでビルドし、コンテナー レジストリにプッシュします。 クラウドでの `docker build` や `docker push` を思い浮かべてください。
* **自動的にトリガーされたタスク** - イメージをビルドする目的で 1 つまたは複数の*トリガー*を有効にします。
  * **[ソース コードの更新でトリガー](#trigger-task-on-source-code-update)** 
  * **[基本イメージの更新でトリガー](#automate-os-and-framework-patching)** 
  * **[スケジュールに従ってトリガー](#schedule-a-task)** 
* **[マルチ ステップ タスク](#multi-step-tasks)** - 複数のステップから成る複数コンテナー ベースのワークフローを持つ、ACR タスクの単一イメージのビルドおよびプッシュ機能を拡張します。 

各 ACR タスクには、[ソース コード コンテキスト](#context-locations)が関連付けられます - コンテナー イメージまたはその他の成果物のビルドに使用される一連のソース ファイルの場所。 見本のコンテキストには、Git リポジトリまたはローカル ファイルシステムが含まれます。

タスクでは、[Run 変数](container-registry-tasks-reference-yaml.md#run-variables)を活用することもできます。そのため、タスクの定義を再利用したり、イメージや成果物のタグを標準化したりできます。

## <a name="quick-task"></a>クイック タスク

内部ループ型の開発サイクルは、コードの記述、ビルド、およびアプリケーションのテストの反復プロセスであり、実のところ、コンテナーのライフサイクル管理の開始点となります。

ACR タスクの[クイック タスク](container-registry-tutorial-quick-task.md)機能は、コードの 1 行目をコミットする前でさえ、コンテナー イメージのビルドを Azure にオフロードすることで、統合された開発環境を提供できます。 クイック タスクを使用すると、コードをコミットする前に、自動化されたビルド定義を検証し、潜在的な問題を知ることができます。

よく知られている `docker build` 形式を使用して、Azure CLI の [az acr build][az-acr-build] コマンドは[コンテキスト](#context-locations) (ビルド対象の一連のファイル) を取得して ACR タスクに送信し、既定では、完了時にビルド済みのイメージをそのレジストリにプッシュします。

概要については、Azure Container Registry での[コンテナー イメージのビルドと実行](container-registry-quickstart-task-cli.md)のクイック スタートを参照してください。  

ACR タスクは、コンテナー ライフサイクル プリミティブとして設計されています。 たとえば、ACR タスクを CI/CD ソリューションに統合します。 [az login][az-login] を[サービス プリンシパル][az-login-service-principal]で実行することにより、CI/CD ソリューションは [az acr build][az-acr-build] コマンドを発行してイメージ ビルドを開始できます。

クイック タスクを使用する方法については、[Azure Container Registry タスクを使用してクラウドでコンテナー イメージをビルドする](container-registry-tutorial-quick-task.md)ことに関する ACR タスクの最初のチュートリアルを参照してください。

> [!TIP]
> Dockerfile を使用せずにソース コードから直接、イメージをビルドしてプッシュする場合、[az acr pack build][az-acr-pack-build] コマンド (プレビュー) が Azure Container Registry に用意されています。 このツールでは、[Cloud Native Buildpacks](https://buildpacks.io/) を使用し、アプリケーション ソース コードからイメージをビルドし、プッシュします。

## <a name="trigger-task-on-source-code-update"></a>ソース コードの更新でタスクをトリガーする

GitHub または Azure DevOps の公開または非公開 Git リポジトリに対して、コードがコミットされたとき、またはプル要求が作成または更新されたときに、コンテナー イメージのビルドまたはマルチステップタスクをトリガーします。 たとえば、Azure CLI コマンドの [az acr task create][az-acr-task-create] でビルド タスクを構成し、その際、Git リポジトリと任意で分岐と Dockerfile を指定します。 チームがリポジトリのコードを更新すると、ACR タスクで作成された Webhook が、リポジトリで定義されているコンテナー イメージのビルドをトリガーします。 

ACR タスクでは、Git リポジトリをタスクのコンテキストとして設定すると、次のトリガーがサポートされます。

| トリガー | 既定で有効 |
| ------- | ------------------ |
| コミット | はい |
| プル要求 | いいえ |

ソース コード更新トリガーを構成するには、公開または非公開 GitHub または Azure DevOps リポジトリに Webhook を設定するアクセス トークン (PAT) をタスクに与える必要があります。

> [!NOTE]
> 現在、GitHub Enterprise リポジトリにおけるコミットやプル要求トリガーは、Azure Container Registry (ACR) タスクではサポートされていません。

ソース コードのコミット時にビルドをトリガーする方法については、[Azure Container Registry タスクを使用してコンテナー イメージのビルドを自動化する](container-registry-tutorial-build-task.md)ことに関する ACR タスクの 2 つ目のチュートリアルを参照してください。

## <a name="automate-os-and-framework-patching"></a>OS とフレームワークの修正プログラムの適用を自動化する

ACR タスクがコンテナー ビルド ワークフローを真に強化する能力は、基本イメージに対する更新を検出する機能に由来します。 更新された基本イメージがレジストリにプッシュされると、または基本イメージが Docker Hub などのパブリック リポジトリで更新されると、ACR タスクではそれに基づいてすべてのアプリケーション イメージを自動的にビルドできます。

コンテナー イメージは、"*基本*" イメージと "*アプリケーション*" イメージに大きく分類できます。 通常、基本イメージには、アプリケーションのビルドとその他のカスタマイズの基になるオペレーティング システムとアプリケーション フレームワークが含まれます。 通常、これらの基本イメージ自体はパブリック アップストリーム イメージに基づいています。たとえば、[Alpine Linux][base-alpine]、[Windows][base-windows]、[.NET][base-dotnet]、[Node.js][base-node] です。 いくつかのアプリケーション イメージは、共通の基本イメージを共有することがあります。

重要な OS セキュリティ更新プログラムなどによって OS またはアプリ フレームワークのイメージが更新された場合は、基本イメージも更新して重要な修正を組み込む必要があります。 各アプリケーション イメージもリビルドして、基本イメージに組み込まれたこれらのアップストリームの修正プログラムを組み込む必要があります。

ACR タスクはコンテナー イメージをビルドするときに基本イメージの依存関係を動的に検出するため、アプリケーション イメージの基本イメージが更新されるとそれを検出することができます。 事前に構成された 1 つの[ビルド タスク](container-registry-tutorial-base-image-update.md#create-a-task)により、ACR タスクは**すべてのアプリケーション イメージを自動的にリビルド**します。 ACR タスクはこの自動検出とリビルドによって、更新された基本イメージを参照しているすべてのアプリケーション イメージを手動で追跡し、更新するために通常は必要となる時間と労力を削減しています。

Dockerfile からイメージをビルドする場合、ACR タスクでは、基本イメージが次のいずれかの場所にある場合に基本イメージの更新が追跡されます。

* タスクが実行されるのと同じ Azure コンテナー レジストリ
* 同じリージョン内の別の Azure コンテナー レジストリ 
* Docker Hub 内のパブリック リポジトリ
* Microsoft Container Registry 内のパブリック リポジトリ

> [!NOTE]
> * 基本イメージ更新トリガーは、ACR タスクでは、既定で有効になっています。 
> * 現在のところ、ACR タスクでは、アプリケーション (*実行時*) イメージの基本イメージの更新のみが追跡されます。 ACR タスクでは、マルチステージ Dockerfiles で使用される中間 (*ビルド時*) イメージの基本イメージの更新は追跡されません。 

OS とフレームワークの修正プログラムの適用については、[Azure Container Registry タスクを使用して基本イメージ更新時のイメージ ビルドを自動化する](container-registry-tutorial-base-image-update.md)ことに関する ACR タスクの 3 つ目のチュートリアルを参照してください。

## <a name="schedule-a-task"></a>タスクのスケジュール設定

任意で、タスクの作成時または更新時に 1 つまたは複数の "*タイマー トリガー*" を設定し、タスクをスケジュール設定します。 タスクのスケジュール設定は、決まったスケジュールでコンテナー ワークロードを実行する場合やレジストリに定期的にプッシュされるイメージで保守管理操作やテストを実行する場合に役立ちます。 詳細については、「[定義したスケジュールで ACR タスクを実行する](container-registry-tasks-scheduled.md)」を参照してください。

## <a name="multi-step-tasks"></a>複数ステップのタスク

複数ステップのタスクでは、クラウドでのコンテナー イメージのビルド、テスト、および修正プログラムの適用のために、ステップベースでタスクの定義と実行を行うことができます。 [YAML ファイル](container-registry-tasks-reference-yaml.md)に定義されているタスク手順では、コンテナー イメージまたはその他の成果物に対する個別のビルド/プッシュ操作が指定されます。 各ステップで実行環境としてコンテナーを使用するように、1 つまたは複数のコンテナーの実行を定義することもできます。

たとえば、以下を自動化するマルチ ステップ タスクを作成できます。

1. Web アプリケーション イメージをビルドする
1. Web アプリケーション コンテナーを実行する
1. Web アプリケーションのテスト イメージをビルドする
1. 実行中のアプリケーション コンテナーに対するテストを実行する Web アプリケーション テスト コンテナーを実行する
1. テストに成功した場合は Helm グラフのアーカイブ パッケージをビルドする
1. 新しい Helm グラフのアーカイブ パッケージを使用して `helm upgrade` を実行する

マルチ ステップ タスクを使用すると、イメージのビルド、実行、およびテストを、ステップ間の依存関係がサポートされる、よりコンポーザブルなステップに分割できます。 ACR タスクでマルチ ステップ タスクを使用すると、イメージのビルド、テスト、OS やフレームワークへの修正プログラム適用のワークフローをより細かく制御できます。

マルチ ステップ タスクについては、[ACR タスクでビルド、テスト、および修正プログラムの適用を行うマルチ ステップ タスクを実行する](container-registry-tasks-multi-step.md)ことに関するページを参照してください。

## <a name="context-locations"></a>コンテキストの場所

次の表は、ACR タスクでサポートされているコンテキストの場所の例を示しています。

| コンテキストの場所 | 説明 | 例 |
| ---------------- | ----------- | ------- |
| ローカル ファイルシステム | ローカル ファイル システム上のディレクトリ内のファイル。 | `/home/user/projects/myapp` |
| GitHub master ブランチ | 公開または非公開 GitHub リポジトリのマスター（または他のデフォルト）ブランチ内のファイル。  | `https://github.com/gituser/myapp-repo.git` |
| GitHub ブランチ | 公開または非公開 GitHub リポジトリの特定のブランチ。| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub のサブフォルダー | 公開または非公開 GitHub リポジトリのサブフォルダー内のファイル。 例では、ブランチとサブフォルダーの指定の組み合わせが示されています。 | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Azure DevOps サブフォルダー | 公開または非公開 Azure リポジトリのサブフォルダー内のファイル。 例では、ブランチとサブフォルダーの指定の組み合わせが示されています。 | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| リモート tarball | リモート Web サーバー上の圧縮されたアーカイブ内のファイル。 | `http://remoteserver/myapp.tar.gz` |

> [!NOTE]
> 非公開 Git リポジトリをタスクのコンテキストとして使用する場合、非公開アクセストークン（PAT）を提供する必要があります。

## <a name="image-platforms"></a>イメージのプラットフォーム

既定では、ACR タスクによって Linux OS と amd64 アーキテクチャ用のイメージが構築されます。 他のアーキテクチャ用の Windows イメージまたは Linux イメージを構築するための `--platform` タグを指定します。 OS と、必要に応じて OS/アーキテクチャ形式 (`--platform Linux/arm` など) でサポートされているアーキテクチャを指定します。 ARM アーキテクチャの場合は、必要に応じて、次のように OS/アーキテクチャ/バリアント形式でバリアントを指定します (例: `--platform Linux/arm64/v8`)。

| OS | アーキテクチャ|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-logs"></a>タスク ログを表示する

それぞれのタスク実行で、タスク ステップが正常に実行されたかどうかを判別するために検査できるログ出力が生成されます。 [az acr build](/cli/azure/acr#az-acr-build)、[az acr run](/cli/azure/acr#az-acr-run)、または [az acr task run](/cli/azure/acr/task#az-acr-task-run) コマンドを実行してタスクをトリガーすると、そのタスク実行のログ出力がコンソールにストリーミングされ、さらに、後で取得するために格納されます。 ソース コードのコミットや基本イメージの更新などによってタスクが自動的にトリガーされる場合、タスク ログは格納されるだけです。 タスク実行のログは Azure portal で表示するか、[az acr task logs](/cli/azure/acr/task#az-acr-task-logs) コマンドを使用します。

既定では、レジストリ内のタスク実行のデータとログは、30 日間保持された後、自動的に消去されます。 タスク実行のデータをアーカイブする場合は、[az acr task update-run](/cli/azure/acr/task#az-acr-task-update-run) コマンドを使用してアーカイブを有効にしてください。 次の例は、レジストリ *myregistry* 内のタスク実行 *cf11* のアーカイブを有効にします。

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>次の手順

クラウドでコンテナー イメージのビルドと保守管理を自動化する準備ができたら、[ACR タスクのチュートリアル シリーズ](container-registry-tutorial-quick-task.md)をご覧ください。

Azure コンテナー レジストリを操作するには、必要に応じて [Visual Studio Code 用の Docker 拡張機能](https://code.visualstudio.com/docs/azure/docker)と [Azure アカウント](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)拡張機能をインストールします。 Azure コンテナー レジストリとの間でイメージをプルおよびプッシュしたり、ACR タスクを実行したりします。すべて Visual Studio Code 内で実行します。

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
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
