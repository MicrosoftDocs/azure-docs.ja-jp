---
title: Git を使用した共同コーディング - Team Data Science Process
description: アジャイル計画で Git を使用したデータ サイエンス プロジェクトの共同コード開発を行う方法です。
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 551d0cd149c4d1555a40ccf0d7baeff97c6809c2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863300"
---
# <a name="collaborative-coding-with-git"></a>Git を使用した共同コーディング

この記事では、共有コード開発フレームワークとして Git を使用して、データ サイエンス プロジェクトの共同コード開発を行う方法について説明します。 [アジャイル開発](agile-development.md)で計画された作業にこれらのコーディング アクティビティをリンクする方法と、コード レビューを実行する方法について説明します。


## 1.<a name='Linkaworkitemwithagitbranch-1'></a>作業項目を Git ブランチにリンクする 

Azure DevOps Services は、作業項目 (ストーリーやタスク) を Git ブランチに接続する便利な手段を提供します。 これにより、関連付けられているコードにストーリーやタスクを直接リンクすることができます。 

作業項目を新しいブランチに接続するには、作業項目をダブルクリックし、ポップアップ ウィンドウで、**[+ リンクを追加]** の下の **[新しいブランチを作成]** をクリックします。  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

ブランチ名、ベース Git リポジトリ、ブランチなど、この新しいブランチの情報を指定します。 選択された Git リポジトリは、作業項目が属する同じプロジェクトの下のリポジトリにある必要があります。 ベース ブランチには、master ブランチまたはその他の既存のブランチを指定できます。

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

各ストーリーの作業項目の Git ブランチを作成することをお勧めします。 次に、タスク作業項目ごとに、ストーリー ブランチに基づいてブランチを作成します。 ストーリーとタスクのリレーションシップに対応するこの階層的な方法でブランチを編成すると、複数のユーザーが同じプロジェクトの異なるストーリーを作業する場合や、複数のユーザーが同じストーリーの異なるタスクを作業する場合に便利です。 各チーム メンバーが異なるブランチに取り組むときや、ブランチを共有している各メンバーが異なるコードまたはその他のアーティファクトに取り組むときの競合を最小化できます。 

次の図は、TDSP の推奨のブランチ戦略を示しています。 同じプロジェクトに取り組むユーザーが 1 人か 2 人だけの場合や、1 人のユーザーだけがストーリーのすべてのタスクに取り組む場合には特に、ここで示すほど多くのブランチは必要ありません。 ただし、開発ブランチは master ブランチから分離することを常にお勧めします。 これにより、リリース ブランチが開発アクティビティによって中断されるのを防ぐことができます。 Git ブランチ モデルのより詳細な説明は、「[A Successful Git Branching Model (成功する Git ブランチ モデル)](https://nvie.com/posts/a-successful-git-branching-model/)」をご覧ください。

![3](./media/collaborative-coding-with-git/3-git-branches.png)

作業するブランチに切り替えるには、シェル コマンド (Windows または Linux) で次のコマンドを実行します。 

    git checkout <branch name>

*<ブランチ名\>* を **master** に変更すると、**master** ブランチに切り替わります。 作業ブランチに切り替えた後で、その作業項目での作業を開始し、項目を完成するために必要なコードまたはドキュメント アーティファクトを開発できます。 

作業項目を既存のブランチにリンクすることもできます。 作業項目の **[詳細]** ページで、**[新しいブランチを作成]** をクリックする代わりに **[+ リンクを追加]** をクリックします。 次に、作業項目のリンク先のブランチを選択します。 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

Git Bash コマンドで新しいブランチを作成することもできます。 <ベースブランチ名\> が見つからない場合、<新しいブランチ名\> は _master_ ブランチに基づきます。 
    
    git checkout -b <new branch name> <base branch name>


## 2.<a name='WorkonaBranchandCommittheChanges-2'></a>ブランチで作業し、変更をコミットする 

作業項目の *data\_ingestion* ブランチに、ローカル コンピューター上のブランチへの R ファイルの追加などの変更を加えるとします。 Git シェルでこの作業項目のブランチにいる場合は、次の Git コマンドを使用して、そのブランチに追加した R ファイルをコミットできます。

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## 手順 3.<a name='CreateapullrequestonVSTS-3'></a>Azure DevOps Services での pull request を作成します 

いくつかのコミットとプッシュの後、現在のブランチをそのベース ブランチにマージする準備ができたら、Azure DevOps Services サーバーに **pull request** を送信できます。 

プロジェクトのメイン ページに移動し、**[CODE]\(コード\)** をクリックします。 マージするブランチと、ブランチのマージ先の Git リポジトリ名を選択します。 次に、**[Pull Requests]** をクリックし、**[新しい pull request]** をクリックして、ブランチでの作業がベース ブランチにマージされる前に pull request レビューを作成します。

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

この pull request に関するいくつかの説明を入力し、レビュー担当者を追加し、送信します。

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## 4.<a name='ReviewandMerge-4'></a>レビューとマージ 

pull request が作成されると、レビュー担当者は、pull request のレビューを求める電子メール通知を受け取ります。 レビュー担当者は、変更が機能しているかどうかを検査し、可能であれば、要求元と共に変更をテストする必要があります。 アセスメントに基づいて、レビュー担当者は pull request を承認または拒否できます。 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

![9](./media/collaborative-coding-with-git/9-spring-approve-pullrequest.png)

レビューの完了後、**[完了]** をクリックすると、作業しているブランチがそのベース ブランチにマージされます。 マージ後に、作業しているブランチを削除することを選択できます。 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

左上隅で、要求が **[完了]** としてマークされていることを確認します。 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

**[CODE]\(コード\)** の下のリポジトリに戻ると、master ブランチに切り替えられたことが示されます。

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

次の Git コマンドを使用して、作業しているブランチをそのベース ブランチにマージし、作業しているブランチをマージ後に削除することもできます。

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)


 
## <a name="next-steps"></a>次の手順

[データ サイエンス タスクの実行](execute-data-science-tasks.md)に関するページに、ユーティリティを使用して、インタラクティブなデータ探索、データ分析、レポート作成、およびモデル作成などの複数の共通データ サイエンス タスクを完了する方法が示されています。

また、**特定のシナリオ**のプロセスにおけるすべての手順を説明するチュートリアルも用意されています。 これらは、[サンプル チュートリアル](walkthroughs.md)の記事で簡単な説明と共にリンク付きで紹介されています。 チュートリアルでは、クラウド、オンプレミスのツール、サービスをワークフローまたはパイプラインに組み込んでインテリジェント アプリケーションを作成する方法を説明しています。 

