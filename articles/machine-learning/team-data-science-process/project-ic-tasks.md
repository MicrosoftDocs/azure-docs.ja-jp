---
title: Team Data Science Process での個々の共同作成者のタスク
description: データ サイエンス チーム プロジェクトの個々の共同作成者のタスクについて概要を説明します。
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6a52907fa6c0e2483479031fbb3d1ad68a121d95
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55475560"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Team Data Science Process での個々の共同作成者のタスク

このトピックでは、個々の共同作成者がデータ サイエンス チームのために遂行する必要があるタスクの概要を説明します。 目的は、[Team Data Science Process](overview.md) (TDSP) を標準とするチーム コラボレーション環境を確立することです。 このプロセスを標準とするデータ サイエンス チームの人員の役割とそれに関連したタスクの概要については、「[Team Data Science Process Roles and tasks](roles-tasks.md)」(Team Data Science Process での役割とタスク) を参照してください。

プロジェクトの TDSP 環境を設定するプロジェクトの個々の共同作成者 (データ サイエンティスト) には、次の図のようなタスクがあります。 

![1](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **GroupUtilities** は、グループが、グループ全体で便利なユーティリティを共有するために維持しているリポジトリです。 
- **TeamUtilities** は、チームが、自分のチームのためにのみ維持しているリポジトリです。 

TDSP でデータ サイエンス プロジェクトを実行する手順については、「[Execution of Data Science Projects](project-execution.md)」(データ サイエンス プロジェクトの実行) を参照してください。 

>[AZURE.NOTE] 以下の方法で Azure DevOps を使用して TDSP チーム環境を設定するために必要な手順の概要について説明します。 Azure DevOps でこれらのタスクを達成する方法を指定するのは、それが Microsoft で TDSP を実装する方法であるためです。 グループで別のコード ホスティング プラットフォームを使用している場合も、一般にチーム リーダーが実行する必要があるタスクは変わりません。 ただし、これらのタスクを実行する方法が異なります。


## <a name="repositories-and-directories"></a>リポジトリとディレクトリ

このチュートリアルでは、リポジトリとディレクトリの省略名を使用します。 これらの名前により、リポジトリとディレクトリ間での操作がわかりやすくなります。 以降のセクションでは次の表記を使用します (**R** は Git リポジトリを表し、**D** は DSVM 上のローカル ディレクトリを表します)。

- **R2**:グループ マネージャーが Azure DevOps グループ サーバーで設定した Git の GroupUtilities リポジトリ。
- **R4**:チーム リーダーが設定した Git の TeamUtilities リポジトリ。
- **R5**:プロジェクト リーダーが設定した Git の Project リポジトリ。
- **D2**:R2 から複製されたローカル ディレクトリ。
- **D4**:R4 から複製されたローカル ディレクトリ。
- **D5**:R5 から複製されたローカル ディレクトリ。


## <a name="step-0-prerequisites"></a>手順 0:前提条件

前提条件を満たすには、[データ サイエンス チームのグループ マネージャーのタスク](group-manager-tasks.md)に関する記事で説明する、グループ マネージャーに割り当てられたタスクを完了します。 まとめると、チーム リーダーのタスクを開始する前に、次の要件を満たす必要があります。 
- グループ マネージャーが **GroupUtilities** リポジトリ (ある場合) を設定している。 
- チーム リーダーが **TeamUtilities** リポジトリ (ある場合) を設定している。
- プロジェクト リーダーがプロジェクト リポジトリを設定している。 
- 自分がプロジェクト リーダーによってプロジェクト リポジトリに追加されており、プロジェクト リポジトリから複製し、プロジェクト リポジトリにプッシュする特権を付与されている。

チームがチーム固有のユーティリティ リポジトリを持っているかどうかに応じて、2 つ目の **TeamUtilities** リポジトリに関する前提条件は省略可能です。 他の 3 つの前提条件のいずれかが完了していない場合は、チーム リーダー、プロジェクト リーダー、またはリーダーの代理人に連絡し、「[Team Lead tasks for a data science team](team-lead-tasks.md)」(データ サイエンス チームのチーム リーダーのタスク) または「[Project Lead tasks for a data science team](project-lead-tasks.md)」(データ サイエンス チームのプロジェクト リーダーのタスク) の手順に従って設定します。

- マシンに Git をインストールする必要があります。 データ サイエンス仮想マシン (DSVM) を使用している場合は、Git がプレインストールされているので、インストールは不要です。 それ以外の場合は、[プラットフォームとツールに関する記事の付録](platforms-and-tools.md#appendix)をご覧ください。  
- **Windows DSVM** を使用している場合は、マシンに [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) がインストールされている必要があります。 README.md ファイルで、下へスクロールして「**Download and Install**」(ダウンロードとインストール) セクションを表示し、「*latest installer*」(最新のインストーラー) をクリックします。 これにより、最新のインストーラーのページに移動します。 このページから .exe インストーラーをダウンロードして実行します。 
- **Linux DSVM** を使用している場合は、DSVM で SSH 公開キーを作成し、それをグループの Azure DevOps Services に追加します。 SSH の詳細については、[プラットフォームとツールに関する記事の付録](platforms-and-tools.md#appendix)の **SSH 公開キーの作成**に関するセクションをご覧ください。 
- DSVM にマウントする必要がある何らかの Azure ファイル ストレージをチーム リーダーやプロジェクト リーダーが作成した場合は、そこから Azure ファイル ストレージ情報を取得する必要があります。 

## <a name="step-1-3-clone-group-team-and-project-repositories-to-local-machine"></a>手順 1 - 3:グループ、チーム、およびプロジェクトのリポジトリをローカル コンピューターに複製する

ここでは、プロジェクトの個々の共同作成者について、最初の 3 つのタスクを完了する手順について説明します。 

- **GroupUtilities** リポジトリ R2 を D2 に複製する
- **TeamUtilities** リポジトリ R4 を D4 に複製する 
- **Project** リポジトリ R5 を D5 に複製する

ローカル コンピューターでディレクトリ ***C:\GitRepos*** (Windows) または ***$home/GitRepos*** (Linux) を作成し、そのディレクトリに移動します。 

(使用している OS に合わせて) 次のコマンドのいずれかを実行して、**GroupUtilities**、**TeamUtilities**、および **Project** リポジトリをローカル コンピューターのディレクトリに複製します。 

**Windows**
    
    git clone <the URL of the GroupUtilities repository>
    git clone <the URL of the TeamUtilities repository>
    git clone <the URL of the Project repository>
    
![2](./media/project-ic-tasks/project-ic-2-clone-three-repo-to-ic.png)

プロジェクト ディレクトリ以下に 3 つのフォルダーが表示されることを確認します。

![3](./media/project-ic-tasks/project-ic-3-three-repo-cloned-to-ic.png)

**Linux**
    
    git clone <the SSH URL of the GroupUtilities repository>
    git clone <the SSH URL of the TeamUtilities repository>
    git clone <the SSH URL of the Project repository>

![4](./media/project-ic-tasks/project-ic-4-clone-three-repo-to_ic-linux.png)

プロジェクト ディレクトリ以下に 3 つのフォルダーが表示されることを確認します。

![5](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="step-4-5-mount-azure-file-storage-to-your-dsvm-optional"></a>手順 4 - 5:Azure ファイル ストレージを DSVM にマウントする (省略可能)

Azure ファイル ストレージを DSVM にマウントするには、「[Team Lead tasks for a data science team](team-lead-tasks.md)」(データ サイエンス チームのチーム リーダーのタスク) のセクション 4 の手順を参照してください。

## <a name="next-steps"></a>次の手順

Team Data Science Process で定義されている役割とタスクの詳細な説明へのリンクを次に示します。

- [データ サイエンス チームのグループ マネージャーのタスク](group-manager-tasks.md)
- [データ サイエンス チームのチーム リーダーのタスク](team-lead-tasks.md)
- [データ サイエンス チームのプロジェクト リーダーのタスク](project-lead-tasks.md)
- [データ サイエンス チームの個々の共同作成者](project-ic-tasks.md)

