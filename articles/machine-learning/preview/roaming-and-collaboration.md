---
title: "Azure Machine Learning Workbench のローミングとコラボレーション | Microsoft Docs"
description: "既知の問題の一覧とトラブルシューティングに役立つガイド"
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/05/2017
ms.openlocfilehash: 156dd1b7f928df22b3feb9e7a13396d3b53a91d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench のローミングとコラボレーション
このドキュメントでは、Azure Machine Learning Workbench を使用した、複数のコンピューターにわたるプロジェクトのローミングや、他のチーム メンバーとのコラボレーションについて説明します。 

リモート Git リポジトリ (リポジトリ) リンクを使用して Azure Machine Learning プロジェクトを作成すると、プロジェクトのメタデータとスナップショットは、クラウドに保存されます。 クラウドのリンクを使って、別のコンピューターからプロジェクトにアクセス (ローミング) することができます。 また、同僚にアクセスを許可して、コラボレーションすることができます。 

## <a name="prerequisites"></a>前提条件
まず、実験アカウントにアクセス権のある Azure Machine Learning ワークベンチをインストールします。 詳細については、[インストール ガイド](quickstart-installation.md)を参照してください。

次に、[Visual Studio Team System](https://www.visualstudio.com) にアクセスして、プロジェクトをリンクするリポジトリを作成します。 Git の詳細については、[Azure Machine Learning Workbench プロジェクトで Git リポジトリを使用する](using-git-ml-project.md)に関する記事を参照してください。

## <a name="create-a-new-azure-machine-learning-project"></a>新しい Azure Machine Learning プロジェクトを作成する
Azure Machine Learning Workbench を起動し、新しいプロジェクトを作成します (たとえば、_iris_)。 **[Visualstudio.com GIT Repository URL]\(Visualstudio.com GIT リポジトリの URL\)** テキストボックスに有効な VSTS Git リポジトリの URL を入力します。 
>[!IMPORTANT]
>Git リポジトリの読み取り/書き込みアクセス権がなく、リポジトリが空でない (つまりマスター分岐が既にある) 場合はプロジェクトの作成が失敗します。

プロジェクトが作成されたら、プロジェクト内で任意のスクリプトを何回か実行します。 この操作により、リモート Git リポジトリの実行履歴の分岐に、プロジェクトの状態がコミットされます。 

Git の認証を設定した場合も、明示的にマスター分岐で操作したり、新しい分岐を作成できます。 

例 
```
# check current repo status
$ git status

# stage all changes in the current repo
$ git add -A

# commit changes
$ git commit -m "my commit fixes this weird bug!"

# push to remote repo.
$ git push origin master
```

## <a name="roaming"></a>ローミング
<a name="roaming"></a>

### <a name="open-azure-machine-learning-workbench-on-second-machine"></a>Azure Machine Learning Workbench を 2 番目のコンピューターで開く
VSTS Git リポジトリがプロジェクトにリンクされると、Azure Machine Learning ワークベンチがインストールされている任意のコンピューターから _iris_ プロジェクトにアクセスできるようになります。 

別のコンピューターにある iris プロジェクトにアクセスするには、プロジェクトの作成中に使用されたものと同じ資格情報で、アプリにログインする必要があります。 さらに、同じ実験アカウントとワークスペースに移動する必要があります。 _iris_ プロジェクトは、ワークスペース内で他のプロジェクトと共にアルファベット順に一覧表示されます。 

### <a name="download-project-on-second-machine"></a>2 番目のコンピューターにプロジェクトをダウンロードします。
2 番目のコンピューターでワークスペースを開くと、一般的なフォルダーのアイコンとは異なるアイコンが _iris_ プロジェクトの横に表示されます。 ダウンロード アイコンは、プロジェクトのコンテンツがクラウド上にあり、現在のコンピューターにダウンロードする必要があることを示します。 

![プロジェクトの作成](./media/roaming-and-collaboration/downloadable-project.png)

_iris_ プロジェクトをクリックすると、ダウンロード操作が開始されます。 ダウンロードが完了するまで少し待つと、2 番目のコンピューターのプロジェクトにアクセスできるようになります。 

Windows では `C:\Users\<username>\Documents\AzureML` にダウンロードされます。

macOS では、`/home/<username>/Documents/AzureML` にダウンロードされます。

今後のリリースで、ダウンロード先のフォルダーを選択できるように機能を拡張する予定です。 

>プロジェクトとまったく同じ名前のフォルダーが Azure ML ディレクトリ内にあると、ダウンロードが失敗します。 機能が拡張されるまで、この問題を回避するには、既存のフォルダーの名前を変更する必要があります。


### <a name="work-on-the-downloaded-project"></a>ダウンロードしたプロジェクトでの作業 
新しくダウンロードしたプロジェクトには、プロジェクトの前回の実行時点でプロジェクトの状態が反映されます。 プロジェクトの状態のスナップショットは、実行を送信するたびに VSTS Git リポジトリの実行履歴分岐に対し自動的にコミットされます。 2 番目のコンピューターでプロジェクトをインスタンス化するときに、最新の実行に関連付けられているスナップショットを使用します。 
 

## <a name="collaboration"></a>コラボレーション
VSTS の Git リポジトリにリンクされているプロジェクトの他のチーム メンバーとコラボレーションできます。 実験アカウント、ワークスペース、およびプロジェクトのユーザーに権限を割り当てることができます。 この時点で、Azure CLI を使用して Azure Resource Manager のコマンドを実行できます。 [Azure ポータル](https://portal.azure.com)を使用することもできます。 [次のセクション](#portal)を参照してください。    

### <a name="using-command-line-to-add-users"></a>コマンドラインを使用してユーザーを追加する
例を見てましょう。 たとえば、e_Iris_ プロジェクトの所有者であるアリスは、ボブとアクセスを共有する必要があります。 

アリスは、**[ファイル]** メニューをクリックし、**[コマンド プロンプト]** メニュー項目を選択して、_iris_ プロジェクトに構成されているコマンド プロンプトを起動します。 アリスは、次のコマンドを実行して、ボブに付与するレベルのアクセス権限を決定できます。  

```azurecli
# Find ARM ID of the experimnetation account
az ml account experimentation show --query "id"

# Add Bob to the Experimentation Account as a Reader.
# Bob now has read access to all workspaces and projects under the Account by inheritance.
az role assignment create --assignee bob@contoso.com --role Reader --scope <experimentation account ARM ID>

# Find ARM ID of the workspace
az ml workspace show --query "id"

# Add Bob to the workspace as a Contributor.
# Bob now has read/write access to all projects under the Workspace by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <workspace ARM ID>

# find ARM ID of the project 
az ml project show --query "id"

# Add Bob to the Project as an Owner.
# Bob now has read/write access to the Project, and can add others too.
az role assignment create --assignee bob@contoso.com --role Owner --scope <project ARM ID>
```

ロールの割り当て後に、直接または継承により、ボブは Workbench プロジェクトの一覧で、プロジェクトを参照できます。 プロジェクトを表示するために、アプリケーションを再起動する必要がある場合があります。 ボブは、[ローミング セクション](#roaming)に説明されているとおり、プロジェクトをダウンロードしてアリスとコラボレーションできます。 

プロジェクトでコラボレーションを行うすべてのユーザーの実行履歴は、同じリモート Git リポジトリにコミットされます。 アリスが実行すると、ボブは、それを Workbench アプリのプロジェクトの実行履歴セクションで確認できます。 ボブは、アリスによって開始された実行を含む、任意の実行状態にプロジェクトを復元することもできます。 

プロジェクトのリモート Git リポジトリを共有することにより、アリスとボブは、マスター分岐でもコラボレーションできます。 必要な場合は、個人用分岐を作成して Git プル要求や結合を使用してコラボレーションすることもできます。 

### <a name="using-azure-portal-to-add-users"></a>Azure ポータルを使用してユーザーを追加する
<a name="portal"></a>

Azure Machine Learning の実験アカウント、ワークスペース、およびプロジェクトは、Azure Resource Manager のリソースです。 [Azure ポータル](https://portal.azure.com)では、[アクセス制御] リンクを使用して、ロールを割り当てることができます。 

[All Resources]\(すべてのリソース\) ビューから、ユーザーを追加するリソースを特定します。 ページ内のアクセス制御 (IAM) リンクをクリックします。 Add users 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

