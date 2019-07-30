---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/02/2019
ms.author: danlep
ms.openlocfilehash: 40cc1856a5e943ca5596e7d11712febadd30e3ec
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133431"
---
## <a name="prerequisites"></a>前提条件

### <a name="get-sample-code"></a>サンプル コードを取得する

このチュートリアルは、[前のチュートリアル](../articles/container-registry/container-registry-tutorial-quick-task.md)の手順を既に完了し、サンプル リポジトリをフォークおよび複製していることを前提としています。 完了していない場合は、先に進む前に、前のチュートリアルの「[前提条件](../articles/container-registry/container-registry-tutorial-quick-task.md#prerequisites)」セクションの手順を行ってください。

### <a name="container-registry"></a>コンテナー レジストリ

このチュートリアルを行うには、Azure サブスクリプションに Azure コンテナー レジストリが必要です。 レジストリが必要な場合は、[前のチュートリアル](../articles/container-registry/container-registry-tutorial-quick-task.md)または [Azure CLI を使用したコンテナー レジストリの作成に関するクイック スタート](../articles/container-registry/container-registry-get-started-azure-cli.md)を参照してください。

## <a name="create-a-github-personal-access-token"></a>GitHub 個人用アクセス トークンを作成する

Git リポジトリへのコミットでタスクをトリガーするには、リポジトリにアクセスするための個人用アクセス トークン (PAT) が ACR タスクに必要です。 まだ PAT がない場合は、次の手順に従って GitHub に PAT を生成してください。

1. GitHub で PAT 作成ページ (https://github.com/settings/tokens/new ) に移動します
1. トークンの短い**説明**を入力します (例: "ACR タスクのデモ")
1. リポジトリにアクセスする ACR のスコープを選択します。 このチュートリアルのようにパブリック リポジトリにアクセスするには、 **[リポジトリ]** の下の **[repo:status]** と **[public_repo]** を有効にします。

   ![GitHub の個人用アクセス トークン生成ページのスクリーンショット][build-task-01-new-token]

   > [!NOTE]
   > "*プライベート*" リポジトリにアクセスする PAT を生成するには、**リポジトリ**を完全に制御するためのスコープを選択します。

1. **[Generate token]\(トークンの生成\)** ボタンを選びます (パスワードの入力が必要な場合があります)
1. 生成されたトークンをコピーし、**安全な場所**に保存します (このトークンは、次のセクションでタスクを定義するときに使います)

   ![GitHub で生成された個人用アクセス トークンのスクリーンショット][build-task-02-generated-token]

<!-- Images -->
[build-task-01-new-token]: ./media/container-registry-task-tutorial-prereq/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-task-tutorial-prereq/build-task-02-generated-token.png
