---
title: Team Data Science Process でのプロジェクト リーダーのタスク
description: Team Data Science Process チームでのプロジェクト リーダーのタスクの詳細なチュートリアル
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9d9b2546553e03d1555cf4c587d699d9a4ea7e51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96000982"
---
# <a name="project-lead-tasks-in-the-team-data-science-process"></a>Team Data Science Process でのプロジェクト リーダーのタスク

この記事では、[Team Data Science Process](overview.md) (TDSP) でプロジェクト チームのリポジトリを設定するために *プロジェクト リーダー* が完了するタスクについて説明します。 TDSP は、Microsoft によって開発されたフレームワークであり、クラウドベースの予測分析ソリューションを効率的に実行するための体系化された一連のアクティビティを提供します。 TDSP は、コラボレーションとチーム学習の向上を支援するように設計されています。 TDSP を標準とするデータ サイエンス チームの個人的役割とそれに関連したタスクの概要については、「[Team Data Science Process の役割とタスク](roles-tasks.md)」をご覧ください。

プロジェクト リーダーは、TDSP の特定のデータ サイエンス プロジェクトに関する個々のデータ サイエンティストの毎日のアクティビティを管理します。 次の図は、プロジェクト リーダーのタスクのワークフローを示しています。

![プロジェクト リーダーのタスクのワークフロー](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

このチュートリアルでは、手順 1 の「プロジェクト リポジトリを作成する」と手順 2 の「Team ProjectTemplate リポジトリからプロジェクト リポジトリをシードする」を取り上げます。 

手順 3 の「プロジェクト用の機能の作業項目を作成する」と手順 4 の「プロジェクト フェーズでストーリーを追加する」については、「[データ サイエンス プロジェクトのアジャイル開発](agile-development.md)」を参照してください。

手順 5 の「ストレージ/分析資産を作成してカスタマイズし、共有する」については、必要であれば、「[チーム データと分析のリソースを作成する](team-lead-tasks.md#create-team-data-and-analytics-resources)」を参照してください。

手順 6 の「プロジェクト リポジトリのセキュリティ制御を設定する」については、「[チーム メンバーを追加し、アクセス許可を構成する](team-lead-tasks.md#add-team-members-and-configure-permissions)」を参照してください。

> [!NOTE] 
> この記事では、Azure Repos を使用して TDSP プロジェクトを設定します。これが Microsoft で TDSP を実装する方法であるためです。 チームで別のコード ホスティング プラットフォームを使用している場合、プロジェクト リーダーのタスクは同じですが、それを完了する方法は異なることがあります。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、[グループ マネージャー](group-manager-tasks.md)と[チーム リーダー](team-lead-tasks.md)が次のリソースとアクセス許可を設定していることが前提となっています。

- データ ユニット用の Azure DevOps **組織**
- データ サイエンス チーム用のチーム **プロジェクト**
- チーム テンプレートとユーティリティの **リポジトリ**
- プロジェクトのリポジトリを作成し、編集するための組織のアカウントに対する **アクセス許可**

ローカル コンピューターまたは Data Science Virtual Machine (DSVM) でリポジトリをクローンしてその内容を変更したり、Azure ファイル ストレージを設定して DSVM にマウントしたりするには、次のチェックリストを検討することも必要です。

- Azure サブスクリプション。
- コンピューターにインストールされた Git。 DSVM を使用している場合、Git は事前にインストールされています。 それ以外の場合は、[プラットフォームとツールに関する記事の付録](platforms-and-tools.md#appendix)をご覧ください。
- DSVM を使用する場合は、Azure で作成して構成された Windows または Linux の DSVM。 詳細と手順については、[Data Science Virtual Machine ドキュメント](../data-science-virtual-machine/index.yml)を参照してください。
- Windows DSVM の場合、[Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) をコンピューターにインストールしておきます。 *README.md* ファイルを下にスクロールして **[Download and Install]** セクションを見つけ、**最新のインストーラー** を選択します。 インストーラー ページから *.exe* インストーラーをダウンロードして実行します。 
- Linux DSVM の場合は、DSVM で設定されて Azure DevOps に追加された SSH 公開キー。 詳細と手順については、[プラットフォームとツールの付録](platforms-and-tools.md#appendix)の **SSH 公開キーの作成** に関するセクションをご覧ください。 

## <a name="create-a-project-repository-in-your-team-project"></a>チーム プロジェクトでプロジェクト リポジトリを作成する

チームの **MyTeam** プロジェクトでプロジェクト リポジトリを作成するには:

1. *https:\//\<server name>/\<organization name>/\<team name>* (例: **https:\//dev.azure.com/DataScienceUnit/MyTeam**) でチームのプロジェクトの **まとめ** ページに移動し、左側のナビゲーションから **[リポジトリ]** を選択します。 
   
1. ページの一番上にあるリポジトリ名を選択し、ドロップダウンから **[新しいリポジトリ]** を選択します。
   
   ![[新しいリポジトリ] を選択する](./media/project-lead-tasks/project-leads-9-select-repos.png)
   
1. **[新しいリポジトリの作成]** ダイアログで、 **[種類]** として **[Git]** が選択されていることを確認します。 **[リポジトリ名]** に「*DSProject1*」と入力し、 **[作成]** を選択します。
   
   ![リポジトリを作成する](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)
   
1. プロジェクトの設定ページに新しい **DSProject1** リポジトリが表示されることを確認します。 
   
   ![プロジェクト設定のプロジェクト リポジトリ](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

## <a name="import-the-team-template-into-your-project-repository"></a>チーム テンプレートをプロジェクト リポジトリにインポートする

チーム テンプレート リポジトリの内容をプロジェクト リポジトリに入力するには:

1. チームのプロジェクト **まとめ** ページから、左側のナビゲーションにある **[リポジトリ]** を選択します。 
   
1. ページの一番上にあるリポジトリ名を選択し、ドロップダウンから **[DSProject1]** を選択します。
   
1. **[DSProject1 は空です]** ページで、 **[インポート]** を選択します。 
   
   ![[インポート] を選択する](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)
   
1. **[Git リポジトリをインポートする]** ダイアログで、 **[ソースの種類]** として **[Git]** を選択し、 **[クローン URL]** に **TeamTemplate** リポジトリの URL を入力します。 URL は *https:\//\<server name>/\<organization name>/\<team name>/_git/\<team template repository name>* です。 例: **https:\//dev.azure.com/DataScienceUnit/MyTeam/_git/TeamTemplate**. 
   
1. **[インポート]** を選択します。 チーム テンプレート リポジトリの内容が、プロジェクト リポジトリにインポートされます。 
   
   ![チーム テンプレート リポジトリのインポート](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

プロジェクト固有のニーズに合わせてプロジェクト リポジトリの内容をカスタマイズする必要がある場合、リポジトリのファイルとフォルダーを追加、削除、変更できます。 Azure Repos で直接操作したり、ローカル コンピューターまたは DSVM にリポジトリをクローンし、変更後、共有プロジェクト リポジトリに更新内容をコミット/プッシュしたりできます。 「[チーム リポジトリの内容をカスタマイズする](team-lead-tasks.md#customize-the-contents-of-the-team-repositories)」の指示に従ってください。

## <a name="next-steps"></a>次のステップ

Team Data Science Process で定義されている他の役割とタスクの詳細な説明へのリンクを次に示します。

- [データ サイエンス チームのグループ マネージャーのタスク](group-manager-tasks.md)
- [データ サイエンス チームのチーム リーダーのタスク](team-lead-tasks.md)
- [データ サイエンス チームの個々の共同作成者タスク](project-ic-tasks.md)