---
title: Azure Log Analytics の IT Service Management Connector | Microsoft Docs
description: この記事では、IT Service Management Connector (ITSMC) の概要を説明し、このソリューションを使用して ITSM の作業項目を Azure Log Analytics で一元的に監視および管理して、問題を迅速に解決する方法に関する情報を示します。
services: log-analytics
documentationcenter: ''
author: jyothirmaisuri
manager: riyazp
editor: ''
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: v-jysur
ms.openlocfilehash: f2574cc64e157ff0f8a6cb875a832db88cf13dd6
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67479815"
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>IT Service Management Connector を使用して Azure を ITSM ツールに接続する

![IT Service Management Connector シンボル](media/itsmc-overview/itsmc-symbol.png)

IT Service Management Connector (ITSMC) を使用すると、サポートされている IT Service Management (ITSM) 製品/サービスと Azure を接続できます。

Log Analytics、Azure Monitor などの Azure サービスには、Azure および Azure 以外のリソースに関する問題の検出、分析、トラブルシューティングを実行するツールが用意されています。 ただし、問題に関連する作業項目は、通常、ITSM 製品/サービスに存在します。 ITSM Connector は Azure と ITSM ツールの間に双方向接続を提供し、問題解決の迅速化を支援します。

ITSMC は、次の ITSM ツールとの接続をサポートしています。

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

ITSMC を使用すると、次のことができます

-  Azure アラート (メトリック アラート、アクティビティ ログ アラート、および Log Analytics アラート) に基づいて、ITSM ツールで作業項目を作成する。
-  オプションで、ITSM ツールのインシデントと変更要求データを Azure Log Analytics ワークスペースに同期する。


ITSM Connector の使用を開始するには、次の手順を使用します。

1.  [ITSM Connector ソリューションを追加する](#adding-the-it-service-management-connector-solution)
2.  ITSM 接続を作成する
3.  [接続を使用する](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>IT Service Management Connector ソリューションの追加

接続を作成する前に、ITSM Connector ソリューションを追加する必要があります。

1. Azure Portal で、 **[+ 新規]** アイコンをクリックします。

   ![Azure の新しいリソース](media/itsmc-overview/azure-add-new-resource.png)

2. Marketplace で **IT Service Management Connector** を検索し、 **[作成]** をクリックします。

   ![ITSMC ソリューションの追加](media/itsmc-overview/add-itsmc-solution.png)

3. **[OMS ワークスペース]** セクションで、ソリューションをインストールする Azure Log Analytics ワークスペースを選択します。
   >[!NOTE]
   > * Microsoft Operations Management Suite (OMS) から Azure Monitor への現在進行中の移行の一環として、OMS ワークスペースは Log Analytics ワークスペースと呼ばれるようになります。
   > * ITSM Connector は、以下のリージョンの Log Analytics ワークスペースにのみインストールできます。米国東部、西ヨーロッパ、東南アジア、オーストラリア南東部、米国中西部、東日本、英国南部、インド中部、カナダ中部。

4. **[OMS ワークスペースの設定]** セクションで、ソリューション リソースを作成する ResourceGroup を選択します。

   ![ITSMC ワークスペース](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >Microsoft Operations Management Suite (OMS) から Azure Monitor への現在進行中の移行の一環として、OMS ワークスペースは Log Analytics ワークスペースと呼ばれるようになります。

5. **Create** をクリックしてください。

ソリューション リソースがデプロイされると、ウィンドウの右上に通知が表示されます。


## <a name="creating-an-itsm--connection"></a>ITSM 接続の作成

ソリューションをインストールしたら、接続を作成できます。

接続を作成するには、ITSM Connector ソリューションからの接続を許可するように ITSM ツールを準備する必要があります。  

接続先の ITSM 製品に応じて、次の手順に従います。

- [System Center Service Manager (SCSM)](../../azure-monitor/platform/itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](../../azure-monitor/platform/itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](../../azure-monitor/platform/itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](../../azure-monitor/platform/itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

ITSM ツールの準備が完了したら、次の手順に従って接続を作成します。

1. **[すべてのリソース]** に移動し、**ServiceDesk(YourWorkspaceName)** を探します。
2. 左側のウィンドウの **[ワークスペースのデータ ソース]** で、 **[ITSM 接続]** をクリックします。
   ![ITSM 接続](media/itsmc-overview/itsm-connections.png)

   このページには接続の一覧を表示されています。
3. **[接続の追加]** をクリックします。

   ![ITSM 接続の追加](media/itsmc-overview/add-new-itsm-connection.png)

4. [ITSM 製品/サービスとの ITSMC 接続の構成](../../azure-monitor/platform/itsmc-connections.md)に関する記事の説明に従って、接続の設定を指定します。

   > [!NOTE]
   > 
   > 既定では、ITSMC は、接続の構成データを 24 時間に 1 回更新します。 編集内容やテンプレートの更新を反映するために接続データをすぐに更新するには、接続のブレードに表示される **[同期]** ボタンをクリックします。

   ![接続の更新](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>ソリューションの使用
   ITSM Connector ソリューションを使用すると、Azure アラート、Log Analytics アラート、および Log Analytics ログ レコードから作業項目を作成できます。

## <a name="create-itsm-work-items-from-azure-alerts"></a>Azure アラートから ITSM 作業項目を作成する

ITSM 接続を作成したら、**アクション グループ**の **ITSM アクション**を使用して、Azure アラートに基づいて ITSM ツールで作業項目を作成できます。

アクション グループでは、Azure アラートのアクションをトリガーする再利用可能なモジュール化された方法が提供されます。 Azure Portal で、アクション グループと、メトリック アラート、アクティビティ ログ アラート、および Azure Log Analytics アラートを使用できます。

次の手順に従います。

1. Azure Portal で、 **[監視]** をクリックします。
2. 左側のウィンドウで、 **[アクション グループ]** をクリックします。 **[アクション グループの追加]** ウィンドウが表示されます。

    ![Action Groups](media/itsmc-overview/action-groups.png)

3. アクション グループの**名前**と**短い名前**を入力します。 アクション グループを作成する**リソース グループ**と**サブスクリプション**を選択します。

    ![アクション グループの詳細](media/itsmc-overview/action-groups-details.png)

4. アクション リストで、 **[アクションの種類]** のドロップダウン メニューから **[ITSM]** を選択します。 アクションの**名前**を入力し、 **[詳細の編集]** をクリックします。
5. Log Analytics ワークスペースが配置される**サブスクリプション**を選択します。 ご使用のワークスペース名が後に続く**接続**名 (ITSM Connector の名前) を選択します。 たとえば、"MyITSMMConnector(MyWorkspace)" などです。

    ![ITSM アクションの詳細](media/itsmc-overview/itsm-action-details.png)

6. ドロップダウン メニューから **[作業項目]** の種類を選択します。
   既存のテンプレートを使用するか、ITSM 製品に必要なフィールドに入力します。
7. Click **OK**.

Azure のアラート ルールを作成/編集する場合は、ITSM アクションを含むアクション グループを使用します。 アラートがトリガーされると、ITSM ツールで作業項目が作成/更新されます。

> [!NOTE]
> 
> ITSM アクションの価格については、アクション グループの[価格ページ](https://azure.microsoft.com/pricing/details/monitor/)を参照してください。


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>インシデントと変更要求データを視覚化および分析する

接続を設定したときの構成に基づいて、ITSM Connector は最大 120 日のインシデントと変更要求データを同期できます。 このデータのログ レコードのスキーマについては、[次のセクション](#additional-information)をご覧ください。

インシデントと変更要求データを視覚化するには、ソリューションで ITSM Connector ダッシュボードを使用します。

![Log Analytics 画面](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

ダッシュボードでは、接続の問題を分析するときの開始ポイントとして使用できるコネクタの状態に関する情報も確認できます。

Service Map ソリューション内で、影響を受けるコンピューターに対して同期されたインシデントを視覚化することもできます。

サービス マップは、Windows と Linux システムのアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップします。 これを使用すると、サーバーを重要なサービスを提供する相互接続されたシステムとして表示することができます。 サービス マップは、TCP 接続アーキテクチャ全体のサーバー、プロセス、ポート間の接続を表示します。エージェントのインストール以外の構成は必要ありません。 [詳細情報](../../azure-monitor/insights/service-map.md)。

Service Map ソリューションを使用する場合は、ITSM ソリューションで作成されたサービス デスク項目が次の例のように表示されます。

![Log Analytics 画面](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

詳細情報: [サービス マップ](../../azure-monitor/insights/service-map.md)


## <a name="additional-information"></a>追加情報

### <a name="data-synced-from-itsm-product"></a>ITSM 製品から同期されるデータ
インシデントと変更要求は、接続の構成に基づいて、ITSM 製品から Log Analytics ワークスペースに同期されます。

ITSMC によって収集されるデータの例を以下に示します。

> [!NOTE]
> 
> Log Analytics にインポートされる作業項目の種類によっては、**ServiceDesk_CL** には以下のフィールドが含まれます。

**作業項目:** **インシデント**  
ServiceDeskWorkItemType_s="Incident"

**フィールド**

- サービス デスク接続名
- サービス デスク ID
- 状態
- 緊急度
- 影響
- 優先順位
- エスカレーション
- 作成者
- 解決者
- 終了者
- source
- 割当先
- Category
- タイトル
- 説明
- 作成日
- 終了日
- 解決日
- 最終更新日
- Computer


**作業項目:** **変更要求**

ServiceDeskWorkItemType_s="ChangeRequest"

**フィールド**
- サービス デスク接続名
- サービス デスク ID
- 作成者
- 終了者
- source
- 割当先
- タイトル
- Type
- Category
- 状態
- エスカレーション
- 競合状態
- 緊急度
- 優先順位
- リスク
- 影響
- 割当先
- 作成日
- 終了日
- 最終更新日
- 要求日
- 開始予定日
- 終了予定日
- 作業開始日
- 作業終了日
- 説明
- Computer

## <a name="output-data-for-a-servicenow-incident"></a>ServiceNow インシデントの出力データ

| Log Analytics のフィールド | ServiceNow のフィールド |
|:--- |:--- |
| ServiceDeskId_s| Number |
| IncidentState_s | 状態 |
| Urgency_s |緊急度 |
| Impact_s |影響|
| Priority_s | 優先順位 |
| CreatedBy_s | 開始者 |
| ResolvedBy_s | 解決者|
| ClosedBy_s  | 終了者 |
| Source_s| 連絡先の種類 |
| AssignedTo_s | 割り当て先  |
| Category_s | Category |
| Title_s|  簡単な説明 |
| Description_s|  メモ |
| CreatedDate_t|  開始済み |
| ClosedDate_t| closed|
| ResolvedDate_t|解決済み|
| Computer  | 構成項目 |

## <a name="output-data-for-a-servicenow-change-request"></a>ServiceNow 変更要求の出力データ

| Log Analytics | ServiceNow のフィールド |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | 要求者 |
| ClosedBy_s | 終了者 |
| AssignedTo_s | 割り当て先  |
| Title_s|  簡単な説明 |
| Type_s|  Type |
| Category_s|  Category |
| CRState_s|  状態|
| Urgency_s|  緊急度 |
| Priority_s| 優先順位|
| Risk_s| リスク|
| Impact_s| 影響|
| RequestedDate_t  | Requested by date\(要求日\) |
| ClosedDate_t | 終了日 |
| PlannedStartDate_t  |     開始予定日 |
| PlannedEndDate_t  |   終了予定日 |
| WorkStartDate_t  | 実際の開始日 |
| WorkEndDate_t | 実際の終了日|
| Description_s | 説明 |
| Computer  | 構成項目 |


## <a name="troubleshoot-itsm-connections"></a>ITSM 接続のトラブルシューティング
1. 接続されているソースの UI からの接続が失敗し、"**接続の保存中にエラーが発生しました**" というメッセージが表示された場合は、次の手順を実行してください。
   - ServiceNow、Cherwell および Provance の接続の場合  
   - 各接続のユーザー名、パスワード、クライアント ID、およびクライアント シークレットが正しく入力されていることを確認します。  
   - 対応する ITSM 製品で接続を作成するための十分な特権があるかどうかを確認します。  
   - Service Manager 接続の場合  
   - Web アプリが正常にデプロイされ、ハイブリッド接続が作成されていることを確認します。 オンプレミスの Service Manager マシンとの接続が正常に確立されていることを確認するには、[ハイブリッド接続](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection)の構成に関するドキュメントで詳述するように、Web アプリの URL にアクセスします。  

2. ServiceNow のデータが Log Analytics に同期されていない場合は、ServiceNow インスタンスがスリープ状態でないことを確認します。 ServiceNow Dev インスタンスは、長時間アイドル状態になっている場合、スリープ状態になることがあります。 それ以外の場合は、問題を報告してください。
3. Log Analytics アラートが発生しても、ITSM 製品で作業項目が作成されない場合や、構成項目が作成されない/作業項目にリンクされない場合、または一般情報が必要な場合は、以下を確認してください。
   -  ITSMC: このソリューションでは、接続/作業項目/コンピューターなどの概要が表示されます。**接続の状態**を示すタイルをクリックします。これにより、関連するクエリを使用する **[ログ検索]** が表示されます。 詳細については、LogType_S が ERROR のログ レコードを確認してください。
   - **[ログ検索]** ページ: `*`ServiceDeskLog_CL`*` というクエリを使用して直接エラー/関連情報を表示します。

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Service Manager Web アプリのデプロイのトラブルシューティング
1.  Web アプリのデプロイに関する問題が発生した場合は、該当するサブスクリプションでリソースを作成/デプロイするための十分なアクセス許可があることを確認します。
2.  [スクリプト](itsmc-service-manager-script.md)の実行時に "**オブジェクト参照がオブジェクトのインスタンスに設定されていません**" というエラーが発生した場合は、 **[ユーザー構成]** セクションに有効な値が入力されていることを確認します。
3.  Service Bus Relay 名前空間を作成できない場合は、必要なリソース プロバイダーがサブスクリプションに登録されていることを確認します。 登録されていない場合は、Azure Portal で Service Bus Relay 名前空間を手動で作成します。 リソース プロバイダーは、Azure Portal で[ハイブリッド接続を作成](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection)するときに作成することもできます。


## <a name="contact-us"></a>お問い合わせ

IT Service Management Connector に関するご質問やフィードバックは、[omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com) にお寄せください。

## <a name="next-steps"></a>次の手順
[ITSM 製品/サービスを IT Service Management Connector に追加する](../../azure-monitor/platform/itsmc-connections.md)。
