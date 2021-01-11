---
title: 自動化を使用した Azure コストの管理
description: この記事では、自動化を使用して Azure コストを管理する方法について説明します。
author: bandersmsft
ms.author: banders
ms.date: 08/19/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: a5ab84794884cc0c87bd766be7a0fa2fe4c52aa9
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684407"
---
# <a name="manage-costs-with-automation"></a>自動化を使用したコストの管理

Cost Management の自動化を使用して、コスト データの取得と管理を行う一連のカスタム ソリューションをビルドすることができます。 この記事では、Cost Management の自動化の一般的なシナリオと状況に基づいて使用できるオプションについて説明します。 API を使用して開発する場合に、開発プロセスの時間を短縮するために役立つ一般的な API 要求の例を紹介します。

## <a name="automate-cost-data-retrieval-for-offline-analysis"></a>オフライン分析のためのコスト データの取得を自動化する

ご自分の Azure コスト データをダウンロードし、それを他のデータ セットとマージすることが必要になる場合があります。 また、コスト データを自身のシステムに統合することが必要になる場合もあります。 関連するデータ量に応じて使用できるさまざまなオプションがあります。 どのような場合でも、API とツールを使用するには、適切なスコープでの Cost Management へのアクセス許可が必要です。 詳細については、[データへのアクセスの割り当て](https://docs.microsoft.com/azure/cost-management-billing/costs/assign-access-acm-data)に関するページを参照してください。

## <a name="suggestions-for-handling-large-datasets"></a>大規模なデータ セットの処理に関する推奨事項

組織で、多くのリソースまたはサブスクリプションで Azure の使用率が高い場合、使用状況の詳細データが大量に生成されます。 多くの場合、Excel では、このような大きなファイルを読み込むことはできません。 このような場合、次のオプションをお勧めします。

**Power BI**

Power BI は、大量のデータの取り込みと処理に使用されます。 マイクロソフトエンタープライズ契約のお客様の場合、Power BI テンプレート アプリを使用して、ご使用の課金アカウントのコストを分析することができます。 レポートには、お客様が使用される主要なビューが含まれます。 詳細については、[Power BI テンプレート アプリを使用して Azure のコストを分析する](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app)方法に関するページを参照してください。

**Power BI データ コネクタ**

データを毎日分析する必要がある場合、[Power BI データ コネクタ](https://docs.microsoft.com/power-bi/connect-data/desktop-connect-azure-cost-management)を使用して詳細な分析のためのデータを取得することをお勧めします。 作成するレポートは、より多くのコストが発生すると、コネクタにより最新の状態に維持されます。

**Cost Management のエクスポート**

データを毎日分析する必要がない場合もあります。 その場合は、Cost Management の[エクスポート](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data)機能を使用して、Azure Storage アカウントへのデータのエクスポートをスケジュールすることを検討してください。 その後、必要に応じてデータを Power BI に読み込むか、ファイルが十分に小さい場合は Excel でデータを分析できます。 エクスポートは Azure portal でご使用いただけます。また、[Exports API](https://docs.microsoft.com/rest/api/cost-management/exports) を使用してエクスポートを構成することもできます。

**Usage Details API**

コスト データ セットが小さい場合、[Usage Details API](https://docs.microsoft.com/rest/api/consumption/usageDetails) の使用を検討してください。 コスト データが大量にある場合は、ある期間の可能な限り最小量の使用量データを要求する必要があります。 そのためには、要求で短い時間範囲を指定するか、フィルターを使用します。 たとえば、3 年分のコスト データを必要とするシナリオでは、この API は、1 回の呼び出しではなく、さまざまな時間範囲に対して複数の呼び出しを行う方がより効果的です。 この後、データを Excel に読み込んで、さらに分析を行うことができます。

## <a name="automate-retrieval-with-usage-details-api"></a>Usage Details API を使用して取得を自動化する

[Usage Details API](https://docs.microsoft.com/rest/api/consumption/usageDetails) を使用すると、Azure の請求書に対応する未加工の集計されていないコスト データを簡単に取得できます。 この API は、組織がプログラムによるデータの取得ソリューションを必要とする場合に役立ちます。 小さいコスト データ セットを分析する場合は、この API の使用をご検討ください。 ただし、データ セットが大きい場合は、前述の他のソリューションを使用する必要があります。 Usage Details の詳細データは、1 日あたりの測定ごとに提供されます。 これは、毎月の請求額の計算に使用されます。 この API の一般提供 (GA) バージョンは、`2019-10-01` です。 API を使用して、予約および Azure Marketplace での購入のためにプレビュー バージョンにアクセスするには、`2019-04-01-preview` を使用してください。

### <a name="usage-details-api-suggestions"></a>Usage Details API に関する推奨事項

**要求のスケジュール**

Usage Details API に対する要求は、1 日あたり "_1 回のみ_" 行うことをお勧めします。 コスト データの更新の頻度および丸め処理の方法の詳細については、「[Cost Management のデータを理解する](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data#rated-usage-data-refresh-schedule)」を参照してください。

**フィルター処理を行わずに最上位レベルのスコープを対象とする**

この API を使用して、使用可能な最も高いレベルのスコープで、必要なすべてのデータを取得します。 必要なすべてのデータが取り込まれるまで待ってから、フィルター処理、グループ化、または集計分析を行います。 この API は、特に大量の集計されていない未加工のコスト データを提供するために最適化されています。 Cost Management の使用可能なスコープの詳細については、「[スコープを理解して使用する](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-work-scopes)」を参照してください。 スコープの必要なデータをダウンロードした後、Excel で、フィルターやピボット テーブルを使用してデータを詳細に分析します。

## <a name="example-usage-details-api-requests"></a>Usage Details API に対する要求の例

次の要求例は、Microsoft のお客様が、直面する可能性のある一般的なシナリオに対処するために使用されています。

### <a name="get-usage-details-for-a-scope-during-specific-date-range"></a>特定の日付範囲内のスコープの使用状況の詳細を取得する

要求によって返されるデータは、使用状況が課金システムによって受信された日付に対応します。 これには、複数の請求書のコストが含まれている場合があります。

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?$filter=properties%2FusageStart%20ge%20'2020-02-01'%20and%20properties%2FusageEnd%20le%20'2020-02-29'&$top=1000&api-version=2019-10-01

```

### <a name="get-amortized-cost-details"></a>償却コストの詳細を取得する

未払いの購入を表示するために実際のコストが必要な場合、次の要求の *metric* を `ActualCost` に変更します。 償却コストと実際のコストを使用する場合、`2019-04-01-preview` バージョンを使用する必要があります。 この API の現在のバージョンは、`2019-10-01` と同様に動作します。ただし、新しい型およびメトリック属性が追加され、プロパティ名が変更されています。 Microsoft 顧客契約をお持ちの場合は、次の例の `startDate` と `endDate` がフィルターになります。

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

## <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>エクスポートを使用して大規模なコスト データ セットを繰り返し取得する

Cost Management からエクスポートを使用して、大量のデータを定期的にエクスポートできます。 エクスポートは、未集計のコスト データを取得する際に推奨される方法です。 特に、使用状況ファイルが大きすぎるために、Usage Details API を使用して確実に呼び出したりダウンロードしたりできない場合に推奨されます。 エクスポートされたデータは、自分が選択した Azure Storage アカウントに配置されます。 そこから、お使いのシステムにそれを読み込み、必要に応じて分析できます。 Azure portal でエクスポートを構成するには、[データのエクスポート](tutorial-export-acm-data.md)に関するページを参照してください。

さまざまなスコープでエクスポートを自動化する場合は、次のセクションのサンプル API 要求が出発点として適しています。 Exports API を使用して、一般的な環境構成の一部として自動エクスポートを作成できます。 自動エクスポートは、必要なデータを確実に入手するのに役立ちます。 Azure の使用の拡大に伴って、ご自分の組織のシステムで使用できます。

### <a name="common-export-configurations"></a>一般的なエクスポート構成

最初のエクスポートを作成する前に、実際のシナリオと、それを実現するために必要な構成オプションを検討します。 次のエクスポート オプションを検討してください。

- **繰り返し** - エクスポート ジョブを実行する頻度と、自分の Azure Storage アカウントにファイルを配置するタイミングを決定します。 Daily、Weekly、Monthly から選択します。 自分の組織の内部システムで使用されているデータ インポート ジョブに合わせて繰り返しを構成してみてください。
- **繰り返し期間** - エクスポートの有効期間を決定します。 ファイルは、繰り返し期間中にのみエクスポートされます。
- **期間** - 特定の実行時にエクスポートによって生成されるデータの量を決定します。 一般的なオプションは、MonthToDate と WeekToDate です。
- **開始日** - エクスポート スケジュールをいつ開始するかを構成します。 エクスポートは開始日に作成され、その後は繰り返しに基づいて作成されます。
- **種類** - エクスポートには 3 つの種類があります。
  - ActualCost - 指定された期間の合計使用量とコストを示します。これらが計上され、お客様の請求書に表示されます。
  - AmortizedCost - 該当する予約購入コストに償却を適用して、指定された期間の合計使用量とコストを示します。
  - Usage - 2020 年 7 月 20 日より前に作成されたエクスポートは、いずれも種類が Usage になります。 スケジュールされたすべてのエクスポートを、ActualCost または AmortizedCost として更新します。
- **列** - エクスポート ファイルに含めるデータ フィールドを定義します。 これらは、Usage Details API で使用できるフィールドに対応しています。 詳細については、[Usage Details API](/rest/api/consumption/usagedetails/list) に関する記事をご覧ください。

### <a name="create-a-daily-month-to-date-export-for-a-subscription"></a>サブスクリプションに対して月度累計の日単位のエクスポートを作成する

要求 URL: `PUT https://management.azure.com/{scope}/providers/Microsoft.CostManagement/exports/{exportName}?api-version=2020-06-01`

```json
{
  "properties": {
    "schedule": {
      "status": "Active",
      "recurrence": "Daily",
      "recurrencePeriod": {
        "from": "2020-06-01T00:00:00Z",
        "to": "2020-10-31T00:00:00Z"
      }
    },
    "format": "Csv",
    "deliveryInfo": {
      "destination": {
        "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MYDEVTESTRG/providers/Microsoft.Storage/storageAccounts/{yourStorageAccount} ",
        "container": "{yourContainer}",
        "rootFolderPath": "{yourDirectory}"
      }
    },
    "definition": {
      "type": "ActualCost",
      "timeframe": "MonthToDate",
      "dataSet": {
        "granularity": "Daily",
        "configuration": {
          "columns": [
            "Date",
            "MeterId",
            "ResourceId",
            "ResourceLocation",
            "Quantity"
          ]
        }
      }
    }
}
```

### <a name="automate-alerts-and-actions-with-budgets"></a>予算を使用してアラートとアクションを自動化する

クラウドへの投資の価値を最大化するための重要な要素が 2 つあります。 1 つは予算の自動作成です。 もう 1 つは、予算アラートに対応するコストベースのオーケストレーションの構成です。 Azure の予算作成を自動化するには、さまざまな方法があります。 構成済みのアラートのしきい値を超えると、さまざまなアラート応答が発生します。

以下のセクションでは、使用可能なオプションについて説明し、予算の自動化を開始するためのサンプル API 要求を示します。

#### <a name="how-costs-are-evaluated-against-your-budget-threshold"></a>予算しきい値に対してコストが評価されるしくみ

コストは、1 日に 1 回、予算しきい値に対して評価されます。 新しい予算を作成したとき、または予算のリセット日には、評価が行われていない可能性があるため、しきい値と比較したコストはゼロまたは null になります。

コストがしきい値を超えたことが Azure によって検出されると、検出後 1 時間以内に通知がトリガーされます。

#### <a name="view-your-current-cost"></a>現在のコストを表示する

現在のコストを表示するには、[Query API](/rest/api/cost-management/query) を使用して GET 呼び出しを行う必要があります。

Budgets API への GET 呼び出しでは、コスト分析で示される現在のコストは返されません。 代わりに、この呼び出しでは最後に評価されたコストが返されます。

### <a name="automate-budget-creation"></a>予算の作成を自動化する

[Budgets API](/rest/api/consumption/budgets) を使用して、予算の作成を自動化できます。 [予算テンプレート](quick-create-budget-template.md)を使用して予算を作成することもできます。 テンプレートを使用すると、コスト管理を適切に構成し、確実に適用して、Azure デプロイを簡単に標準化できます。

#### <a name="common-budgets-api-configurations"></a>Budgets API の一般的な構成

Azure 環境で予算を構成する方法は多数あります。 まず、実際のシナリオを検討してから、それを実現する構成オプションを特定します。 次のオプションを確認します。

- **時間グレイン** - 予算でコストの計上と評価に使用する定期的な期間を表します。 最も一般的なオプションは、Monthly、Quarterly、Annual です。
- **期間** - 予算の有効期間を表します。 予算は、有効な間だけアクティブに監視され、アラートが通知されます。
- **通知**
  - 連絡先のメール - 予算でコストが計上され、予算が定義済みのしきい値を超えると、このメール アドレスにアラートが送信されます。
  - 連絡先のロール - このオプションでは、特定のスコープで一致する Azure RBAC ロールが割り当てられているすべてのユーザーに、メール アラートが送信されます。 たとえば、サブスクリプション所有者は、サブスクリプション スコープで作成された予算のアラートを受け取ることができます。
  - 連絡先グループ - アラートのしきい値を超えると、構成済みのアクション グループが予算によって呼び出されます。
- **コスト ディメンション フィルター** - コスト分析または Query API で実行できるのと同じフィルター処理を予算に対しても実行できます。 このフィルターを使用して、予算で監視しているコストの範囲を縮小します。

自分のニーズに合った予算作成オプションを特定したら、API を使用して予算を作成します。 次の例は、一般的な予算構成を開始する際に役立ちます。

**複数のリソースとタグにフィルター処理された予算を作成する**

要求 URL: `PUT https://management.azure.com/subscriptions/{SubscriptionId} /providers/Microsoft.Consumption/budgets/{BudgetName}/?api-version=2019-10-01`

```json
{
  "eTag": "\"1d34d016a593709\"",
  "properties": {
    "category": "Cost",
    "amount": 100.65,
    "timeGrain": "Monthly",
    "timePeriod": {
      "startDate": "2017-10-01T00:00:00Z",
      "endDate": "2018-10-31T00:00:00Z"
    },
    "filter": {
      "and": [
        {
          "dimensions": {
            "name": "ResourceId",
            "operator": "In",
            "values": [
              "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{meterName}",
              "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{meterName}"
            ]
          }
        },
        {
          "tags": {
            "name": "category",
            "operator": "In",
            "values": [
              "Dev",
              "Prod"
            ]
          }
        },
        {
          "tags": {
            "name": "department",
            "operator": "In",
            "values": [
              "engineering",
              "sales"
            ]
          }
        }
      ]
    },
    "notifications": {
      "Actual_GreaterThan_80_Percent": {
        "enabled": true,
        "operator": "GreaterThan",
        "threshold": 80,
        "contactEmails": [
          "user1@contoso.com",
          "user2@contoso.com"
        ],
        "contactRoles": [
          "Contributor",
          "Reader"
        ],
        "contactGroups": [
          "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.insights/actionGroups/{actionGroupName}
        ],
        "thresholdType": "Actual"
      }
    }
  }
}
```

### <a name="configure-cost-based-orchestration-for-budget-alerts"></a>予算アラートに対応するコストベースのオーケストレーションを構成する

Azure アクション グループを使用して自動化されたアクションを開始するように予算を構成できます。 予算を使用したアクションの自動化の詳細については、[Azure Budgets を使用した自動化](../manage/cost-management-budget-scenario.md)に関する記事をご覧ください。

## <a name="data-latency-and-rate-limits"></a>データ待機時間とレート制限

この API は、1 日に 1 回だけ呼び出すことをお勧めします。 Cost Management データは、4 時間ごとに Azure リソース プロバイダーから新しい使用状況データを受け取って更新されます。 呼び出しの頻度が増やしても、追加のデータは提供されません。 代わりに、負荷が増加します。 データが変更される頻度と、データ待機時間の処理方法の詳細については、「[Cost Management のデータを理解する](understand-cost-mgt-data.md)」を参照してください。

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>エラー コード 429 - 呼び出し回数がレート制限を超えた場合

Cost Management サブスクリプションをお持ちのすべてのユーザーに対して一貫したエクスペリエンスを実現するために、Cost Management API にはレート制限があります。 この制限に達すると、HTTP 状態コード `429: Too many requests` が返されます。 API の現在のスループット制限は、次のとおりです。

- 1 分あたり 30 回の呼び出し - スコープあたりのユーザーあたり、またはアプリケーションあたりの回数です。
- 1 分あたり 200 回の呼び出し - テナントあたりのユーザーあたり、またはアプリケーションあたりの回数です。

## <a name="next-steps"></a>次のステップ

- [Power BI テンプレート アプリを使用して Azure のコストを分析する](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app)。
- エクスポートを使用して、[データをエクスポートし、管理する](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data)。
- [Usage Details API](https://docs.microsoft.com/rest/api/consumption/usageDetails) の詳細情報