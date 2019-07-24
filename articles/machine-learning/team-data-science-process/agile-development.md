---
title: データ サイエンス プロジェクトのアジャイル開発 - Team Data Science Process
description: 開発者が Team Data Science Process を使用して、プロジェクト チーム内でバージョン コントロールされた体系的、協調的な方法でデータ サイエンス プロジェクトをどのようにして実行できるかについて説明します。
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: adf713fc3f875168f99b302b0a9affef88e8414f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457685"
---
# <a name="agile-development-of-data-science-projects"></a>データ サイエンス プロジェクトのアジャイル開発

このドキュメントでは、開発者が [Team Data Science Process](overview.md) (TDSP) を使用して、プロジェクト チーム内でバージョン コントロールされた体系的、協調的な方法でデータ サイエンス プロジェクトをどのようにして実行できるかについて説明します。 TDSP は、Microsoft によって開発されたフレームワークであり、クラウドベースの予測分析ソリューションを効率的に実行するための体系化された一連のアクティビティを提供します。 このプロセスを標準とするデータ サイエンス チームの人員の役割とそれに関連したタスクのアウトラインについては、「[Team Data Science Process Roles and tasks (Team Data Science Process での役割とタスク)](roles-tasks.md)」をご覧ください。 

この記事では次の方法について説明します。 

1. プロジェクトに関連する作業項目の**スプリント計画**を行います。<br> スプリント計画をよく理解していない場合は、[こちら](https://en.wikipedia.org/wiki/Sprint_(software_development) "こちら")で詳細情報と一般的な情報を参照してください。 
2. スプリントに**作業項目を追加**します。 

> [!NOTE]
> ここでは、Azure DevOps Services を使用して TDSP チーム環境を設定するために必要な一連の手順のアウトラインを説明します。 Azure DevOps Services でこれらのタスクを達成する方法を指定するのは、それが Microsoft で TDSP を実装する方法であるためです。  前の一覧の項目 (3) と (4) は、Azure DevOps Services の使用を選択した場合に当然得られるメリットを示しています。 グループで別のコード ホスティング プラットフォームを使用している場合も、一般にチーム リーダーが実行する必要があるタスクは変わりません。 ただし、これらのタスクを実行する方法が異なります。 たとえば、セクション 6 の項目「**作業項目を git ブランチにリンクする**」は、Azure DevOps Services 上で行う場合ほど簡単ではない可能性があります。
>
>

次の図は、データ サイエンス プロジェクトの実装に関連する一般的なスプリント計画、コーディング、ソース管理ワークフローを示しています。

![1](./media/agile-development/1-project-execute.png)


##  1.<a name='Terminology-1'></a>用語 

TDSP スプリント計画フレームワークには、よく使用される 4 種類の**作業項目**(**機能**、**ユーザー ストーリー**、**タスク**、**バグ**) があります。 各プロジェクトでは、すべての作業項目の 1 つのバックログを維持します。 プロジェクト下での Git リポジトリ レベルにバックログがありません。 その定義を次に示します。

- **機能**:機能はプロジェクト契約に対応しています。 クライアントとのさまざまな契約が、それぞれ異なる機能と見なされます。 同様に、クライアントとのプロジェクトのさまざまなフェーズをそれぞれ異なる機能と見なすことが最適です。 ***ClientName EngagementName*** などのスキーマを選択して機能に名前を付けると、名前自体からプロジェクト/契約のコンテキストを簡単に認識できます。
- **ストーリー**:ストーリーは、機能 (プロジェクト) をエンド ツー エンドで完了するために必要な別の作業項目です。 ストーリーの例を次に示します。
    - データの取得 
    - データの探索 
    - 機能の生成
    - モデルのビルド
    - モデルの運用 
    - モデルの再トレーニング
- **タスク**:タスクは、特定のストーリーを完了するために実行する必要がある割り当て可能なコードまたはドキュメント作業項目やその他のアクティビティです。 たとえば、ストーリー "*データの取得*" のタスクは次のようになります。
    -  SQL Server の資格情報の取得 
    -  SQL Data Warehouse へのデータのアップロード。 
- **バグ**:バグは通常、タスクの完了時に実行される既存のコードまたはドキュメントに必要な修正を表します。 バグの原因がステージまたはタスクの不足の場合は、それぞれストーリーまたはタスクにエスカレートできます。 

> [!NOTE]
> ソフトウェア コード管理 (SCM) における機能、ストーリー、タスク、バグの概念を借用して、データ サイエンスで使用します。 これらは、従来の SCM 定義とは若干異なる場合があります。
>
>

> [!NOTE]
> データ サイエンティストの場合、TDSP ライフサイクル ステージに合わせて細かく調整されているアジャイル テンプレートが使いやすい可能性があります。 この点を考慮してアジャイル由来のスプリント計画テンプレートは作成され、エピック、ストーリーなどは TDSP ライフサイクル ステージやサブステージで置き換えられています。 アジャイル テンプレートを作成する方法の手順については、[Visual Studio Online でのアジャイル データ サイエンス プロセスの設定](agile-development.md#set-up-agile-dsp-6)に関するセクションをご覧ください。
>
>

## 2.<a name='SprintPlanning-2'></a>スプリント計画 

スプリント計画は、プロジェクトの優先順位付けと、リソースの計画と割り当てに役立ちます。 多くのデータ サイエンティストは、それぞれ完了に数か月かかる複数のプロジェクトに従事しています。 多くの場合、プロジェクトはそれぞれ異なるペースで進行します。 Azure DevOps Services において、プロジェクト内で作業項目を簡単に作成、管理、および追跡することができ、プロジェクトの前方への移動が期待どおりに進むことを確認するためのスプリント計画を実施することができます。 

Azure DevOps Services におけるスプリント計画に関する詳細な手順説明については、[このリンク](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning)に従ってください。 


## 手順 3.<a name='AddFeature-3'></a>機能の追加  

プロジェクトにプロジェクト リポジトリが作成されたら、チームの **[概要]** ページに移動し、**[作業の管理]** をクリックします。

![2](./media/agile-development/2-sprint-team-overview.png)

機能をバックログに含めるには、**[バックログ]**  -->  **[機能]**  -->  **[新規]** をクリックし、機能の **[タイトル]** (通常はプロジェクト名) を入力し、**[追加]** をクリックします。

![3](./media/agile-development/3-sprint-team-add-work.png)

作成した機能をダブルクリックします。 説明を入力し、この機能のチーム メンバーを割り当て、この機能の計画パラメーターを設定します。 

この機能をプロジェクト リポジトリにリンクすることもできます。 **[開発]** セクションの下にある **[リンクの追加]** をクリックします。 機能の編集が完了したら、**[保存して閉じる]** をクリックして終了します。


## 4.<a name='AddStoryunderfeature-4'></a>機能の下にストーリーを追加する 

機能の下に、(機能) プロジェクトの完了に必要な主要なステップを記述するストーリーを追加できます。 新しいストーリーを追加するには、バックログ ビューの機能の左側にある **[+]** 記号をクリックします。  

![4](./media/agile-development/4-sprint-add-story.png)

状態、説明、コメント、計画、優先順位などのストーリーの詳細をポップアップ ウィンドウで編集することができます。

![5](./media/agile-development/5-sprint-edit-story.png)

このストーリーを既存のリポジトリにリンクするには、**[開発]** の下にある **[+ リンクを追加]** をクリックします。 

![6](./media/agile-development/6-sprint-link-existing-branch.png)


## 5.<a name='AddTaskunderstory-5'></a>タスクをストーリーに追加する 

タスクは、各ストーリーを完了するために必要な特定の詳細な手順です。 ストーリーのすべてのタスクが完了したら、ストーリーも完了する必要があります。 

ストーリーにタスクを追加するには、ストーリー項目の横にある **[+]** 記号をクリックし、**[タスク]** を選択してから、このタスクの詳細情報をポップアップ ウィンドウに入力します。

![7](./media/agile-development/7-sprint-add-task.png)

機能、ストーリー、タスクを作成したら、それらを **[バックログ]** または **[ボード]** ビューで表示して、状態を追跡できます。

![8](./media/agile-development/8-sprint-backlog-view.png)

![9](./media/agile-development/9-link-to-a-new-branch.png)


## 6.<a name='set-up-agile-dsp-6'></a>Visual Studio Online でのアジャイル TDSP 作業テンプレート

この記事では、TDSP データ サイエンス ライフサイクル ステージを使用して Visual Studio Online (vso) で作業項目を追跡するアジャイル データ サイエンス プロセス テンプレートを設定する方法について説明します。 以下の手順では、データ サイエンス固有のアジャイル プロセス テンプレートである *AgileDataScienceProcess* を設定する例を確認して、テンプレートに基づいてデータ サイエンスの作業項目を作成する方法を示します。

### <a name="agile-data-science-process-template-setup"></a>アジャイル データ サイエンス プロセス テンプレートの設定

1. サーバーのホーム ページから、**[構成]** -> **[プロセス]** に移動します。

    ![10](./media/agile-development/10-settings.png) 

2. **[アジャニル]** で **[All processes\(すべてのプロセス\)]** -> **[プロセス]** に移動して、**[継承されたプロセスの作成]** をクリックします。 次に、プロセスの名前に「AgileDataScienceProcess」と入力し、**[プロセスの作成]** をクリックします。

    ![11](./media/agile-development/11-agileds.png)

3. **[AgileDataScienceProcess]** ->  **[作業項目の種類]** タブで、**[構成] -> [無効]** の順にクリックして、**[エピック]**、**[機能]**、**[ユーザー ストーリー]**、および **[タスク]** 作業項目を無効にします。

    ![12](./media/agile-development/12-disable.png)

4. **[AgileDataScienceProcess]** -> **[バックログ レベル]** タブに移動します。**[構成]** -> **[編集/名前変更]** の順にクリックして、"Epics" から "TDSP Projects" へ名前を変更します。 同じダイアログ ボックスで、データ サイエンス プロジェクトの **[+新しい作業項目の種類]** をクリックし、**[既定の作業項目の種類]** を "TDSP Project" に変更します。 

    ![13](./media/agile-development/13-rename.png)  

5. 同様に、バックログ名を "Features" から "TDSP Stages" に変更して、**[新しい作業項目の種類]** に以下を追加します。

    - ビジネスの把握
    - データの取得
    - モデリング
    - Deployment

6. 新しく作成された "TDSP Substage" 種類に既定の作業項目の種類を設定して、"User Story" を "TDSP Substages" という名前に変更します。

7. "Tasks" を新しく作成された作業項目の種類である "TDSP Task" に設定します。 

8. これらの手順を終えると、バックログ レベルは以下のように表示されます。

    ![14](./media/agile-development/14-template.png)  

 
### <a name="create-data-science-work-items"></a>データ サイエンスの作業項目の作成

データ サイエンス プロセス テンプレートを作成した後は、TDSP ライフ サイクルに対応するデータ サイエンスの作業項目を作成して追跡できます。

1. 新しいプロジェクトを作成するときは、**[作業項目プロセス]** に "Agile\AgileDataScienceProcess" を選択します。

    ![15](./media/agile-development/15-newproject.png)

2. 新しく作成されたプロジェクトに移動して、**[作業]** -> **[バックログ]** の順にクリックします。

3. "TDSP Projects" が表示されるようにするには、**[チームの設定を構成します]** をクリックして、"TDSP Projects" のチェックをオンにして保存します。

    ![16](./media/agile-development/16-enabledsprojects.png)

4. これで、データ サイエンス固有の作業項目の作成を開始できるようになりました。

    ![17](./media/agile-development/17-dsworkitems.png)

5. データ サイエンス プロジェクトの作業項目が表示された状態のサンプルを次に示します。

    ![18](./media/agile-development/18-workitems.png)


## <a name="next-steps"></a>次の手順

「[Collaborative coding with Git (Git による共同でのコーディング)](collaborative-coding-with-git.md)」では、共有コード開発フレームワークとして Git を使用してデータ サイエンス プロジェクトの共同コード開発を行う方法と、アジャイル プロセスを使って計画された作業にこれらのコーディング アクティビティをリンクする方法について説明しています。

アジャイル プロセスに関する資料への追加リンクを次に示します。

- アジャイル プロセス   [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process)
- アジャイル プロセスの作業項目の種類とワークフロー   [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow)


また、**特定のシナリオ**のプロセスにおけるすべての手順を説明するチュートリアルも用意されています。 これらは、[サンプル チュートリアル](walkthroughs.md)の記事で簡単な説明と共にリンク付きで紹介されています。 チュートリアルでは、クラウド、オンプレミスのツール、サービスをワークフローまたはパイプラインに組み込んでインテリジェント アプリケーションを作成する方法を説明しています。 
