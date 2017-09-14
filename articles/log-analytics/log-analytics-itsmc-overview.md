---
title: "OMS の IT Service Management Connector | Microsoft Docs"
description: "IT Service Management Connector を使用して、OMS の ITSM 作業項目を一元的に監視、管理し、問題をすばやく解決できます。"
services: log-analytics
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: v-jysur
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 54974ef06efdae69ddbfa12b1ba9278b48b113d3
ms.contentlocale: ja-jp
ms.lasthandoff: 06/20/2017


---
# <a name="centrally-manage-itsm-work-items-using-it-service-management-connector-preview"></a>IT Service Management Connector を使用した ITSM 作業項目の一元管理 (プレビュー)

![IT Service Management Connector シンボル](./media/log-analytics-itsmc/itsmc-symbol.png)

OMS ログ分析の IT Service Management Connector (ITSMC) を使用して、ITSM 製品/サービスの作業項目を一元的に監視、管理できます。

IT Service Management Connector には、既存の IT Service Management (ITSM) 製品/サービスと OMS Log Analytics が統合されています。  このソリューションには複数の ITSM 製品/サービスが相互に関連付けられて統合されているため、OMS ユーザーは ITSM ソリューション内のインシデント、アラート、イベントなどの作成を自由に選択できます。 また、このコネクタは、インシデントなどのデータや ITSM ソリューションからの変更要求を OMS ログ分析にインポートします。

IT Service Management Connector では、次のことができます。

  - 組織全体で使用される ITSM 製品/サービスの作業項目を一元的に監視、管理する。
  - OMS アラートとログ検索から、ITSM で ITSM 作業項目 (アラート、イベント、インシデントなど) を作成する。
  - ITSM ソリューションのインシデントと変更要求を読み取り、Log Analytics ワークスペースで関連するログ データを相互に関連付ける。
  - エンド ユーザーがヘルプデスクに電話したり報告したりしなくても、予期しない異常なイベントを事前に見つけ、それを解決する。
  - 作業項目のデータを Log Analytics にインポートし、主要業績評価指標 (KPI) レポートを作成する。  これらのレポートを使用して、マルウェアの評価などの複数の重要項目の確認、評価、アクションを行う。
  - インシデント、変更要求、影響を受けるシステムに関するより深い洞察をまとめたダッシュボードを表示する。
  - Log Analytics ワークスペースで他の管理ソリューションと相互に関連付けることによって迅速にトラブルシューティングする。


## <a name="prerequisites"></a>前提条件

ITSM 作業項目を OMS Log Analytics にインポートするには、OMS の IT Service Management Connector と、作業項目のインポート元である ITSM 製品/サービスとが、このソリューションにおいて相互に接続されている必要があります。


## <a name="configuration"></a>構成

[ソリューション ギャラリーからの Log Analytics ソリューションの追加](log-analytics-add-solutions.md)に関するページの手順に従い、IT Service Management Connector ソリューションを OMS ワーク スペースに追加します。

ソリューション ギャラリーで表示される IT Service Management Connector タイル:

![コネクタ タイル](./media/log-analytics-itsmc/itsmc-solutions-tile.png)

追加が正常に行われると、**[OMS]** > **[設定]** > **[接続されたソース]** で IT Service Management Connector が確認できます。

![ITSMC の接続](./media/log-analytics-itsmc/itsmc-overview-solution-in-connected-sources.png)

> [!NOTE]

> 既定では、IT Service Management Connector は、接続のデータを 24 時間に 1 回更新します。 編集内容やテンプレートの更新を反映するために接続のデータをすぐに更新するには、接続の横に表示される更新ボタンをクリックします。

 ![ITSMC の更新](./media/log-analytics-itsmc/itsmc-connection-refresh.png)

## <a name="management-packs"></a>管理パック
このソリューションでは、管理パックは不要です。

## <a name="connected-sources"></a>接続先ソース

IT Service Management Connector では、次の ITSM 製品/サービスがサポートされています。

- [System Center Service Manager (SCSM)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-oms)

- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-oms)

- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-oms)  

- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-oms)

## <a name="using-the-solution"></a>ソリューションの使用

OMS の IT Service Management Connector を ITSM サービスに接続すると、Log Analytics サービスは接続された ITSM 製品/サービスからデータの収集を開始します。

> [!NOTE]
> - IT Service Management Connector ソリューションによってインポートされたデータは、**ServiceDesk_CL** という名前のイベントとして Log Analytics に表示されます。
- イベントには **ServiceDeskWorkItemType_s** という名前のフィールドが含まれています。 このフィールドは、**ServiceDesk_CL** イベントに含まれている作業項目データに応じて、インシデントまたは変更要求の値を取ることができます。

## <a name="input-data"></a>入力データ
作業項目は、ITSM 製品/サービスからインポートされます。

IT Service Management Connector によって収集されるデータの例を以下に示します。

> [!NOTE]
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
- から
- 割当先
- カテゴリ
- タイトル
- Description
- 作成日
- 終了日
- 解決日
- 最終更新日
- コンピューター


**作業項目:** **変更要求**

ServiceDeskWorkItemType_s="ChangeRequest"

**フィールド**
- サービス デスク接続名
- サービス デスク ID
- 作成者
- 終了者
- から
- 割当先
- タイトル
- 型
- カテゴリ
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
- コンピューター

## <a name="output-data-for-a-servicenow-incident"></a>ServiceNow インシデントの出力データ

| OMS のフィールド | ITSM のフィールド |
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
| Category_s | カテゴリ |
| Title_s|  簡単な説明 |
| Description_s|  メモ |
| CreatedDate_t|  開始済み |
| ClosedDate_t| closed|
| ResolvedDate_t|解決済み|
| コンピューター  | 構成項目 |

## <a name="output-data-for-a-servicenow-change-request"></a>ServiceNow 変更要求の出力データ

| OMS のフィールド | ITSM のフィールド |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | 要求者 |
| ClosedBy_s | 終了者 |
| AssignedTo_s | 割り当て先  |
| Title_s|  簡単な説明 |
| Type_s|  型 |
| Category_s|  カテゴリ |
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
| Description_s | Description |
| コンピューター  | 構成項目 |

**ITSM データに関する Log Analytics 画面 (サンプル):**

![Log Analytics 画面](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

## <a name="it-service-management-connector--integration-with-other-oms-solutions"></a>IT Service Management Connector – その他の OMS ソリューションとの統合

現在、IT Service Management Connector ではサービス マップ ソリューションとの統合がサポートされています。

サービス マップは、Windows と Linux システムのアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップします。 これを使用すると、サーバーを重要なサービスを提供する相互接続されたシステムとして表示することができます。 サービス マップは、TCP 接続アーキテクチャ全体のサーバー、プロセス、ポート間の接続を表示します。エージェントのインストール以外の構成は必要ありません。 詳細: [サービス マップ](../operations-management-suite/operations-management-suite-service-map.md)。

サービス マップが統合されたことによって、ITSM ソリューションで作成したサービス デスク項目が次のように表示されます。

![統合ソリューション ](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)
## <a name="create-itsm-work-items-for-oms-alerts"></a>OMS アラートの ITSM 作業項目を作成する

接続先の ITSM ソース内に OMS アラートに関連する作業項目を作成できます。  これを行うには、次の手順に従います。

1. **[ログ検索]** ウィンドウで、ログ検索クエリを実行しデータを表示します。 クエリ結果は、作業項目のソースです。
2. **[ログ検索]** ウィンドウで、**[アラート]** をクリックし、**[アラート ルールの追加]** ページを開きます。

    ![Log Analytics 画面](./media/log-analytics-itsmc/itsmc-work-items-for-oms-alerts.png)

3. **[アラート ルールの追加]** ウィンドウで、**[名前]**、**[重大度]**、**[検索クエリ]**、**[アラートの条件]** ([時間枠] や [メトリック測定]) などの必要な情報を確認できます。
4. **[ITSM 操作]** で **[はい]** を選択します。
5. **[接続を選択]** リストで、お使いの ITSM 接続を選択します。
6. 必要に応じてその他の情報も入力します。
7. このアラートについて各ログ エントリに個別の作業項目を作成するには、**[各ログ エントリに対して、個々の作業項目を作成します]** チェックボックスをオンします。

    または

    このアラートについてログ エントリの数に関係なく 1 つの作業項目を作成するには、このチェックボックスをオフのままにしておきます。

7. [ **Save**] をクリックします。

OMS アラートは **[アラート]** の下に作成されます。 指定したアラートの条件が満たされた場合は、それに対応する ITSM 接続の作業項目が作成されます。

## <a name="create-itsm-work-items-from-oms-logs"></a>OMS ログから ITSM 作業項目を作成する

OMS ログ検索を使用して、接続先の ITSM ソース内に作業項目を作成できます。 これを行うには、次の手順に従います。

1. **[ログ検索]** で、必要なデータを検索し、詳細を選択して **[作業項目の作成]** をクリックします。

    **[ITSM 作業項目の作成]** ウィンドウが表示されます。

    ![Log Analytics 画面](media/log-analytics-itsmc/itsmc-work-items-from-oms-logs.png)

2.   次の情報を追加します。

  - **[作業項目のタイトル]**: 作業項目のタイトル。
  - **[作業項目の説明]**: 新しい作業項目についての説明。
  - **[影響を受けるコンピューター]**: このログ データが見つかったコンピューターの名前。
  - **[接続を選択]**: この作業項目を作成する ITSM 接続。
  - **[作業項目]**: 作業項目の種類。

3. インシデントの既存の作業項目テンプレートを使用するには、**[テンプレートに基づいて作業項目を生成する]** オプションで **[はい]** を選択し、**[作成]** をクリックします。

    または、

    カスタムした値を入力する場合は **[いいえ]** をクリックします。

4. **[連絡先の種類]**、**[影響]**、**[緊急度]**、**[カテゴリ]**、**[サブ カテゴリ]** のテキスト ボックスに適切な値を入力し、**[作成]** をクリックします。

作業項目は ITSM 内に作成されますが、OMS 内でも確認できます。

## <a name="troubleshoot-itsm-connections-in-oms"></a>OMS の ITSM 接続のトラブルシューティング
1.  接続されているソースの UI からの接続が失敗し、"**接続の保存中にエラーが発生しました**" というメッセージが表示された場合は、次のことを行ってください。
 - ServiceNow、Cherwell、Provance の接続の場合、各接続のユーザー名/パスワードとクライアント ID/クライアント シークレットが正しく入力されていることを確認します。 エラーが解決しない場合は、対応する ITSM 製品で接続を作成するための十分な特権があるかどうかを確認します。
 - Service Manager の場合、Web アプリが正常にデプロイされ、ハイブリッド接続が作成されていることを確認します。 オンプレミスの Service Manager コンピューターとの接続が正常に確立されていることを確認するには、[ハイブリッド接続](log-analytics-itsmc-connections.md#configure-the-hybrid-connection)の作成に関するドキュメントで詳述するように、Web アプリの URL にアクセスします。

2.  ServiceNow のデータが OMS で同期されていない場合は、ServiceNow インスタンスがスリープ状態でないことを確認します。 これは、ServiceNow Dev インスタンスがアイドル状態のときに発生する場合があります。 それ以外の場合は、問題を報告してください。
3.  OMS でアラートが発生しても、ITSM 製品で作業項目が作成されない場合や、構成項目が作成されない/作業項目にリンクされない場合、または一般情報が必要な場合は、次のことを行ってください。
 -  OMS ポータルの IT Service Management Connector ソリューションを使用して、接続、作業項目、コンピューターなどの概要を取得できます。状態ブレードでエラー メッセージをクリックし、**[ログ検索]** に移動します。エラー メッセージの詳細情報を使用してエラーがある接続を表示します。
 - *Type=ServiceDeskLog_CL* を使用して、**[ログ検索]** ページにエラーと関連情報を直接表示できます。

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Service Manager Web アプリのデプロイのトラブルシューティング
1.  Web アプリのデプロイに関する問題が発生した場合は、該当するサブスクリプションでリソースを作成/デプロイするための十分なアクセス許可があることを確認します。
2.  [スクリプト](log-analytics-itsmc-service-manager-script.md)の実行中に、"**オブジェクト参照がオブジェクトのインスタンスに設定されていません**" というエラー メッセージが表示された場合は、**[ユーザー構成]** セクションに有効な値が入力されていることを確認します。
3.  Service Bus Relay 名前空間を作成できない場合は、必要なリソース プロバイダーがサブスクリプションに登録されていることを確認します。 登録されていない場合は、Azure Portal でリソース プロバイダーを手動で作成します。 リソース プロバイダーは、Azure Portal で[ハイブリッド接続を作成](log-analytics-itsmc-connections.md#configure-the-hybrid-connection)するときに作成することもできます。


## <a name="contact-us"></a>お問い合わせ

IT Service Management Connector に関するご質問やフィードバックは、[omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com) にお寄せください。

## <a name="next-steps"></a>次のステップ
[ITSM 製品/サービスを IT Service Management Connector に追加する](log-analytics-itsmc-connections.md)。

