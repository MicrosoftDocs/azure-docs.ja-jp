---
title: "Team Data Science Process のプロジェクト リーダーのタスク - Azure | Microsoft Docs"
description: "データ サイエンス チーム プロジェクトのプロジェクト リーダーのタスクの概要を説明します。"
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev;
ms.openlocfilehash: ed3dc8d441989239f02e12231f06d22fbef9d3dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="project-lead-tasks"></a>プロジェクト リーダーのタスク

このチュートリアルでは、プロジェクト リーダーが、プロジェクト チームのために実行するべきことについて説明します。 目的は、[Team Data Science Process](overview.md) (TDSP) を標準とするチーム コラボレーション環境を確立することです。 TDSP は、Microsoft によって開発されたフレームワークであり、クラウドベースの予測分析ソリューションを効率的に実行するための体系化された一連のアクティビティを提供します。 このプロセスを標準とするデータ サイエンス チーム メンバーの役割とそれに関連したタスクの概要については、「[Team Data Science Process Roles and tasks (Team Data Science Process での役割とタスク)](roles-tasks.md)」をご覧ください。

**プロジェクト リーダー**は、特定のデータ サイエンス プロジェクトに関する個々 のデータ サイエンティストの毎日のアクティビティを管理します。 この環境を設定するためにプロジェクト リーダーが実行するタスクのワークフローを次の図に示します。

![1](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

現在、このトピックでは、プロジェクト リーダーの、このワークフローのタスク 1、2、6 について説明します。

>[AZURE.NOTE] ここでは、Visual Studio Team Services (VSTS) を使用してプロジェクトの TDSP チーム環境を設定するために必要な手順の概要を説明します。 VSTS を使用してこれらのタスクを達成する方法を示しているのは、Microsoft がこの方法で TDSP を実装しているためです。 グループで別のコード ホスティング プラットフォームを使用している場合も、一般にチーム リーダーが実行する必要があるタスクは変わりません。 ただし、これらのタスクを実行する方法が異なります。


## <a name="repositories-and-directories"></a>リポジトリとディレクトリ

このチュートリアルでは、リポジトリとディレクトリの省略名を使用します。 これらの名前により、リポジトリとディレクトリ間での操作がわかりやすくなります。 以降のセクションでは次の表記を使用します (R は Git リポジトリを表し、D は DSVM 上のローカル ディレクトリを表します)。

- **R3**: チーム リーダーが設定する Git の **ProjectTemplate** チーム リポジトリ。
- **R5**: プロジェクトに設定する Git 上のプロジェクト リポジトリ。
- **D3**: R3 から複製されたローカル ディレクトリ。
- **D5**: R5 から複製されたローカル ディレクトリ。


## <a name="0-prerequisites"></a>0.前提条件

前提条件を満たすには、[データ サイエンス チームのグループ マネージャーのタスク](group-manager-tasks.md)で説明されている、グループ マネージャーに割り当てられたタスクと、[データ サイエンス チームのチーム リーダーのタスク](team-lead-tasks.md)で説明されているチーム リーダーに割り当てられたタスクを完了します。 

まとめると、チーム リーダーのタスクを開始する前に、次の要件を満たす必要があります。 

- グループ マネージャーによって、**グループの VSTS サーバー** (または他のコード ホスティング プラットフォームのグループ アカウント) が設定されている。
- 使用する予定のコード ホスティング プラットフォームで、チーム リーダーによって、グループ アカウントに **TeamProjectTemplate リポジトリ** (R3) が設定されている。
- グループ アカウントにチームのリポジトリを作成することが、チーム リーダーによって**承認**されている。
- マシンに Git をインストールする必要があります。 データ サイエンス仮想マシン (DSVM) を使用している場合は、Git がプレインストールされているので、インストールは不要です。 それ以外の場合は、[プラットフォームとツールに関する記事の付録](platforms-and-tools.md#appendix)をご覧ください。  
- **Windows DSVM** を使用している場合は、マシンに [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) がインストールされている必要があります。 README.md ファイルで、下へスクロールして「**Download and Install**」(ダウンロードとインストール) セクションを表示し、「*latest installer*」(最新のインストーラー) をクリックします。 これにより、最新のインストーラーのページに移動します。 このページから .exe インストーラーをダウンロードして実行します。 
- **Linux DSVM** を使用している場合は、DSVM で SSH 公開キーを作成し、グループの VSTS サーバーに追加します。 SSH の詳細については、[プラットフォームとツールに関する記事の付録](platforms-and-tools.md#appendix)の **SSH 公開キーの作成**に関するセクションをご覧ください。 


## <a name="1-create-a-project-repository-r5"></a>1.プロジェクト リポジトリ (R5) を作成します。

- *https://\<VSTS サーバー名\>. visualstudio.com* にあるグループ VSTS サーバーにログインします。 
- **[最近使ったプロジェクトとチーム]** にある、**[参照]** をクリックします。 ポップアップ表示されるウィンドウに、VSTS サーバー上のすべてのチーム プロジェクトが一覧表示されます。 

    ![2](./media/project-lead-tasks/project-leads-2-create-project-repo.png)

- プロジェクト リポジトリを作成するチーム プロジェクト名をクリックします。 この例では、**[MyTeam]** をクリックします。 
- **[移動]** をクリックすると、チーム プロジェクト **MyTeam** のホーム ページに移動します。

    ![3](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)

- **[コードで共同作業]** をクリックすると、チーム プロジェクトの Git のホーム ページに転送されます。  

    ![4](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

- 左上隅にある下向きの矢印をクリックして、**[新しいリポジトリ]** を選択します。 
    
    ![5](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)

- **[新しいリポジトリの作成]** ウィンドウに、プロジェクトの Git リポジトリの名前を入力します。 リポジトリの種類として **[Git]** を選択してください。 この例では、*DSProject1* という名前を使用します。 

    ![6](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

- ***DSProject1*** プロジェクトの Git リポジトリを作成するには、**[作成]** をクリックします。


## <a name="2-seed-the-dsproject1-project-repository"></a>2.DSProject1 プロジェクト リポジトリをシードします。

ここで、タスクは、チーム プロジェクト テンプレート リポジトリ (R3) から **DSProject1** プロジェクト リポジトリ (R5) をシード処理することです。 このシード処理の手順では、ローカル DSVM 上のディレクトリ D3 および D5 を中間ステージング サイトとして使用します。 要約すると、シード処理のパスは R3 -> D3 -> D5 -> R5 です。

プロジェクトの特定のニーズに合わせて **DSProject1** リポジトリをカスタマイズする必要がある場合は、以下の手順の最後から 2 番目の手順でカスタマイズします。 **DSProject1** プロジェクトのリポジトリのコンテンツをシードに使用する手順の概要を、ここに示します。 各手順は、シード処理の手順のサブセクションに対応しています。

- ローカル ディレクトリに、チームのプロジェクト テンプレート リポジトリを複製します: チーム R3 を ローカル D3 に複製。
- ローカル ディレクトリ に、DSProject1 リポジトリを複製します: チーム R5 を ローカル D5 に複製。
- 複製されたチーム プロジェクト テンプレートのコンテンツを DSProject1 リポジトリのローカル複製にコピーします: D3 コンテンツを D5 にコピー。
- (省略可能) ローカル D5 のカスタマイズ
- ローカル DSProject1 のコンテンツをチーム リポジトリにプッシュします: D5 のコンテンツをチーム R5 に追加します。


### <a name="clone-your-team-project-template-repository-r3-to-a-directory-d3-on-your-local-machine"></a>チーム プロジェクト テンプレート リポジトリ (R3) を、ローカル コンピューター上のディレクトリ (D3) に複製します。

ローカル コンピューターにディレクトリを作成します。

- Windows の場合、*C:\GitRepos\MyTeamCommon* です 
- Linux の場合、*$home/GitRepos/MyTeamCommon* です

そのディレクトリを変更します。 次に、次のコマンドを実行して、チーム プロジェクト テンプレート リポジトリをローカル コンピューターに複製します。 

**Windows**
            
    git clone <the HTTPS URL of the TeamProjectTemplate repository>
    
VSTS をコード ホスト プラットフォームとして使用している場合、*チーム プロジェクトのテンプレート リポジトリの HTTPS URL* は、通常、次のようになります。

 ***https://\<VSTS サーバー名\>.visualstudio.com/\<、チーム プロジェクト名\>/_git/\<、チーム プロジェクトのテンプレートのリポジトリ名\>***。 

この例では、

***https://mysamplegroup.visualstudio.com/MyTeam/_git/MyTeamProjectTemplate*** です。 

![7](./media/project-lead-tasks/project-leads-7-clone-team-project-template.png)
            
**Linux**

    git clone <the SSH URL of the TeamProjectTemplate repository>
        
![8](./media/project-lead-tasks/project-leads-8-clone-team-project-template-linux.png)

VSTS をコード ホスト プラットフォームとして使用している場合は、*チーム プロジェクトのテンプレート リポジトリの SSH URL* は、通常、以下のようになります。

***ssh://\<VSTS サーバー名\>@\<VSTS サーバー名\>.visualstudio.com:22/\<、チーム プロジェクト名 >/_git/\<チーム プロジェクトのテンプレート リポジトリ名前\>です。*** 

この例では、

***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/MyTeamProjectTemplate***。 

### <a name="clone-dsproject1-repository-r5-to-a-directory-d5-on-your-local-machine"></a>ローカル コンピューター上のディレクトリ (D5) に DSProject1 リポジトリ (R5) を複製します。

ディレクトリを **GitRepos** に変更し、次のコマンドを実行して、プロジェクト リポジトリをローカル コンピューターに複製します。 

**Windows**
            
    git clone <the HTTPS URL of the Project repository>

![9](./media/project-lead-tasks/project-leads-9-clone-project-repository.png)

VSTS を コード ホスト プラットフォームとして使用する場合は、_プロジェクト リポジトリの HTTPS URL_ は、通常、***https://\<VSTS サーバー名\>.visualstudio.com/\<チームプロジェクト名>/_git/<プロジェクト リポジトリ名\>*** です。 この例では、***https://mysamplegroup.visualstudio.com/MyTeam/_git/DSProject1*** です。

**Linux**

    git clone <the SSH URL of the Project repository>

![10](./media/project-lead-tasks/project-leads-10-clone-project-repository-linux.png)

VSTS を コード ホスト プラットフォームとして使用する場合は、_プロジェクト リポジトリの SSH URL_ は、通常、_ssh://<VSTS サーバー名\>@<VSTS サーバー名\>.visualstudio.com:22/<Your Team Project Name>/\_git/<プロジェクト リポジトリ名\>です。 この例では、***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/DSProject1*** です。

### <a name="copy-contents-of-d3-to-d5"></a>D5 に D3 のコンテンツをコピーします。 

次に、ローカル コンピューター上で、git ディレクトリ内の git メタデータを除き、_D3_ のコンテンツを _D5_ にコピーする必要があります。 これは、次のスクリプトで実行できます。 ディレクトリへの完全パスを正しく入力してください。 ソース フォルダーは、チームのフォルダー (_D3_) で コピー先フォルダーはプロジェクトの フォルダー (_D5_) です。    

**Windows**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 -role 3
    
![11](./media/project-lead-tasks/project-leads-11-local-copy-project-lead-new.png)

これで、_DSProject1_ フォルダーに、すべてのファイル (.git を除く) が、_MyTeamProjectTemplate_ からコピーされました。

![12](./media/project-lead-tasks/project-leads-12-teamprojectTemplate_copied_to_local.png)

**Linux**
            
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 3
        
![13](./media/project-lead-tasks/project-leads-13-local_copy_project_lead_linux_new.png)

これで、_DSProject1_ フォルダーに、すべてのファイル (.git のメタデータを除く) が、_MyTeamProjectTemplate_ からコピーされました。

![14](./media/project-lead-tasks/project-leads-14-teamprojectTemplate_copied_to_local_linux_new.png)


### <a name="customize-d5-if-you-need-to-optional"></a>必要がある場合は、D5 をカスタマイズします (省略可能)。

プロジェクトで、チーム プロジェクト テンプレートから取得された以外の、特定のディレクトリまたはドキュメントが必要な場合 (前の手順で、D5 ディレクトリにコピーされたもの) は、ここで D5 のコンテンツをカスタマイズできます。 

### <a name="add-contents-of-dsproject1-in-d5-to-r5-on-your-group-vsts-server"></a>グループ VSTS サーバー上の R5 に D5 のDSProject1 のコンテンツを追加します。

次に、**_DSProject1_** のコンテンツを、グループの VSTS サーバーにあるチーム プロジェクトの _R5_ リポジトリにプッシュする必要があります。 


- ディレクトリを **D5** に変更します。 
- 次の git コマンドを使用して **D5** の内容を **R5** に追加します。 コマンドは、Windows と Linux の両方のシステムで共通です。 
    
    git status git add .
    git commit -m"push from win DSVM" git push
    
- 変更をコミットしプッシュします。 

>[AZURE.NOTE] Git リポジトリに初めてコミットする場合は、`git commit` コマンドを実行する前に、グローバル パラメーター *user.name* と *user.email* を設定する必要があります。 次の 2 つのコマンドを実行します。
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> 複数の Git リポジトリにコミットする場合は、同じ名前とメール アドレスを使用して、すべてのリポジトリにコミットします。 同じ名前とメール アドレスを使用すると、後で PowerBI ダッシュボードを作成して複数のリポジトリの Git アクティビティを追跡するときに便利です。

![15](./media/project-lead-tasks/project-leads-15-git-config-name.png)


## <a name="6-create-and-mount-azure-file-storage-as-project-resources-optional"></a>6.プロジェクト リソースとして Azure のファイル ストレージを作成してマウントします (省略可能)。

すべてのプロジェクト メンバーが複数の DSVM から同じデータセットにアクセスできるように、プロジェクト 生データなどのデータや、プロジェクトのために作られた機能を共有するために Azure ファイルストレージを作成するには、[データ サイエンス チームのチーム リーダーのタスク](team-lead-tasks.md)のセクション 3 および 4 の説明に従ってください。 


## <a name="next-steps"></a>次のステップ

Team Data Science Process で定義されている役割とタスクの詳細な説明へのリンクを次に示します。

- [データ サイエンス チームのグループ マネージャーのタスク](group-manager-tasks.md)
- [データ サイエンス チームのチーム リーダーのタスク](team-lead-tasks.md)
- [データ サイエンス チームのプロジェクト リーダーのタスク](project-lead-tasks.md)
- [データ サイエンス チームの個々の共同作成者](project-ic-tasks.md)