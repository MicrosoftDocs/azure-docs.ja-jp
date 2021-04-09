---
title: Azure Monitor を使用して System Center Operations Manager を評価する
description: System Center Operations Manager Health Check ソリューションを使用すると、環境のリスクと正常性を定期的に評価できます。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2018
ms.openlocfilehash: 35ae1e09fd0a06014a747cef99631a7bfe2dee1c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731410"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>System Center Operations Manager Health Check (プレビュー) ソリューションを使用して環境を最適化する

![System Center Operations Manager Health Check のシンボル](./media/scom-assessment/scom-assessment-symbol.png)

System Center Operations Manager Health Check ソリューションを使用すると、System Center Operations Manager 管理グループのリスクと正常性を定期的に評価できます。 この記事は、潜在的な問題の修正措置を実行できるように、ソリューションをインストール、構成、および使用するために役立ちます。

このソリューションでは、デプロイされているサーバー インフラストラクチャに固有の優先順位付けされた推奨事項の一覧を提供します。 推奨事項は 4 つの対象領域に分類されているので、すばやくリスクを把握し、修正措置を実行できます。

推奨事項は、マイクロソフトのエンジニアによる数多くの顧客訪問によって得られた知識と経験に基づいています。 各推奨事項では、問題が重要である理由と推奨される変更を実装する方法に関するガイダンスが提供されます。

組織にとって最も重要な対象領域を選択し、リスクのない正常な環境の実行に向けた進行状況を追跡できます。

ソリューションを追加し、評価が実行されると、インフラストラクチャの **[System Center Operations Manager Health Check]** ダッシュボードに対象領域の概要情報が表示されます。 次のセクションでは、 **[System Center Operations Manager Health Check]** ダッシュボードの情報を使用する方法について説明します。ここでは、Operations Manager 環境を確認し、推奨された解決方法を実行できます。

![System Center Operations Manager ソリューションのタイル](./media/scom-assessment/log-analytics-scom-healthcheck-tile.png)

![System Center Operations Manager Health Check ダッシュボード](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>ソリューションのインストールと構成

このソリューションは、Microsoft System Center 2012 Operations Manager Service Pack 1、Microsoft System Center 2012 R2 Operations Manager、Microsoft System Center 2016 Operations Manager、Microsoft System Center 2016 Operations Manager、および Microsoft System Center Operations Manager 1807 で動作します。 サポートされているバージョンの .NET Framework 4.6.2 を各管理サーバーにインストールする必要があります。

次の情報を使用して、ソリューションをインストールおよび構成します。

- Log Analytics の正常性チェック ソリューションを使用するには、ソリューションが事前にインストールされている必要があります。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview) からソリューションをインストールします。

- ソリューションをワークスペースに追加すると、ダッシュ ボードの **[System Center Operations Manager Health Check]** タイルには、追加の構成が必要だというメッセージが表示されます。 タイルをクリックし、ページに表示される構成の手順に従います。

  ![System Center Operations Manager ダッシュボード タイル](./media/scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> System Center Operations Manager の構成は、Log Analytics のソリューションの構成ページに表示される手順に従って、スクリプトを使用して実行できます。

 Operations Manager オペレーション コンソールで評価を構成するには、以下の手順を順番に実行します。
1. [System Center Operations Manager Health Check で使用する実行アカウントを設定します](#operations-manager-run-as-accounts-for-log-analytics)  
2. System Center Operations Manager Health Check ルールを構成します

## <a name="system-center-operations-manager-health-check-data-collection-details"></a>System Center Operations Manager Health Check によるデータ収集の詳細

System Center Operations Manager Health Check ソリューションは、次のソースからデータを収集します。

* レジストリ
* Windows Management Instrumentation (WMI)
* イベント ログ
* ファイル データ
* 指定した管理サーバーから、PowerShell と SQL クエリを使用して Operations Manager のデータを直接収集します。  

データは管理サーバーで収集され、7 日ごとに Log Analytics に転送されます。  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Log Analytics で使用される Operations Manager の実行アカウント

Log Analytics は、ワークロード用の管理パックを基に付加価値サービスを実現しています。 それぞれのワークロードがさまざまなセキュリティ コンテキストで管理パックを実行するためには、ワークロード固有の特権が必要となります (ドメイン ユーザー アカウントなど)。 特権を持つ資格情報を使用して Operations Manager の実行アカウントを構成します。 詳細については、Operations Manager ドキュメントの「[How to create a Run As account](/previous-versions/system-center/system-center-2012-R2/hh321655(v=sc.12))」(実行アカウントを作成する方法) を参照してください。

次の情報を使用すると、System Center Operations Manager Health Check 用に Operations Manager の実行アカウントを設定できます。

### <a name="set-the-run-as-account"></a>実行アカウントの設定

次の手順に進む前に、実行アカウントは以下の要件を満たす必要があります。

* 任意の Operations Manager ロールをサポートするすべてのサーバー (管理サーバー、運用、データ ウェアハウス、および ACS データベースをホストする SQL Server、レポート、Web コンソール、およびゲートウェイ サーバー) で、ローカルの Administrator グループのメンバーであるドメイン ユーザー アカウント。
* 評価する管理グループの Operation Manager 管理者ロール
* アカウントに SQL sysadmin 権限がない場合は、[スクリプト](#sql-script-to-grant-granular-permissions-to-the-run-as-account)を実行して、Operations Manager データベースの 1 つまたはすべてをホストする各 SQL Server インスタンスにアカウントに細かいアクセス許可を付与します。

1. Operations Manager コンソールで、 **[管理]** ナビゲーション ボタンを選択します。
2. **[実行アカウントの構成]** で、 **[アカウント]** をクリックします。
3. **[実行アカウントの作成]** ウィザードの **[はじめに]** ページで **[次へ]** をクリックします。
4. **[全般プロパティ]** ページの **[実行アカウントの種類]** リストで **[Windows]** を選択します。
5. **[表示名]** テキスト ボックスに表示名を入力し、 **[説明]** ボックス (省略可能) に説明を入力し、 **[次へ]** をクリックします。
6. **[配布セキュリティ]** ページで、 **[高いセキュリティ]** を選択します。
7. **Create** をクリックしてください。  

以上の手順で実行アカウントが作成されます。次は管理グループで管理サーバーをターゲットにして、定義済みの実行プロファイルと関連付け、ワークフローが資格情報を使用して実行されるようにします。  

1. 結果ウィンドウの **[実行アカウントの構成]** の **[アカウント]** で、前述の手順で作成したアカウントをダブルクリックします。
2. **[配布]** タブで、 **[選択したコンピューター]** ボックスの **[追加]** をクリックし、管理サーバーを追加してアカウントを配布します。  **[OK]** をクリックして変更を保存します。
3. **[実行アカウントの構成]** で、 **[プロファイル]** をクリックします。
4. "*SCOM Assessment プロファイル*" を検索します。
5. プロファイル名は、*Microsoft System Center Operations Manager Health Check Run As Profile* になります。
6. 右クリックしてそのプロパティを更新し、前述の手順で作成した実行アカウントを追加します。

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>実行アカウントに詳細なアクセス許可を付与する SQL スクリプト

次の SQL スクリプトを実行して、運用、データ ウェアハウス、および ACS データベースをホストする Operations Manager で使用する SQL Server インスタンスの実行アカウントに必要なアクセス許可を付与します。

```
-- Replace <UserName> with the actual user name being used as Run As Account.
USE master

-- Create login for the user, comment this line if login is already created.
CREATE LOGIN [UserName] FROM WINDOWS


--GRANT permissions to user.
GRANT VIEW SERVER STATE TO [UserName]
GRANT VIEW ANY DEFINITION TO [UserName]
GRANT VIEW ANY DATABASE TO [UserName]

-- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
-- NOTE: This command must be run anytime new databases are added to SQL Server instances.
EXEC sp_msforeachdb N'USE [?]; CREATE USER [UserName] FOR LOGIN [UserName];'

Use msdb
GRANT SELECT To [UserName]
Go

--Give SELECT permission on all Operations Manager related Databases

--Replace the Operations Manager database name with the one in your environment
Use [OperationsManager];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager DatawareHouse database name with the one in your environment
Use [OperationsManagerDW];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager Audit Collection database name with the one in your environment
Use [OperationsManagerAC];
GRANT SELECT To [UserName]
GO

--Give db_owner on [OperationsManager] DB
--Replace the Operations Manager database name with the one in your environment
USE [OperationsManager]
GO
ALTER ROLE [db_owner] ADD MEMBER [UserName]

```

### <a name="configure-the-health-check-rule"></a>正常性チェック ルールの構成

System Center Operations Manager Health Check ソリューションの管理パックには、*Microsoft System Center Operations Manager Run Health Check Rule* という名前のルールが含まれています。 このルールは、正常性チェックの実行に関するルールです。 ルールを有効にし、頻度を構成するには、次の手順に従います。

既定では、Microsoft System Center Operations Manager Run Health Check Rule は無効になります。 正常性チェックを実行するには、管理サーバーでルールを有効にする必要があります。 次の手順に従います。

#### <a name="enable-the-rule-for-a-specific-management-server"></a>特定の管理サーバーのルールを有効にする

1. Operations Manager Operations コンソールの **[作成]** ワークスペースの **[ルール]** ウィンドウで、*Microsoft System Center Operations Manager Run Health Check Rule* を検索します。
2. 検索結果で、"*タイプ:管理サーバー*" というテキストが含まれているルールを選択します。
3. ルールを右クリックし、 **[オーバーライド]**  >  **[クラス "管理サーバー" の特定のManagement Server]\(クラス "管理サーバー" のすべてのオブジェクト\)** の順にクリックします。
4.  利用できる管理サーバーの一覧で、ルールを実行する管理サーバーを選択します。  前述の手順で実行アカウントを関連付けるために構成したものと同じ管理サーバーを選択する必要があります。
5.  **[Enabled (有効)]** パラメーター値の [オーバーライド値] を **[True]** に変更します。<br><br> ![パラメーターのオーバーライド](./media/scom-assessment/rule.png)

    引き続きこのウィンドウで次の手順を使用して、実行の頻度を構成します。

#### <a name="configure-the-run-frequency"></a>実行頻度を構成する

評価は、既定の間隔で 10,080 分 (7 日間) ごとに実行されるように構成されます。 この値は、最小値の 1,440 分 (1 日) までの値にオーバーライドすることができます。 この値は、評価を連続して実行する際に必要な最小の時間間隔を表します。 間隔をオーバーライドするには、次の手順を使用します。

1. Operations Manager コンソールの **[作成]** ワークスペースの **[ルール]** セクションで、ルール *Microsoft System Center Operations Manager Run Health Check Rule* を検索します。
2. 検索結果で、"*タイプ:管理サーバー*" というテキストが含まれているルールを選択します。
3. ルールを右クリックし、 **[Override the Rule]\(ルールをオーバーライドする\)**  >  **[For all objects of class:Management Server]\(クラス "管理サーバー" のすべてのオブジェクト\)** の順にクリックします。
4. **[間隔]** パラメーター値を目的の間隔値に変更します。 次の例では、値を 1,440 分 (1 日) に設定しています。<br><br> ![間隔パラメーター](./media/scom-assessment/interval.png)<br>  

    1,440 分未満の値を設定した場合、ルールは 1 日間隔で実行されます。 この例では、ルールは、間隔値を無視して 1 日の頻度で実行されます。


## <a name="understanding-how-recommendations-are-prioritized"></a>推奨事項の優先順位設定方法について

提供されるすべての推奨事項には、推奨事項の相対的な重要度を示す重み付け値が与えられます。 最も重要な 10 個の推奨事項のみが表示されます。

### <a name="how-weights-are-calculated"></a>重み付けの計算方法

重み付けは、次の 3 つの重要な要因に基づく集計値です。

- 識別された注意点によって問題が発生する *確率* 。 確率が高いほど、推奨事項に割り当てられる総合スコアが大きくなります。
- 問題が発生する原因となった場合の注意点の組織への *影響度* 。 影響度が高いほど、推奨事項に割り当てられる総合スコアが大きくなります。
- 推奨実行を実装するために必要な *作業量* 。 作業量が多いほど、推奨事項に割り当てられる総合スコアが小さくなります。

各推奨事項に対する重み付けは、各対象領域で使用可能な合計スコアの割合として表されます。 たとえば、可用性とビジネス継続性対象領域の推奨事項のスコアが 5% である場合、その推奨事項を実装すると可用性とビジネス継続性の総合スコアが 5% 上昇します。

### <a name="focus-areas"></a>対象領域

**可用性とビジネス継続性** - この対象領域は、サービスの可用性、インフラストラクチャの回復性、およびビジネス保護に関する推奨事項を示しています。

**パフォーマンスとスケーラビリティ** - この対象領域は、組織の IT インフラストラクチャを拡張し、IT 環境が現在のパフォーマンス要件を満たして、インフラストラクチャのニーズの変化に対応できるようにするために役立つ推奨事項を示しています。

**アップグレード、移行、デプロイ** - この対象領域は、既存のインフラストラクチャへの SQL Server のアップグレード、移行、およびデプロイに役立つ推奨事項を示しています。

**運用と監視** - この対象領域は、IT の運用の合理化、予防保守の実装、パフォーマンスの最大化に役立つ推奨事項を示しています。

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>すべての対象領域で 100% のスコアを目指す必要がありますか?

必ずしもその必要はありません。 推奨事項は、マイクロソフトのエンジニアによる数多くの顧客訪問によって得られた知識と経験に基づいています。 ただし、まったく同じサーバー インフラストラクチャは存在せず、関連性の高い、または低い推奨事項が存在する可能性があります。 たとえば、仮想マシンがインターネットに対して公開されていない場合、セキュリティに関する一部の推奨事項は関連性が低い場合があります。 優先度の低いアドホックなデータ収集とレポート作成を提供するサービスの場合、可用性に関する一部の推奨事項は関連性が低い可能性があります。 成熟したビジネスにとって重要な問題は、起業したばかりの会社には重要性が低い場合があります。 ユーザーが優先度の高い対象領域を識別して、スコアの経時変化を監視できます。

すべての推奨事項には、重要である理由についてのガイダンスが含まれます。 このガイダンスを使用し、IT サービスの性質と組織のビジネス ニーズに基づいて、推奨事項を実装することが会社にとって適切かどうかを評価する必要があります。

## <a name="use-health-check-focus-area-recommendations"></a>正常性チェックの関心領域に関する推奨事項の使用

Log Analytics の正常性チェック ソリューションを使用するには、ソリューションが事前にインストールされている必要があります。 ソリューションのインストールの詳細については、[管理ソリューションのインストール](./solutions.md)に関する記事を参照してください。 インストール後は、Azure portal でワークスペースの **[概要]** ページの [System Center Operations Manager Health Check] タイルを使用して、推奨事項の概要を表示できます。

インフラストラクチャの準拠に関する評価の概要を表示してから、推奨事項を確認します。

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>対象領域の推奨事項を表示して修正措置を行うには
1. Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にログインします。
2. Azure ポータルで、左下隅にある **[その他のサービス]** をクリックします。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics]** を選択します。
3. Log Analytics サブスクリプション ウィンドウでワークスペースを選択し、 **[ワークスペースの概要]** メニュー項目をクリックします。  
4. **[概要]** ページの **[System Center Operations Manager Health Check]** タイルをクリックします。
5. **[System Center Operations Manager Health Check]** ページの対象領域のいずれかのブレードで概要情報を確認し、いずれかの情報をクリックして、その対象領域の推奨事項を表示します。
6. いずれの対象領域ページでも、ユーザーの環境を対象とした、優先順位が付けられた推奨事項を表示できます。 推奨事項の理由の詳細を確認するには、 **[影響を受けるオブジェクト]** でその推奨事項をクリックします。<br><br> ![対象領域](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. **[推奨する解決方法]** で推奨された修正措置を実行することができます。 項目に対応すると、それ以降の評価では、推奨されたアクションが行われたと記録され、準拠のスコアが上がります。 修正された項目は **[合格したオブジェクト]** として表示されます。

## <a name="ignore-recommendations"></a>推奨事項を無視する

無視する推奨事項がある場合は、Log Analytics が使用するテキスト ファイルを作成して、推奨事項が評価結果に表示されないようにすることができます。

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>無視する推奨事項を識別するには
1. Azure portal の選択したワークスペースの Log Analytics ワークスペース ページで、 **[ログ検索]** メニュー項目をクリックします。
2. 次のクエリを使用して、環境内のコンピューターで失敗した推奨事項の一覧を表示します。

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > ワークスペースが[新しい Log Analytics クエリ言語](../logs/log-query-overview.md)にアップグレードされている場合は、上記のクエリによって次が変更されます。
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    ログ検索のクエリを示すスクリーンショットを次に示します。<br><br> ![ログ検索](./media/scom-assessment/scom-log-search.png)<br>

3. 無視する推奨事項を選択します。 次の手順で RecommendationId の値を使用します。

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>IgnoreRecommendations.txt テキスト ファイルを作成および使用するには

1. IgnoreRecommendations.txt という名前のファイルを作成します。
2. Log Analytics に個別の行で無視させ、ファイルを保存して閉じさせるには、推奨事項ごとにそれぞれ RecommendationId を貼り付けるか入力します。
3. Log Analytics に推奨事項を無視させる各コンピューターの次のフォルダーにファイルを配置します。
4. Operations Manager 管理サーバー - *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server。

### <a name="to-verify-that-recommendations-are-ignored"></a>推奨事項が無視されていることを確認するには

1. 次回スケジュールされている評価が実行した後は、既定では 7 日おきで、推奨事項が Ignored とマークされ、正常性チェック ダッシュボードには表示されません。
2. 次のログ検索クエリを使用して、無視されるすべての推奨事項の一覧を表示します。

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > ワークスペースが[新しい Log Analytics クエリ言語](../logs/log-query-overview.md)にアップグレードされている場合は、上記のクエリによって次が変更されます。
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. 無視された推奨事項を表示することを後で決定する場合は、IgnoreRecommendations.txt ファイルを削除します。また、そのファイルから RecommendationID を削除することもできます。

## <a name="system-center-operations-manager-health-check-solution-faq"></a>System Center Operations Manager Health Check ソリューションについてよく寄せられる質問 (FAQ)

*Log Analytics ワークスペースに Health Check ソリューションを追加しました。推奨事項が表示されません。なぜ表示されないのですか?* " ソリューションを追加したら、次の手順に従って Log Analytics のダッシュ ボードに推奨事項を表示します。  

- [System Center Operations Manager Health Check で使用する実行アカウントを設定します](#operations-manager-run-as-accounts-for-log-analytics)  
- [System Center Operations Manager Health Check ルールを構成します](#configure-the-health-check-rule)


*チェックの実行頻度を構成する方法はありますか?* はい。 「[実行頻度を構成する](#configure-the-run-frequency)」を参照してください。

*System Center Operations Manager Health Check ソリューションを追加した後に別のサーバーが検出された場合、それはチェックされますか?* はい。検出された時点からチェックされます (既定では 7 日ごと)。

*データ収集を行うプロセスの名前は何ですか?* AdvisorAssessment.exe です。

"*AdvisorAssessment.exe プロセスはどこで実行されますか。* " AdvisorAssessment.exe は、正常性チェック ルールが有効になっている管理サーバーの HealthService プロセスで実行されます。 そのプロセスを使用して、リモート データ コレクションを通じて環境全体の検出が実現します。

"*データの収集にはどれくらいの時間がかかりますか。* " サーバー上でのデータ収集には約 1 時間かかります。 多数の Operations Manager インスタンスまたはデータベースが存在する環境では、それよりも長く時間がかかる場合があります。

"*評価の間隔を 1,440 分未満に設定するとどうなりますか。* " 評価は、最大で 1 日に 1 回実行するように事前構成されています。 間隔値を 1,440 分未満の値にオーバーライドすると、1,440 分が評価の間隔値として使用されます。

*前提条件に障害が発生しているかどうかを調べる方法はありますか?* 正常性チェックを実行したのに結果が表示されない場合は、チェックの前提条件のいくつかが失敗している可能性があります。 ログ検索で `Operation Solution=SCOMAssessment` と `SCOMAssessmentRecommendation FocusArea=Prerequisites` のクエリを実行すると、失敗した前提条件を確認できます。

*前提条件のエラーにメッセージ `Failed to connect to the SQL Instance (….).` が含まれています。どのような問題が発生していますか。* " データを収集するプロセスである AdvisorAssessment.exe は、管理サーバーの HealthService プロセスで実行されます。 このプロセスは、正常性チェックの一環として Operations Manager データベースが存在する SQL Server への接続を試みます。 このエラーは、ファイアウォール規則によって SQL Server インスタンスへの接続がブロックされた場合に発生することがあります。

*どのような種類のデータが収集されますか?* 次の種類のデータが収集されます。- WMI データ - レジストリ データ - EventLog データ - Operations Manager データ (Windows PowerShell、SQL クエリ、およびファイル情報コレクターを通じて)。

*実行アカウントを構成しなければならないのはなぜですか?* Operations Manager では、さまざまな SQL クエリが実行されます。 SQL クエリを実行するためには、必要なアクセス許可を持つ実行アカウントを使用する必要があります。 さらに、WMI を照会するには、ローカル管理者の資格情報が必要です。

*上位 10 個の推奨事項しか表示されないのはなぜですか?* タスクの一覧を余すことなく完全に提供するのでなく、まず優先的な推奨事項への対処に重点を置くことをお勧めしています。 優先的な推奨事項に対処すると、追加の推奨事項が表示されます。 詳細な一覧を確認する場合は、ログ検索を使用してすべての推奨事項を表示することができます。

*推奨事項を無視する方法はありますか?* はい。「[推奨事項を無視する](#ignore-recommendations)」を参照してください。


## <a name="next-steps"></a>次のステップ

- [ログの検索](../logs/log-query-overview.md)で、詳細な System Center Operations Manager Health Check データと推奨事項を分析する方法を学びます。

