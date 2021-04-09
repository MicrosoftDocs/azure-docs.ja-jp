---
title: Git を使用した共同コーディング - Team Data Science Process
description: アジャイル計画で Git を使用したデータ サイエンス プロジェクトの共同コード開発を行う方法です。
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ca24a781f4f3ad5c210813dabbb896de35056ed6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "97588711"
---
# <a name="collaborative-coding-with-git"></a>Git を使用した共同コーディング

この記事では、データ サイエンス プロジェクトの共同コード開発フレームワークとして Git を使用する方法について説明します。 Azure Repos のコードを Azure Boards の[アジャイル開発](agile-development.md)作業項目にリンクする方法、コード レビューを実行する方法、および変更の pull request を作成およびマージする方法について取り上げます。

## <a name="link-a-work-item-to-an-azure-repos-branch"></a><a name='Linkaworkitemwithagitbranch-1'></a>Azure Repos ブランチに作業項目をリンクする 

Azure DevOps では、Azure Boards のユーザー ストーリーまたはタスク作業項目を Azure Repos Git リポジトリ ブランチに接続する便利な方法を提供します。 関連付けられているコードにユーザー ストーリーまたはタスクを直接リンクできます。 

作業項目を新しいブランチに接続するには、作業項目の横にある **アクション** の省略記号 ( **...** ) を選択し、コンテキスト メニューでスクロールして **[新しいブランチ]** を選択します。  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

**[ブランチの作成]** ダイアログで、新しいブランチの名前と、Azure Repos Git のベース リポジトリおよびブランチを指定します。 ベース リポジトリは、作業項目と同じ Azure DevOps プロジェクト内になければなりません。 ベース ブランチには、既存のブランチを指定できます。 **[ブランチの作成]** を選択します。 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Windows または Linux で次の Git bash コマンドを使用して新しいブランチを作成することもできます。

```bash
git checkout -b <new branch name> <base branch name>

```
\<base branch name> を指定しない場合、新しいブランチは、`main` に基づきます。 

作業ブランチに切り替えるには、次のコマンドを実行します。 

```bash
git checkout <working branch name>
```

作業ブランチに切り替えた後に、その作業項目を完成するために、コードやドキュメント アーティファクトの開発を開始できます。 `git checkout main` を実行すると、`main` ブランチに戻ります。

ユーザー ストーリー作業項目ごとに Git ブランチを作成することをお勧めします。 次に、タスク作業項目ごとに、ユーザー ストーリー ブランチに基づいてブランチを作成できます。 複数の担当者が同じプロジェクトの異なるユーザー ストーリーや、同じユーザー ストーリーの異なるタスクで作業している場合は、そのユーザー ストーリーとタスクの関係に対応する階層にブランチを編成します。 ブランチを共有する場合は、各チーム メンバーが異なるブランチや異なるコード、または他のアーティファクトに取り組むようにすることで、競合を最小限に抑えることができます。 

次の図は、TDSP の推奨のブランチ戦略を示しています。 1 つのプロジェクトに取り組む担当者が 1 人か 2 人だけの場合や、1 人の担当者のみがユーザー ストーリーのすべてのタスクに取り組む場合には特に、ここで示すほど多くのブランチは必要ありません。 ただし、開発ブランチはプライマリ ブランチから常に分離することをお勧めします。こうすると、リリース ブランチが開発アクティビティによって中断されるのを防ぐことができます。 Git ブランチ モデルの詳細な説明については、「[成功する Git ブランチ モデル](https://nvie.com/posts/a-successful-git-branching-model/)」をご覧ください。

![3](./media/collaborative-coding-with-git/3-git-branches.png)

作業項目を既存のブランチにリンクすることもできます。 作業項目の **[詳細]** ページで、 **[リンクの追加]** を選択します。 次に、作業項目をリンクする既存のブランチを選択し、 **[OK]** を選択します。 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name="work-on-the-branch-and-commit-changes"></a><a name='WorkonaBranchandCommittheChanges-2'></a>ブランチで作業し、変更をコミットする 

作業項目に変更を加えたら (ローカル マシンの `script` ブランチへの R スクリプト ファイルの追加など)、次の Git bash コマンドを使用して、ローカル ブランチから上流の作業ブランチへ変更をコミットできます。

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name="create-a-pull-request"></a><a name='CreateapullrequestonVSTS-3'></a>Pull request を作成する

1 回以上のコミットおよびプッシュの実行後に、現在の作業ブランチをそのベース ブランチにマージする準備ができたら、Azure Repos で *pull request* を作成して送信できます。 

Azure DevOps プロジェクトのメイン ページから、左のナビゲーションで **[Repos]**  >  **[Pull request]** をポイントします。 次に、 **[新しい pull request]** ボタンまたは **[Pull request の作成]** リンクのいずれかを選択します。

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

必要に応じて、 **[新しい pull request]** 画面で、変更をマージする Git リポジトリとブランチに移動します。 必要な情報を追加または変更します。 **[レビュー担当者]** で、レビュー担当者の名前を追加し、 **[作成]** を選択します。 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name="review-and-merge"></a><a name='ReviewandMerge-4'></a>レビューとマージ

pull request が作成されると、レビュー担当者は、pull request のレビューを求める電子メール通知を受け取ります。 レビュー担当者は、変更が機能するかどうかをテストし、可能であれば要求者と一緒に変更を確認します。 レビュー担当者は、評価に基づいて、コメントの作成、変更の要求、pull request の承認または拒否を行います。 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

レビュー担当者が変更を承認すると、マージ権限を持つ担当者が作業ブランチをそのベース ブランチにマージできます。 **[完了]** を選択し、 **[Pull request の完了]** ダイアログで **[マージの完了]** を選択します。 マージ後に、作業しているブランチを削除することを選択できます。 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

要求が **[完了]** としてマークされていることを確認します。 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

左側のナビゲーションで **Repos** に戻ると、`script` ブランチが削除されたためにメイン ブランチに切り替わったことを確認できます。

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

次の Git bash コマンドを使用して、`script` 作業ブランチをそのベース ブランチにマージし、マージ後に作業ブランチを削除することもできます。

```bash
git checkout main
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>次のステップ

[データ サイエンス タスクの実行](execute-data-science-tasks.md)に関するページに、ユーティリティを使用して、インタラクティブなデータ探索、データ分析、レポート作成、およびモデル作成などの複数の共通データ サイエンス タスクを完了する方法が示されています。

[例を含むチュートリアル](walkthroughs.md)の記事に、特定のシナリオのチュートリアルが、簡潔な説明とリンク付きで示されています。 これらのリンクされたシナリオでは、クラウドとオンプレミスのツールおよびサービスをワークフローまたはパイプラインに組み込んで、インテリジェントなアプリケーションを作成する方法について説明します。 

