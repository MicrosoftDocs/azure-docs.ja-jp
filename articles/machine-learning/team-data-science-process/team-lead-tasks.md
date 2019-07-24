---
title: Team Data Science Process チームでのチーム リーダーのタスク
description: データ サイエンス チーム プロジェクトのチーム リーダーがデータ サイエンス チームのために遂行する必要があるタスクの概要を説明します。
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 45be3d7f865c7b72ae62efbf99dbbb4594b1846f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55465267"
---
# <a name="tasks-for-the-team-lead-in-the-team-data-science-process-team"></a>Team Data Science Process チームでのチーム リーダーのタスク

このトピックでは、チーム リーダーがデータ サイエンス チームのために遂行する必要があるタスクの概要を説明します。 [Team Data Science Process](overview.md) (TDSP) を標準とするチーム コラボレーション環境を確立することを目標とします。 TDSP は、予測分析ソリューションとインテリジェント アプリケーションを効率的に実現するアジャイルで反復的なデータ サイエンス手法です。 コラボレーションとチーム学習の向上を支援することを目的としています。 このプロセスは、企業が分析プログラムのメリットを完全に実現できるように、データ サイエンスへの取り組みの実施を成功に導くために必要なベスト プラクティスと構造を Microsoft と業界から抽出したものです。 このプロセスを標準とするデータ サイエンス チームの人員の役割とそれに関連したタスクの概要については、「[Team Data Science Process Roles and tasks (Team Data Science Process での役割とタスク)](roles-tasks.md)」をご覧ください。

**チーム リーダー**は、企業のデータ サイエンス部のチームを管理します。 チームは複数のデータ サイエンティストで構成されます。 データ サイエンティストがわずかしかいないデータ サイエンス部では、**グループ マネージャー**と**チーム リーダー**を 1 人が兼任している場合や、それぞれがタスクを代理人に委任している場合があります。 ただし、タスク自体は変わりません。 この環境を設定するためにチーム リーダーが実行するタスクのワークフローを次の図に示します。

![1](./media/team-lead-tasks/team-leads-1-creating-teams.png)

>[AZURE.NOTE] コード ホスティング プラットフォームとして Azure DevOps を使用しており、独自のチーム用に個別の Azure DevOps プロジェクトが必要な場合は、図のブロック 1 および 2 内のタスクが必要になります。 これらのタスクが完了したら、このプロジェクトの下にチームのすべてのリポジトリを作成できます。 

次のセクションで示すいくつかの前提条件となるタスクをグループ マネージャーが完了した後に、このチュートリアルでチーム リーダーが実行する 5 つの主要なタスク (一部のタスクは省略可能) があります。 これらのタスクは、このトピックの番号付きの主要セクションに対応しています。

1. グループの Azure DevOps Services に**プロジェクト**を、またそのプロジェクト内に 2 つのチーム リポジトリを作成します。
    - **ProjectTemplate リポジトリ** 
    - **TeamUtilities リポジトリ**
2. グループ マネージャーが設定した **GroupProjectTemplate** リポジトリから **ProjectTemplate** チーム リポジトリをシード処理します。 
3. チームのデータおよび分析リソースを作成します。
    - チーム固有のユーティリティを **TeamUtilities** リポジトリに追加します。 
    - (省略可能) チーム全体にとって役立つデータ資産の保存に使用する **Azure File Storage** を作成します。 
4. (省略可能) Azure File Storage をチーム リーダーの**データ サイエンス仮想マシン** (DSVM) にマウントし、データ資産を追加します。
5. チーム メンバーを追加し、アクセス許可を構成して、**セキュリティ制御**を設定します。

>[AZURE.NOTE] 以下の方法で Azure DevOps を使用して TDSP チーム環境を設定するために必要な手順の概要について説明します。 Azure DevOps でこれらのタスクを達成する方法を指定するのは、それが Microsoft で TDSP を実装する方法であるためです。 グループで別のコード ホスティング プラットフォームを使用している場合も、一般にチーム リーダーが実行する必要があるタスクは変わりません。 ただし、これらのタスクを実行する方法が異なります。

## <a name="repositories-and-directories"></a>リポジトリとディレクトリ

このトピックでは、リポジトリとディレクトリの省略名を使用します。 これらの名前により、リポジトリとディレクトリ間での操作がわかりやすくなります。 以降のセクションでは次の表記を使用します (**R** は Git リポジトリを表し、**D** は DSVM 上のローカル ディレクトリを表します)。

- **R1**:グループ マネージャーが Azure DevOps グループ サーバーで設定する Git 上の **GroupProjectTemplate** リポジトリ。
- **R3**:チーム リーダーが設定する Git の **ProjectTemplate** チーム リポジトリ。
- **R4**:チーム リーダーが設定する Git の **TeamUtilities** リポジトリ。
- **D1**:R1 から複製され、D3 にコピーされるローカル ディレクトリ。
- **D3**:R3 から複製され、カスタマイズされた後 R3 にコピーされて戻されるローカル ディレクトリ。
- **D4**:R4 から複製され、カスタマイズされた後 R4 にコピーされて戻されるローカル ディレクトリ。

このチュートリアルでリポジトリやディレクトリに指定されている名前は、より大きなデータ サイエンス グループ内の独自のチーム用に個別のプロジェクトを確立することが目的であるという前提の下に提供されています。 ただし、チーム リーダーには他の選択肢もあります。

- グループ全体を選択して 1 つのプロジェクトを作成できます。 その場合は、すべてのデータ サイエンス チームのすべてのプロジェクトがこの 1 つのプロジェクトの下になります。 これを実現するには、この手順に従って 1 つのプロジェクトを作成する Git 管理者を指定できます。 このシナリオは次のようなグループで有効です。
    -  複数のデータ サイエンス チームが存在しない小規模なデータ サイエンス グループ 
    -  複数のデータ サイエンス チームが存在し、グループ レベルのスプリントの計画などのアクティビティでチーム間のコラボレーションを最適化することを求めている大規模なデータ サイエンス グループ 
- 各チームは、グループ全体の 1 つのプロジェクトの下にチーム固有のプロジェクト テンプレートまたはチーム固有のユーティリティを持つことを選択できます。 この場合は、同じプロジェクトの下に、チーム リーダーがプロジェクト テンプレート リポジトリまたはチーム ユーティリティ リポジトリ、あるいはその両方を作成する必要があります。 これらのリポジトリには、*<チーム名\>ProjectTemplate* および *<チーム名\>Utilities* という名前を付けます (例: *TeamJohnProjectTemplate**TeamJohnUtilities*)。 

いずれの場合も、チームリーダーはプロジェクトおよびユーティリティ リポジトリを設定し、複製するときに、導入するテンプレート リポジトリとユーティリティ リポジトリをチーム メンバーに知らせる必要があります。 プロジェクト リーダーは、個別のプロジェクトまたは 1 つのプロジェクトのどちらであるかにかかわらず、「[データ サイエンス チームのプロジェクト リーダーのタスク](project-lead-tasks.md)」に従ってプロジェクト リポジトリを作成する必要があります。 


## <a name="0-prerequisites"></a>0.前提条件

前提条件を満たすには、[データ サイエンス チームのグループ マネージャーのタスク](group-manager-tasks.md)に関する記事で説明する、グループ マネージャーに割り当てられたタスクを完了します。 まとめると、チーム リーダーのタスクを開始する前に、次の要件を満たす必要があります。 

- **グループの Azure DevOps Services** (または、その他の一部のコード ホスティング プラットフォームではグループ アカウント) がグループ マネージャーによって設定されている。
- 使用する予定のコード ホスティング プラットフォームで、グループ マネージャーによって、グループ アカウントに **GroupProjectTemplate リポジトリ** (R1) が設定されている。
- チーム リーダーが、チームのリポジトリを作成するためのグループ アカウントで**承認**されている。
- マシンに Git をインストールする必要があります。 データ サイエンス仮想マシン (DSVM) を使用している場合は、Git がプレインストールされているので、インストールは不要です。 それ以外の場合は、[プラットフォームとツールに関する記事の付録](platforms-and-tools.md#appendix)をご覧ください。  
- **Windows DSVM** を使用している場合は、マシンに [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) がインストールされている必要があります。 README.md ファイルで、下へスクロールして「**Download and Install**」(ダウンロードとインストール) セクションを表示し、「*latest installer*」(最新のインストーラー) をクリックします。 これにより、最新のインストーラーのページに移動します。 このページから .exe インストーラーをダウンロードして実行します。 
- **Linux DSVM** を使用している場合は、DSVM で SSH 公開キーを作成し、それをグループの Azure DevOps Services に追加します。 SSH の詳細については、[プラットフォームとツールに関する記事の付録](platforms-and-tools.md#appendix)の **SSH 公開キーの作成**に関するセクションをご覧ください。 
    
## <a name="1-create-a-project-and-repositories"></a>1.プロジェクトとリポジトリを作成する

バージョン管理とコラボレーションのためのコード ホスティング プラットフォームとして Azure DevOps を使用している場合は、この手順を完了します。 このセクションでは、グループの Azure DevOps Services 内に次の 3 つのアーティファクトを作成します。

- Azure DevOps 内の **MyTeam** プロジェクト
- Git の **MyProjectTemplate** リポジトリ (**R3**)
- Git の **MyTeamUtilities** リポジトリ (**R4**)

### <a name="create-the-myteam-project"></a>MyTeam プロジェクトを作成する

- グループの Azure DevOps Services ホームページ (URL `https://<Azure DevOps Services Name\>.visualstudio.com`) に移動します。 
- **[新規作成]** をクリックしてプロジェクトを作成します。 

    ![2](./media/team-lead-tasks/team-leads-2-create-new-team.png)

- [プロジェクトの作成] ウィンドウで、プロジェクト名 (この例では **MyTeam**) を入力するよう求められます。 **[プロセス テンプレート]** で **[アジャイル]** を選択し、**[バージョン管理]** で **[Git]** を選択します。 

    ![3](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)

- **[プロジェクトの作成]** をクリックします。 **MyTeam** プロジェクトが 1 分未満で作成されます。 

- **MyTeam** プロジェクトが作成されたら、**[プロジェクトに移動]** ボタンをクリックして、プロジェクトのホーム ページに移動します。 

    ![4](./media/team-lead-tasks/team-leads-4-create-new-team-3.png)

- **[おめでとうございます!]** ポップアップ ウィンドウが表示された場合は、 **[コードの追加]** (赤色の枠内のボタン) をクリックします。 それ以外の場合は、**[コード]** (黄色の枠内) をクリックします。 これにより、プロジェクトの Git リポジトリ ページが表示されます。 

    ![5](./media/team-lead-tasks/team-leads-5-team-project-home.png)

### <a name="create-the-myprojecttemplate-repository-r3-on-git"></a>Git の MyProjectTemplate リポジトリ (R3) を作成する

- プロジェクトの Git リポジトリ ページで、リポジトリ名 **[MyTeam]** の横にある下向き矢印をクリックし、**[リポジトリの管理...]** を選択します。

    ![6](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)

- プロジェクトのコントロール パネルの **[バージョン管理]** タブで、**[MyTeam]** をクリックし、**[リポジトリの名前変更...]** を選択します。 

    ![7](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)

- **[Rename the MyTeam repository]\(MyTeam のリポジトリの名前変更\)** ウィンドウで、リポジトリの新しい名前を入力します。 この例では、「*MyTeamProjectTemplate*」と入力します。 *<チーム名\>ProjectTemplate* のような名前をご利用いただけます。 **[名前の変更]** をクリックして続行します。

    ![8](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)

### <a name="create-the-myteamutilities-repository-r4-on-git"></a>Git の MyTeamUtilities リポジトリ (R4) を作成する

- プロジェクトの下に新しいリポジトリ *<チーム名\>Utilities* を作成するには、プロジェクトのコントロール パネルの **[バージョン管理]** タブで **[新しいリポジトリ...]** をクリックします。  

    ![9](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)

- 表示された **[新しいリポジトリの作成]** ウィンドウで、このリポジトリの名前を指定します。 この例では、このリポジトリに *MyTeamUtilities* という名前を付けます (このチュートリアルでは **R4** と表記されます)。 *<チーム名\>Utilities* のような名前を使用します。 **[種類]** で **[Git]** を選択します。 **[作成]** をクリックして続行します。

    ![10](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)

- **MyTeam** プロジェクトの下に作成された次の 2 つの新しい Git リポジトリが表示されていることを確認します。 次の点に注意してください。 

- **MyTeamProjectTemplate** (R3) 
- **MyTeamUtilities** (R4)

    ![11](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)


## <a name="2-seed-your-projecttemplate-and-teamutilities-repositories"></a>2.ProjectTemplate および TeamUtilities リポジトリをシード処理する

このシード処理の手順では、ローカル DSVM 上のディレクトリを中間ステージング サイトとして使用します。 チームの特定のニーズに合わせて **ProjectTemplate** リポジトリと **TeamUtilities** リポジトリをカスタマイズする必要がある場合は、以下の手順の最後から 2 番目の手順でカスタマイズします。 データ サイエンス チームの **MyTeamProjectTemplate** リポジトリと **MyTeamUtilities** リポジトリの内容をシード処理する手順の概要を次に示します。 個々の手順は、シード処理の手順のサブセクションに対応しています。

- グループ リポジトリをローカル ディレクトリに複製する: チーム リポジトリ R1 をローカル ディレクトリ D1 に複製
- チーム リポジトリをローカル ディレクトリに複製する: チーム リポジトリ R3 と R4 をローカル ディレクトリ D3 と D4 に複製
- グループ プロジェクト テンプレートの内容をローカル チーム フォルダーにコピーする:D1 の内容を D3 にコピー
- (省略可能) ローカル ディレクトリ D3 と D4 をカスタマイズする
- ローカル ディレクトリの内容をチーム リポジトリにプッシュする:D3 と D4 のコンテンツをチーム R3 と R4 に追加


### <a name="initialize-the-team-repositories"></a>チーム リポジトリを初期化する

この手順では、グループ プロジェクト テンプレート リポジトリからプロジェクト テンプレート リポジトリを初期化します。

- **GroupProjectTemplate** (**R1**) リポジトリから **MyTeamProjectTemplate** (**R3**) リポジトリ


### <a name="clone-group-repositories-into-local-directories"></a>グループ リポジトリをローカル ディレクトリに複製する

この手順を開始するには、次の作業を行います。

- ローカル コンピューターにディレクトリを作成します。
    - **Windows** の場合:**C:\GitRepos\GroupCommon** と **C:\GitRepos\MyTeam** を作成
    - **Linux** の場合:ホーム ディレクトリに **GitRepos\GroupCommon** と **GitRepos\MyTeam** を作成 
- **GitRepos\GroupCommon** ディレクトリに変更します。
- ローカル コンピューターのオペレーティング システムで、必要に応じて次のコマンドを実行します。

**Windows**

    git clone https://<Your Azure DevOps Services name>.visualstudio.com/GroupCommon/_git/GroupProjectTemplate
    

![12](./media/team-lead-tasks/team-leads-12-create-two-group-repos.png)

**Linux**
    
    git clone ssh://<Your Azure DevOps Services name>@<Your Azure DevOps Services name>.visualstudio.com:22/GroupCommon/_git/GroupProjectTemplate
    
    
![13](./media/team-lead-tasks/team-leads-13-clone_two_group_repos_linux.png)

これらのコマンドは、グループの Azure DevOps Services 上の **GroupProjectTemplate** (R1) リポジトリを、ローカル コンピューター上の **GitRepos\GroupCommon** 内のローカル ディレクトリに複製します。 複製後、**GitRepos\GroupCommon** ディレクトリに **GroupProjectTemplate** (D1) ディレクトリが作成されます。 ここでは、グループ マネージャーが **GroupCommon** プロジェクトを作成したこと、および **GroupProjectTemplate** リポジトリがこのプロジェクトの下にあることを前提にしています。 


### <a name="clone-your-team-repositories-into-local-directories"></a>チーム リポジトリをローカル ディレクトリに複製する

これらのコマンドは、グループの Azure DevOps Services 上の **MyTeam** プロジェクトの下にある **MyTeamProjectTemplate** (R3) および **MyTeamUtilities** (R4) リポジトリを、ローカル コンピューター上の **GitRepos\MyTeam** 内の **MyTeamProjectTemplate** (D3) および **MyTeamUtilities** (D4) ディレクトリに複製します。 

- **GitRepos\MyTeam** ディレクトリに変更します。
- ローカル コンピューターのオペレーティング システムで、必要に応じて次のコマンドを実行します。 

**Windows**

    git clone https://<Your Azure DevOps Services name>.visualstudio.com/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone https://<Your Azure DevOps Services name>.visualstudio.com/<Your Team Name>/_git/MyTeamUtilities

![14](./media/team-lead-tasks/team-leads-14-clone_two_empty_team_repos.png)
        
**Linux**
    
    git clone ssh://<Your Azure DevOps Services name>@<Your Azure DevOps Services name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone ssh://<Your Azure DevOps Services name>@<Your Azure DevOps Services name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamUtilities
    
![15](./media/team-lead-tasks/team-leads-15-clone_two_empty_team_repos_linux.png)

複製後、**GitRepos\MyTeam** ディレクトリに **MyTeamProjectTemplate** (D3) と **MyTeamUtilities** (D4) の 2 つのディレクトリが作成されます。 ここでは、プロジェクト テンプレートとユーティリティ リポジトリに **MyTeamProjectTemplate** と **MyTeamUtilities** という名前が付けられたことを前提にしてきました。 

### <a name="copy-the-group-project-template-content-to-the-local-project-template-directory"></a>グループ プロジェクト テンプレートの内容をローカルのプロジェクト テンプレート ディレクトリにコピーする

ローカルの **GroupProjectTemplate** (D1) フォルダーの内容をローカルの **MyTeamProjectTemplate** (D3) にコピーするには、次のシェル スクリプトのいずれかを実行します。 

#### <a name="from-the-powershell-command-line-for-windows"></a>Windows の PowerShell コマンド ラインから実行       

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 2

    
![16](./media/team-lead-tasks/team-leads-16-local_copy_team_lead_new.png)

#### <a name="from-the-linux-shell-for-the-linux-dsvm"></a>**Linux DSVM** の Linux シェルから実行
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 2
    
![17](./media/team-lead-tasks/team-leads-17-local-copy-team-lead-linux-new.png)

これらのスクリプトでは、.git ディレクトリの内容は除外されます。 コピー元ディレクトリ D1 とコピー先ディレクトリ D3 の**完全なパス**を入力するよう求められます。
        

### <a name="customize-your-project-template-or-team-utilities-optional"></a>プロジェクト テンプレートまたはチーム ユーティリティをカスタマイズする (省略可能)

セットアップ プロセスのこの段階で、必要に応じて、**MyTeamProjectTemplate** (D3) と **MyTeamUtilities** (D4) をカスタマイズします。 

- チームの特定のニーズに合わせて D3 の内容をカスタマイズする場合は、テンプレート ドキュメントを変更するか、ディレクトリ構造を変更します。

- チーム全体で共有するユーティリティをチームが開発した場合は、それらのユーティリティをコピーし、D4 ディレクトリに貼り付けます。 


### <a name="push-local-directory-content-to-team-repositories"></a>ローカル ディレクトリの内容をチーム リポジトリにプッシュする

(必要に応じてカスタマイズした) ローカル ディレクトリ D3 と D4 の内容をチーム リポジトリ R3 と R4 に追加するには、Windows PowerShell コンソールまたは Linux シェルから次の Git Bash コマンドを実行します。 コマンドは、**GitRepos\MyTeam\MyTeamProjectTemplate** ディレクトリから実行します。

    git status
    git add .
    git commit -m"push from DSVM"
    git push
    
![18](./media/team-lead-tasks/team-leads-18-push-to-group-server-2.png)

このスクリプトが実行されると、グループの Azure DevOps Services の MyTeamProjectTemplate リポジトリ内のファイルがほぼ即座に同期されます。

![19](./media/team-lead-tasks/team-leads-19-push-to-group-server-showed-up.png)

次に、4 つの Git コマンドの同じセットを **GitRepos\MyTeam\MyTeamUtilities** ディレクトリから実行します。 

> [AZURE.NOTE]Git リポジトリに初めてコミットする場合は、`git commit` コマンドを実行する前に、グローバル パラメーター *user.name* と *user.email* を設定する必要があります。 次の 2 つのコマンドを実行します。
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> 複数の Git リポジトリにコミットする場合は、各リポジトリにコミットするときに同じ名前とメール アドレスを使用します。 同じ名前とメール アドレスを使用すると、後で PowerBI ダッシュボードを作成して複数のリポジトリの Git アクティビティを追跡するときに便利です。

![20](./media/team-lead-tasks/team-leads-20-git-config-name.png)


## <a name="3-create-team-data-and-analytics-resources-optional"></a>手順 3.チームのデータおよび分析リソースの作成 (省略可能)

データおよび分析リソースをチーム全体で共有すると、パフォーマンスとコストの面でメリットが得られます。チーム メンバーは、共有リソースでプロジェクトを実行し、予算を節約して、より効率的に共同作業を行うことができます。 このセクションでは、Azure File Storage を作成する方法を説明します。 次のセクションでは、Azure File Storage をローカル マシンにマウントする方法を説明します。 Azure データ サイエンス仮想マシンや Azure HDInsight Spark クラスターなど、他のリソースを共有する方法の詳細については、[プラットフォームとツール](platforms-and-tools.md)に関する記事をご覧ください。 このトピックでは、ニーズに適したリソースの選択に関するデータ サイエンスの観点からのガイダンスを提供し、公開されている製品ページと他の関連する便利なチュートリアルへのリンクを示します。

>[AZURE.NOTE] 低速でコストがかかる可能性のあるデータ センター間でのデータ転送を回避するために、リソース グループ、ストレージ アカウント、Azure VM (DSVM) が同じ Azure データ センターに存在することを確認してください。 

以下のスクリプトを実行して、チームの Azure File Storage を作成します。 チームの Azure File Storage を使用して、チーム全体にとって役立つデータ資産を保存できます。 各スクリプトでは、Azure アカウントとサブスクリプション情報の入力を求められるので、これらの資格情報を入力する準備をしておいてください。 

### <a name="create-azure-file-storage-with-powershell-from-windows"></a>Windows の PowerShell を使用して Azure File Storage を作成する

PowerShell コマンド ラインから次のスクリプトを実行します。

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
    .\CreateFileShare.ps1

![21](./media/team-lead-tasks/team-leads-21-create-fileshare-win.png)   

メッセージが表示されたら、Microsoft Azure アカウントにログインします。

![22](./media/team-lead-tasks/team-leads-22-file-create-s1.png)

使用する Azure サブスクリプションを選択します。

![23](./media/team-lead-tasks/team-leads-23-file-create-s2.png)

使用するストレージ アカウントを選択するか、選択したサブスクリプションの新しいストレージ アカウントを作成します。

![24](./media/team-lead-tasks/team-leads-24-file-create-s3.png)

作成する Azure File Storage の名前を入力します。 名前には、小文字、数字、- のみをご利用いただけます。

![25](./media/team-lead-tasks/team-leads-25-file-create-s4.png)

このストレージの作成後にストレージのマウントと共有を容易にするために、Azure File Storage の情報をテキスト ファイルに保存し、ファイルの場所へのパスを書き留めておきます。 具体的には、次のセクションで Azure File Storage を Azure 仮想マシンにマウントするときに、このファイルが必要になります。 

このテキスト ファイルを ProjectTemplate リポジトリにチェックインすることをお勧めします。 **Docs\DataDictionaries** ディレクトリに配置することが推奨されます。 これにより、チームのすべてのプロジェクトからこのデータ資産にアクセスできます。 

![26](./media/team-lead-tasks/team-leads-26-file-create-s5.png)


### <a name="create-azure-file-storage-with-a-linux-script"></a>Linux スクリプトを使用して Azure File Storage を作成する

Linux シェルから次のスクリプトを実行します。

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
    bash CreateFileShare.sh

この画面の指示に従って、Microsoft Azure アカウントにログインします。

![27](./media/team-lead-tasks/team-leads-27-file-create-linux-s1.png)

使用する Azure サブスクリプションを選択します。

![28](./media/team-lead-tasks/team-leads-28-file-create-linux-s2.png)

使用するストレージ アカウントを選択するか、選択したサブスクリプションの新しいストレージ アカウントを作成します。

![29](./media/team-lead-tasks/team-leads-29-file-create-linux-s3.png)

作成する Azure File Storage の名前を入力します。名前には、小文字、数字、- のみをご利用いただけます。

![30](./media/team-lead-tasks/team-leads-30-file-create-linux-s4.png)

このストレージの作成後にストレージへのアクセスを容易にするために、Azure File Storage の情報をテキスト ファイルに保存し、ファイルの場所へのパスを書き留めておきます。 具体的には、次のセクションで Azure File Storage を Azure 仮想マシンにマウントするときに、このファイルが必要になります。

このテキスト ファイルを ProjectTemplate リポジトリにチェックインすることをお勧めします。 **Docs\DataDictionaries** ディレクトリに配置することが推奨されます。 これにより、チームのすべてのプロジェクトからこのデータ資産にアクセスできます。 

![31](./media/team-lead-tasks/team-leads-31-file-create-linux-s5.png)


## <a name="4-mount-azure-file-storage-optional"></a>4.Azure File Storage をマウントする (省略可能)

Azure File Storage が正常に作成されたら、次の PowerShell スクリプトまたは Linux スクリプトのいずれかを使用して、ローカル マシンにマウントできます。

### <a name="mount-azure-file-storage-with-powershell-from-windows"></a>Windows の PowerShell を使用して Azure File Storage をマウントする

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
    .\AttachFileShare.ps1
    
まだログインしていない場合は、まずログインするよう求められます。 

Azure File Storage の情報ファイルがあるかどうかをたずねられたら、**Enter** キーまたは **y** キーを押して続行し、前の手順で作成したファイルの**完全なパスと名前**を入力します。 Azure File Storage をマウントするための情報がそのファイルから直接読み取られ、次の手順に進む準備が整います。

![32](./media/team-lead-tasks/team-leads-32-attach-s1.png)

> [AZURE.NOTE] Azure File Storage の情報が含まれたファイルがない場合は、このセクションの最後に、キーボードからこの情報を入力する手順が示されています。

次に、仮想マシンに追加するドライブの名前を入力するよう求められます。 既存のドライブ名の一覧が画面に出力されます。 一覧に存在しないドライブ名を指定する必要があります。

![33](./media/team-lead-tasks/team-leads-33-attach-s2.png)

新しい F ドライブがマシンに正常にマウントされていることを確認します。

![34](./media/team-lead-tasks/team-leads-34-attach-s3.png)

**Azure File Storage の情報を手動で入力する方法:** Azure File Storage の情報がテキスト ファイルに保存されていない場合は、次の画面の指示に従って、必要なサブスクリプション、ストレージ アカウント、Azure File Storage の情報を入力できます。

![35](./media/team-lead-tasks/team-leads-35-attach-s4.png)

Azure サブスクリプション名を入力し、Azure File Storage が作成されているストレージ アカウントを選択して、Azure File Storage の名前を入力します。

![36](./media/team-lead-tasks/team-leads-36-attach-s5.png)

### <a name="mount-azure-file-storage-with-a-linux-script"></a>Linux スクリプトを使用して Azure File Storage をマウントする

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
    bash AttachFileShare.sh

![37](./media/team-lead-tasks/team-leads-37-attach-s1-linux.png)

まだログインしていない場合は、まずログインするよう求められます。 

Azure File Storage の情報ファイルがあるかどうかをたずねられたら、**Enter** キーまたは **y** キーを押して続行し、前の手順で作成したファイルの**完全なパスと名前**を入力します。 Azure File Storage をマウントするための情報がそのファイルから直接読み取られ、次の手順に進む準備が整います。

![38](./media/team-lead-tasks/team-leads-38-attach-s2-linux.png)

次に、仮想マシンに追加するドライブの名前を入力するよう求められます。 既存のドライブ名の一覧が画面に出力されます。 一覧に存在しないドライブ名を指定する必要があります。

![39](./media/team-lead-tasks/team-leads-39-attach-s3-linux.png)

新しい F ドライブがマシンに正常にマウントされていることを確認します。

![40](./media/team-lead-tasks/team-leads-40-attach-s4-linux.png)

**Azure File Storage の情報を手動で入力する方法:** Azure File Storage の情報がテキスト ファイルに保存されていない場合は、次の画面の指示に従って、必要なサブスクリプション、ストレージ アカウント、Azure File Storage の情報を入力できます。

- 入力 **n**。
- 前の手順で Azure File Storage が作成されたサブスクリプション名のインデックスを選択します。

    ![41](./media/team-lead-tasks/team-leads-41-attach-s5-linux.png)

- サブスクリプションのストレージ アカウントを選択し、Azure File Storage の名前を入力します。

    ![42](./media/team-lead-tasks/team-leads-42-attach-s6-linux.png)

- マシンに追加するドライブの名前を入力します。既存のドライブ名と異なる名前を入力する必要があります。

    ![43](./media/team-lead-tasks/team-leads-43-attach-s7-linux.png)


## <a name="5-set-up-security-control-policy"></a>5.セキュリティ制御ポリシーの設定 

グループの Azure DevOps Services のホームページから、右上隅のユーザー名の横にある**歯車アイコン**をクリックし、**[セキュリティ]** タブを選択します。ここでは、メンバーをチームに追加してさまざまなアクセス許可を適用できます。

![44](./media/team-lead-tasks/team-leads-44-add-team-members.png)

## <a name="next-steps"></a>次の手順

Team Data Science Process で定義されている役割とタスクの詳細な説明へのリンクを次に示します。

- [データ サイエンス チームのグループ マネージャーのタスク](group-manager-tasks.md)
- [データ サイエンス チームのチーム リーダーのタスク](team-lead-tasks.md)
- [データ サイエンス チームのプロジェクト リーダーのタスク](project-lead-tasks.md)
- [データ サイエンス チームの個々の共同作成者](project-ic-tasks.md)
