---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/02/2019
ms.author: danlep
ms.openlocfilehash: 79e2ca71d6b8178be63c3429edba89cf3bb523e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96029993"
---
## <a name="prerequisites"></a>前提条件

### <a name="get-sample-code"></a>サンプル コードを取得する

このチュートリアルは、[前のチュートリアル](../articles/container-registry/container-registry-tutorial-quick-task.md)の手順を既に完了し、サンプル リポジトリをフォークおよび複製していることを前提としています。 完了していない場合は、先に進む前に、前のチュートリアルの「[前提条件](../articles/container-registry/container-registry-tutorial-quick-task.md#prerequisites)」セクションの手順を行ってください。

### <a name="container-registry"></a>コンテナー レジストリ

このチュートリアルを行うには、Azure サブスクリプションに Azure コンテナー レジストリが必要です。 レジストリが必要な場合は、[前のチュートリアル](../articles/container-registry/container-registry-tutorial-quick-task.md)または「[クイック スタート: Azure CLI を使用したコンテナー レジストリの作成](../articles/container-registry/container-registry-get-started-azure-cli.md)」をご覧ください。

### <a name="create-a-github-personal-access-token"></a>GitHub 個人用アクセス トークンを作成する

Git リポジトリへのコミットでタスクをトリガーするには、リポジトリにアクセスするための個人用アクセス トークン (PAT) が ACR タスクに必要です。 まだ PAT がない場合は、次の手順に従って GitHub に PAT を生成してください。

1. GitHub で PAT 作成ページ (https://github.com/settings/tokens/new) に移動します
1. トークンの短い **説明** を入力します (例: "ACR タスクのデモ")
1. リポジトリにアクセスする ACR のスコープを選択します。 このチュートリアルのようにパブリック リポジトリにアクセスするには、**[リポジトリ]** の下の **[repo:status]** と **[public_repo]** を有効にします。

   ![GitHub の個人用アクセス トークン生成ページのスクリーンショット][build-task-01-new-token]

   > [!NOTE]
   > "*プライベート*" リポジトリにアクセスする PAT を生成するには、**リポジトリ** を完全に制御するためのスコープを選択します。

1. **[Generate token]\(トークンの生成\)** ボタンを選びます (パスワードの入力が必要な場合があります)
1. 生成されたトークンをコピーし、**安全な場所** に保存します (このトークンは、次のセクションでタスクを定義するときに使います)

   ![GitHub で生成された個人用アクセス トークンのスクリーンショット][build-task-02-generated-token]

<!-- Images -->
[build-task-01-new-token]: ./media/container-registry-task-tutorial-prereq/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-task-tutorial-prereq/build-task-02-generated-token.png
