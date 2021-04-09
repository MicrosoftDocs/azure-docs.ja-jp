---
title: ACR タスクのサンプル
description: Azure Container Registry タスク (ACR) のサンプルでコンテナー イメージをビルド、実行、パッチ適用する
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "74456088"
---
# <a name="acr-tasks-samples"></a>ACR タスクのサンプル

この記事は、一部の [Azure Container Registry タスク](container-registry-tasks-overview.md) (ACR タスク) シナリオのサンプル `task.yaml` ファイルと関連 Dockerfile にリンクされています。 

追加のサンプルが必要であれば、[Azure サンプル][task-examples] リポジトリを参照してください。

## <a name="scenarios"></a>シナリオ

* **イメージをビルドする** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml)、[Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **コンテナーを実行する** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **イメージをビルドしてプッシュする** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml)、[Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **イメージをビルドして実行する** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml)、[Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **複数のイメージをビルドしてプッシュする** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml)、[Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **イメージを並列でビルドしてテストする** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml)、[Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **イメージをビルドして複数のレジストリにプッシュする** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml)、[Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>次のステップ

AKS タスクについて学習してください。

* [複数ステップのタスク](container-registry-tasks-multi-step.md) - クラウドでコンテナー イメージをビルド、テスト、パッチ適用するための ACR タスクベースのワークフロー。
* [タスクのリファレンス](container-registry-tasks-reference-yaml.md) - タスク ステップの種類、プロパティ、使い方。
* [コマンド リポジトリ](https://github.com/AzureCR/cmd) - ACR のタスクのコマンドとしてのコンテナーのコレクション。


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks
