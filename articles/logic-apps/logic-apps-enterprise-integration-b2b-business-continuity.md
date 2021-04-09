---
title: 統合アカウントのディザスター リカバリー
description: Azure Logic Apps のリージョン間ディザスター リカバリーで統合アカウントと B2B 成果物を設定します
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/10/2017
ms.openlocfilehash: 1e006dd690e9c008afada8a490da6c3238bc0791
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91565413"
---
# <a name="set-up-cross-region-disaster-recovery-for-integration-accounts-in-azure-logic-apps"></a>Azure Logic Apps での統合アカウントの複数のリージョンにわたるディザスター リカバリーを設定する

B2B のワークロードには、発注や請求などの金銭が関係するトランザクションが含まれます。 ビジネスでは、障害イベント時に迅速に復旧して、パートナーと合意したビジネス レベルの SLA を満たすことが重要です。 この記事では、B2B ワークロード向けのビジネス継続性計画の構築方法について説明します。 

* ディザスター リカバリーの準備 
* 障害イベント時のセカンダリ リージョンへのフェールオーバー 
* 障害イベント後のプライマリ リージョンへの復帰

## <a name="disaster-recovery-readiness"></a>ディザスター リカバリーの準備  

1. セカンダリ リージョンを指定し、セカンダリ リージョンに[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)を作成します。

2. パートナー、スキーマ、および実行状態をセカンダリ リージョンの統合アカウントにレプリケートする必要がある必須のメッセージ フローの契約を追加します。

   > [!TIP]
   > リージョン間で統合アカウントのアーティファクトの命名規則に整合性があることを確認します。 

3. プライマリ リージョンから実行状態を取得するには、セカンダリ リージョンにロジック アプリを作成します。 

   このロジック アプリには、*トリガー* と *アクション* が必要です。 
   トリガーはプライマリ リージョンの統合アカウントに接続し、アクションはセカンダリ リージョンの統合アカウントに接続する必要があります。 
   時間間隔に基づいて、トリガーはプライマリ リージョン実行状態テーブルのポーリングを実行し、新しいレコードが存在すればそれを取得します。 アクションはセカンダリ リージョンの統合アカウントのテーブルを更新します。 
   これは、プライマリ リージョンからセカンダリ リージョンに増分ランタイム状態を取得するのに役立ちます。

4. Logic Apps 統合アカウントのビジネス継続性は、X12、AS2、EDIFACT の各 B2B プロトコルに基づいてサポートするために設計されています。 詳細な手順については、それぞれのリンクを選択してください。

5. プライマリ リージョンのすべてのリソースを、セカンダリ リージョンにもデプロイすることをお勧めします。 

   プライマリ リージョンのリソースには、Azure SQL Database または Azure Cosmos DB、メッセージングに使用する Azure Service Bus と Azure Event Hubs、Azure API Management、Azure App Service の Azure Logic Apps 機能が含まれます。   

6. プライマリ リージョンからセカンダリ リージョンへの接続を確立します。 プライマリ リージョンから実行状態を取得するには、セカンダリ リージョンにロジック アプリを作成します。 

   このロジック アプリには、トリガーとアクションが必要です。 
   トリガーは、プライマリ リージョンの統合アカウントに接続される必要があります。 
   アクションは、セカンダリ リージョンの統合アカウントに接続される必要があります。 
   時間間隔に基づいて、トリガーはプライマリ リージョン実行状態テーブルのポーリングを実行し、新しいレコードが存在すればそれを取得します。 
   アクションはセカンダリ リージョンの統合アカウントのテーブルを更新します。 
   このプロセスは、プライマリ リージョンからセカンダリ リージョンに増分ランタイム状態を取得するのに役立ちます。

Logic Apps 統合アカウントのビジネス継続性は、X12、AS2、EDIFACT の各 B2B プロトコルに基づいてサポートを提供します。 X12 と AS2 を使用する手順について詳しくは、この記事の「[X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12)」と「[AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2)」をご覧ください。

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>障害イベント時のセカンダリ リージョンへのフェールオーバー

障害イベント時に、プライマリ リージョンでビジネス継続性を確保できない場合は、セカンダリ リージョンにトラフィックをダイレクトします。 セカンダリ リージョンは、パートナーと合意した RPO/RTO を満たすため、迅速にビジネスに必要な機能を復旧するのに役立ちます。 また、リージョン間でフェールオーバーするための労力を最小限に抑えます。 

プライマリ リージョンからセカンダリ リージョンに制御番号をコピーする際に、待ち時間が予想されます。 障害イベント時に、重複して生成された制御番号をパートナーに送信することを回避するには、[PowerShell コマンドレット](/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0)を使用してセカンダリ リージョン契約で制御番号を増やすことをお勧めします。

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>障害イベント後のプライマリ リージョンへの復帰

使用可能な場合にプライマリ リージョンへ復帰するには、次の手順に従います。

1. セカンダリ リージョンでパートナーからのメッセージの受け入れを停止します。  

2. [PowerShell コマンドレット](/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0)を使用して、すべてのプライマリ リージョン契約について、生成される制御番号を増やします。  

3. セカンダリ リージョンからプライマリ リージョンにトラフィックをダイレクトします。

4. プライマリ リージョンから実行状態を取得するためにセカンダリ リージョンで作成したロジック アプリが有効になっていることを確認します。

## <a name="x12"></a>X12 

EDI X12 ドキュメントのビジネス継続性は、制御番号に基づいています。

> [!TIP]
> [X12 クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/201-logic-app-b2b-disaster-recovery-replication/)を使用してロジック アプリを作成することもできます。 プライマリ統合アカウントとセカンダリ統合アカウントの作成は、テンプレートを使用するための前提条件です。 テンプレートは 2 つのロジック アプリを作成するのに役立ちます。1 つは受信した制御番号用、もう 1 つは生成された制御番号用です。 ロジック アプリでそれぞれのトリガーとアクションが作成されます。トリガーはプライマリ統合アカウントに、アクションはセカンダリ統合アカウントに接続します。

**前提条件**

受信メッセージのディザスター リカバリーを有効にするには、X12 契約の受信設定で重複チェック設定を選択します。

![重複チェック設定を選択する](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. セカンダリ リージョンに[ロジック アプリ](../logic-apps/quickstart-create-first-logic-app-workflow.md)を作成します。    

2. **X12** で検索し、 **[X12 - 制御番号が変更されたとき]** を選択します。   

   ![X12 を検索する](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   トリガーを選択すると、統合アカウントへの接続を確立するように求められます。 
   トリガーは、プライマリ リージョンの統合アカウントに接続される必要があります。

3. 接続名を入力し、リストから *プライマリ リージョンの統合アカウント* を選択して、 **[作成]** を選択します。   

   ![接続名を入力し、プライマリ リージョンの統合アカウントを選択する場所を示すスクリーンショット。 ](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. **制御番号の同期を開始する DateTime** 設定は省略できます。 **頻度** は、 **[日]** 、 **[時間]** 、 **[分]** 、または **[秒]** の間隔に設定できます。   

   ![制御番号の同期を開始する DateTime 設定を示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. **[新しいステップ]**  >  **[アクションの追加]** の順に選択します。

   ![[新しいステップ] ボタンと [アクションの追加] オプションを示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. **X12** で検索し、 **[X12 - 制御番号を追加または更新します]** を選択します。   

   ![制御番号の追加または更新](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. アクションをセカンダリ リージョン統合アカウントに接続するには、 **[接続の変更]**  >  **[新しい接続の追加]** の順に選択し、使用可能な統合アカウントのリストを表示します。 接続名を入力し、リストから *セカンダリ リージョンの統合アカウント* を選択して、 **[作成]** を選択します。 

   ![セカンダリ リージョンの統合アカウント名を追加する場所を示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. 右上隅にあるアイコンをクリックして、生の入力に切り替えます。

   ![生に切り替えるために選択するアイコンを示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. 動的コンテンツ ピッカーの [本文] を選択し、ロジック アプリを保存します。

   ![動的コンテンツのフィールド](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   時間間隔に基づいて、トリガーはプライマリ リージョンで受信した制御番号テーブルのポーリングを実行し、新しいレコードを取得します。 
   このアクションによって、セカンダリ リージョンの統合アカウントのレコードが更新されます。 
   更新がない場合、トリガーの状態は **[スキップ済み]** と表示されます。   

   ![制御番号テーブル](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

時間間隔に基づいて、増分ランタイム状態がプライマリ リージョンからセカンダリ リージョンにレプリケートされます。 障害イベント時に、プライマリ リージョンが使用できない場合は、セカンダリ リージョンにトラフィックをダイレクトしてビジネス継続性を確保します。 

## <a name="edifact"></a>EDIFACT 

EDI EDIFACT ドキュメントのビジネス継続性は、制御番号に基づいています。

**前提条件**

受信メッセージのディザスター リカバリーを有効にするには、EDIFACT 契約の受信設定で重複チェック設定を選択します。

![EDIFACT 契約の受信設定の重複チェック設定を示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. セカンダリ リージョンに[ロジック アプリ](../logic-apps/quickstart-create-first-logic-app-workflow.md)を作成します。    

2. **EDIFACT** で検索し、 **[EDIFACT - 制御番号が変更されたとき]** を選択します。

   ![EDIFACT の検索](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   トリガーを選択すると、統合アカウントへの接続を確立するように求められます。 
   トリガーは、プライマリ リージョンの統合アカウントに接続される必要があります。 

3. 接続名を入力し、リストから *プライマリ リージョンの統合アカウント* を選択して、 **[作成]** を選択します。    

   ![プライマリ リージョンの統合アカウント名](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. **制御番号の同期を開始する DateTime** 設定は省略できます。 **頻度** は、 **[日]** 、 **[時間]** 、 **[分]** 、または **[秒]** の間隔に設定できます。    

   ![DateTime および頻度の設定を示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. **[新しいステップ]**  >  **[アクションの追加]** の順に選択します。    

   ![[アクションの追加] を選択する場所を示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. **EDIFACT** で検索し、 **[EDIFACT - 制御番号を追加または更新します]** を選択します。   

   ![制御番号を追加または更新する場所を示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. アクションをセカンダリ リージョン統合アカウントに接続するには、 **[接続の変更]**  >  **[新しい接続の追加]** の順に選択し、使用可能な統合アカウントのリストを表示します。 接続名を入力し、リストから *セカンダリ リージョンの統合アカウント* を選択して、 **[作成]** を選択します。

   ![セカンダリ リージョンの統合アカウント名を作成します。](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. 右上隅にあるアイコンをクリックして、生の入力に切り替えます。

   ![生の入力に切り替えるときに選択するアイコンが強調表示されているスクリーンショット。](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. 動的コンテンツ ピッカーの [本文] を選択し、ロジック アプリを保存します。   

   ![[本文] を選択できる動的コンテンツ ピッカーを示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   時間間隔に基づいて、トリガーはプライマリ リージョンで受信した制御番号テーブルのポーリングを実行し、新しいレコードを取得します。
   このアクションによって、セカンダリ リージョンの統合アカウントのレコードが更新されます。 
   更新がない場合、トリガーの状態は **[スキップ済み]** と表示されます。

   ![制御番号テーブル](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

時間間隔に基づいて、増分ランタイム状態がプライマリ リージョンからセカンダリ リージョンにレプリケートされます。 障害イベント時に、プライマリ リージョンが使用できない場合は、セカンダリ リージョンにトラフィックをダイレクトしてビジネス継続性を確保します。 

## <a name="as2"></a>AS2 

AS2 プロトコルを使用するドキュメントのビジネス継続性は、メッセージ ID と MIC 値に基づいています。

> [!TIP]
> [AS2 クイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates/pull/3302)を使用してロジック アプリを作成することもできます。 プライマリ統合アカウントとセカンダリ統合アカウントの作成は、テンプレートを使用するための前提条件です。 テンプレートは、トリガーとアクションをもつロジック アプリを作成するのに役立ちます。 このロジック アプリは、トリガーからプライマリ統合アカウントへの接続と、アクションからセカンダリ統合アカウントへの接続を作成します。

1. セカンダリ リージョンで[ロジック アプリ](../logic-apps/quickstart-create-first-logic-app-workflow.md)を作成します。  

2. **AS2** を検索し、 **[AS2 - When a MIC value is created] \(AS2 - MIC 値を作成する場合)** を選択します。   

   ![AS2 の検索](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   トリガーを選択すると、統合アカウントへの接続を確立するように求められます。 
   トリガーは、プライマリ リージョンの統合アカウントに接続される必要があります。 
   
3. 接続名を入力し、リストから *プライマリ リージョンの統合アカウント* を選択して、 **[作成]** を選択します。

   ![MIC 値が作成されるときに接続名を入力する場所を示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. **MIC 値の同期を開始する DateTime** 設定は省略できます。 **頻度** は、 **[日]** 、 **[時間]** 、 **[分]** 、または **[秒]** の間隔に設定できます。   

   ![日付時刻と頻度](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. **[新しいステップ]**  >  **[アクションの追加]** の順に選択します。  

   ![[新しいステップ]、[アクションの追加]](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. **AS2** を検索し、 **[AS2 - MIC コンテンツの追加または更新]** を選択します。  

   ![MIC の追加または更新](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. アクションをセカンダリ リージョン統合アカウントに接続するには、 **[接続の変更]**  >  **[新しい接続の追加]** の順に選択し、使用可能な統合アカウントのリストを表示します。 接続名を入力し、リストから *セカンダリ リージョンの統合アカウント* を選択して、 **[作成]** を選択します。

   ![セカンダリ リージョンの統合アカウント名](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. 右上隅にあるアイコンをクリックして、生の入力に切り替えます。

   ![生の入力に切り替える](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. 動的コンテンツ ピッカーの [本文] を選択し、ロジック アプリを保存します。   

   ![動的コンテンツ](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   時間間隔に基づいて、トリガーはプライマリ リージョン テーブルのポーリングを実行し、新しいレコードを取得します。 アクションはセカンダリ リージョンの統合アカウントのレコードを更新します。 
   更新がない場合、トリガーの状態は **[スキップ済み]** と表示されます。  

   ![プライマリ リージョン テーブル](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

時間間隔に基づいて、増分ランタイム状態がプライマリ リージョンからセカンダリ リージョンにレプリケートされます。 障害イベント時に、プライマリ リージョンが使用できない場合は、セカンダリ リージョンにトラフィックをダイレクトしてビジネス継続性を確保します。 

## <a name="next-steps"></a>次のステップ

[Azure Monitor ログで B2B メッセージを監視する](../logic-apps/monitor-b2b-messages-log-analytics.md)
