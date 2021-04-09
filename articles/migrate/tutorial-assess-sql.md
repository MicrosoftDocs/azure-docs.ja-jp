---
title: Azure SQL Managed Instance および Azure SQL Database に移行するために、SQL インスタンスを評価するためのチュートリアル
description: Azure Migrate で Azure SQL の評価を作成する方法について説明します
author: rashi-ms
ms.author: rajosh
ms.topic: tutorial
ms.date: 02/07/2021
ms.openlocfilehash: 9b33890d53f67eee870b42462a65b4a0b7ba9981
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102053625"
---
# <a name="tutorial-assess-sql-instances-for-migration-to-azure-sql"></a>チュートリアル: Azure SQL への移行のために SQL インスタンスを評価する

Azure への移行に取り組む過程では、オンプレミスのワークロードを評価し、クラウドへの対応性を測り、リスクを明らかにして、コストと複雑さを見積もります。
この記事では、Azure Migrate: 検出および評価ツールを使用して、Azure SQL への移行に備えて、検出された SQL Server インスタンスおよびデータベースを評価する方法について説明します。

> [!Note]
> VMware 環境で実行されている SQL Server インスタンスおよびデータベースの検出と評価は、現在プレビュー段階にあります。 この機能を試すには、[**このリンク**](https://aka.ms/AzureMigrate/SQL)を使用して、**オーストラリア東部** リージョンにプロジェクトを作成します。 オーストラリア東部に既にプロジェクトがあり、この機能を試したい場合は、ポータルでこれらの [**前提条件**](how-to-discover-sql-existing-project.md)が完了していることを確認してください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * サーバー構成とパフォーマンス データに基づいて評価を実行する
> * Azure SQL の評価を確認する 

> [!NOTE]
> チュートリアルでは、シナリオを試すための最も簡単な方法を説明し、可能な限り既定のオプションを使用します。 


## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。

- Azure SQL に移行するために、このチュートリアルに従って SQL Server インスタンスを評価する前に、Azure Migrate アプライアンスを使用して、評価する SQL インスタンスが検出されていることを確認してください ([こちらのチュートリアル](tutorial-discover-vmware.md)をご覧ください)。

## <a name="run-an-assessment"></a>評価を実行する
評価を実行するには次のようにします。
1. **[概要]** ページの **[Windows, Linux and SQL Server]\(Windows、Linux、SQL Server\)** で、 **[サーバーの評価と移行]** をクリックします。
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Azure Migrate の [概要] ページ":::
2. **[Azure Migrate: Discovery and assessment]\(Azure Migrate: 検出および評価\)** で、 **[評価]** をクリックし、評価の種類として **Azure SQL** を選択します。
    :::image type="content" source="./media/tutorial-assess-sql/assess.png" alt-text="評価の種類として Azure SQL を選択するドロップダウン":::
3. **[サーバーの評価]** では、評価の種類として **Azure SQL** が事前に選択されており、検出ソースが既定で **[Azure Migrate アプライアンスから検出されたサーバー]** に設定されていることを確認できます。

4. **[編集]** をクリックして、評価のプロパティを確認します。
     :::image type="content" source="./media/tutorial-assess-sql/assess-servers-sql.png" alt-text="評価のプロパティをカスタマイズできる [編集] ボタン":::
5. [評価のプロパティ] > **[ターゲット プロパティ]** で:
    - 移行先となる Azure リージョンを **[ターゲットの場所]** で指定します。 
        - Azure SQL の構成とコストに関する推奨事項は、指定した場所に基づいています。 
    - **[Target deployment type]\(ターゲットのデプロイの種類\)** で:
        - Azure Migrate によって、Azure SQL MI および Azure SQL DB に移行するための SQL インスタンスの対応性が評価され、最適なターゲット デプロイ オプション、ターゲット レベル、Azure SQL 構成、月間見積もりが推奨されるようにする場合は、 **[推奨]** を選択します。 [詳細情報](concepts-azure-sql-assessment-calculation.md)
        - Azure SQL Database への移行のみを目的として SQL インスタンスの対応性を評価し、ターゲット レベル、Azure SQL 構成、月間見積もりを確認する場合は、 **[Azure SQL DB]** を選択します。
        - Azure SQL Managed Instance への移行のみを目的として SQL インスタンスの対応性を評価し、ターゲット レベル、Azure SQL 構成、月間見積もりを確認する場合は、 **[Azure SQL MI]** を選択します。
    - **[予約容量]** では、移行後に予約容量を SQL サーバーに使用するかどうかを指定します。
        - 予約容量オプションを選択した場合、[割引 (%)] を指定することはできません。

6. [評価のプロパティ] > **[Assessment criteria]\(評価基準\)** で:
    - [Sizing criteria]\(サイズ変更の設定基準\) は、既定で **[パフォーマンスベース]** に設定されています。つまり、Azure Migrate によって、SQL インスタンスとそれによって管理されているデータベースに関連するパフォーマンス メトリックが収集され、最適なサイズの Azure SQL Database 構成および Azure SQL Managed Instance 構成が推奨されます。 以下を指定できます。
        - **[パフォーマンス履歴]** : 評価の基準とするデータ期間を示します (既定値は 1 日)。
        - **[百分位の使用率]** : パフォーマンス サンプルに使用するパーセンタイル値を示します (既定値は 95 パーセンタイル)。
    - 評価中に使用したいバッファーを **[快適性係数]** で指定します。 ここでは、季節ごとの使用量、短期間のパフォーマンス履歴、将来に使用量が増える可能性などの問題が考慮されます。 たとえば、快適性係数を 2 とした場合、 
        
        **コンポーネント** | **有効使用率** | **快適性係数の追加 (2.0)**
        --- | --- | ---
        コア | 2  | 4
        メモリ | 8 GB | 16 GB
   
7. **[価格]** では:
    - **[オファーまたはライセンス プログラム]** では、Azure プランを指定します (登録している場合)。 現時点では、従量課金制または開発テスト用の従量課金制プランのみを選択できます。 
        - 従量課金制プランに加えて、予約容量と Azure ハイブリッド特典を適用することで、追加の割引を受けることができます。 
        - 開発テスト用の従量課金制プランに加えて、Azure ハイブリッド特典を適用できます。 現在、評価では、開発テスト用の従量課金制プランでの予約容量の適用はサポートされていません。
    - **[サービス レベル]** では、Azure SQL Database および Azure SQL Managed Instance への移行に関するビジネス ニーズに対応するために、最も適切なサービス レベル オプションを選択します。 
        - Azure Migrate によって、サーバーに最適なサービス レベルが推奨されるようにする場合は、 **[推奨]** を選択します。 これを General Purpose または Business Critical に設定することもできます。 詳細情報
        - 予算重視のワークロード向けに設計された Azure SQL 構成が必要な場合は、 **[General Purpose]** を選択します。
        - 障害に対する高い回復性と高速フェールオーバーを備え、低遅延のワークロード向けに設計された Azure SQL 構成が必要な場合は、 **[Business Critical]** を選択します。
    - Azure プランとは別に適用されるサブスクリプション固有の割引を **[割引 (%)]** に追加します。 既定の設定は 0% です。
    - 自分のアカウントの請求通貨を **[通貨]** で選択します。
    - **[Azure ハイブリッド特典]** では、SQL Server ライセンスを既に所有しているかどうかを指定します。 ライセンスを所有しており、SQL Server サブスクリプションのアクティブなソフトウェア アシュアランスの対象となっている場合は、ライセンスを Azure に持ち込む際に Azure ハイブリッド特典を申請できます。
    - 変更内容を確定する場合は [保存] をクリックします。
     :::image type="content" source="./media/tutorial-assess-sql/view-all.png" alt-text="評価のプロパティの [保存] ボタン":::
8. **[サーバーの評価]** で [次へ] をクリックします。
9.  **[評価するサーバーの選択]**  >  **[評価名]** で、評価の名前を指定します。
10. **[グループの選択または作成]** で **[新規作成]** を選択し、グループ名を指定します。
     :::image type="content" source="./media/tutorial-assess-sql/assessment-add-servers.png" alt-text="グループの新規作成ボタンの場所":::
11. アプライアンスを選択し、グループに追加するサーバーを選択します。 それから、[次へ] をクリックします。
12. **[評価の確認と作成]** で評価の詳細を確認したら、 [評価の作成] をクリックしてグループを作成し、評価を実行します。
     :::image type="content" source="./media/tutorial-assess-sql/assessment-create.png" alt-text="評価の確認と作成ボタンの場所":::
13. 評価が作成されたら、 **[Windows, Linux and SQL Server]\(Windows、Linux、SQL Server\)**  >  **[Azure Migrate: Discovery and assessment]\(Azure Migrate: 検出および評価\)** タイルの順に移動し、Azure SQL の評価の横にある数字をクリックします。
     :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-navigation.png" alt-text="作成された評価へのナビゲーション":::
15. 表示する評価の名前をクリックします。

> [!NOTE]
> Azure SQL の評価はパフォーマンスベースの評価であるため、検出の開始後、少なくとも 1 日待ってから評価を作成することをお勧めします。 これにより、パフォーマンス データを収集する時間が確保され、信頼度が上がります。 検出がまだ進行中の場合、SQL インスタンスの対応性は **[不明]** としてマークされます。 信頼度で高評価を得るために、検出の開始後、**指定したパフォーマンス期間 (日、週、月) を置いてから** 評価を作成または再計算するのが理想的です。 

## <a name="review-an-assessment"></a>評価を確認する

**評価を表示するには**:

1. **[Windows, Linux and SQL Server]\(Windows、Linux、SQL Server\)**  >  **[Azure Migrate: Discovery and assessment]\(Azure Migrate: 検出および評価\)** で、Azure SQL の評価の横にある数字をクリックします。
2. 表示する評価の名前をクリックします。 次に例を示します (見積もりとコストはあくまで例です)。:::image type="content" source="./media/tutorial-assess-sql/assessment-sql-summary.png" alt-text="SQL の評価の概要":::
3. 評価の概要を確認します。 評価のプロパティを編集することも、評価を再計算することもできます。

#### <a name="discovered-items"></a>検出されたアイテム

これは、この評価で評価された SQL サーバー、インスタンス、データベースの数を示しています。
    
#### <a name="azure-readiness"></a>Azure の準備状況

これは、評価された SQL インスタンスの分布を示しています。 
    
**ターゲットのデプロイの種類 (評価のプロパティ)** | **対応性**   
--- | --- |
**推奨** |  Azure SQL Database に対応、Azure SQL Managed Instance に対応、Azure VM に対応の可能性あり、Readiness 不明 (検出が進行中であるか、修正すべき検出の問題がある場合)
**Azure SQL DB** または **Azure SQL MI** | Azure SQL Database または Azure SQL Managed Instance に対応、Azure SQL Database または Azure SQL Managed Instance に未対応、Readiness 不明 (検出が進行中であるか、修正すべき検出の問題がある場合)
     
Azure SQL に移行する前に修正が可能な、移行に関する問題や警告について詳細を把握するためにドリルダウンできます ([詳細](concepts-azure-sql-assessment-calculation.md))。Azure SQL Database または Managed Instance に移行する際に推奨される Azure SQL 構成を確認することもできます。
    
#### <a name="azure-sql-database-and-managed-instance-cost-details"></a>Azure SQL Database と Managed Instance のコストの詳細

月間コスト見積もりには、推奨されるデプロイの種類 (Azure SQL Database または Azure SQL Managed Instance) に対応する Azure SQL 構成のコンピューティングとストレージのコストが含まれています。 [詳細情報](concepts-azure-sql-assessment-calculation.md#calculate-monthly-costs)


### <a name="review-readiness"></a>対応性を確認する

1. **[Azure SQL 対応性]** をクリックします。
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-readiness.png" alt-text="Azure SQL 対応性の詳細":::
1. [Azure SQL 対応性] で、評価された SQL インスタンスの **[Azure SQL DB readiness]\(Azure SQL DB 対応性\)** と **[Azure SQL MI readiness]\(Azure SQL MI 対応性\)** を確認します。
    - **準備完了**: 移行に関する問題や警告がなく、インスタンスを Azure SQL DB または MI に移行する準備ができています。 
        - 準備完了 (ハイパーリンクされた青色の情報アイコン): 移行に関する問題は発生しておらず、Azure SQL DB または MI にインスタンスを移行する準備ができていますが、確認する必要がある移行に関する警告がいくつかあります。 ハイパーリンクをクリックすると、移行に関する警告と推奨される修復ガイダンスを確認できます。:::image type="content" source="./media/tutorial-assess-sql/assess-ready.png" alt-text="準備完了状態が表示された評価":::       
    - **準備中**: Azure SQL DB または MI に移行するインスタンスに、移行に関する問題が 1 つ以上あります。 ハイパーリンクをクリックし、移行に関する問題と推奨される修復ガイダンスを確認できます。
    - **不明**: 検出が進行中であるか、検出中に、通知ブレードから修正する必要がある問題が発生したため、Azure Migrate は対応性を評価できません。 問題が解決しない場合は、Microsoft サポートにお問い合わせください。 
1. 次のマトリックスに従って特定された、SQL インスタンスの推奨されるデプロイの種類を確認します。

    - **ターゲットのデプロイの種類** (評価のプロパティで選択): **推奨**

        **Azure SQL DB 対応性** | **Azure SQL MI 対応性** | **推奨されるデプロイの種類** | **Azure SQL 構成とコスト見積もりは計算済みか?**
         --- | --- | --- | --- |
        準備 | 準備 | Azure SQL DB または Azure SQL MI ([詳細](concepts-azure-sql-assessment-calculation.md#recommended-deployment-type)) | はい
        Ready | 準備中または不明 | Azure SQL DB | はい
        準備中または不明 | Ready | Azure SQL MI  | はい
        準備不完了 | 準備不完了 | Azure VM に対応の可能性あり ([詳細](concepts-azure-sql-assessment-calculation.md#potentially-ready-for-azure-vm)) | いいえ
        準備中または不明 | 準備中または不明 | Unknown | いいえ
    
    - **ターゲットのデプロイの種類** (評価のプロパティで選択): **Azure SQL DB**
    
        **Azure SQL DB 対応性** | **Azure SQL 構成とコスト見積もりは計算済みか?**
        --- | --- |
        Ready | はい
        準備不完了 | いいえ
        Unknown | いいえ
    
    - **ターゲットのデプロイの種類** (評価のプロパティで選択): **Azure SQL MI**
    
        **Azure SQL MI 対応性** | **Azure SQL 構成とコスト見積もりは計算済みか?**
         --- | --- |
        Ready | はい
        準備不完了 | いいえ
        Unknown | いいえ

4. インスタンス名のドリルダウンをクリックして、ユーザー データベースの数、インスタンスの詳細 (インスタンスのプロパティなど)、コンピューティング (インスタンス スコープ)、ソース データベース ストレージの詳細を確認します。
5. ユーザー データベースの数をクリックして、データベースとその詳細の一覧を確認します。 次に例を示します (見積もりとコストはあくまで例です)。:::image type="content" source="./media/tutorial-assess-sql/assessment-db.png" alt-text="SQL インスタンスの詳細":::
5. [Migration issues]\(移行の問題\) 列の [詳細の表示] をクリックして、特定のターゲットのデプロイの種類について移行に関する問題と警告を確認します。 
    :::image type="content" source="./media/tutorial-assess-sql/assessment-db-issues.png" alt-text="DB の移行に関する問題と警告":::

### <a name="review-cost-estimates"></a>コスト見積もりを確認する
評価の概要には、推奨されるデプロイの種類 (Azure SQL Database、Managed Instance) に対応する Azure SQL 構成について、コンピューティングとストレージの月間推定コストが表示されます。

1. 月間合計コストを確認します。 評価対象のグループ内のすべての SQL インスタンスのコストが集計されます。
    - コスト見積もりは、インスタンスの推奨される Azure SQL 構成に基づいています。 
    - コンピューティングとストレージの月間推定コストが表示されます。 次に例を示します (見積もりとコストはあくまで例です)。
    
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-cost.png" alt-text="コスト詳細":::

1. インスタンス レベルでドリルダウンすると、Azure SQL 構成とコスト見積もりをインスタンス レベルで確認できます。  
1. Azure SQL Database 構成が推奨されている場合は、データベースの一覧にドリルダウンして、データベースごとの Azure SQL 構成とコスト見積もりを確認することもできます。

### <a name="review-confidence-rating"></a>信頼度レーティングを確認する
Azure Migrate では、評価対象のすべての SQL インスタンスとデータベースの評価を計算するために必要なパフォーマンス データ ポイントと使用率データ ポイントの可用性に基づいて、Azure SQL のすべての評価に信頼度を割り当てます。 レーティングの範囲は、星 1 つ (最も低い) から星 5 つ (最も高い) までです。
信頼度レーティングを使うと、評価の推奨サイズの信頼性を見積もることができます。 信頼度の評価は次のとおりです。

**データ ポイントの可用性** | **信頼度レーティング**
--- | ---
0% - 20% | 1 つ星
21% - 40% | 2 つ星
41% - 60% | 3 つ星
61% - 80% | 4 つ星
81% - 100% | 5 つ星

信頼度レーティングについての[詳しい情報](concepts-azure-sql-assessment-calculation.md#confidence-ratings)をご覧ください。


## <a name="next-steps"></a>次の手順

- Azure SQL の評価の計算方法の[詳細を確認](concepts-azure-sql-assessment-calculation.md)します。
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) を使用して、SQL インスタンスとデータベースの移行を開始します。
