---
title: Team Data Science Process のグループ マネージャーのタスク
description: データ サイエンス チーム プロジェクトのグループ マネージャーのタスクの概要を説明します。
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: fb0482be1670a96befdd69a5356c9e21476d9f9f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55458144"
---
# <a name="tasks-for-a-group-manager-on-a-data-science-team-project"></a>データ サイエンス チーム プロジェクトのグループ マネージャーのタスク

このトピックでは、グループ マネージャーがデータ サイエンス組織のために遂行する必要があるタスクの概要を説明します。 [Team Data Science Process](overview.md) (TDSP) を標準とするグループ コラボレーション環境を確立することを目標とします。 このプロセスを標準とするデータ サイエンス チーム メンバーの役割とそれに関連したタスクの概要については、「[Team Data Science Process Roles and tasks (Team Data Science Process での役割とタスク)](roles-tasks.md)」をご覧ください。

**グループ マネージャー**とは、企業のデータ サイエンス ユニット全体のマネージャーのことです。 データ サイエンス ユニット内には複数のチームがあり、それぞれのチームが、異なるビジネスの複数のデータ サイエンス プロジェクトに携わっている場合があります。 グループ マネージャーは、そのタスクを代理人に委任する場合もありますが、グループ マネージャーの役割に関連付けられているタスクに変わりはありません。 次の図に示すように、6 つの主要なタスクがあります。

![0](./media/group-manager-tasks/tdsp-group-manager.png)


> [!NOTE] 
> 以下の方法で Azure DevOps Services を使用して TDSP グループ環境を設定するために必要な手順の概要について説明します。 Azure DevOps Services でこれらのタスクを達成する方法を指定するのは、それが Microsoft で TDSP を実装する方法であるためです。 グループで別のコード ホスティング プラットフォームを使用している場合も、一般にグループ マネージャーが実行する必要があるタスクは変わりません。 ただし、これらのタスクを実行する方法が異なります。

1. グループの **Azure DevOps Services** を設定します。
2. Azure DevOps Services 上の (Azure DevOps Services ユーザーのための) **グループ プロジェクト**を作成します。
3. **GroupProjectTemplate** リポジトリを作成します。
4. **GroupUtilities** リポジトリを作成します。
5. Azure DevOps Services の **GroupProjectTemplate** および **GroupUtilities** リポジトリを TDSP リポジトリの内容でシード処理します。
6. チーム メンバーの GroupProjectTemplate および GroupUtilities リポジトリへのアクセスに対する**セキュリティ制御**を設定します。

この後、上記の各手順について説明します。 その前に、省略名と、リポジトリを操作するための前提条件を説明します。

### <a name="abbreviations-for-repositories-and-directories"></a>リポジトリとディレクトリの省略名

このチュートリアルでは、リポジトリとディレクトリの省略名を使用します。 これらの定義により、リポジトリとディレクトリ間での操作がわかりやすくなります。 以下のセクションでは、次の表記を使用します。

- **G1**:Microsoft の TDSP チームが開発および管理する、プロジェクト テンプレートのリポジトリ。
- **G2**:Microsoft の TDSP チームが開発および管理する、ユーティリティ リポジトリ。
- **R1**:Azure DevOps グループ サーバーで設定する Git 上の GroupProjectTemplate リポジトリ。
- **R2**:Azure DevOps グループ サーバーで設定する Git 上の GroupUtilities リポジトリ。
- **LG1** と **LG2**:G1 と G2 をそれぞれ複製する、マシン上のローカル ディレクトリ。
- **LR1** と **LR2**:R1 と R2 をそれぞれ複製する、マシン上のローカル ディレクトリ。

### <a name="pre-requisites-for-cloning-repositories-and-checking-code-in-and-out"></a>リポジトリの複製と、コードのチェックインおよびチェックアウトの前提条件

- マシンに Git をインストールする必要があります。 データ サイエンス仮想マシン (DSVM) を使用している場合は、Git がプレインストールされているので、インストールは不要です。 それ以外の場合は、[プラットフォームとツールに関する記事の付録](platforms-and-tools.md#appendix)をご覧ください。
- **Windows DSVM** を使用している場合は、マシンに [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) がインストールされている必要があります。 README.md ファイルで、下へスクロールして "**Download and Install**" セクションを表示し、"*latest installer*" をクリックします。 この手順により、最新のインストーラーのページに移動します。 このページから .exe インストーラーをダウンロードして実行します。
- **Linux DSVM** を使用している場合は、DSVM で SSH 公開キーを作成し、それをグループの Azure DevOps Services に追加します。 SSH の詳細については、[プラットフォームとツールに関する記事の付録](platforms-and-tools.md#appendix)の **SSH 公開キーの作成**に関するセクションをご覧ください。


## <a name="1-create-account-on-azure-devops-services"></a>1.Azure DevOps Services 上のアカウントを作成する

Azure DevOps Services は、次のリポジトリをホストします。

- **グループ共通リポジトリ**:複数のデータ サイエンス プロジェクトに取り組むグループ内の、複数のチームに適用できる汎用リポジトリ。 たとえば、*GroupProjectTemplate* リポジトリや *GroupUtilities* リポジトリなどです。
- **チーム リポジトリ**:グループ内の特定のチーム向けリポジトリ。 これらはチームのニーズに固有のリポジトリであり、該当チームの実行する複数のプロジェクトには適用できますが、データ サイエンス グループ内の複数のチームで使用できるほどの汎用性はありません。
- **プロジェクト リポジトリ**:特定のプロジェクトで使用できるリポジトリ。 このようなリポジトリは、チームが実行する複数のプロジェクトや、データ サイエンス グループ内の複数のチームで使用できるほどの汎用性はない場合があります。


### <a name="setting-up-the-azure-devops-services-sign-into-your-microsoft-account"></a>Microsoft アカウントへの Azure DevOps Services のサインインの設定

[Visual Studio Online](https://www.visualstudio.com/) にアクセスし、右上隅の **[サインイン]** をクリックして、ご利用の Microsoft アカウントにサインインします。

![1](./media/group-manager-tasks/login.PNG)

Microsoft アカウントがない場合は、**[Sign up now]\(今すぐサインアップ\)** をクリックして Microsoft アカウントを作成した後に、このアカウントを使用してサインインします。

組織で Visual Studio または MSDN サブスクリプションを利用している場合は、緑色の囲み罫線内の **[職場または学校アカウントでサインインする]** をクリックし、このサブスクリプションに関連付けられている資格情報を使用してサインインします。

![2](./media/group-manager-tasks/signin.PNG)



サインインしたら、次の画像に示すように、右上隅の **[新しいアカウントの作成]** をクリックします。

![3](./media/group-manager-tasks/create-account-1.PNG)

**[Create your account]\(アカウントの作成)** ウィザードで、作成する Azure DevOps Services に関する情報として次の値を入力します。

- **サーバー URL**:*mysamplegroup* を、独自の*サーバー名*に置き換えます。 サーバーの URL は、*https://\<servername\>.visualstudio.com* になります。
- **コードの管理に次を使用:****_[Git]_** を選択します。
- **プロジェクト名:**「*GroupCommon*」と入力します。
- **以下を使用して作業を整理します:***[アジャイル]* を選択します。
- **プロジェクトをホストする地域:** 地理的な場所を選択します。 この例では、*[米国中南部]* を選択しています。

![4](./media/group-manager-tasks/fill-in-account-information.png)

> [!NOTE] 
> **[新しいアカウントの作成]** をクリックした後に次のポップアップ ウィンドウが表示された場合は、**[詳細の変更]** をクリックして、すべての項目のフィールドを表示する必要があります。

![5](./media/group-manager-tasks/create-account-2.png)


**[続行]** をクリックします。

## <a name="2-groupcommon-project"></a>2.GroupCommon プロジェクト

Azure DevOps Services が作成されると、**GroupCommon** ページ (*https://\<servername\>.visualstudio.com/GroupCommon*) が開きます。

![6](./media/group-manager-tasks/server-created-2.PNG)

## <a name="3-create-the-grouputilities-r2-repository"></a>手順 3.GroupUtilities (R2) リポジトリを作成する

Azure DevOps Services の下に **GroupUtilities** (R2) リポジトリを作成するには:

- **[新しいリポジトリの作成]** ウィザードを開くには、プロジェクトの **[バージョン管理]** タブで **[新しいリポジトリ]** をクリックします。

  ![7](./media/group-manager-tasks/create-grouputilities-repo-1.png)

- **[種類]** に *[Git]* を選択し、**[名前]** に「*GroupUtilities*」と入力して、**[作成]** をクリックします。

  ![8](./media/group-manager-tasks/create-grouputilities-repo-2.png)

**[バージョン コントロール]** ページの左の列に、**GroupProjectTemplate** と **GroupUtilities** の、2 つの Git リポジトリが表示されます。

![9](./media/group-manager-tasks/two-repo-under-groupCommon.PNG)


## <a name="4-create-the-groupprojecttemplate-r1-repository"></a>4.GroupProjectTemplate (R1) リポジトリを作成する

Azure DevOps グループ サーバーのリポジトリの設定は、次の 2 つのタスクで構成されています。

- 既定の **GroupCommon** リポジトリの名前を ***GroupProjectTemplate*** に変更します。
- Azure DevOps Services 上の **GroupCommon** プロジェクトの下に **GroupUtilities** リポジトリを作成します。

1 つ目のタスクに関しては、名前付け規則、リポジトリおよびディレクトリについて、このセクションで説明されています。 2 つ目のタスクの説明は、手順 4 の後のセクションに記載されています。

### <a name="rename-the-default-groupcommon-repository"></a>既定の GroupCommon リポジトリの名前を変更する

次の手順を実行して、既定の **GroupCommon** リポジトリの名前を *GroupProjectTemplate* (このチュートリアルでは **R1** と呼ばれます) に変更します。

- **[GroupCommon]** プロジェクト ページで **[コードで共同作業]** をクリックします。 これにより、**GroupCommon** プロジェクトの既定の Git リポジトリ ページが表示されます。 現時点では、この Git リポジトリは空です。

  ![10](./media/group-manager-tasks/rename-groupcommon-repo-3.png)

- **GroupCommon** のGit リポジトリ ページで、左上隅の **[GroupCommon]** (次の図では赤い囲み罫線で強調されている) をクリックして、**[リポジトリの管理]** (次の図では緑色の囲み罫線で強調されている) を選択します。 この手順により、**コントロール パネル**が開きます。
- プロジェクトの **[バージョン管理]** タブを選択します。

  ![11](./media/group-manager-tasks/rename-groupcommon-repo-4.png)

- 左側のパネルで、**GroupCommon** リポジトリの右側の **[...]** をクリックし、**[リポジトリの名前変更]** を選択します。

  ![12](./media/group-manager-tasks/rename-groupcommon-repo-5.png)

- ポップ アップ表示された **[Rename the GroupCommon repository]\(GroupCommon リポジトリの名前変更\)** ウィザードで、**[リポジトリ名]** ボックスに「*GroupProjectTemplate*」と入力してから **[名前の変更]** をクリックします。

  ![13](./media/group-manager-tasks/rename-groupcommon-repo-6.png)



## <a name="5-seed-the-r1--r2-repositories-on-the-azure-devops-services"></a>5.Azure DevOps Services 上の R1 および R2 リポジトリをシード処理する

この手順では、上記セクションで設定した *GroupProjectTemplate* (R1) リポジトリと *GroupUtilities* (R2) リポジトリをシード処理します。 これらのリポジトリは、Microsoft が Team Data Science Process 用に管理する、***ProjectTemplate*** (**G1**) および ***Utilities*** (**G2**) リポジトリを使用してシード処理されます。 このシード処理が完了すると、

- R1 リポジトリには、G1 と同じディレクトリとドキュメント テンプレートのセットが含まれるようになります。
- R2 リポジトリには、Microsoft が開発したデータ サイエンス ユーティリティのセットが含まれるようになります。

このシード処理の手順では、ローカル DSVM 上のディレクトリを中間ステージング サイトとして使用します。 このセクションで実行する手順を次に示します。

- G1 と G2 を LG1 と LG2 に複製する
- R1 と R2 を LR1 と LR2 に複製する
- LG1 と LG2 のファイルを LR1 と LR2 にコピーする
- (省略可能) LR1 と LR2 をカスタマイズする
- LR1 と LR2 の内容を R1 と R2 に追加する


### <a name="clone-g1--g2-repositories-to-your-local-dsvm"></a>G1 および G2 リポジトリをローカル DSVM に複製する

この手順では、Team Data Science Process (TDSP) の ProjectTemplate リポジトリ (G1) と Utilities (G2) を、TDSP の GitHub リポジトリからローカル DSVM のフォルダーに、LG1 と LG2 として複製します。

- リポジトリのすべての複製をホストするルート ディレクトリとして機能する、ディレクトリを作成します。
  -  Windows DSVM の場合は、ディレクトリ *C:\GitRepos\TDSPCommon* を作成します。
  -  Linux DSVM の場合は、ホーム ディレクトリにディレクトリ *GitRepos\TDSPCommon* を作成します。

- *GitRepos\TDSPCommon* ディレクトリから、次のコマンド セットを実行します。

  `git clone https://github.com/Azure/Azure-TDSP-ProjectTemplate`<br>
  `git clone https://github.com/Azure/Azure-TDSP-Utilities`

  ![14](./media/group-manager-tasks/two-folder-cloned-from-TDSP-windows.PNG)

- 上記スクリプトで達成された内容をリポジトリの省略名を使用して表すと、次のとおりです。
    - G1 を LG1 に複製する
    - G2 を LG2 に複製する
- 複製が完了すると、**GitRepos\TDSPCommon** ディレクトリに _ProjectTemplate_ と _Utilities_ の 2 つのディレクトリが表示されます。

### <a name="clone-r1--r2-repositories-to-your-local-dsvm"></a>R1 および R2 リポジトリをローカル DSVM に複製する

この手順では、GroupProjectTemplate リポジトリ (R1) と GroupUtilities リポジトリ (R2) を、DSVM 上の **GitRepos\GroupCommon** のローカル ディレクトリ (それぞれ LR1 と LR2 と呼ばれます) に複製します。

- R1 および R2 リポジトリの URL を取得するには、Azure DevOps Services 上の **GroupCommon** ホーム ページに移動します。 ここには通常、*https://\<Azure DevOps Services 名\>.visualstudio.com/GroupCommon* という URL が含まれています。
- **[コード]** をクリックします。
- **GroupProjectTemplate** リポジトリと **GroupUtilities** リポジトリを選択します。 **[クローン URL]** の要素から各 URL (Windows の場合は HTTPS、Linux の場合は SSH) をコピーして保存し、次のスクリプトで使用します。

  ![15](./media/group-manager-tasks/find_https_ssh_2.PNG)

- Windows または Linux DSVM 上の **GitRepos\GroupCommon** ディレクトリに移動し、次のコマンド セットのいずれかを実行して、このディレクトリに R1 と R2 を複製します。

Windows と Linux のスクリプトを次に示します。

    # Windows DSVM

    git clone <the HTTPS URL of the GroupProjectTemplate repository>
    git clone <the HTTPS URL of the GroupUtilities repository>

![16](./media/group-manager-tasks/clone-two-empty-group-reo-windows-2.PNG)

    # Linux DSVM

    git clone <the SSH URL of the GroupProjectTemplate repository>
    git clone <the SSH URL of the GroupUtilities repository>

![17](./media/group-manager-tasks/clone-two-empty-group-reo-linux-2.PNG)

> [!NOTE] 
> LR1 と LR2 が空であることを警告するメッセージが表示されます。

- 上記スクリプトで達成された内容をリポジトリの省略名を使用して表すと、次のとおりです。
    - R1 を LR1 に複製する
    - R2 を LR2 に複製する   


### <a name="seed-your-groupprojecttemplate-lr1-and-grouputilities-lr2"></a>GroupProjectTemplate (LR1) と GroupUtilities (LR2) をシード処理する

次に、ローカル コンピューターで、GitRepos\TDSPCommon の ProjectTemplate ディレクトリと Utilities ディレクトリの内容 (.git ディレクトリのメタデータを除く) を、**GitRepos\GroupCommon** の GroupProjectTemplate ディレクトリと GroupUtilities ディレクトリにコピーします。 このステップで実行するタスクは次の 2 つです。

- GitRepos\TDSPCommon\ProjectTemplate (**LG1**) 内のファイルを、GitRepos\GroupCommon\GroupProjectTemplate (**LR1**) にコピーします。
- GitRepos\TDSPCommon\Utilities (**LG2**) 内のファイルを、GitRepos\GroupCommon\Utilities (**LR2**) にコピーします。

これら 2 つのタスクを実行するには、PowerShell コンソール (Windows) またはシェル スクリプト コンソール (Linux) で次のスクリプトを実行します。 LG1、LR1、LG2、および LR2 への完全なパスの入力を求められます。 入力したパスが検証されます。 存在しないディレクトリを入力すると、入力し直すように要求されます。

    # Windows DSVM
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 1

![18](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows-2.PNG)

LG1 ディレクトリと LG2 ディレクトリ内のファイル (.git ディレクトリのファイルを除く) が、LR1 と LR2 にそれぞれコピーされたことを確認できます。

![19](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows.PNG)

    # Linux DSVM

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 1

![20](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux-2.PNG)

2 つのフォルダー内のファイル (.git ディレクトリのファイルを除く) が、GroupProjectTemplate と GroupUtilities にそれぞれコピーされていることを確認できます。

![21](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux.PNG)

- 上記スクリプトで達成された内容をリポジトリの省略名を使用して表すと、次のとおりです。
    - LG1 のファイルを LR1 にコピーする
    - LG2 のファイルを LR2 にコピーする

### <a name="option-to-customize-the-contents-of-lr1--lr2"></a>LR1 と LR2 の内容をカスタマイズするオプション
    
グループの特定のニーズに対応するために LR1 と LR2 の内容をカスタマイズする場合は、この手順の段階で実行するのが適切です。 テンプレート ドキュメントを変更したり、ディレクトリ構造を変更したり、グループが開発した既存のユーティリティ、またはグループ全体に役立つ既存のユーティリティを追加したりできます。

### <a name="add-the-contents-in-lr1--lr2-to-r1--r2-on-group-server"></a>LR1 と LR2 の内容を、グループ サーバーの R1 と R2 に追加する

LR1 と LR2 の内容を、R1 リポジトリと R2 リポジトリに追加する必要があります。 Windows PowerShell と Linux のいずれでも実行できる Git Bash コマンドは次のとおりです。

GitRepos\GroupCommon\GroupProjectTemplate ディレクトリから次のコマンドを実行します。

    git status
    git add .
    git commit -m"push from DSVM"
    git push

-m オプションを使用して、Git コミットのメッセージを設定できます。

![22](./media/group-manager-tasks/push-to-group-server-2.PNG)

グループの Azure DevOps Services の GroupProjectTemplate リポジトリではファイルが即座に同期されることを確認できます。

![23](./media/group-manager-tasks/push-to-group-server-showed-up-2.PNG)

最後に、**GitRepos\GroupCommon\GroupUtilities** ディレクトリに移動して、同じセットの Git Bash コマンドを実行します。

    git status
    git add .
    git commit -m"push from DSVM"
    git push

> [!NOTE] 
> Git リポジトリに初めてコミットする場合は、`git commit` コマンドを実行する前に、グローバル パラメーター *user.name* と *user.email* を構成する必要があります。 次の 2 つのコマンドを実行します。
>
>  `git config --global user.name <your name>`  
>  `git config --global user.email <your email address>`
>
> 複数の Git リポジトリにコミットする場合は、各リポジトリにコミットするときに同じ名前とメール アドレスを使用します。 同じ名前とメール アドレスを使用すると、後で PowerBI ダッシュボードを作成して複数のリポジトリの Git アクティビティを追跡するときに便利です。


- 上記スクリプトで達成された内容をリポジトリの省略名を使用して表すと、次のとおりです。
    - LR1 の内容を R1 に追加する
    - LR2 の内容を R2 に追加する

## <a name="6-add-group-members-to-the-group-server"></a>6.グループ メンバーをグループ サーバーに追加する

グループの Azure DevOps Services のホームページから、右上隅のユーザー名の横にある**歯車アイコン**をクリックし、**[セキュリティ]** タブを選択します。ここでは、メンバーをグループに追加してさまざまなアクセス許可を適用できます。

![24](./media/group-manager-tasks/add_member_to_group.PNG)


## <a name="next-steps"></a>次の手順

Team Data Science Process で定義されている役割とタスクの詳細な説明へのリンクを次に示します。

- [データ サイエンス チームのグループ マネージャーのタスク](group-manager-tasks.md)
- [データ サイエンス チームのチーム リーダーのタスク](team-lead-tasks.md)
- [データ サイエンス チームのプロジェクト リーダーのタスク](project-lead-tasks.md)
- [データ サイエンス チームの個々の共同作成者](project-ic-tasks.md)
