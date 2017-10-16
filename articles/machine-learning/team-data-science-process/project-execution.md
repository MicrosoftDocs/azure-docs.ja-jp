---
title: "データ サイエンス プロジェクトの実行 - Azure | Microsoft Docs"
description: "データ サイエンティストがバージョン コントロールされた追跡可能で協調的な方法でデータ サイエンス プロジェクトを実行する方法。"
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
ms.openlocfilehash: 8c318f87243d0c98b6a42bebcdffb433f9cc456e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="execution-of-data-science-projects"></a>データ サイエンス プロジェクトの実行

このドキュメントでは、データ サイエンティストが [Team Data Science Process](overview.md) (TDSP) を使用して、プロジェクト チーム内でバージョン コントロールされた体系的、協調的な方法でデータ サイエンス プロジェクトをどのようにして実行できるかについて説明します。 TDSP は、Microsoft によって開発されたフレームワークであり、クラウドベースの予測分析ソリューションを効率的に実行するための体系化された一連のアクティビティを提供します。 このプロセスを標準とするデータ サイエンス チームの人員の役割とそれに関連したタスクのアウトラインについては、「[Team Data Science Process Roles and tasks (Team Data Science Process での役割とタスク)](roles-tasks.md)」をご覧ください。 

このトピックでは次の方法について説明します。 

1. プロジェクトに関連する作業項目の**スプリント計画**を行います。<br> スプリント計画をよく理解していない場合は、以下の詳細と、[こちら](https://en.wikipedia.org/wiki/Sprint_(software_development) "こちら")の一般的な情報をご覧ください。 
2. スプリントに**作業項目を追加**します。
3. git によって追跡されている**コーディング アクティビティに作業項目をリンク**します。
4. **コード レビュー**を行います。 


>[AZURE.NOTE] ここでは、Visual Studio Team Services (VSTS) を使用して TDSP チーム環境を設定するために必要な一連の手順のアウトラインを説明します。 VSTS を使用してこれらのタスクを達成する方法を示しているのは、Microsoft がこの方法で TDSP を実装しているためです。 前の一覧の項目 (3) と (4) は、VSTS の使用を選択した場合に当然得られるメリットを示しています。 グループで別のコード ホスティング プラットフォームを使用している場合も、一般にチーム リーダーが実行する必要があるタスクは変わりません。 ただし、これらのタスクを実行する方法が異なります。 たとえば、セクション 6 の項目「**作業項目をgit ブランチにリンクする**」は、VSTS 上で行う場合ほど簡単ではない可能性があります。

次の図は、データ サイエンス プロジェクトの実装に関連する一般的なスプリント計画、コーディング、ソース管理ワークフローを示しています。

![1](./media/project-execution/project-execution-1-project-execute.png)


##  1.<a name='Terminology-1'></a>用語 

TDSP スプリント計画フレームワークには、よく使用される 4 種類の**作業項目** (**機能**、**ユーザー ストーリー**、**タスク**、**バグ**) があります。 各チーム プロジェクトでは、すべての作業項目に対して 1 つのバックログを維持します。 チーム プロジェクトの下の git リポジトリ レベルにはバックログがありません。 その定義を次に示します。

- **機能**: 機能はプロジェクト契約に対応しています。 クライアントとのさまざまな契約が、それぞれ異なる機能と見なされます。 同様に、クライアントとのプロジェクトのさまざまなフェーズをそれぞれ異なる機能と見なすことが最適です。 ***ClientName EngagementName*** などのスキーマを選択して機能に名前を付けると、名前自体からプロジェクト/契約のコンテキストを簡単に認識できます。
- **ストーリー**: ストーリーは、機能 (プロジェクト) をエンド ツー エンドで完了するために必要な別の作業項目です。 ストーリーの例を次に示します。
    - データの取得 
    - データの探索 
    - 機能の生成
    - モデルのビルド
    - モデルの運用 
    - モデルの再トレーニング
- **タスク**: タスクは、特定のストーリーを完了するために実行する必要がある割り当て可能なコードまたはドキュメント作業項目やその他のアクティビティです。 たとえば、ストーリー "*データの取得*" のタスクは次のようになります。
    -  SQL Server の資格情報の取得 
    -  SQL Data Warehouse へのデータのアップロード。 
- **バグ**: バグは通常、タスクの完了時に実行される既存のコードまたはドキュメントに必要な修正を表します。 バグの原因がステージまたはタスクの不足の場合は、それぞれストーリーまたはタスクにエスカレートできます。 

>[AZURE.NOTE] ソフトウェア コード管理 (SCM) における機能、ストーリー、タスク、バグの概念を借用して、データ サイエンスで使用します。 これらは、従来の SCM 定義とは若干異なる場合があります。

##  2.<a name='SprintPlanning-2'></a>スプリント計画 

スプリント計画は、プロジェクトの優先順位付けと、リソースの計画と割り当てに役立ちます。 多くのデータ サイエンティストは、それぞれ完了に数か月かかる複数のプロジェクトに従事しています。 多くの場合、プロジェクトはそれぞれ異なるペースで進行します。 VSTS サーバーでは、チーム プロジェクトの作業項目を簡単に作成、管理、追跡でき、プロジェクトが期待どおりに進むようにスプリント計画を実施できます。 

VSTS でのスプリント計画の詳しい手順については、[こちらのリンク](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning)に従ってください。 


##  3.<a name='AddFeature-3'></a>機能の追加  

チーム プロジェクトにプロジェクト リポジトリが作成されたら、チームの **[概要]** ページに移動し、**[作業の管理]** をクリックします。

![2](./media/project-execution/project-execution-2-sprint-team-overview.png)

機能をバックログに含めるには、**[バックログ]**  -->  **[機能]**  -->  **[新規]** をクリックし、機能の **[タイトル]** (通常はプロジェクト名) を入力し、**[追加]** をクリックします。

![3](./media/project-execution/project-execution-3-sprint-team-add-work.png)

作成した機能をダブルクリックします。 説明を入力し、この機能のチーム メンバーを割り当て、この機能の計画パラメーターを設定します。 

この機能をプロジェクト リポジトリにリンクすることもできます。 **[開発]** セクションの下にある **[リンクの追加]** をクリックします。 機能の編集が完了したら、**[保存して閉じる]** をクリックして終了します。


##  4.<a name='AddStoryunderfeature-4'></a>機能の下にストーリーを追加する 

機能の下に、(機能) プロジェクトの完了に必要な主要なステップを記述するストーリーを追加できます。 新しいストーリーを追加するには、バックログ ビューの機能の左側にある **[+]** 記号をクリックします。  

![4](./media/project-execution/project-execution-4-sprint-add-story.png)

状態、説明、コメント、計画、優先順位などのストーリーの詳細をポップアップ ウィンドウで編集することができます。

![5](./media/project-execution/project-execution-5-sprint-edit-story.png)

このストーリーを既存のリポジトリにリンクするには、**[開発]** の下にある **[+ リンクを追加]** をクリックします。 

![6](./media/project-execution/project-execution-6-sprint-link-existing-branch.png)


##  5.<a name='AddTaskunderstory-5'></a>タスクをストーリーに追加する 

タスクは、各ストーリーを完了するために必要な特定の詳細な手順です。 ストーリーのすべてのタスクが完了したら、ストーリーも完了する必要があります。 

ストーリーにタスクを追加するには、ストーリー項目の横にある **[+]** 記号をクリックし、**[タスク]** を選択してから、このタスクの詳細情報をポップアップ ウィンドウに入力します。

![7](./media/project-execution/project-execution-7-sprint-add-task.png)

機能、ストーリー、タスクを作成したら、それらを **[バックログ]** または **[ボード]** ビューで表示して、状態を追跡できます。

![8](./media/project-execution/project-execution-8-sprint-backlog-view.png)

![9](./media/project-execution/project-execution-9-link-to-a-new-branch.png)


##  6.<a name='Linkaworkitemwithagitbranch-6'></a>作業項目を git ブランチにリンクする 

VSTS は、作業項目 (ストーリーやタスク) を git ブランチに接続する便利な手段を提供します。 これにより、関連付けられているコードにストーリーやタスクを直接リンクすることができます。 

作業項目を新しいブランチに接続するには、作業項目をダブルクリックし、ポップアップ ウィンドウで、**[+ リンクを追加]** の下の **[新しいブランチを作成]** をクリックします。  

![10](./media/project-execution/project-execution-10-sprint-board-view.png)

ブランチ名、ベース git リポジトリ、ブランチなど、この新しいブランチの情報を指定します。 選択された git リポジトリは、作業項目が属する同じチーム プロジェクトの下のリポジトリにある必要があります。 ベース ブランチには、master ブランチまたはその他の既存のブランチを指定できます。

![11](./media/project-execution/project-execution-11-create-a-branch.png)

各ストーリーの作業項目の git ブランチを作成することをお勧めします。 次に、タスク作業項目ごとに、ストーリー ブランチに基づいてブランチを作成します。 ストーリーとタスクのリレーションシップに対応するこの階層的な方法でブランチを編成すると、複数のユーザーが同じプロジェクトの異なるストーリーを作業する場合や、複数のユーザーが同じストーリーの異なるタスクを作業する場合に便利です。 各チーム メンバーが異なるブランチに取り組むときや、ブランチを共有している各メンバーが異なるコードまたはその他のアーティファクトに取り組むときの競合を最小化できます。 

次の図は、TDSP の推奨のブランチ戦略を示しています。 同じプロジェクトに取り組むユーザーが 1 人か 2 人だけの場合や、1 人のユーザーだけがストーリーのすべてのタスクに取り組む場合には特に、ここで示すほど多くのブランチは必要ありません。 ただし、開発ブランチは master ブランチから分離することを常にお勧めします。 これにより、リリース ブランチが開発アクティビティによって中断されるのを防ぐことができます。 git ブランチ モデルのより詳細な説明は、「[A Successful Git Branching Model (成功する Git ブランチ モデル)](http://nvie.com/posts/a-successful-git-branching-model/)」をご覧ください。

![12](./media/project-execution/project-execution-12-git-branches.png)

作業するブランチに切り替えるには、シェル コマンド (Windows または Linux) で次のコマンドを実行します。 

    git checkout <branch name>

*<ブランチ名\>* を **master** に変更すると、**master** ブランチに切り替わります。 作業ブランチに切り替えた後で、その作業項目での作業を開始し、項目を完成するために必要なコードまたはドキュメント アーティファクトを開発できます。 

作業項目を既存のブランチにリンクすることもできます。 作業項目の **[詳細]** ページで、**[新しいブランチを作成]** をクリックする代わりに **[+ リンクを追加]** をクリックします。 次に、作業項目のリンク先のブランチを選択します。 

![13](./media/project-execution/project-execution-13-link-to-an-existing-branch.png)

git bash コマンドで新しいブランチを作成することもできます。 <ベースブランチ名\> が見つからない場合、<新しいブランチ名\> は _master_ ブランチに基づきます。 
    
    git checkout -b <new branch name> <base branch name>


##  7.<a name='WorkonaBranchandCommittheChanges-7'></a>ブランチで作業し、変更をコミットする 

作業項目の *data\_ingestion* ブランチに、ローカル コンピューター上のブランチへの R ファイルの追加などの変更を加えるとします。 Git シェルでこの作業項目のブランチにいる場合は、次の Git コマンドを使用して、そのブランチに追加した R ファイルをコミットできます。

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![14](./media/project-execution/project-execution-14-sprint-push-to-branch.png)

##  8.<a name='CreateapullrequestonVSTS-8'></a>VSTS でプル要求を作成する 

いくつかのコミットとプッシュの後、現在のブランチをそのベース ブランチにマージする準備ができたら、VSTS サーバーに**プル要求**を送信できます。 

チーム プロジェクトのメイン ページに移動し、**[CODE]\(コード\)** をクリックします。 マージするブランチと、ブランチのマージ先の git リポジトリ名を選択します。 次に、**[プル要求]**をクリックし、**[新しいプル要求]** をクリックして、ブランチでの作業がベース ブランチにマージされる前にプル要求レビューを作成します。

![15](./media/project-execution/project-execution-15-spring-create-pull-request.png)

このプル要求に関するいくつかの説明を入力し、レビュー担当者を追加し、送信します。

![16](./media/project-execution/project-execution-16-spring-send-pull-request.png)

##  9.<a name='ReviewandMerge-9'></a>レビューとマージ 

プル要求が作成されると、レビュー担当者は、プル要求のレビューを求める電子メール通知を受け取ります。 レビュー担当者は、変更が機能しているかどうかを検査し、可能であれば、要求元と共に変更をテストする必要があります。 アセスメントに基づいて、レビュー担当者はプル要求を承認または拒否できます。 

![17](./media/project-execution/project-execution-17-add_comments.png)

![18](./media/project-execution/project-execution-18-spring-approve-pullrequest.png)

レビューの完了後、**[完了]** をクリックすると、作業しているブランチがそのベース ブランチにマージされます。 マージ後に、作業しているブランチを削除することを選択できます。 

![19](./media/project-execution/project-execution-19-spring-complete-pullrequest.png)

左上隅で、要求が **[完了]** としてマークされていることを確認します。 

![20](./media/project-execution/project-execution-20-spring-merge-pullrequest.png)

**[CODE]\(コード\)** の下のリポジトリに戻ると、master ブランチに切り替えられたことが示されます。

![21](./media/project-execution/project-execution-21-spring-branch-deleted.png)

次の Git コマンドを使用して、作業しているブランチをそのベース ブランチにマージし、作業しているブランチをマージ後に削除することもできます。

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![22](./media/project-execution/project-execution-22-spring-branch-deleted-commandline.png)


##  10.<a name='DataQualityReportUtility-10'></a>インタラクティブなデータ探索、分析、レポート (IDEAR) ユーティリティ

この R Markdown ベースのユーティリティは、データ セットを評価および調査するための柔軟なインタラクティブ ツールを提供します。 ユーザーは、最小限のコーディングでデータ セットからレポートをすばやく生成できます。 ユーザーは、ボタンをクリックして、インタラクティブ ツールに表示される調査結果を最終レポートにエクスポートできます。このレポートをクライアントに配信したり、それ以降のモデリング手順に含める変数の決定に使用したりできます。

現時点では、ツールはメモリ内のデータ フレームでのみ機能します。 調査するデータ セットのパラメーターを指定するには .yaml ファイルが必要です。 詳細については、[TDSP データ サイエンス ユーティリティの IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils) に関するページをご覧ください。


##  11.<a name='ModelingUtility-11'></a>ベースライン モデリングおよびユーティリティ レポート

このユーティリティは、ハイパー パラメーター スイープでモデルの作成を実行し、それらのモデルの精度を比較するためのカスタマイズ可能な半自動ツールを提供します。 

モデル作成ユーティリティは、さまざまなセクションを簡単にナビゲートするためのコンテンツのテーブルを含む自己包含 html 出力を生成するために実行できる R Markdown ファイルです。 マークダウン ファイルが実行 (Knit) されると、glmnet パッケージを使用した正規化回帰、randomForest パッケージを使用したランダム フォレスト、xgboost パッケージを使用したブースティング ツリーの 3 つのアルゴリズムが実行されます。 これらの各アルゴリズムがトレーニング済みのモデルを生成します。 これらのモデルの精度が比較され、機能の相対的な重要度のプロットがレポートされます。 現時点では、2 つのユーティリティがあります。1 つは二項分類タスク用で、もう 1 つは回帰タスク用です。 これらの主な違いはパラメーターの制御方法で、精度のメトリックがこれらの学習タスクに対して指定されます。 

Yaml ファイルを使用して以下を指定します。

- データ入力 (SQL ソースまたは R データ ファイル) 
- トレーニングに使用されるデータの部分とテストに使用される部分
- 実行するアルゴリズム 
- モデルの最適化のための制御パラメーターの選択:
    - クロス検証 
    - ブートストラップ
    - クロス検証のフォールド
- 各アルゴリズムのハイパー パラメーター セット。 

モデルをすばやく実行するために、アルゴリズムの数、最適化のフォールドの数、ハイパー パラメーター、スイープするハイパー パラメーター セットの数も Yaml ファイルで変更できます。 たとえば、より少ない CV フォールド数で実行して、パラメーター セットの数を減らすことができます。 保証されている場合は、より多くの CV フォールド、またはより多くのパラメーター セットでより包括的に実行することもできます。

詳細については、[TDSP データ サイエンス ユーティリティでの自動化されたモデリングおよびユーティリティ](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling)に関するページをご覧ください。


##  12.<a name='PowerBI-12'></a>Power BI ダッシュ ボードでのプロジェクトの進行状況の追跡

データ サイエンス グループ マネージャー、チーム リーダー、プロジェクト リーダーは、プロジェクトの進行状況、プロジェクトに対して実行した作業とその実行者、To-do リストに残っている項目を追跡する必要があります。 VSTS を使用している場合、Git リポジトリに関連付けられているアクティビティと作業項目を追跡するために Power BI ダッシュボードを構築することができます。 Power BI を Visual Studio Team Services に接続する方法について詳しくは、[Team Services への Power BI の接続](https://www.visualstudio.com/en-us/docs/report/powerbi/connect-vso-pbi-vs)に関する記事をご覧ください。 

VSTS のデータが Power BI に接続された後に Git リポジトリのアクティビティと作業項目を追跡するために Power BI ダッシュボードおよびレポートを作成する方法については、「[Create Power BI dashboards and reports (Power BI ダッシュボードとレポートの作成)](https://www.visualstudio.com/en-us/docs/report/powerbi/report-on-vso-with-power-bi-vs)」をご覧ください。 

Git のアクティビティと作業項目を追跡するために構築する 2 つの簡単なダッシュボード例を次に示します。 最初のダッシュボード例では、git コミットメント アクティビティがユーザー別、日付別、リポジトリ別に一覧表示されています。 詳細な分析を簡単に行って、関心対象にフィルター処理できます。

![23](./media/project-execution/project-execution-23-powerbi-git.png)

2 つ目のダッシュボード例では、異なるイテレーションの作業項目 (ストーリーとタスク) が表示されています。 これらは割り当て先と優先度のレベルでグループ化され、状態ごとに色分けされています。

![24](./media/project-execution/project-execution-24-powerbi-workitem.png)

 
## <a name="next-steps"></a>次のステップ

また、 **特定のシナリオ** のプロセスに伴うすべての段階をリハーサル的に最初から最後まで実証することも可能です。 これらは、[サンプル チュートリアル](walkthroughs.md)のトピックで簡単な説明と共にリンク付きで紹介されています。 チュートリアルでは、クラウド、オンプレミスのツール、サービスをワークフローまたはパイプラインに組み込んでインテリジェント アプリケーションを作成する方法を説明しています。 

Azure Machine Learning Studio を使用する Team Data Science Process のステップを実行する例については、[Azure ML の使用](http://aka.ms/datascienceprocess)ラーニング パスをご覧ください。