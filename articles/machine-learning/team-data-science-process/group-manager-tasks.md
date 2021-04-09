---
title: Team Data Science Process のグループ マネージャーのタスク
description: データ サイエンス チーム プロジェクトでグループ マネージャーが行うタスクの詳細な手順を説明します。
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7ab6d6511d1e2cec82b321003c9d663249ddcf49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "94740228"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Team Data Science Process のグループ マネージャーのタスク

この記事では、データ サイエンス組織の "*グループ マネージャー*" が行うタスクについて説明します。 グループ マネージャーは、企業のデータ サイエンス ユニット全体を管理します。 データ サイエンス ユニット内には複数のチームがあり、それぞれのチームが、異なるビジネスの多数のデータ サイエンス プロジェクトに携わっている場合があります。 グループ マネージャーの目標は、[Team Data Science Process](overview.md) (TDSP) を標準とするグループ コラボレーション環境を確立することです。 TDSP を標準とするデータ サイエンス チームのすべての個人的役割とそれに関連したタスクの概要については、「[Team Data Science Process の役割とタスク](roles-tasks.md)」をご覧ください。

次の図では、6 つの主要なグループ マネージャー セットアップ タスクを示します。 グループ マネージャーは、そのタスクを代理人に委任する場合もありますが、グループ マネージャーの役割に関連付けられているタスクに変わりはありません。

![グループ マネージャーのタスク](./media/group-manager-tasks/tdsp-group-manager.png)

1. グループに対して **Azure DevOps 組織** をセットアップします。
2. Azure DevOps 組織に既定の **GroupCommon プロジェクト** を作成します。
3. Azure Repos で **GroupProjectTemplate** リポジトリを作成します。
4. Azure Repos で **GroupUtilities** リポジトリを作成します。
5. Microsoft TDSP チームの **ProjectTemplate** リポジトリ と **Utilities** リポジトリの内容を、グループ共通リポジトリにインポートします。
6. チーム メンバーがグループにアクセスするための **メンバーシップ** と **アクセス許可** を設定します。

以下のチュートリアルでは、その手順を詳しく説明します。 

> [!NOTE] 
> この記事では、Azure DevOps を使用して TDSP グループ環境を設定します。これが、Microsoft で TDSP を実装する方法です。 グループで他のコード ホスティング プラットフォームまたは開発プラットフォームが使用されている場合、グループ マネージャーのタスクは同じですが、それらを行う方法は異なる場合があります。

## <a name="create-an-organization-and-project-in-azure-devops"></a>Azure DevOps で組織とプロジェクトを作成する

1. [visualstudio.microsoft.com](https://visualstudio.microsoft.com) にアクセスし、右上にある **[サインイン]** を選択して、Microsoft アカウントにサインインします。 
   
   ![Microsoft アカウントへのサインイン](./media/group-manager-tasks/signinvs.png)
   
   Microsoft アカウントがない場合は、 **[Sign up now]\(今すぐサインアップ\)** を選択して Microsoft アカウントを作成した後に、このアカウントを使用してサインインします。 組織に Visual Studio サブスクリプションがある場合は、そのサブスクリプションの資格情報でサインインします。
   
1. サインインした後、Azure DevOps ページの右上にある **[新しい組織の作成]** を選択します。
   
   ![新しい組織を作成する](./media/group-manager-tasks/create-organization.png)
   
1. サービス利用規約、プライバシーに関する声明、倫理規定への同意を求めるメッセージが表示されたら、 **[続行]** を選択します。
   
1. サインアップ ダイアログ ボックスで、Azure DevOps 組織の名前を指定し、ホスト リージョンの割り当てを受け入れるか、ドロップダウンで別のリージョンを選択します。 その後 **[続行]** を選択します。 

1. **[プロジェクトを作成して開始します]** で「*GroupCommon*」と入力し、 **[プロジェクトの作成]** を選択します。 
   
   ![Create project](./media/group-manager-tasks/create-project.png)

**GroupCommon** プロジェクトの **[概要]** ページが開きます。 ページの URL は *https:\//\<servername>/\<organization-name>/GroupCommon* です。

![プロジェクトの概要ページ](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>グループ共通リポジトリを設定する

Azure Repos では、グループ用に次の種類のリポジトリをホストします。

- **グループ共通リポジトリ**: データ サイエンス ユニット内の複数のチームが多数のデータ サイエンス プロジェクト用に使用できる汎用リポジトリ。 
- **チーム リポジトリ**: データ サイエンス ユニット内の特定のチーム用のリポジトリ。 これらはチームのニーズに固有のリポジトリであり、そのチーム内の複数のプロジェクトに使用できますが、データ サイエンス ユニット内の複数のチームで使用できるほどの汎用性はありません。
- **プロジェクト リポジトリ**: 特定のプロジェクト用のリポジトリ。 このようなリポジトリは、チーム内の複数のプロジェクト用や、データ サイエンス ユニット内の他のチーム用の、十分な汎用性はありません。

プロジェクトでグループ共通リポジトリを設定するには、次のようにします。 
- 既定の **GroupCommon** リポジトリの名前を **GroupProjectTemplate** に変更します
- 新しい **GroupUtilities** リポジトリを作成します

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>既定のプロジェクト リポジトリの名前を GroupProjectTemplate に変更する

既定の **GroupCommon** プロジェクト リポジトリの名前を **GroupProjectTemplate** に変更するには:

1. **GroupCommon** プロジェクトの **[概要]** ページで、 **[リポジトリ]** を選択します。 この操作により、GroupCommon プロジェクトの既定の **GroupCommon** リポジトリが表示されます。これは、現在は空です。
   
1. ページの上部で **GroupCommon** の横にある矢印をドロップダウンし、 **[リポジトリの管理]** を選択します。
   
   ![リポジトリの管理](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. **[プロジェクトの設定]** ページで、**GroupCommon** の横にある **[...]** を選択し、 **[リポジトリの名前変更]** を選択します。 
   
   ![[...]、[リポジトリの名前変更] の順に選択する](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. **[GroupCommon リポジトリの名前を変更]** ポップアップで、「*GroupProjectTemplate*」と入力して、 **[名前の変更]** を選択します。 
   
   ![リポジトリの名前を変更する](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>GroupUtilities リポジトリを作成する

**GroupUtilities** リポジトリを作成するには:

1. **GroupCommon** プロジェクトの **[概要]** ページで、 **[リポジトリ]** を選択します。 
   
1. ページの上部で **GroupProjectTemplate** の横にある矢印をドロップダウンし、 **[新しいリポジトリ]** を選択します。
   
   ![[新しいリポジトリ] を選択する](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. **[新しいリポジトリの作成]** ダイアログで、 **[種類]** として **[Git]** を選択し、 **[リポジトリ名]** に「*GroupUtilities*」と入力して、 **[作成]** を選択します。
   
   ![GroupUtilities リポジトリを作成する](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. **[プロジェクトの設定]** ページの左側のナビゲーションで、 **[リポジトリ]** の下の **[リポジトリ]** を選択すると、次の 2 つのグループ リポジトリが表示されます: **GroupProjectTemplate** と **GroupUtilities**。
   
   ![2 つのグループ リポジトリ](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>Microsoft TDSP チーム リポジトリをインポートする

チュートリアルのこの部分では、Microsoft TDSP チームによって管理されている **ProjectTemplate** および **Utilities** リポジトリの内容を、**GroupProjectTemplate** および **GroupUtilities** リポジトリにインポートします。 

TDSP チーム リポジトリをインポートするには:

1. **GroupCommon** プロジェクトのホーム ページで、左側のナビゲーションにある **[リポジトリ]** を選択します。 既定の **GroupProjectTemplate**  リポジトリが開きます。 
   
1. **[GroupProjectTemplate は空です]** ページで、 **[インポート]** を選択します。 
   
   ![[インポート] を選択する](./media/group-manager-tasks/import-repo.png)
   
1. **[Git リポジトリをインポートする]** ダイアログで、 **[ソースの種類]** として **[Git]** を選択し、 **[クローン URL]** に「*https:\//github.com/Azure/Azure-TDSP-ProjectTemplate.git*」と入力します。 その後、 **[インポート]** を選択します。 Microsoft TDSP チームの ProjectTemplate リポジトリの内容が、GroupProjectTemplate リポジトリにインポートされます。 
   
   ![Microsoft TDSP チーム リポジトリをインポートする](./media/group-manager-tasks/import-repo-2.png)
   
1. **[リポジトリ]** ページの上部にあるドロップダウンで、**GroupUtilities** リポジトリを選択します。
   
2 つの各グループ リポジトリに、Microsoft TDSP チームの対応するリポジトリの、 *.git* ディレクトリ内のものを除くすべてのファイルが含まれるようになります。 

## <a name="customize-the-contents-of-the-group-repositories"></a>グループ リポジトリの内容をカスタマイズする

グループの特定のニーズに合わせてグループ リポジトリの内容をカスタマイズする場合は、ここで行うことができます。 ファイルを修正したり、ディレクトリ構造を変更したり、グループが開発したファイルやグループに役立つファイルを追加したりできます。

### <a name="make-changes-in-azure-repos"></a>Azure Repos で変更を行う

リポジトリの内容をカスタマイズするには:

1. **GroupCommon** プロジェクトの **[概要]** ページで、 **[リポジトリ]** を選択します。 
   
1. ページの上部で、カスタマイズするリポジトリを選択します。

1. リポジトリのディレクトリ構造で、変更するフォルダーまたはファイルに移動します。 
   
   - 新しいフォルダーまたはファイルを作成するには、 **[新規]** の横にある矢印を選択します。 
     
     ![新しいファイルを作成する](./media/group-manager-tasks/new-file.png)
     
   - ファイルをアップロードするには、 **[ファイルのアップロード]** を選択します。 
     
     ![ファイルをアップロードする](./media/group-manager-tasks/upload-files.png)
     
   - 既存のファイルを編集するには、ファイルに移動し、 **[編集]** を選択します。 
     
     ![ファイルを編集する](./media/group-manager-tasks/edit-file.png)
     
1. ファイルを追加または編集した後、 **[コミット]** を選択します。
   
   ![変更をコミットする](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>ローカル コンピューターまたは DSVM を使用して変更を行う

ローカル コンピューターまたは DSVM を使用して変更を行い、グループ リポジトリに変更をプッシュする場合は、Git と DSVM を使用するための前提条件が満たされていることを確認します。

- DSVM を作成する場合は、Azure サブスクリプション。
- コンピューターにインストールされた Git。 DSVM を使用している場合、Git は事前にインストールされています。 それ以外の場合は、[プラットフォームとツールに関する記事の付録](platforms-and-tools.md#appendix)をご覧ください。
- DSVM を使用する場合は、Azure で作成して構成された Windows または Linux の DSVM。 詳細と手順については、[Data Science Virtual Machine ドキュメント](../data-science-virtual-machine/index.yml)を参照してください。
- Windows DSVM の場合、[Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) をコンピューターにインストールしておきます。 *README.md* ファイルを下にスクロールして **[Download and Install]** セクションを見つけ、**最新のインストーラー** を選択します。 インストーラー ページから *.exe* インストーラーをダウンロードして実行します。 
- Linux DSVM の場合は、DSVM で設定されて Azure DevOps に追加された SSH 公開キー。 詳細と手順については、[プラットフォームとツールの付録](platforms-and-tools.md#appendix)の **SSH 公開キーの作成** に関するセクションをご覧ください。 

最初に、リポジトリをローカル コンピューターにコピーまたは "*複製*" します。 
   
1. **GroupCommon** プロジェクトの **[概要]** ページで、 **[リポジトリ]** を選択し、ページの上部で複製するリポジトリを選択します。
   
1. [リポジトリ] ページで、右上にある **[クローン]** を選択します。
   
1. **[Clone repository]\(リポジトリのクローン\)** ダイアログで、HTTP 接続には **[HTTP]** を選択し、SSH 接続には **[SSH]** を選択し、 **[コマンド ライン]** の下にあるクローン URL をクリップボードにコピーします。
   
   ![リポジトリをクローンする](./media/group-manager-tasks/clone.png)
   
1. ローカル コンピューターで、次のディレクトリを作成します。
   
   - Windows の場合:**C:\GitRepos\GroupCommon**
   - Linux の場合: ホーム ディレクトリ上の **$/GitRepos/GroupCommon** 
   
1. 作成したディレクトリに移動します。
   
1. Git Bash で、コマンド `git clone <clone URL>.` を実行します。
   
   たとえば、次のコマンドのいずれかで、**GroupUtilities** リポジトリがローカル コンピューターの *GroupCommon* ディレクトリに複製されます。 
   
   **HTTPS 接続:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **SSH 接続:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

リポジトリのローカル複製で必要な変更を行った後、共有グループ共通リポジトリに変更をプッシュできます。 

ローカル環境の **GroupProjectTemplate** または **GroupUtilities** ディレクトリから、次の Git Bash コマンドを実行します。

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Git リポジトリに初めてコミットする場合は、`git commit` コマンドを実行する前に、グローバル パラメーター *user.name* と *user.email* を設定する必要があります。 次の 2 つのコマンドを実行します。
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> 複数の Git リポジトリにコミットする場合は、そのすべてに対して同じ名前とメール アドレスを使用します。 同じ名前とメール アドレスを使用すると、Power BI ダッシュボードを作成して複数のリポジトリの Git アクティビティを追跡するときに便利です。

## <a name="add-group-members-and-configure-permissions"></a>グループ メンバーを追加してアクセス許可を構成する

グループにメンバーを追加するには:

1. Azure DevOps の **GroupCommon** プロジェクトのホーム ページで、左側のナビゲーションから **[プロジェクトの設定]** を選択します。 
   
1. **[プロジェクトの設定]** の左側のナビゲーションで **[チーム]** を選択し、 **[チーム]** ページで **[GroupCommon チーム]** を選択します。 
   
   ![チームを構成する](./media/group-manager-tasks/teams.png)
   
1. **[チーム プロファイル]** ページで、 **[追加]** を選択します。
   
   ![GroupCommon チームに追加する](./media/group-manager-tasks/add-to-team.png)
   
1. **[ユーザーおよびグループの追加]** ダイアログで、グループに追加するメンバーを検索して選択し、 **[変更の保存]** を選択します。 
   
   ![ユーザーとグループを追加する](./media/group-manager-tasks/add-users.png)
   

メンバーのアクセス許可を構成するには:

1. **[プロジェクトの設定]** の左側のナビゲーションで、 **[アクセス許可]** を選択します。 
   
1. **[アクセス許可]** ページで、メンバーを追加するグループを選択します。 
   
1. そのグループのページで、 **[メンバー]** を選択し、 **[追加]** を選択します。 
   
1. **[メンバーを招待する]** ポップアップで、グループに追加するメンバーを検索して選択し、 **[保存]** を選択します。 
   
   ![メンバーにアクセス許可を付与する](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>次のステップ

Team Data Science Process での他の役割とタスクの詳細な説明へのリンクを次に示します。

- [データ サイエンス チームのチーム リーダーのタスク](team-lead-tasks.md)
- [データ サイエンス チームのプロジェクト リーダーのタスク](project-lead-tasks.md)
- [データ サイエンス チームのプロジェクトの個々の共同作成者](project-ic-tasks.md)
