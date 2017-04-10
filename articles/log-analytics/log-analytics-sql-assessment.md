---
title: "Azure Log Analytics での SQL Server 環境の最適化 | Microsoft Docs"
description: "Azure Log Analytics では、SQL 評価ソリューションを使用して、SQL Server 環境のリスクと正常性を定期的に評価できます。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: e297eb57-1718-4cfe-a241-b9e84b2c42ac
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a0c8af30fbed064001c3fd393bf0440aa1cb2835
ms.openlocfilehash: a78c3877ac64b9376104d853d6b66b90fbb476d8
ms.lasthandoff: 02/28/2017


---
# <a name="optimize-your-sql-server-environment-with-the-sql-assessment-solution-in-log-analytics"></a>Log Analytics で SQL 評価ソリューションを使用して SQL Server 環境を最適化する

SQL 評価ソリューションを使用して、サーバー環境のリスクと正常性を定期的に評価します。 この記事は、潜在的な問題の修正措置を実行できるように、ソリューションをインストールするために役立ちます。

このソリューションでは、デプロイされているサーバー インフラストラクチャに固有の優先順位付けされた推奨事項の一覧を提供します。 推奨事項は&6; つの対象領域に分類されているので、すばやくリスクを把握し、修正措置を実行できます。

推奨事項は、マイクロソフトのエンジニアによる数多くの顧客訪問によって得られた知識と経験に基づいています。 各推奨事項では、問題が重要である理由と推奨される変更を実装する方法に関するガイダンスが提供されます。

組織にとって最も重要な対象領域を選択し、リスクのない正常な環境の実行に向けた進行状況を追跡できます。

ソリューションを追加し、評価が完了すると、環境のインフラストラクチャの **[SQL 評価]** ダッシュボードに対象領域の概要情報が表示されます。 次のセクションでは、**[SQL 評価]** ダッシュボードの情報を使用する方法について説明します。ここでは、SQL サーバー インフラストラクチャを確認し、推奨された解決方法を実行できます。

![[SQL 評価] タイルの画像](./media/log-analytics-sql-assessment/sql-assess-tile.png)

![[SQL 評価] ダッシュボードの画像](./media/log-analytics-sql-assessment/sql-assess-dash.png)

## <a name="installing-and-configuring-the-solution"></a>ソリューションのインストールと構成
SQL 評価は、Standard、Developer、Enterprise の各エディションの、現在サポートされているすべてのバージョンの SQL Server に対応しています。

次の情報を使用して、ソリューションをインストールおよび構成します。

* エージェントは、SQL Server がインストールされているサーバーにインストールする必要があります。
* SQL 評価ソリューションには、OMS エージェントがある各コンピューターにインストールされている、サポートされているバージョンの .NET Framework 4 が必要です。
* ソリューションをインストールするには、Azure Portal で Azure サブスクリプションの管理者か共同作業者になっている必要があります。 さらに、OMS ポータルで OMS ワークスペースの作成者または管理者ロールのメンバーになっている必要もあります。
* Operations Manager エージェントを SQL の評価に使用する場合は、Operations Manager の実行アカウントを使用する必要があります。 詳細については、「 [OMS で使用される Operations Manager の実行アカウント](#operations-manager-run-as-accounts-for-oms) 」を参照してください。

  > [!NOTE]
  > MMA エージェントでは、Operations Manager の実行アカウントはサポートされていません。
  >
  >
* 「 [ソリューション ギャラリーから Log Analytics ソリューションを追加する](log-analytics-add-solutions.md)」で説明されている手順に従って SQL 評価ソリューションを OMS ワークスペースに追加します。 さらに手動で構成する必要はありません。

> [!NOTE]
> ソリューションを追加した後、AdvisorAssessment.exe ファイルがエージェントを含むサーバーに追加されます。 構成データが読み取られ、処理のためにクラウドの OMS サービスに送信されます。 受信したデータにロジックが適用され、クラウド サービスによってそのデータが記録されます。

## <a name="sql-assessment-data-collection-details"></a>SQL 評価データ収集の詳細
SQL 評価では、有効になっているエージェントを使用して、WMI データ、レジストリ データ、パフォーマンス データ、SQL Server の動的管理の表示結果を収集します。

次の表に、エージェントのデータ収集方法、Operations Manager (SCOM) が必要であるかどうか、およびどのくらいの頻度でデータがエージェントによって収集されるかを示します。

| プラットフォーム | 直接エージェント | SCOM エージェント | Azure Storage (Azure Storage) | SCOM の要否 | 管理グループによって送信される SCOM エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![あり](./media/log-analytics-sql-assessment/oms-bullet-green.png) |![あり](./media/log-analytics-sql-assessment/oms-bullet-green.png) |![なし](./media/log-analytics-sql-assessment/oms-bullet-red.png) |![いいえ](./media/log-analytics-sql-assessment/oms-bullet-red.png) |![あり](./media/log-analytics-sql-assessment/oms-bullet-green.png) |7 日 |

## <a name="operations-manager-run-as-accounts-for-oms"></a>OMS で使用される Operations Manager の実行アカウント
OMS の Log Analytics では、データの収集と OMS サービスへのデータの送信に、Operations Manager エージェントと管理グループを使用します。 OMS は、ワークロード用の管理パックを基に付加価値サービスを実現しています。 それぞれのワークロードがさまざまなセキュリティ コンテキストで管理パックを実行するためには、ワークロード固有の特権が必要となります (ドメイン アカウントなど)。 Operations Manager の実行アカウントを構成して資格情報を与えることが必要です。

次の情報を使用すると、SQL の評価用に Operations Manager の実行アカウントを設定できます。

### <a name="set-the-run-as-account-for-sql-assessment"></a>SQL の評価に使用する実行アカウントの設定
 SQL Server 管理パックを既に使用している場合、SQL Server の実行アカウントを使用する必要があります。

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>オペレーション コンソールで SQL の実行アカウントを構成するには
> [!NOTE]
> SCOM エージェントではなく、OMS ダイレクト エージェントを使用している場合、管理パックは、常にローカル システム アカウントのセキュリティ コンテキストで実行されます。 以下の手順 1. ～ 5. をスキップし、ユーザー名として NT AUTHORITY\SYSTEM を指定して、T-SQL または PowerShell のサンプルを実行します。
>
>

1. Operations Manager でオペレーション コンソールを開き、 **[管理]**をクリックします。
2. **[実行アカウントの構成]** の **[プロファイル]** をクリックし、**[OMS SQL の評価の実行プロファイル]** を開きます。
3. **[実行アカウント]** ページの **[追加]** をクリックします。
4. SQL Server に必要な資格情報を含んだ Windows 実行アカウントを選択するか、 **[新規]** をクリックして新たに作成します。

   > [!NOTE]
   > 実行アカウントの種類は Windows であることが必要です。 さらに、SQL Server インスタンスをホストするすべての Windows Server 上のローカルの Administrators グループに、その実行アカウントが属している必要があります。
   >
   >
5. **[保存]**をクリックします。
6. 次の T-SQL サンプルに変更を加えて、各 SQL Server インスタンスで実行します。実行アカウントで SQL の評価を行うために必要な最低限の権限が付与されます。 ただし、実行アカウントが既に SQL Server インスタンスの sysadmin サーバー ロールに属している場合、この作業は不要です。

```
---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```
#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Windows PowerShell を使用して SQL 実行アカウントを構成するには
PowerShell ウィンドウを開き、次のスクリプトに自分の情報を反映して実行します。

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>推奨事項の優先順位設定方法について
提供されるすべての推奨事項には、推奨事項の相対的な重要度を示す重み付け値が与えられます。 最も重要な&10; 個の推奨事項のみが表示されます。

### <a name="how-weights-are-calculated"></a>重み付けの計算方法
重み付けは、次の&3; つの重要な要因に基づく集計値です。

* 識別された注意点によって問題が発生する *確率* 。 確率が高いほど、推奨事項に割り当てられる総合スコアが大きくなります。
* 問題が発生する原因となった場合の注意点の組織への *影響度* 。 影響度が高いほど、推奨事項に割り当てられる総合スコアが大きくなります。
* 推奨実行を実装するために必要な *作業量* 。 作業量が多いほど、推奨事項に割り当てられる総合スコアが小さくなります。

各推奨事項に対する重み付けは、各対象領域で使用可能な合計スコアの割合として表されます。 たとえば、セキュリティとコンプライアンス対象領域の推奨事項のスコアが 5% である場合、その推奨事項を実装するとセキュリティとコンプライアンスの総合スコアが 5% 上昇します。

### <a name="focus-areas"></a>対象領域
**セキュリティとコンプライアンス** - この対象領域は、潜在的なセキュリティの脅威と侵害、企業のポリシー、および技術、法律、規制のコンプライアンス要件に関する推奨事項を示しています。

**可用性とビジネス継続性** - この対象領域は、サービスの可用性、インフラストラクチャの回復性、およびビジネス保護に関する推奨事項を示しています。

**パフォーマンスとスケーラビリティ** - この対象領域は、組織の IT インフラストラクチャを拡張し、IT 環境が現在のパフォーマンス要件を満たして、インフラストラクチャのニーズの変化に対応できるようにするために役立つ推奨事項を示しています。

**アップグレード、移行、デプロイメント** - この対象領域は、既存のインフラストラクチャへの SQL Server のアップグレード、移行、およびデプロイに役立つ推奨事項を示しています。

**運用と監視** - この対象領域は、IT の運用の合理化、予防保守の実装、パフォーマンスの最大化に役立つ推奨事項を示しています。

**変更管理と構成管理** - この対象領域は、日常業務を保護し、変更がインフラストラクチャに悪影響を及ぼさないことを保証し、変更管理手順を確立し、システムの構成を追跡および監査するのに役立つ推奨事項を示します。

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>すべての対象領域で 100% のスコアを目指す必要がありますか?
必ずしもその必要はありません。 推奨事項は、マイクロソフトのエンジニアによる数多くの顧客訪問によって得られた知識と経験に基づいています。 ただし、まったく同じサーバー インフラストラクチャは存在せず、関連性の高い、または低い推奨事項が存在する可能性があります。 たとえば、仮想マシンがインターネットに対して公開されていない場合、セキュリティに関する一部の推奨事項は関連性が低い場合があります。 優先度の低いアドホックなデータ収集とレポート作成を提供するサービスの場合、可用性に関する一部の推奨事項は関連性が低い可能性があります。 成熟したビジネスにとって重要な問題は、起業したばかりの会社には重要性が低い場合があります。 ユーザーが優先度の高い対象領域を識別して、スコアの経時変化を監視できます。

すべての推奨事項には、重要である理由についてのガイダンスが含まれます。 ユーザーはこのガイダンスを使用し、IT サービスの性質と組織のビジネス ニーズに基づいて、推奨事項を実装することが会社にとって適切かどうかを評価する必要があります。

## <a name="use-assessment-focus-area-recommendations"></a>評価の関心領域に関する推奨事項の使用
OMS の評価ソリューションを使用するには、ソリューションが事前にインストールされている必要があります。 ソリューションのインストールの詳細については、「 [ソリューション ギャラリーから Log Analytics ソリューションを追加する](log-analytics-add-solutions.md)」を参照してください。 インストール後は、OMS の [Overview (概要)] ページの [SQL Assessment (SQL 評価)] タイルを使用して、推奨事項の概要を表示できます。

インフラストラクチャの準拠に関する評価の概要を表示してから、推奨事項を確認します。

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>対象領域の推奨事項を表示して修正措置を行うには
1. **[概要]** ページで、**[SQL 評価]** タイルをクリックします。
2. **[SQL 評価]** ページの対象領域のいずれかのブレードで概要情報を確認し、いずれかの情報をクリックして、その対象領域の推奨事項を表示します。
3. いずれの対象領域ページでも、ユーザーの環境を対象とした、優先順位が付けられた推奨事項を表示できます。 推奨事項の理由の詳細を確認するには、 **[影響を受けるオブジェクト]** でその推奨事項をクリックします。  
    ![SQL 評価に関する推奨事項の画像](./media/log-analytics-sql-assessment/sql-assess-focus.png)
4. **[推奨する解決方法]**で推奨された修正措置を実行することができます。 項目に対応すると、それ以降の評価では、推奨されたアクションが行われたと記録され、準拠のスコアが上がります。 修正された項目は **[合格したオブジェクト]**として表示されます。

## <a name="ignore-recommendations"></a>推奨事項を無視する
無視する推奨事項がある場合は、OMS が使用するテキスト ファイルを作成して、推奨事項が評価結果に表示されないようにすることができます。

### <a name="to-identify-recommendations-that-you-will-ignore"></a>無視する推奨事項を識別するには
1. ワークスペースにサインインして、ログ検索を開きます。 次のクエリを使用して、環境内のコンピューターで失敗した推奨事項の一覧を表示します。

   ```
   Type=SQLAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
   ```

   ログ検索のクエリを示すスクリーン ショットを次に示します。![失敗した推奨事項](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)
2. 無視する推奨事項を選択します。 次の手順で RecommendationId の値を使用します。

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>IgnoreRecommendations.txt テキスト ファイルを作成および使用するには
1. IgnoreRecommendations.txt という名前のファイルを作成します。
2. OMS に個別の行で無視させ、ファイルを保存して閉じさせるには、推奨事項ごとにそれぞれ RecommendationId を貼り付けるか入力します。
3. OMS に推奨事項を無視させる各コンピューターの次のフォルダーにファイルを配置します。
   * Microsoft Monitoring Agent がインストールされたコンピューター (直接または Operations Manager 経由で接続されている) - *SystemDrive*:\Program Files\Microsoft Monitoring Agent\Agent
   * Operations Manager 管理サーバー - *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>推奨事項が無視されていることを確認するには
1. 次回スケジュールされている評価が実行した後は、既定では 7 日おきで、推奨事項が Ignored とマークされ、評価ダッシュボードには表示されません。
2. 次のログ検索クエリを使用して、無視されるすべての推奨事項の一覧を表示します。

   ```
   Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
   ```
3. 無視された推奨事項を表示することを後で決定する場合は、IgnoreRecommendations.txt ファイルを削除します。また、そのファイルから RecommendationID を削除することもできます。

## <a name="sql-assessment-solution-faq"></a>SQL 評価ソリューションに関する FAQ
*評価はどのくらいの頻度で実行されますか?*

* 評価は 7 日おきに実行されます。

*評価の実行頻度を構成する方法がありますか?*

* 現時点ではありません。

*SQL 評価ソリューションを追加後、別のサーバーが検出された場合、それは評価されますか?*

* はい。検出されると、それ以降 7 日おきに評価されます。

*サーバーを使用停止にした場合、それはいつ評価対象から除外されますか?*

* サーバーは、3 週間にわたりデータを送信しない場合、除外されます。

*データ収集を行うプロセスの名前は何ですか?*

* AdvisorAssessment.exe です。

*データの収集にはどれくらいの時間がかかりますか?*

* サーバー上での実際のデータ収集には約 1 時間かかります。 SQL のインスタンスまたはデータベースの数が多いサーバーでは、もっと長くなる可能性があります。

*どのような種類のデータが収集されますか?*

* 次の種類のデータが収集されます。
  * WMI
  * レジストリ
  * パフォーマンス カウンター
  * SQL の動的管理ビュー (DMV)

*データが収集されるタイミングを構成する方法はありますか?*

* 現時点ではありません。

*実行アカウントを構成しなければならないのはなぜですか?*

* SQL Server の場合、少数の SQL クエリが実行されます。 これらのクエリが動作するためには、SQL に対する VIEW SERVER STATE 権限を持つ実行アカウントを使用する必要があります。  さらに、WMI を照会するには、ローカル管理者の資格情報が必要です。

*上位 10 個の推奨事項しか表示されないのはなぜですか?*

* タスクの一覧を余すことなく完全に提供するのでなく、まず優先的な推奨事項への対処に重点を置くことをお勧めしています。 優先的な推奨事項に対処すると、追加の推奨事項が表示されます。 詳細な一覧を確認する場合は、OMS のログ検索を使用してすべての推奨事項を表示できます。

*推奨事項を無視する方法はありますか?*

* はい。前のセクション「[推奨事項を無視する](#ignore-recommendations)」を参照してください。

## <a name="next-steps"></a>次のステップ
* [ログ検索](log-analytics-log-searches.md) を実行して、詳細な SQL 評価データと推奨事項を表示します。

