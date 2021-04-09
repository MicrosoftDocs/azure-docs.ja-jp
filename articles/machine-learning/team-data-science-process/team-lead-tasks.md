---
title: Team Data Science Process チームでのチーム リーダーのタスク
description: Team Data Science Process チームでのチーム リーダーのタスクの詳細なチュートリアル
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: df7d2278487c1b098615a14562c498b9187c56eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96000030"
---
# <a name="tasks-for-the-team-lead-on-a-team-data-science-process-team"></a>Team Data Science Process チームでのチーム リーダーのタスク

この記事では、"*チーム リーダー*" がデータ サイエンス チームで行うタスクについて説明します。 リーム リーダーの目的は、[Team Data Science Process](overview.md) (TDSP) を標準とするチーム コラボレーション環境を確立することです。 TDSP は、コラボレーションとチーム学習の向上を支援するように設計されています。 

TDSP は、予測分析ソリューションとインテリジェント アプリケーションを効率的に実現するアジャイルで反復的なデータ サイエンス手法です。 このプロセスによって、Microsoft や業界のベスト プラクティスと構造が抽出されます。  ゴールは、データ サイエンス イニシアチブの実装を成功させ、分析プログラムの利点を完全に実現することです。 TDSP を標準とするデータ サイエンス チームの個人的役割とそれに関連したタスクの概要については、「[Team Data Science Process の役割とタスク](roles-tasks.md)」をご覧ください。

チーム リーダーは、企業のデータ サイエンス ユニットの複数のデータ サイエンティストで構成されるチームを管理します。 データ サイエンス ユニットの規模と構成により、[グループ マネージャー](group-manager-tasks.md)とチーム リーダーを 1 人が兼任している場合や、それぞれがタスクを代理人に委任している場合があります。 ただし、タスク自体は変わりません。 

次の図では、チームの環境を設定するためにチーム リーダーが行うタスクのワークフローを示します。

![チーム リーダーのタスクのワークフロー](./media/team-lead-tasks/team-leads-1-creating-teams.png)

1. Azure DevOps でグループの組織に **チーム プロジェクト** を作成します。 
  
1. 既定のチーム リポジトリの名前を **TeamUtilities** に変更します。
  
1. チーム プロジェクトに新しい **TeamTemplate** リポジトリを作成します。 
  
1. グループの **GroupUtilities** および **GroupProjectTemplate** リポジトリの内容を、**TeamUtilities** および **TeamTemplate** リポジトリにインポートします。 
  
1. チーム メンバーを追加し、アクセス許可を構成して、**セキュリティ制御** を設定します。
  
1. 必要に応じて、チームのデータおよび分析リソースを作成します。
   - チーム固有のユーティリティを **TeamUtilities** リポジトリに追加します。 
   - チーム全体にとって役立つデータ資産を格納するための **Azure ファイル ストレージ** を作成します。 
   - Azure ファイル ストレージをチーム リーダーの **Data Science Virtual Machine** (DSVM) にマウントし、データ資産を追加します。

以下のチュートリアルでは、その手順を詳しく説明します。

> [!NOTE] 
> この記事では、Azure DevOps と DSVM を使用して TDSP チーム環境を設定します。これが、Microsoft で TDSP を実装する方法です。 チームで他のコード ホスティング プラットフォームまたは開発プラットフォームが使用されている場合、チーム リーダーのタスクは同じですが、それらを行う方法は異なる場合があります。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、[グループ マネージャー](group-manager-tasks.md)によって次のリソースとアクセス許可が設定されているものとします。

- データ ユニット用の Azure DevOps **組織**
- **GroupProjectTemplate** および **GroupUtilities** リポジトリ。Microsoft TDSP チームの **ProjectTemplate** および **Utilities** リポジトリの内容が設定されているもの
- チーム用のプロジェクトとリポジトリを作成するための、組織のアカウントに対するアクセス許可

ローカル コンピューターまたは DSVM でリポジトリを複製してその内容を変更したり、Azure ファイル ストレージを設定して DSVM にマウントしたりできるようにするには、次のものが必要です。

- Azure サブスクリプション。
- コンピューターにインストールされた Git。 DSVM を使用している場合、Git は事前にインストールされています。 それ以外の場合は、[プラットフォームとツールに関する記事の付録](platforms-and-tools.md#appendix)をご覧ください。
- DSVM を使用する場合は、Azure で作成して構成された Windows または Linux の DSVM。 詳細と手順については、[Data Science Virtual Machine ドキュメント](../data-science-virtual-machine/index.yml)を参照してください。
- Windows DSVM の場合、[Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) をコンピューターにインストールしておきます。 *README.md* ファイルを下にスクロールして **[Download and Install]** セクションを見つけ、**最新のインストーラー** を選択します。 インストーラー ページから *.exe* インストーラーをダウンロードして実行します。 
- Linux DSVM の場合は、DSVM で設定されて Azure DevOps に追加された SSH 公開キー。 詳細と手順については、[プラットフォームとツールの付録](platforms-and-tools.md#appendix)の **SSH 公開キーの作成** に関するセクションをご覧ください。 

## <a name="create-a-team-project-and-repositories"></a>チーム プロジェクトとリポジトリの作成

このセクションでは、グループの Azure DevOps 組織に次のリソースを作成します。

- Azure DevOps 内の **MyTeam** プロジェクト
- **TeamTemplate** リポジトリ
- **TeamUtilities** リポジトリ

このチュートリアルでリポジトリやディレクトリに指定されている名前では、より大きなデータ サイエンス組織内で自分のチーム用に個別のプロジェクトを確立することが想定されています。 ただし、グループ マネージャーまたは組織管理者によって作成された 1 つのプロジェクトをグループ全体の作業に選択することもできます。 その場合は、すべてのデータ サイエンス チームがこの単一のプロジェクトの下にリポジトリを作成します。 このシナリオは次のような場合に有効です。
- 複数のデータ サイエンス チームが存在しない小規模なデータ サイエンス グループ。 
- 複数のデータ サイエンス チームが存在し、グループ レベルのスプリントの計画などのアクティビティでチーム間のコラボレーションを最適化することを求めている大規模なデータ サイエンス グループ。 

チームで 1 つのグループ プロジェクトの下にチーム固有のリポジトリを作成することを選択した場合、チーム リーダーは、 *\<TeamName>Template* や *\<TeamName>Utilities* のような名前のリポジトリを作成する必要があります。 次に例を示します。*TeamATemplate* および *TeamAUtilities*。 

どのような場合でも、チーム リーダーは、設定および複製するテンプレート リポジトリとユーティリティ リポジトリを、チーム メンバーに知らせる必要があります。 プロジェクト リーダーは、個別のプロジェクトまたは 1 つのプロジェクトのどちらであるかにかかわらず、[データ サイエンス チームのプロジェクト リーダーのタスク](project-lead-tasks.md)に関する記事に従って、プロジェクト リポジトリを作成する必要があります。 

### <a name="create-the-myteam-project"></a>MyTeam プロジェクトを作成する

チーム用に別のプロジェクトを作成するには:

1. Web ブラウザーで、グループの Azure DevOps 組織のホームページ (URL: *https:\//\<server name>/\<organization name>* ) に移動し、 **[新しいプロジェクト]** を選択します。 
   
   ![[新しいプロジェクト] を選択する](./media/team-lead-tasks/team-leads-2-create-new-team.png)
   
1. **[プロジェクトの作成]** ダイアログで、 **[プロジェクト名]** にチームの名前 (例: *MyTeam*) を入力し、 **[Advanced]\(詳細\)** を選択します。 
   
1. **[バージョン管理]** で **[Git]** を選択し、 **[作業項目プロセス]** で **[アジャイル]** を選択します。 **[作成]** を選択します。 
   
   ![Create project](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)
   
チーム プロジェクトの **[概要]** ページが開きます。ページの URL は *https:\//\<server name>/\<organization name>/\<team name>* です。

### <a name="rename-the-myteam-default-repository-to-teamutilities"></a>MyTeam の既定のリポジトリの名前を TeamUtilities に変更する

1. **MyTeam** プロジェクトの **[概要]** ページの **[どのサービスを開始しますか?]** で、 **[Repos]** を選択します。 
   
   ![[Repos] を選択する](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)
   
1. **MyTeam** のリポジトリ ページの上部で **MyTeam** リポジトリを選択し、ドロップダウンから **[リポジトリの管理]** を選択します。 
   
   ![[リポジトリの管理] を選択する](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)
1. **[プロジェクトの設定]** ページで、**MyTeam** リポジトリの横にある **[...]** を選択し、 **[リポジトリの名前変更]** を選択します。 
   
   ![[リポジトリの名前変更] を選択する](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)
   
1. **[MyTeam リポジトリの名前を変更]** ポップアップで「*TeamUtilities*」と入力し、 **[名前の変更]** を選択します。 

### <a name="create-the-teamtemplate-repository"></a>TeamTemplate リポジトリを作成する

1. **[プロジェクトの設定]** ページで、 **[新しいリポジトリ]** を選択します。 
   
   ![[新しいリポジトリ] を選択する](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)
   
   または、**MyTeam** プロジェクトの **[概要]** ページの左側のナビゲーションで **[リポジトリ]** を選択し、ページの上部でリポジトリを選択した後、ドロップダウンから **[新しいリポジトリ]** を選択します。
   
1. **[新しいリポジトリの作成]** ダイアログで、 **[種類]** として **[Git]** が選択されていることを確認します。 **[リポジトリ名]** に「*TeamTemplate*」と入力し、 **[作成]** を選択します。
   
   ![リポジトリを作成する](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)
   
1. プロジェクトの設定ページに 2 つのリポジトリ **TeamUtilities** と **TeamTemplate** が表示されることを確認します。 
   
   ![2 つのチーム リポジトリ](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)

### <a name="import-the-contents-of-the-group-common-repositories"></a>グループ共通リポジトリの内容をインポートする

グループ マネージャーによって設定されたグループ共通リポジトリの内容を、チーム リポジトリに設定するには:

1. **MyTeam** プロジェクトのホーム ページで、左側のナビゲーションにある **[リポジトリ]** を選択します。 **MyTeam** テンプレートが見つからないというメッセージが表示される場合は、 **[それ以外の場合は、既定の TeamTemplate リポジトリに移動します]** 内のリンクを選択します。 
   
   既定の **TeamTemplate** リポジトリが開きます。 
   
1. **[TeamTemplate は空です]** ページで、 **[インポート]** を選択します。 
   
   ![[インポート] を選択する](./media/team-lead-tasks/import-repo.png)
   
1. **[Git リポジトリをインポートする]** ダイアログで、 **[ソースの種類]** として **[Git]** を選択し、 **[クローン URL]** にグループ共通テンプレート リポジトリの URL を入力します。 URL は *https:\//\<server name>/\<organization name>/_git/\<repository name>* です。 たとえば、*https:\//dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupProjectTemplate* のようになります。 
   
1. **[インポート]** を選択します。 グループ テンプレート リポジトリの内容が、チーム テンプレート リポジトリにインポートされます。 
   
   ![グループ共通テンプレート リポジトリをインポートする](./media/team-lead-tasks/import-repo-2.png)
   
1. プロジェクトの **[リポジトリ]** ページの上部にあるドロップダウンで、**TeamUtilities** リポジトリを選択します。
   
1. インポート プロセスを繰り返して、グループ共通ユーティリティ リポジトリ (例: *GroupUtilities*) の内容を、**TeamUtilities** リポジトリにインポートします。 
   
2 つのチーム リポジトリに、対応するグループ共通リポジトリのファイルが含まれるようになりました。 

### <a name="customize-the-contents-of-the-team-repositories"></a>チーム リポジトリの内容をカスタマイズする

チームの固有のニーズに合わせてチーム リポジトリの内容をカスタマイズする場合は、ここで行うことができます。 ファイルの変更、ディレクトリ構造の変更、ファイルやフォルダーの追加を行うことができます。

Azure DevOps でファイルやフォルダーの変更、アップロード、作成を直接行うには:

1. **MyTeam** プロジェクトの **[概要]** ページで、 **[リポジトリ]** を選択します。 
   
1. ページの上部で、カスタマイズするリポジトリを選択します。

1. リポジトリのディレクトリ構造で、変更するフォルダーまたはファイルに移動します。 
   
   - 新しいフォルダーまたはファイルを作成するには、 **[新規]** の横にある矢印を選択します。 
     
     ![新しいファイルを作成する](./media/team-lead-tasks/new-file.png)
     
   - ファイルをアップロードするには、 **[ファイルのアップロード]** を選択します。 
     
     ![ファイルをアップロードする](./media/team-lead-tasks/upload-files.png)
     
   - 既存のファイルを編集するには、ファイルに移動し、 **[編集]** を選択します。 
     
     ![ファイルを編集する](./media/team-lead-tasks/edit-file.png)
     
1. ファイルを追加または編集した後、 **[コミット]** を選択します。
   
   ![変更をコミットする](./media/team-lead-tasks/commit.png)

ローカル コンピューターまたは DSVM でリポジトリの作業を行うには、最初にリポジトリをローカル コンピューターにコピーまたは "*複製*" した後、変更をコミットして共有チーム リポジトリにプッシュします。 

リポジトリを複製するには:

1. **MyTeam** プロジェクトの **[概要]** ページで、 **[リポジトリ]** を選択し、ページの上部で複製するリポジトリを選択します。
   
1. [リポジトリ] ページで、右上にある **[クローン]** を選択します。
   
1. **[リポジトリの複製]** ダイアログの **[コマンド ライン]** で、HTTP 接続として **[HTTPS]** を選択するか、SSH 接続として **[SSH]** を選択し、クローン URL をクリップボードにコピーします。
   
   ![クローン URL をコピーする](./media/team-lead-tasks/clone.png)
   
1. ローカル コンピューターで、次のディレクトリを作成します。
   
   - Windows の場合:**C:\GitRepos\MyTeam**
   - Linux の場合: **$home/GitRepos/MyTeam** 
   
1. 作成したディレクトリに移動します。
   
1. Git Bash で、コマンド `git clone <clone URL>` を実行します。\<clone URL> は、 **[複製]** ダイアログからコピーした URL です。
   
   たとえば、**TeamUtilities** リポジトリをローカル コンピューターの *MyTeam* ディレクトリに複製するには、次のいずれかのコマンドを使用します。 
   
   **HTTPS 接続:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH 接続:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```

リポジトリのローカル複製で必要な変更を行った後、変更をコミットして共有チーム リポジトリにプッシュします。 

ローカル環境の **GitRepos\MyTeam\TeamTemplate** または **GitRepos\MyTeam\TeamUtilities** ディレクトリから、次の Git Bash コマンドを実行します。

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

## <a name="add-team-members-and-configure-permissions"></a>チーム メンバーを追加してアクセス許可を構成する

チームにメンバーを追加するには:

1. Azure DevOps の **MyTeam** プロジェクトのホーム ページで、左側のナビゲーションから **[プロジェクトの設定]** を選択します。 
   
1. **[プロジェクトの設定]** の左側のナビゲーションで **[チーム]** を選択し、 **[チーム]** ページで **[MyTeam チーム]** を選択します。 
   
   ![チームを構成する](./media/team-lead-tasks/teams.png)
   
1. **[チーム プロファイル]** ページで、 **[追加]** を選択します。
   
   ![MyTeam チームに追加する](./media/team-lead-tasks/add-to-team.png)
   
1. **[ユーザーおよびグループの追加]** ダイアログで、グループに追加するメンバーを検索して選択し、 **[変更の保存]** を選択します。 
   
   ![ユーザーとグループを追加する](./media/team-lead-tasks/add-users.png)
   

チーム メンバーのアクセス許可を構成するには:

1. **[プロジェクトの設定]** の左側のナビゲーションで、 **[アクセス許可]** を選択します。 
   
1. **[アクセス許可]** ページで、メンバーを追加するグループを選択します。 
   
1. そのグループのページで、 **[メンバー]** を選択し、 **[追加]** を選択します。 
   
1. **[メンバーを招待する]** ポップアップで、グループに追加するメンバーを検索して選択し、 **[保存]** を選択します。 
   
   ![メンバーにアクセス許可を付与する](./media/team-lead-tasks/grant-permissions.png)

## <a name="create-team-data-and-analytics-resources"></a>チームのデータおよび分析リソースを作成する

このステップは省略可能ですが、データと分析リソースをチーム全体で共有すると、パフォーマンスとコストの面でメリットがあります。 チーム メンバーは、共有リソースでプロジェクトを実行し、予算を節約し、効率的に共同作業を行うことができます。 Azure ファイル ストレージを作成し、それを DSVM にマウントして、チーム メンバーと共有することができます。 

Azure HDInsight Spark クラスターなど、他のリソースをチームと共有する方法の詳細については、[プラットフォームとツール](platforms-and-tools.md)に関する記事をご覧ください。 このトピックでは、ニーズに適したリソースの選択に関するデータ サイエンスの観点からのガイダンスを提供し、製品ページと他の関連する便利なチュートリアルへのリンクを示します。

>[!NOTE]
> 低速でコストがかかる可能性のあるデータ センター間でのデータ転送を回避するため、Azure リソース グループ、ストレージ アカウント、DSVM がすべて同じ Azure リージョンでホストされていることを確認してください。 

### <a name="create-azure-file-storage"></a>Azure ファイル ストレージを作成する

1. チーム全体にとって役に立つデータ資産用の Azure ファイル ストレージを作成するには、次のスクリプトを実行します。 スクリプトでは Azure サブスクリプションの情報の入力を求められるので、用意しておいてください。 

   - Windows コンピューターでは、PowerShell コマンド プロンプトからスクリプトを実行します。
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
     .\CreateFileShare.ps1
     ```
     
   - Linux コンピューターでは、Linux シェルからスクリプトを実行します。
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
     bash CreateFileShare.sh
     ```
   
1. 求められたら Microsoft Azure アカウントにログインし、使用するサブスクリプションを選択します。
   
1. 使用するストレージ アカウントを選択するか、選択したサブスクリプションに新しいストレージ アカウントを作成します。 Azure ファイル ストレージの名前には、小文字、数字、ハイフンを使用できます。
   
1. ストレージのマウントと共有を容易にするため、Enter キーを押すか、「*Y*」と入力して、Azure ファイル ストレージの情報を現在のディレクトリ内のテキスト ファイルに保存します。 このテキスト ファイルを **TeamTemplate** リポジトリにチェックインし (理想的には、**Docs\DataDictionaries** の下)、チームのすべてのプロジェクトでアクセス可能にすることができます。 また、次のセクションで Azure DSVM に Azure ファイル ストレージをマウントするためにも、ファイルの情報が必要です。 
   
### <a name="mount-azure-file-storage-on-your-local-machine-or-dsvm"></a>ローカル コンピューターまたは DSVM に Azure ファイル ストレージをマウントする

1. Azure ファイル ストレージをローカル コンピューターまたは DSVM にマウントするには、次のスクリプトを使用します。
   
   - Windows コンピューターでは、PowerShell コマンド プロンプトからスクリプトを実行します。
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
     .\AttachFileShare.ps1
     ```
     
   - Linux コンピューターでは、Linux シェルからスクリプトを実行します。
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
     bash AttachFileShare.sh
     ```
   
1. 前のステップで Azure ファイル ストレージの情報ファイルを保存した場合は、Enter キーを押すか、「*Y*」と入力して続行します。 作成したファイルの完全なパスと名前を入力します。 
   
   Azure ファイル ストレージの情報ファイルがない場合は、「*n*」と入力し、指示に従ってサブスクリプション、Azure ストレージ アカウント、Azure ファイル ストレージの情報を入力します。
   
1. ファイル共有をマウントするローカル ドライブまたは TDSP ドライブの名前を入力します。 既存のドライブ名の一覧が画面に表示されます。 まだ存在しないドライブ名を指定します。
   
1. 新しいドライブとストレージがコンピューターに正常にマウントされたことを確認します。

## <a name="next-steps"></a>次のステップ

Team Data Science Process で定義されている他の役割とタスクの詳細な説明へのリンクを次に示します。

- [データ サイエンス チームのグループ マネージャーのタスク](group-manager-tasks.md)
- [データ サイエンス チームのプロジェクト リーダーのタスク](project-lead-tasks.md)
- [データ サイエンス チームのプロジェクトの個々の共同作成者](project-ic-tasks.md)