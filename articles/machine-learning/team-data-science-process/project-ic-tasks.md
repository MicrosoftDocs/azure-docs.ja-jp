---
title: Team Data Science Process での個々の共同作成者のタスク
description: データ サイエンス チーム プロジェクトの個々の共同作成者のタスクについて段階的に詳しく説明します。
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ecb5fef9c9b14bde72de29a45e29d7e16131bd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96000999"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Team Data Science Process での個々の共同作成者のタスク

このトピックでは、[Team Data Science Process](overview.md) (TDSP) でプロジェクトを設定するために *個々の共同作成者* が完了するタスクについて説明します。 目的は TDSP を標準とするチーム コラボレーション環境で作業することです。 TDSP は、コラボレーションとチーム学習の向上を支援するように設計されています。 TDSP を標準とするデータ サイエンス チーム メンバーの役割とそれに関連したタスクの概要については、「[Team Data Science Process での役割とタスク](roles-tasks.md)」をご覧ください。

次の図は、プロジェクトの個々の共同作成者 (データ サイエンティスト) がチーム環境を設定するために完了するタスクを示しています。 TDSP でデータ サイエンス プロジェクトを実行する手順については、「[データ サイエンス プロジェクトの実行](./agile-development.md)」を参照してください。 

![個々の共同作成者のタスク](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **ProjectRepository** は、プロジェクトのテンプレートとアセットを共有する目的でプロジェクト チームが保守管理するリポジトリです。
- **TeamUtilities** は、チームが自分のチームのためにのみ維持しているユーティリティ リポジトリです。 
- **GroupUtilities** は、グループがグループ全体で便利なユーティリティを共有するために維持しているリポジトリです。 

> [!NOTE] 
> この記事では、Azure Repos と Data Science Virtual Machine (DSVM) を使用して TDSP チーム環境を設定します。これが Microsoft で TDSP を実装する方法であるためです。 チームで他のコード ホスティング プラットフォームまたは開発プラットフォームが使用されている場合、個々の共同作成者のタスクは同じですが、それらを行う方法は異なる場合があります。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、[グループ マネージャー](group-manager-tasks.md)、[チーム リーダー](team-lead-tasks.md)、[プロジェクト リーダー](project-lead-tasks.md)によって次のリソースとアクセス許可が設定されているものとします。

- データ サイエンス ユニット用の Azure DevOps **組織**
- プロジェクトのテンプレートとアセットを共有する目的プロジェクト リーダーが設定した **プロジェクト リポジトリ**
- 該当する場合、グループ マネージャーとチーム リーダーが設定した **GroupUtilities** リポジトリと **TeamUtilities** リポジトリ
- 該当する場合、チームまたはプロジェクトの共有アセットに対して設定された Azure **ファイル ストレージ**
- プロジェクト リポジトリとの間でクローン/プッシュを行うための **アクセス許可** 

ローカル コンピューターまたは DSVM でリポジトリをクローンしてその内容を変更したり、Azure ファイル ストレージを DSVM にマウントしたりするには、次のチェックリストを考慮する必要があります。

- Azure サブスクリプション。
- コンピューターにインストールされた Git。 DSVM を使用している場合、Git は事前にインストールされています。 それ以外の場合は、[プラットフォームとツールに関する記事の付録](platforms-and-tools.md#appendix)をご覧ください。
- DSVM を使用する場合は、Azure で作成して構成された Windows または Linux の DSVM。 詳細と手順については、[Data Science Virtual Machine ドキュメント](../data-science-virtual-machine/index.yml)を参照してください。
- Windows DSVM の場合、[Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) をコンピューターにインストールしておきます。 *README.md* ファイルを下にスクロールして **[Download and Install]** セクションを見つけ、**最新のインストーラー** を選択します。 インストーラー ページから *.exe* インストーラーをダウンロードして実行します。 
- Linux DSVM の場合は、DSVM で設定されて Azure DevOps に追加された SSH 公開キー。 詳細と手順については、[プラットフォームとツールの付録](platforms-and-tools.md#appendix)の **SSH 公開キーの作成** に関するセクションをご覧ください。 
- DSVM にマウントする必要がある Azure ファイル ストレージの Azure ファイル ストレージ情報 

## <a name="clone-repositories"></a>リポジトリのクローン

リポジトリをローカルで操作し、共有チームとプロジェクトのリポジトリに変更内容をプッシュするには、まず、ローカル コンピューターにリポジトリをコピーまたは *クローン* します。 

1. Azure DevOps で、*https:\//\<server name>/\<organization name>/\<team name>* (例: **https:\//dev.azure.com/DataScienceUnit/MyTeam**) にあるチームのプロジェクトまとめページに移動します。
   
1. 左側のナビゲーションで **[リポジトリ]** を選択し、ページの上部で、クローンするリポジトリを選択します。
   
1. [リポジトリ] ページで、右上にある **[クローン]** を選択します。
   
1. **[Clone repository]\(リポジトリのクローン\)** ダイアログで、HTTP 接続には **[HTTP]** を選択し、SSH 接続には **[SSH]** を選択し、 **[コマンド ライン]** の下にあるクローン URL をクリップボードにコピーします。
   
   ![リポジトリをクローンする](./media/project-ic-tasks/clone.png)
   
1. ローカル コンピューターまたは DSVM で、次のディレクトリを作成します。
   
   - Windows の場合:**C:\GitRepos**
   - Linux の場合: **$home/GitRepos**
   
1. 作成したディレクトリに移動します。
   
1. Git Bash の場合、クローンするリポジトリごとにコマンド `git clone <clone URL>` を実行します。 
   
   たとえば、**TeamUtilities** リポジトリをローカル コンピューターの *MyTeam* ディレクトリにクローンするには、次のコマンドを使用します。 
   
   **HTTPS 接続:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH 接続:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. ローカル プロジェクト ディレクトリに、クローンしたリポジトリのフォルダーが表示されることを確認します。
   
   ![3 つのローカル リポジトリ フォルダー](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>Azure ファイル ストレージを DSVM にマウントする

チームまたはプロジェクトが Azure ファイル ストレージでアセットを共有している場合、ローカル コンピューターまたは DSVM にファイル ストレージをマウントします。 「[ローカル コンピューターまたは DSVM に Azure ファイル ストレージをマウントする](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm)」の指示に従ってください。

## <a name="next-steps"></a>次のステップ

Team Data Science Process で定義されている他の役割とタスクの詳細な説明へのリンクを次に示します。

- [データ サイエンス チームのグループ マネージャーのタスク](group-manager-tasks.md)
- [データ サイエンス チームのチーム リーダーのタスク](team-lead-tasks.md)
- [データ サイエンス チームのプロジェクト リーダーのタスク](project-lead-tasks.md)