---
title: 基本イメージの更新 - タスク
description: アプリケーション コンテナー イメージの基本イメージと、基本イメージの更新で Azure Container Registry タスクをトリガーする方法について説明します。
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: df33096830cd7b34a288c38c105aff3610315337
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97707488"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>ACR タスクの基本イメージの更新について

この記事では、アプリケーションの基本イメージの更新に関する背景情報を提供し、その更新によって Azure Container Registry タスクをトリガーする方法について説明します。

## <a name="what-are-base-images"></a>基本イメージとは

ほとんどのコンテナー イメージを定義する Docker ファイルは、イメージの基になる親イメージを指定するものであり、しばしば "*基本イメージ*" と呼ばれています。 通常、基本イメージにはオペレーティング システム ([Alpine Linux][base-alpine] や [Windows Nano Server][base-windows] など) が含まれます。ここでは、残りのコンテナーのレイヤーが適用されます。 [Node.js][base-node] や [.NET Core][base-dotnet] などのアプリケーション フレームワークが含まれる場合もあります。 通常、これらの基本イメージ自体はパブリック アップストリーム イメージに基づいています。 いくつかのアプリケーション イメージは、共通の基本イメージを共有することがあります。

基本イメージは多くの場合、OS またはイメージのフレームワークに新機能や拡張機能を含むように、イメージのメンテナンス ツールによって更新されます。 基本イメージの更新の理由として、セキュリティ パッチも挙げられます。 このようなアップストリームが更新されるとき、重要な修正プログラムが含まれるよう、基本イメージも更新する必要があります。 各アプリケーション イメージもリビルドして、基本イメージに組み込まれたこれらのアップストリームの修正プログラムを組み込む必要があります。

プライベートの開発チームなどの場合、基本イメージで複数の OS やフレームワークが指定されることがあります。 基本イメージはたとえば、追跡する必要がある共有サービス コンポーネント イメージの場合があります。 チームのメンバーは、テスト目的でこの基本イメージを追跡しなければならないことがあります。または、アプリケーション イメージの開発時、定期的にイメージを更新しなければならないことがあります。

## <a name="maintain-copies-of-base-images"></a>基本イメージのコピーを保持する

Docker Hub などのパブリック レジストリに保持されている基本コンテンツに依存するレジストリ内のコンテンツについては、Azure Container Registry または別のプライベート レジストリにコンテンツをコピーすることをお勧めします。 その後、プライベート基本イメージを参照して、アプリケーション イメージをビルドします。 Azure Container Registry には、パブリック レジストリまたは他の Azure Container Registry からコンテンツを簡単にコピーするための[イメージのインポート](container-registry-import-images.md)機能が用意されています。 次のセクションでは、アプリケーションの更新プログラムの作成時に、Azure Container Registry タスクを使用して基本イメージの更新を追跡する方法について説明します。 基本イメージの更新は、独自の Azure Container Registry で追跡でき、必要に応じて上流のパブリック レジストリで追跡できます。

## <a name="track-base-image-updates"></a>基本イメージの更新を追跡する

ACR Tasks には、コンテナーの基本イメージが更新されたときにイメージを自動的にビルドする機能が備わっています。 この機能を使用して、Azure Container Registry 内のパブリック基本イメージのコピーを保持および更新し、基本イメージに依存するアプリケーション イメージを再構築できます。

ACR タスクでは、コンテナー イメージのビルド時、基本イメージの依存関係が動的に検出されます。 その結果、アプリケーション イメージの基本イメージが更新されるタイミングを検出できます。 ビルド タスクを 1 つ事前に構成しておくと、ACR タスクでは、基本イメージを参照するすべてのアプリケーション イメージを自動的にリビルドできます。 ACR タスクはこの自動検出とリビルドによって、更新された基本イメージを参照しているすべてのアプリケーション イメージを手動で追跡し、更新するために通常は必要となる時間と労力を削減しています。

## <a name="base-image-locations"></a>基本イメージの場所

Dockerfile からのイメージ ビルドでは、ACR タスクによって、以下の場所にある基本イメージへの依存関係が検出されます。

* タスクが実行されるのと同じ Azure コンテナー レジストリ
* 同じまたは異なるリージョンにある別のプライベート Azure コンテナー レジストリ 
* Docker Hub 内のパブリック リポジトリ 
* Microsoft Container Registry 内のパブリック リポジトリ

`FROM` ステートメントで指定された基本イメージがこれらの場所のいずれかにある場合、基本イメージが更新された場合はこのイメージが常にリビルドされるように、ACR タスクによってフックが追加されます。

## <a name="base-image-notifications"></a>基本イメージの通知

基本イメージが更新されてから依存タスクがトリガーされるまでの時間は、基本イメージの場所によって異なります。

* **Docker Hub または MCR のパブリック リポジトリからの基本イメージ** - パブリック リポジトリ内の基本イメージの場合は、ACR タスクにより、10 から 60 分のランダムな間隔でイメージの更新が確認されます。 依存タスクは、それに従って実行されます。
* **Azure Container Registry からの基本イメージ** - Azure Container Registry 内の基本イメージの場合は、ACR タスクにより、基本イメージが更新されると直ちに実行がトリガーされます。 基本イメージは、タスクの実行場所と同じ ACR 内にあっても、任意のリージョンの異なる ACR 内にあってもかまいません。

## <a name="additional-considerations"></a>その他の注意点

* **アプリケーション イメージの基本イメージ** - 現在、ACR タスクでは、アプリケーション (*実行時*) イメージの基本イメージの更新のみが追跡されます。 マルチステージ Dockerfiles で使用される中間 (*ビルド時*) イメージの基本イメージの更新は追跡されません。  

* **既定で有効** - [az acr task create][az-acr-task-create] コマンドを使用して ACR タスクを作成すると、既定では、そのタスクでは基本イメージの更新によるトリガーが *有効* になっています。 つまり、`base-image-trigger-enabled` プロパティは True に設定されています。 タスクでこの動作を無効にする場合は、このプロパティを False に更新します。 たとえば、次の [az acr task update][az-acr-task-update] コマンドを実行します。

  ```azurecli
  az acr task update --registry myregistry --name mytask --base-image-trigger-enabled False
  ```

* **依存関係追跡のトリガー** - ACR タスクでコンテナー イメージの依存関係 (基本イメージがどこに含まれるか) を特定して追跡できるようにするため、最初に **少なくとも 1 回**、タスクをトリガーしてイメージをビルドする必要があります。 たとえば、[az acr task run][az-acr-task-run] コマンドを使用してタスクを手動でトリガーします。

* **基本イメージの安定タグ** - 基本イメージの更新時にタスクをトリガーするには、基本イメージに `node:9-alpine` などの "*安定した*" タグがなければなりません。 このタグ付けは、OS およびフレームワークの修正プログラムを使用して最新の安定版リリースに更新された基本イメージでは一般的なものです。 基本イメージは、新しいバージョン タグで更新された場合、タスクをトリガーしません。 イメージのタグ付けの詳細については、[ベスト プラクティス ガイダンス](container-registry-image-tag-version.md)に関するページを参照してください。 

* **その他のタスク トリガー** - 基本イメージの更新によってトリガーされるタスクでは、[ソース コードのコミット](container-registry-tutorial-build-task.md)または [スケジュール](container-registry-tasks-scheduled.md)に基づいてトリガーを有効にすることもできます。 基本イメージの更新では、[複数ステップ タスク](container-registry-tasks-multi-step.md)もトリガーできます。

## <a name="next-steps"></a>次のステップ

基本イメージの更新後にアプリケーション イメージのビルドを自動化するシナリオについては、次のチュートリアルを参照してください。

* [同じレジストリで基本イメージの更新時にコンテナー イメージ ビルドを自動化する](container-registry-tutorial-base-image-update.md)

* [異なるレジストリで基本イメージの更新時にコンテナー イメージ ビルドを自動化する](container-registry-tutorial-base-image-update.md)


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
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
