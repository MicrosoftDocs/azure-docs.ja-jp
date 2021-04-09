---
title: Azure Monitor で Azure AD B2C を監視する
titleSuffix: Azure AD B2C
description: 委任されたリソース管理を使用して、Azure Monitor で Azure AD B2C のイベントをログに記録する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: mimart
ms.subservice: B2C
ms.date: 01/29/2021
ms.openlocfilehash: bc1dea8121d7986b8394adf6545a0b2c30afb133
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104580183"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Azure Monitor で Azure AD B2C を監視する

Azure Monitor を使用して、Azure Active Directory B2C (Azure AD B2C) のサインインと[監査](view-audit-logs.md)ログをさまざまな監視ソリューションにルーティングします。 そのログを、長期的な使用のために保持したり、サードパーティのセキュリティ情報およびイベント管理 (SIEM) ツールと統合して環境の分析情報を取得したりすることができます。

ログ イベントは次の場所にルーティングできます。

* Azure [ストレージ アカウント](../storage/blobs/storage-blobs-introduction.md)。
* [Log Analytics ワークスペース](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) (データの分析、ダッシュボードの作成、特定のイベントに対するアラートの作成を行う)。
* Azure [イベント ハブ](../event-hubs/event-hubs-about.md) (Splunk および Sumo Logic のインスタンスと統合できます)。

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

この記事では、Azure Log Analytics ワークスペースにログを転送する方法について説明します。 そうすると、ダッシュボードを作成したり、Azure AD B2C ユーザーのアクティビティに基づいてアラートを作成したりできるようになります。

> [!IMPORTANT]
> Azure AD B2C のログを別の監視ソリューションまたはリポジトリに転送することを計画している場合は、次の点を考慮してください。 Azure AD B2C のログには個人データが含まれています。 そのようなデータは、未承認または違法な処理に対する保護など、個人データの適切なセキュリティを保証する方法で、適切な技術的または組織的手段を使用して処理する必要があります。


## <a name="deployment-overview"></a>デプロイの概要

Azure AD B2C では、[Azure Active Directory 監視](../active-directory/reports-monitoring/overview-monitoring.md)が利用されます。 Azure AD B2C テナント内の Azure Active Directory で "*診断設定*" を有効にするには、[Azure Lighthouse](../lighthouse/concepts/azure-delegated-resource-management.md) を使用して [リソースを委任](../lighthouse/concepts/azure-delegated-resource-management.md)します。これにより、Azure AD B2C (**サービス プロバイダー**) が Azure AD (**顧客**) のリソースを管理できるようになります。 この記事の手順を完了すると、**Azure AD B2C** ポータル内の [Log Analytics ワークスペース](../azure-monitor/logs/quick-create-workspace.md)が含まれる *azure-ad-b2c-monitor* リソース グループにアクセスできるようになります。 また、Azure AD B2C から Log Analytics ワークスペースにログを転送することもできます。

このデプロイ中、Azure サブスクリプションが含まれるテナント内の Log Analytics ワークスペース インスタンスを構成するために、Azure AD B2C ディレクトリでユーザーまたはグループを承認します。 承認を作成するには、サブスクリプションが含まれる Azure AD テナントに [Azure Resource Manager](../azure-resource-manager/index.yml) テンプレートをデプロイします。

次の図は、Azure AD と Azure AD B2C のテナントで構成するコンポーネントを示しています。

![リソース グループのプロジェクション](./media/azure-monitor/resource-group-projection.png)

このデプロイ中に、Log Analytics ワークスペースがホストされる Azure AD B2C テナントと Azure AD テナントの両方を構成します。 Azure AD B2C アカウントには、Azure AD B2C テナントの[全体管理者](../active-directory/roles/permissions-reference.md#limit-use-of-global-administrator)ロールが割り当てられている必要があります。 デプロイを実行するために使用される Azure AD アカウントには、Azure AD サブスクリプションの[所有者](../role-based-access-control/built-in-roles.md#owner)ロールが割り当てられている必要があります。また、説明に従って各手順を完了するときに、正しいディレクトリにサインインしていることを確認することも重要です。

## <a name="1-create-or-choose-resource-group"></a>1. リソース グループの作成または選択

まず、Azure AD B2C からデータを受信する宛先 Log Analytics ワークスペースが含まれるリソース グループを作成するか、選択します。 Azure Resource Manager テンプレートをデプロイするときに、リソース グループ名を指定します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータル ツールバーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、**Azure AD テナント** が含まれているディレクトリを選択します。
1. [リソース グループを作成する](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)か、既存のものを選択します。 この例では、*azure-ad-b2c-monitor* という名前のリソース グループを使用します。

## <a name="2-create-a-log-analytics-workspace"></a>2. Log Analytics ワークスペースの作成

**Log Analytics ワークスペース** は、Azure Monitor ログ データ用の固有の環境です。 この Log Analytics ワークスペースを使用して Azure AD B2C の[監査ログ](view-audit-logs.md)からデータを収集し、それをクエリやブックを使用して視覚化したり、アラートを作成したりします。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータル ツールバーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、**Azure AD テナント** が含まれているディレクトリを選択します。
1. [Log Analytics ワークスペースを作成します](../azure-monitor/logs/quick-create-workspace.md)。 この例では、*AzureAdB2C* という名前の Log Analytics ワークスペースを *azure-ad-b2c-monitor* という名前のリソース グループで使用します。

## <a name="3-delegate-resource-management"></a>3. リソース管理を委任する

この手順では、Azure AD B2C テナントを **サービス プロバイダー** として選択します。 また、適切な Azure 組み込みロールを Azure AD テナントのグループに割り当てるために必要な認可を定義します。

### <a name="31-get-your-azure-ad-b2c-tenant-id"></a>3.1 Azure AD B2C テナント ID を取得する

まず、Azure AD B2C ディレクトリの **テナント ID** (ディレクトリ ID とも呼ばれる) を取得します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ポータル ツールバーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、**Azure AD B2C** テナントが含まれるディレクトリを選択します。
1. **[Azure Active Directory]** を選択し、 **[概要]** を選択します。
1. **テナント ID** を記録します。

### <a name="32-select-a-security-group"></a>3.2 セキュリティ グループを選択する

次に、サブスクリプションが含まれているディレクトリに先ほど作成したリソース グループに対するアクセス許可を付与する Azure AD B2C グループまたはユーザーを選択します。  

管理をより簡単にするには、各ロールに Azure AD のユーザー "*グループ*" を使用することをお勧めします。これにより、個々のユーザーに直接アクセス許可を割り当てるのではなく、ユーザーをグループに追加または削除することができます。 このチュートリアルでは、セキュリティ グループを追加します。

> [!IMPORTANT]
> Azure AD グループのアクセス許可を追加するためには、 **[グループの種類]** を **[セキュリティ]** に設定する必要があります。 このオプションは、グループの作成時に選択します。 詳細については、「[Azure Active Directory を使用して基本グループを作成してメンバーを追加する](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。

1. **Azure AD B2C** ディレクトリで **Azure Active Directory** を選択したまま、 **[グループ]** を選択し、グループを選択します。 既存のグループがない場合は、**セキュリティ** グループを作成してから、メンバーを追加します。 詳細については、「[Azure Active Directory を使用して基本グループを作成してメンバーを追加する](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」の手順に従ってください。 
1. **[概要]** を選択し、グループの **オブジェクト ID** を記録します。

### <a name="33-create-an-azure-resource-manager-template"></a>3.3 Azure Resource Manager テンプレートを作成する

次に、前に作成した Azure AD リソース グループ (たとえば、*azure-ad-b2c-monitor*) への Azure AD B2C アクセス権を付与する Azure Resource Manager テンプレートを作成します。 **[Azure へのデプロイ]** ボタンを使用して GitHub サンプルからテンプレートをデプロイします。これにより、Azure portal が開き、ポータルで直接、テンプレートを構成してデプロイできます。 これらの手順では、(Azure AD B2C テナントではなく) Azure AD テナントにサインインしていることを確認してください。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. ポータル ツールバーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、**Azure AD テナント** が含まれているディレクトリを選択します。
3. **[Azure へのデプロイ]** ボタンを使用して、Azure portal を開き、テンプレートをポータルに直接デプロイします。 詳細については、「[Azure Resource Manager テンプレートの作成](../lighthouse/how-to/onboard-customer.md#create-an-azure-resource-manager-template)」を参照してください。

   [![Azure へのデプロイ](https://aka.ms/deploytoazurebutton)](   https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure-ad-b2c%2Fsiem%2Fmaster%2Ftemplates%2FrgDelegatedResourceManagement.json)

5. **[カスタム デプロイ]** ページで、次の情報を入力します。

   | フィールド   | 定義 |
   |---------|------------|
   | サブスクリプション |  *azure-ad-b2c-monitor* リソース グループが作成された Azure サブスクリプションが含まれているディレクトリを選択します。 |
   | リージョン| リソースがデプロイされるリージョンを選択します。  | 
   | [Msp Offer Name]\(Msp オファー名\)| この定義を説明する名前。 たとえば、*Azure AD B2C Monitoring* など。  |
   | [Msp Offer Description]\(Msp オファーの説明\)| プランの簡単な説明。 たとえば、"*Azure AD B2C で Azure Monitor を有効にする*" などです。|
   | [Managed By Tenant Id]\(テナント ID で管理\)| Azure AD B2C テナントの **テナント ID** (ディレクトリ ID とも呼ばれる)。 |
   |[Authorizations]|Azure AD の `principalId`、`principalIdDisplayName`、および Azure の `roleDefinitionId` が含まれるオブジェクトの JSON 配列を指定します。 `principalId` は、この Azure サブスクリプションのリソースにアクセスできる B2C グループまたはユーザーの **オブジェクト ID** です。 このチュートリアルでは、前に記録したグループのオブジェクト ID を指定します。 `roleDefinitionId` には、"*共同作成者ロール*" の [組み込みロール](../role-based-access-control/built-in-roles.md)の値 `b24988ac-6180-42a0-ab88-20f7382dd24c` を使用します。|
   | Rg Name \(RG 名\) | 前に Azure AD テナントに作成したリソースグループの名前。 たとえば、*azure-ad-b2c-monitor* などです。 |

   次の例は、1 つのセキュリティ グループでの承認配列を示しています。

   ```json
   [
       {
           "principalId": "<Replace with group's OBJECT ID>",
           "principalIdDisplayName": "Azure AD B2C tenant administrators",
           "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
       }
   ]
   ```

テンプレートをデプロイした後、リソース プロジェクションが完了するまでに数分 (通常 5 分以内) かかることがあります。 Azure AD テナントでデプロイを確認し、リソース プロジェクションの詳細を取得することができます。 詳細については、「[サービス プロバイダーを表示し、管理する](../lighthouse/how-to/view-manage-service-providers.md)」を参照してください。

## <a name="4-select-your-subscription"></a>4. サブスクリプションを選択します。

テンプレートをデプロイし、リソース プロジェクションが完了するまで数分待った後、次の手順に従ってサブスクリプションを Azure AD B2C ディレクトリに関連付けます。

1. Azure portal に現在サインインしている場合は、サインアウトします (これにより、次の手順でセッションの資格情報が更新されます)。
2. **Azure AD B2C** の管理者アカウントで [Azure portal](https://portal.azure.com) にサインインします。 このアカウントは、「[リソース管理を委任する](#3-delegate-resource-management)」手順で指定したセキュリティ グループのメンバーである必要があります。
3. ポータルツールバーの **[ディレクトリ + サブスクリプション]** アイコンを選択します。
4. Azure サブスクリプションと、作成した *azure-ad-b2c-monitor* リソース グループが含まれている Azure AD ディレクトリを選択します。

    ![ディレクトリを切り替える](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)

1. 正しいディレクトリとサブスクリプションを選択してあることを確認します。 この例では、すべてのディレクトリとすべてのサブスクリプションが選択されています。

    ![ディレクトリとサブスクリプションのフィルターで選択されたすべてのディレクトリ](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="5-configure-diagnostic-settings"></a>5. 診断設定を構成する

診断設定では、リソースのログとメトリックを送信する場所を定義します。 使用できる送信先は次のとおりです。

- [Azure Storage アカウント](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)
- [イベント ハブ](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) ソリューション
- [Log Analytics ワークスペース](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)

この例では、Log Analytics ワークスペースを使用してダッシュボードを作成します。

### <a name="51-create-diagnostic-settings"></a>5.1 診断設定を作成する

Azure portal で[診断設定を作成する](../active-directory/reports-monitoring/overview-monitoring.md)準備ができました。

Azure AD B2C のアクティビティ ログの監視設定を構成するには:

1. Azure AD B2C の管理者アカウントで [Azure portal](https://portal.azure.com/) にサインインします。 このアカウントは、「[セキュリティ グループを選択する](#32-select-a-security-group)」手順で指定したセキュリティ グループのメンバーである必要があります。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. **[Azure Active Directory]** を選択します
1. **[監視]** で **[診断設定]** を選択します。
1. リソースに対する既存の設定がある場合は、構成済みの設定の一覧が表示されます。 **[診断設定を追加する]** を選択して新しい設定を追加するか、 **[編集]** を選択して既存の設定を編集します。 各設定には、各送信先の種類を 1 つだけ含めることができます。

    ![Azure portal の [診断設定] ペイン](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. 設定にまだ名前がない場合は、名前を付けます。
1. ログを送信する各送信先のチェック ボックスをオンにします。 **[構成]** を選択し、**次の表の説明に従って** それらの設定を指定します。
1. **[Log Analytics への送信]** を選択し、前に作成した **ワークスペースの名前** (`AzureAdB2C`) を選択します。
1. **AuditLogs** と **SignInLogs** を選択します。
1. **[保存]** を選択します。

> [!NOTE]
> イベントが生成されて [Log Analytics ワークスペースに表示される](../azure-monitor/logs/data-ingestion-time.md)までに最大で 15 分かかる可能性があります。 [Active Directory レポートの待機時間](../active-directory/reports-monitoring/reference-reports-latencies.md)の詳細についても確認してください。これは、データの陳腐化に影響し、レポートで重要な役割を果たす可能性があります。

Azure AD B2C ディレクトリで Azure Monitor を使用するための診断設定を指定するには、委任リソース管理を設定する必要があります" というエラー メッセージが表示される場合は、[セキュリティ グループ](#32-select-a-security-group)のメンバーであるユーザーでサインインしていることと、ご使用の[サブスクリプションを選択](#4-select-your-subscription)していることを確認してください。

## <a name="6-visualize-your-data"></a>6. データを視覚化する

これで、Log Analytics ワークスペースを構成してデータを視覚化し、アラートを構成できます。 これらの構成は、Azure AD テナントと Azure AD B2C テナントの両方で行うことができます。

### <a name="61-create-a-query"></a>6.1 クエリを作成する

ログ クエリは、Azure Monitor ログ内に収集されたデータの価値を最大限に活用するのに役立ちます。 強力なクエリ言語により、複数のテーブルのデータを結合したり、大量のデータ セットを集約したり、最小限のコードによって複雑な操作を実行したりできます。 有用なデータが収集されていて、適切なクエリを作成する方法を理解していれば、ほぼすべての疑問に答えたり、分析を実行したりすることができます。 詳細については、「[Azure Monitor でログ クエリの使用を開始する](../azure-monitor/logs/get-started-queries.md)」を参照してください。

1. **Log Analytics ワークスペース** から、 **[ログ]** を選択します。
1. クエリ エディターで、次の [Kusto クエリ言語](/azure/data-explorer/kusto/query/)のクエリを貼り付けます。 このクエリは、過去 x 日間の操作によるポリシーの使用状況を示します。 既定の期間は、90 日間 (90d) に設定されています。 このクエリは、ポリシーによってトークン/コードが発行される操作のみに注目していることに注意してください。

    ```kusto
    AuditLogs
    | where TimeGenerated  > ago(90d)
    | where OperationName contains "issue"
    | extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
    | extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
    | summarize SignInCount = count() by Policy, OperationName
    | order by SignInCount desc  nulls last
    ```

1. **[実行]** を選択します。 クエリの結果が画面の下部に表示されます。
1. 後で使用するためにクエリを保存するには、 **[保存]** を選択します。

   ![Log Analytics ログ エディター](./media/azure-monitor/query-policy-usage.png)

1. 次の詳細情報を入力します。

    - **[名前]** : クエリの名前を入力します。
    - **[名前を付けて保存]** : `query` を選択します
    - **[カテゴリ]** - `Log` を選択します。

1. **[保存]** を選択します。

[render](/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor) 演算子を使用してデータを視覚化するようにクエリを変更することもできます。

```kusto
AuditLogs
| where TimeGenerated  > ago(90d)
| where OperationName contains "issue"
| extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
| extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
| summarize SignInCount = count() by Policy
| order by SignInCount desc  nulls last
| render  piechart
```

![Log Analytics ログ エディターの円グラフ](./media/azure-monitor/query-policy-usage-pie.png)

その他のサンプルについては、Azure AD B2C の [SIEM GitHub リポジトリ](https://aka.ms/b2csiem)を参照してください。

### <a name="62-create-a-workbook"></a>6.2 ブックを作成する

ブックは、Azure portal 内でデータを分析し、高度な視覚的レポートを作成するための柔軟なキャンバスを提供します。 Azure 全体から複数のデータ ソースを活用し、それらを結合して、統合された対話型エクスペリエンスにすることができます。 詳細については、「[Azure Monitor ブック](../azure-monitor/visualize/workbooks-overview.md)」を参照してください。

JSON ギャラリー テンプレートを使用して新しいブックを作成するには、次の手順に従います。 このブックには、Azure AD B2C テナントの **[User Insights]\(ユーザー分析情報\)** と **[Authentication]\(認証\)** ダッシュボードが用意されています。

1. **Log Analytics ワークスペース** から、 **[ブック]** を選択します。
1. ツールバーから **[+ 新規]** オプションを選択して、新しいブックを作成します。
1. **[新しいブック]** ページで、ツールバーの **</>** オプションを使用して **[詳細エディター]** を選択します。

     ![ギャラリー テンプレート](./media/azure-monitor/wrkb-adv-editor.png)

1. **[ギャラリー テンプレート]** を選択します。
1. **ギャラリー テンプレート** の JSON を、[Azure AD B2C の基本ブック](https://raw.githubusercontent.com/azure-ad-b2c/siem/master/workbooks/dashboard.json)の内容に置き換えます。
1. **[適用]** ボタンを使用して、テンプレートを適用します。
1. ツールバーの **[Done Editing]\(編集完了\)** ボタンを選択して、ブックの編集を終了します。
1. 最後に、ツールバーの **[保存]** ボタンを使用して、ブックを保存します。
1. **[タイトル]** ("*Azure AD B2C ダッシュボード*" など) を指定します。
1. **[保存]** を選択します。

    ![ブックを保存する](./media/azure-monitor/wrkb-title.png)

ブックに、ダッシュボードの形式でレポートが表示されます。

![ブックの最初のダッシュボード](./media/azure-monitor/wkrb-dashboard-1.png)

![ブックの 2 番目のダッシュボード](./media/azure-monitor/wrkb-dashboard-2.png)

![ブックの 3 番目のダッシュボード](./media/azure-monitor/wrkb-dashboard-3.png)


## <a name="create-alerts"></a>アラートを作成する

アラートは Azure Monitor のアラート ルールによって作成され、保存済みのクエリまたはカスタム ログ検索を一定の間隔で自動的に実行できます。 特定のパフォーマンス メトリック、特定のイベントが作成されたとき、イベントが欠如しているとき、または特定の時間枠内に作成されたイベントの数に基づくアラートを作成できます。 たとえば、アラートを使用して、サインインの平均数が特定のしきい値を超えたら通知を受けるようにできます。 詳細については、[アラートの作成](../azure-monitor/alerts/tutorial-response.md)に関するページをご覧ください。


次の手順を使用して、**合計要求数** が前の期間と比較して 25% 低下したら [電子メール通知](../azure-monitor/alerts/action-groups.md#configure-notifications)を送信する新しい Azure アラートを作成します。 アラートは 5 分ごとに実行され、過去 24 時間以内の低下が調べられます。 アラートは、Kusto クエリ言語を使用して作成されます。


1. **Log Analytics ワークスペース** から、 **[ログ]** を選択します。 
1. 次のクエリを使用して、新しい **Kusto クエリ** を作成します。

    ```kusto
    let start = ago(24h);
    let end = now();
    let threshold = -25; //25% decrease in total requests.
    AuditLogs
    | serialize TimeGenerated, CorrelationId, Result
    | make-series TotalRequests=dcount(CorrelationId) on TimeGenerated in range(start, end, 1h)
    | mvexpand TimeGenerated, TotalRequests
    | where TotalRequests > 0
    | serialize TotalRequests, TimeGenerated, TimeGeneratedFormatted=format_datetime(todatetime(TimeGenerated), 'yyyy-M-dd [hh:mm:ss tt]')
    | project   TimeGeneratedFormatted, TotalRequests, PercentageChange= ((toreal(TotalRequests) - toreal(prev(TotalRequests,1)))/toreal(prev(TotalRequests,1)))*100
    | order by TimeGeneratedFormatted
    | where PercentageChange <= threshold   //Trigger's alert rule if matched.
    ```

1. **[実行]** を選択してクエリをテストします。 過去 24 時間以内に合計要求数で 25% 以上の低下がある場合、結果が表示されます。
1. 上記のクエリに基づいてアラート ルールを作成するには、ツールバーにある **[+ 新しいアラート ルール]** オプションを使用します。
1. **[アラート ルールの作成]** ページで、 **[条件名]** を選択します。 
1. **[シグナル ロジックの構成]** ページで次の値を設定し、 **[完了]** ボタンを使用して変更を保存します。
    * アラート ロジック: **Number of results** **Greater than** **0** を設定します。
    * 評価基準: [期間 (分単位)] には **1440** を選択し、[頻度 (分単位)] には **5** を選択します。 

    ![アラート ルールの条件を作成する](./media/azure-monitor/alert-create-rule-condition.png)

アラートが作成されたら、**Log Analytics ワークスペース** にアクセスし、 **[アラート]** を選択します。 このページには、 **[時間範囲]** オプションで設定された期間内にトリガーされたすべてのアラートが表示されます。  

### <a name="configure-action-groups"></a>アクション グループを構成する

Azure Monitor および Service Health のアラートでは、アクション グループを使用して、アラートがトリガーされたことをユーザーに通知します。 音声通話、SMS、電子メールの送信やさまざまな種類の自動アクションのトリガーを含めることができます。 「[Azure portal でのアクション グループの作成および管理](../azure-monitor/alerts/action-groups.md)」ガイダンスに従ってください。

アラート通知電子メールの例を次に示します。 

   ![電子メール通知](./media/azure-monitor/alert-email-notification.png)

## <a name="multiple-tenants"></a>複数のテナント

複数の Azure AD B2C テナント ログを同じ Log Analytics ワークスペース (あるいは Azure ストレージ アカウント、またはイベント ハブ) にオンボードするには、異なる **Msp オファー名** の値を使用する別々のデプロイが必要になります。 Log Analytics ワークスペースが、「[リソース グループの作成または選択](#1-create-or-choose-resource-group)」で構成したものと同じリソース グループに含まれていることを確認してください。

複数の Log Analytics ワークスペースで作業している場合は、[クロス ワークスペース クエリ](../azure-monitor/logs/cross-workspace-query.md)を使用して、複数のワークスペースで動作するクエリを作成します。 たとえば、次のクエリでは、同じカテゴリ (たとえば、認証) に基づいて、異なるテナントからの 2 つの監査ログの結合が実行されます。

```kusto
workspace("AD-B2C-TENANT1").AuditLogs
| join  workspace("AD-B2C-TENANT2").AuditLogs
  on $left.Category== $right.Category
```

## <a name="change-the-data-retention-period"></a>データ保持期間の変更

Azure Monitor ログは、企業内の、または Azure にデプロイされた任意のソースから毎日大量のデータを収集し、インデックスを付けて、格納する処理をスケーリングおよびサポートするように設計されています。 既定では、ログは 30 日間保持されますが、データ保持期間は最大 2 年間まで延長できます。 [Azure Monitor ログで使用量とコストを管理する](../azure-monitor/logs/manage-cost-storage.md)方法について説明します。 価格レベルを選択したら、[データ保持期間を変更](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)できます。

## <a name="next-steps"></a>次のステップ

* その他のサンプルについては、Azure AD B2C の [SIEM ギャラリー](https://aka.ms/b2csiem)を参照してください。 

* Azure Monitor での診断設定の追加と構成の詳細については、「[チュートリアル: Azure リソースからリソース ログを収集して分析する](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。

* イベント ハブへの Azure AD ログのストリーム配信の詳細については、「[チュートリアル: Azure Active Directory ログを Azure イベント ハブにストリーム配信する](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)」を参照してください。
