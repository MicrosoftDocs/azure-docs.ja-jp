---
title: "Azure Machine Learning Workbench プロジェクトでの Git リポジトリの使用 | Microsoft Docs"
description: "この記事では、Azure Machine Learning Workbench プロジェクトと組み合わせて Git リポジトリを使用する方法について説明します。"
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: hning86
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 59b07c9834904e01256b75344ba2e6892e56438c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="using-git-repository-with-an-azure-machine-learning-workbench-project"></a>Azure Machine Learning Workbench プロジェクトでの Git リポジトリの使用
このドキュメントでは、Azure Machine Learning Workbench で Git を使用して、データ サイエンス実験での再現性を確保する方法について説明します。 プロジェクトをクラウド Git リポジトリに関連付ける方法も示します。

## <a name="introduction"></a>はじめに
Azure Machine Learning Workbench は Git 統合で新規に設計されています。 新しいプロジェクトを作成するときに、プロジェクト フォルダーがローカル Git リポジトリとして自動的に "Git 初期化" され、2 つ目の非表示のローカル Git リポジトリも _AzureMLHistory/<project_GUID>_ という名前のブランチで作成されて、実行するたびにプロジェクト フォルダーの変更が追跡されます。 

Azure ML プロジェクトを Visual Studio Team Service (VSTS) プロジェクト内にホストされている Git リポジトリに関連付けると、ローカルとリモートの両方で自動バージョン コントロールが有効になります。 このアソシエーションにより、リモート リポジトリへのアクセス権があれば、だれでも別のコンピューター (ローミング) に最新のソース コードをダウンロードできます。  

> [!NOTE]
> VSTS には、Azure Machine Learning 実験サービスから独立した固有のアクセス制御リストがあります。 ユーザー アクセスは、Git リポジトリと Azure ML ワークスペースまたはプロジェクトとで異なる場合があり、管理が必要な場合があります。 このため、単なるワークスペースの共有に加えて、コード レベルのアクセスを含め、Azure ML プロジェクトをチーム メンバーと共有する場合は、VSTS Git リポジトリに対する適切なアクセス権を明示的に付与する必要があります。 

Git では、_master_ ブランチを使用するかリポジトリに他のブランチを作成することで、バージョン コントロールを明示的に管理することもできます。 ローカル Git リポジトリを使用することができ、プロビジョニングされている場合はリモート Git リポジトリにプッシュすることもできます。

この図は、VSTS Git リポジトリと Azure ML プロジェクト間のリレーションシップを示しています。

![AML Git](media/using-git-ml-project/aml_git.png)

リモート Git リポジトリの使用を開始するには、次の基本的な手順に従います。

> [!NOTE]
> 現時点では、Azure Machine Learning では VSTS アカウントでのみ Git リポジトリがサポートされます。 (GitHub などの) 一般的な Git リポジトリのサポートは今後計画されています。

## <a name="step-1-create-an-azure-ml-experimentation-account"></a>手順 1. Azure Machine Learning 実験アカウントを作成する
まだ行っていない場合は、Azure Machine Learning 実験アカウントを作成し、Azure ML Workbench アプリをインストールします。 詳細については、[インストールと作成のクイックスタート](quickstart-installation.md)に関する記事をご覧ください。

## <a name="step-2-create-a-team-project-or-use-an-existing-team-project"></a>手順 2. チーム プロジェクトを作成するか既存のチーム プロジェクトを使用する
[Azure Portal](https://portal.azure.com/) で、新しい**チーム プロジェクト**を作成します。
1. **[+]** をクリックします
2. **"チーム プロジェクト"** を検索します
3. 必要な情報を入力します。
    - 名前: チーム名。
    - バージョン コントロール: **Git**
    - サブスクリプション: Azure Machine Learning 実験アカウントがあるサブスクリプション。
    - 場所: Azure Machine Learning 実験リソースの近くのリージョン内が理想です。
4. **Create** をクリックしてください。 

![Azure Portal でチーム プロジェクトを作成する](media/using-git-ml-project/create_vsts_team.png)


> [!TIP]
> Azure Machine Learning Workbench へのアクセスに使用する Azure Active Directory (AAD) アカウントでサインインしてください。 そうしないと、新しいチーム プロジェクトが間違ったテナント ID に属し、Azure Machine Learning から見つからない可能性があります。 この場合、コマンド ライン インターフェイスを使用し、VSTS トークンを指定する必要があります。

チーム プロジェクトが作成されたら、次の手順に進む準備ができます。

先ほど作成したチーム プロジェクトに直接移動するための URL は `https://<team_project_name>.visualstudio.com` です。

> [!NOTE]
> 現在、Azure Machine Learning では、master ブランチのない空の Git リポジトリのみがサポートされています。 まず、コマンド ライン インターフェイスから、--force 引数を使用して master ブランチを削除できます。 

## <a name="step-3-create-a-new-azure-ml-project-with-a-remote-git-repo"></a>手順 3. リモート Git リポジトリを持つ新しい Azure ML プロジェクトを作成する
Azure ML Workbench を起動し、新しいプロジェクトを作成します。 Git リポジトリのテキスト ボックスに、手順 2. から取得した VSTS Git リポジトリの URL を入力します。 通常は、http://<vsts_account_name>.visualstudio.com/_git/<project_name> のようになります

![Git リポジトリを持つ Azure ML プロジェクトを作成する](media/using-git-ml-project/create_project_with_git_rep.png)

リモート Git リポジトリの統合が有効ですぐに使用できる新しい Azure ML プロジェクトが作成されます。 プロジェクト フォルダーは、常にローカル Git リポジトリとして Git 初期化されます。 Git _remote_ はリモート VSTS Git リポジトリに設定されているため、コミットはリモート Git リポジトリにプッシュできます。

## <a name="step-3a-associate-an-existing-azure-ml-project-with-a-vsts-git-repo"></a>手順 3. 既存の Azure ML プロジェクトを VSTS Git リポジトリに関連付ける
必要に応じて、VSTS Git リポジトリのない Azure ML プロジェクトを作成し、実行履歴のスナップショットにローカル Git リポジトリを使用することもできます。 また、次のコマンドを使用して、後でこの既存の Azure ML プロジェクトに VSTS Git リポジトリを関連付けることができます。

```azurecli
# make sure you are in the project path so CLI has context of your current project
az ml project update --repo http://<vsts_account_name>.visualstudio.com/_git/<project_name
```

## <a name="step-4-capture-project-snapshot-in-git-repo"></a>手順 4. Git リポジトリ内のプロジェクト スナップショットをキャプチャする
これで、プロジェクトで数回実行して、実行と実行の間にいくつか変更を行うことができます。 これはデスクトップ アプリから、または `az ml experiment submit` コマンドを使用して CLI から行うことができます。 詳細については、[アイリスの分類に関するチュートリアル](tutorial-classifying-iris-part-1.md)の記事をご覧ください。 実行のたびに、プロジェクト フォルダー内のファイルに加えた変更がある場合は、全体のプロジェクト フォルダーのスナップショットがコミットされ、リモート Git リポジトリにプッシュされます。 ブランチとコミットを表示するには、VSTS Git リポジトリの URL を参照します。

![実行履歴ブランチ](media/using-git-ml-project/run_history_branch.png)

## <a name="step-5-restore-a-previous-project-snapshot"></a>手順 5. 以前のプロジェクト スナップショットを復元する 
プロジェクト フォルダー全体を以前の実行履歴プロジェクト状態スナップショットの状態に復元するには、AML Workbench で次の操作を行います。
1. アクティビティ バー (時計のアイコン) で **[実行]** をクリックします。
2. **[実行の一覧]** ビューで、復元する実行をクリックします。
3. **[Run Detail]\(実行の詳細\)** ビューで、**[復元]** をクリックします。

![実行履歴ブランチ](media/using-git-ml-project/restore_project.png)

または、Azure ML Workbench CLI ウィンドウから次のコマンドを使用することもできます。

```azurecli
# discover the run I want to restore snapshot from:
az ml history list -o table

# restore the snapshot from a particular run
az ml project restore --run-id <run_id>
```

このコマンドを実行すると、プロジェクト フォルダー全体が、その特定の実行の開始時に取得されたスナップショットで上書きされます。 これは、現在のプロジェクト フォルダー内の**すべての変更が失われる**ことを意味します。 したがって、このコマンドを実行する場合は特に注意してください。

## <a name="step-6-use-the-master-branch"></a>手順 6. master ブランチを使用する
現在のプロジェクト状態が誤って失われないようにする 1 つの方法は、プロジェクトを Git リポジトリの master ブランチにコミットすることです。 コマンド ライン (または他の使い慣れた Git クライアント ツール) から Git を直接使用して、master ブランチを操作できます。 For example:

```
# make sure you are on the master branch
git checkout master

# stage all changes
git add -A

# commit all changes locally on the master branch
git commit -m 'this is my updates so far'

# push changes into the remote VSTS Git repo master branch.
git push origin master
```

これで、master ブランチで行ったコミットにいつでも戻れるため、手順 5. に従ってプロジェクトを以前のスナップショットに安全に復元できます。

## <a name="authentication"></a>認証
プロジェクトのスナップショットの取得と復元を Azure ML の実行履歴機能にのみ依存している場合は、Git リポジトリの認証について心配する必要はありません。 これは実験サービス レイヤーで処理されます。

一方、独自の Git ツールを使用してバージョン コントロールを管理する場合は、VSTS 上のリモート Git リポジトリに対する認証を正しく処理する必要があります。 つまり、そのリモート Git リポジトリに対して Git コマンドを発行する前に、ローカル コンピューター上の Git リポジトリに認証を設定する必要があります。 

これを行う最も簡単な方法は、SSH キー ペアを作成し、Git リポジトリのセキュリティ設定に公開キー部分をアップロードすることです。

### <a name="generate-ssh-key"></a>SSH キー ペアの生成 
まず、コンピューター上に SSH キーのペアを生成しましょう。

#### <a name="on-windows"></a>Windows の場合:
Windows で Git GUI デスクトップ アプリを起動し、_[ヘルプ]_ メニューの _[Show SSH Key]\(SSH キーの表示\)_ をクリックします。

![SSH キー](media/using-git-ml-project/git_gui.png)

SSH をクリップボードにコピーします。

#### <a name="on-macos"></a>macOS の場合:
コマンド シェルからの簡単な手順:
```
# generate the SSH key
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

# start the SSH agent in the background
$ eval "$(ssh-agent -s)"

# add newly generated SSH key to the SSH agent
$ ssh-add -K ~/.ssh/id_rsa

# display the public key so you can copy it.
$ more ~/.ssh/id_rsa.pub
```
詳しい手順については、[こちらの GitHub 記事](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)をご覧ください。

### <a name="upload-public-key-to-git-repo"></a>Git リポジトリに公開キーをアップロードする
Visual Studio アカウントのホーム ページ (https://<vsts_account_name>.visualstudio.com) に移動してログインし、自分のアバターの下の [セキュリティ] をクリックします。

次に、SSH 公開キーを追加し、前の手順で生成した SSH 公開キーを貼り付け、名前を付けます。 ここで複数のキーを追加することができます。

これで、明示的な認証をさらに必要とすることなく、リモート リポジトリに対して Git コマンドをローカルに発行できます。

### <a name="read-more"></a>詳細情報
VSTS のリモート Git リポジトリに対してローカル認証を有効にする詳しい方法については、次の 2 つの記事に従ってください (いずれの方法でも機能します)。
- [SSH キー認証を使用する](https://www.visualstudio.com/en-us/docs/git/use-ssh-keys-to-authenticate)
- [Git 資格情報マネージャーを使用する](https://www.visualstudio.com/en-us/docs/git/set-up-credential-managers)


## <a name="next-steps"></a>次のステップ
Team Data Science Process を使用してプロジェクト構造を整理する方法については、[TDSP 使用したプロジェクトの体系化](how-to-use-tdsp-in-azure-ml.md)に関する記事をご覧ください
