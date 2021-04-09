---
title: Log Analytics の IT Service Management Connector
description: この記事では、IT Service Management Connector (ITSMC) の概要を紹介し、これを使用して ITSM の作業項目を Log Analytics で監視および管理し、問題を迅速に解決することについて説明します。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 98f53ec1b6506a6d47146377e837576254f445e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103601068"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-solution"></a>IT Service Management ソリューションを使用して Azure を ITSM ツールに接続する

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

この記事では、Log Analytics の IT Service Management Connector (ITSMC) を構成して、IT Service Management (ITSM) の作業項目を一元的に管理する方法に関する情報を提供します。

## <a name="add-it-service-management-connector"></a>IT Service Management Connector を追加する

接続を作成する前に、ITSMC をインストールする必要があります。

1. Azure portal で **[リソースの作成]** を選択します。

   ![リソースを作成するためのメニュー項目を示すスクリーンショット。](media/itsmc-overview/azure-add-new-resource.png)

2. Azure Marketplace で **IT Service Management Connector** を検索します。 次に、 **[作成]** を選択します。

   ![Azure Marketplace の [作成] ボタンを示すスクリーンショット。](media/itsmc-overview/add-itsmc-solution.png)

3. **[LA Workspace]\(LA ワークスペース\)** セクションで、ITSMC をインストールする Log Analytics ワークスペースを選択します。
   > [!NOTE]
   > ITSMC は、次のリージョンの Log Analytics ワークスペースにのみインストールできます: 米国東部、米国西部 2、米国中南部、US Gov アリゾナ、US Gov バージニア、カナダ中部、西ヨーロッパ、英国南部、東南アジア、東日本、インド中部、オーストラリア南東部。

4. **[Log Analytics ワークスペース]** セクションで、ITSMC リソースを作成するリソース グループを選択します。

   ![[Log Analytics ワークスペース] セクションを示すスクリーンショット。](media/itsmc-overview/itsmc-solution-workspace.png)
   
   > [!NOTE]
   > 現在進行中の、Microsoft Operations Management Suite (OMS) から Azure Monitor への移行の一環として、OMS ワークスペースは、"*Log Analytics ワークスペース*" と呼ばれるようになっています。

5. **[OK]** を選択します。

ITSMC リソースがデプロイされると、ウィンドウの右上隅に通知が表示されます。

## <a name="create-an-itsm-connection"></a>ITSM 接続を作成する

ITSMC をインストールした後、ITSMC からの接続を許可するように ITSM ツールを準備する必要があります。 手順については、接続先になる ITSM 製品に基づいて、次のいずれかのリンクを選択します。

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

ITSM ツールを準備した後、これらの手順を完了して接続を作成します。

1. **[すべてのリソース]** で、**ServiceDesk( *<実際のワークスペース名>* )** を探します。

   ![Azure portal の最新のリソースを示すスクリーンショット。](media/itsmc-definition/create-new-connection-from-resource.png)

1. 左側のペインの **[ワークスペースのデータ ソース]** で、 **[ITSM 接続]** を選択します。

   ![[ITSM 接続] メニュー項目を示すスクリーンショット。](media/itsmc-overview/add-new-itsm-connection.png)

1. **[接続の追加]** を選択します。

1. 使用している ITSM 製品に従って、接続設定を指定します。

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   > 既定では、ITSMC の接続の構成データは、24 時間に 1 回更新されます。 接続のデータをすぐに最新の情報に更新し、編集内容やテンプレートの更新を反映させるには、接続のペインにある **[同期]** ボタンを選択します。
   >
   > ![接続のペインの [同期] ボタンを示すスクリーンショット。](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="create-itsm-work-items-from-azure-alerts"></a>Azure アラートから ITSM 作業項目を作成する

ITSM 接続を作成した後、ITSM を使用して、Azure アラートに基づいて ITSM ツールで作業項目を作成できます。 作業項目を作成するには、アクション グループで ITSM アクションを使用します。

アクション グループでは、Azure アラートのアクションをトリガーするために、モジュール化された、再利用できる方法が提供されます。 アクション グループは、Azure portal で、メトリック アラート、アクティビティ ログ アラート、および Log Analytics アラートと共に使用できます。

> [!NOTE]
> ITSM 接続を作成した後、同期プロセスが終了するまで 30 分待機する必要があります。

## <a name="define-a-template"></a>テンプレートを定義する

特定の作業項目の種類では、ITSM ツールで定義したテンプレートを使用できます。 テンプレートを使用すると、アクション グループの固定値に従って自動的に入力されるフィールドを定義できます。 アクション グループの定義の一部として使用するテンプレートを定義できます。 テンプレートの作成方法については、ServiceNow ドキュメントの情報を参照してください - (こちらに記載されています)[https://docs.servicenow.com/bundle/paris-platform-administration/page/administer/form-administration/task/t_CreateATemplateUsingTheTmplForm.html ]。

アクション グループを作成するには:

1. Azure portal で、 **[アラート]** を選択します。
2. 画面上部のメニューで、 **[アクションの管理]** を選択します。

    ![[アクションの管理] メニュー項目を示すスクリーンショット。](media/itsmc-overview/action-groups-selection-big.png)

   **[アクション グループの作成]** ウィンドウが表示されます。

3. アクション グループを作成する **サブスクリプション** と **リソース グループ** を選択します。 アクション グループの **[アクション グループ名]** と **[表示名]** の値を指定します。 次に、**次のステップ: 通知** を選択します。

    ![[アクション グループの作成] ウィンドウを示すスクリーンショット。](media/itsmc-overview/action-groups-details.png)

4. **[通知]** タブで、 **[次へ: アクション]** を選択します。
5. **[アクション]** タブで、 **[アクションの種類]** 一覧の **[ITSM]** を選択します。 **[名前]** には、アクションの名前を指定します。 その後、 **[詳細の編集]** を表すペンのボタンを選択します。

    ![アクション グループを作成するための選択を示すスクリーンショット。](media/itsmc-definition/action-group-pen.png)

6. **[サブスクリプション]** 一覧で、Log Analytics ワークスペースを含むサブスクリプションを選択します。 **[接続]** の一覧で、ITSM コネクタ名を選択します。 その後には実際のワークスペース名が続きます。 例として、*MyITSMConnector(MyWorkspace)* があります。

7. **作業項目** の種類を選択します。

8. 標準状態のフィールドに固定値を入力する場合は、 **[カスタム テンプレートを使用する]** を選択します。 それ以外の場合は、 **[テンプレート]** の一覧で既存の [テンプレート](#define-a-template)を選択し、テンプレート フィールドに固定値を入力します。

9. ITSM アクション グループを作成するためのインターフェイスの最後のセクションでは、各アラートに対して作成される作業項目の数を定義できます。

   > [!NOTE]
   > このセクションはログ検索アラートのみに関連しています。 その他のすべてのアラートの種類については、アラートごとに 1 つの作業項目を作成します。

   * **[作業項目]** ドロップダウン リストで **[インシデント]** または **[アラート]** を選択した場合は、各構成アイテムに対して個別の作業項目を作成するオプションがあります。
    
     ![作業項目として [インシデント] が選択された [ITSM チケット] 領域を示すスクリーンショット。](media/itsmc-overview/itsm-action-configuration.png)
    
     * **[各構成アイテムに対して個々の作業項目を作成する]** チェック ボックスをオンにすると、すべてのアラートのすべての構成アイテムに新しい作業項目が作成されます。 影響を受ける同じ構成アイテムに対して複数のアラートが発生するため、各構成アイテムに対して複数の作業項目が存在することになります。

       たとえば、構成アイテムが 3 つのアラートでは、3 つの作業項目が作成されます。 構成アイテムが 1 つのアラートでは 1 つの作業項目が作成されます。
        
     * **[各構成アイテムに対して個々の作業項目を作成する]** チェック ボックスをオフにすると、ITSMC により、各アラート ルールに対して 1 つの作業項目が作成され、影響を受けるすべての構成アイテムがそれに追加されます。 前の作業項目がクローズされている場合は、新しいものが作成されます。

       >[!NOTE]
       > この場合、発生したアラートの一部では、ITSM ツールで新しい作業項目が生成されません。

       たとえば、構成アイテムが 3 つのアラートでは、1 つの作業項目が作成されます。 前の例と同じアラート ルールのアラートに 1 つの構成アイテムがある場合、その構成アイテムは、作成された作業項目内の影響を受ける構成アイテムの一覧にアタッチされます。 構成アイテムが 1 つの別のアラート ルールのアラートでは、1 つの作業項目が作成されます。

   * **[作業項目]** ドロップダウン リストで **[イベント]** を選択した場合は、各ログ エントリまたは各構成アイテムに対して個別の作業項目を作成することを選択できます。
    
     ![作業項目として [イベント] が選択された [ITSM チケット] 領域を示すスクリーンショット。](media/itsmc-overview/itsm-action-configuration-event.png)

     * **[Create individual work items for each Log Entry (Configuration item field is not filled.Can result in large number of work items.)]\(各ログ エントリに対して、個々の作業項目を作成します (構成アイテム フィールドが入力されていません。大量の作業項目が生成される可能性があります)\)** を選んだ場合は、ログ検索アラート クエリの検索結果の各行に対して作業項目が作成されます。 作業項目のペイロードの description プロパティには、検索結果の行が含まれるようになります。
      
     * **[各構成アイテムに対して個々の作業項目を作成する]** を選んだ場合は、すべてのアラートのすべての構成アイテムに新しい作業項目が作成されます。 ITSM システムでは、各構成アイテムに複数の作業項目を含めることができます。 このオプションは、作業項目の種類として **[インシデント]** を選択した後に表示されるチェック ボックスをオンにした場合と同じです。

10. **[OK]** を選択します。

Azure アラートのルールを作成または編集するときには、ITSM アクションを含むアクション グループを使用します。 アラートがトリガーされると、ITSM ツールで、作業項目が作成または更新されます。

> [!NOTE]
> ITSM アクションの価格については、アクション グループの[価格に関するページ](https://azure.microsoft.com/pricing/details/monitor/)を参照してください。
>
> アラート ルール定義の短い説明のフィールドは、ITSM アクションを使用して送信する場合は 40 文字に制限されます。

## <a name="next-steps"></a>次のステップ

* [ITSMC での問題のトラブルシューティングを行う](./itsmc-resync-servicenow.md)
