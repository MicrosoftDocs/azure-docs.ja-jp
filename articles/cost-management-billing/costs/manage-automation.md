---
title: 自動化を使用した Azure コストの管理
description: この記事では、自動化を使用して Azure コストを管理する方法について説明します。
author: bandersmsft
ms.author: banders
ms.date: 03/08/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: f5cebffeaba1ce198be347758004068e8c03133b
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499681"
---
# <a name="manage-costs-with-automation"></a>自動化を使用したコストの管理

Cost Management の自動化を使用して、コスト データの取得と管理を行う一連のカスタム ソリューションをビルドすることができます。 この記事では、Cost Management の自動化の一般的なシナリオと状況に基づいて使用できるオプションについて説明します。 API を使用して開発する場合に、開発プロセスの時間を短縮するために役立つ一般的な API 要求の例を紹介します。

## <a name="automate-cost-data-retrieval-for-offline-analysis"></a>オフライン分析のためのコスト データの取得を自動化する

ご自分の Azure コスト データをダウンロードし、それを他のデータ セットとマージすることが必要になる場合があります。 また、コスト データを自身のシステムに統合することが必要になる場合もあります。 関連するデータ量に応じて使用できるさまざまなオプションがあります。 どのような場合でも、API とツールを使用するには、適切なスコープでの Cost Management へのアクセス許可が必要です。 詳細については、[データへのアクセスの割り当て](./assign-access-acm-data.md)に関するページを参照してください。

## <a name="suggestions-for-handling-large-datasets"></a>大規模なデータ セットの処理に関する推奨事項

組織で、多くのリソースまたはサブスクリプションで Azure の使用率が高い場合、使用状況の詳細データが大量に生成されます。 多くの場合、Excel では、このような大きなファイルを読み込むことはできません。 このような場合、次のオプションをお勧めします。

**Power BI**

Power BI は、大量のデータの取り込みと処理に使用されます。 マイクロソフトエンタープライズ契約のお客様の場合、Power BI テンプレート アプリを使用して、ご使用の課金アカウントのコストを分析することができます。 レポートには、お客様が使用される主要なビューが含まれます。 詳細については、[Power BI テンプレート アプリを使用して Azure のコストを分析する](./analyze-cost-data-azure-cost-management-power-bi-template-app.md)方法に関するページを参照してください。

**Power BI データ コネクタ**

データを毎日分析する必要がある場合、[Power BI データ コネクタ](/power-bi/connect-data/desktop-connect-azure-cost-management)を使用して詳細な分析のためのデータを取得することをお勧めします。 作成するレポートは、より多くのコストが発生すると、コネクタにより最新の状態に維持されます。

**Cost Management のエクスポート**

データを毎日分析する必要がない場合もあります。 その場合は、Cost Management の[エクスポート](./tutorial-export-acm-data.md)機能を使用して、Azure Storage アカウントへのデータのエクスポートをスケジュールすることを検討してください。 その後、必要に応じてデータを Power BI に読み込むか、ファイルが十分に小さい場合は Excel でデータを分析できます。 エクスポートは Azure portal でご使用いただけます。また、[Exports API](/rest/api/cost-management/exports) を使用してエクスポートを構成することもできます。

**Usage Details API**

コスト データ セットが小さい場合、[Usage Details API](/rest/api/consumption/usageDetails) の使用を検討してください。 コスト データが大量にある場合は、ある期間の可能な限り最小量の使用量データを要求する必要があります。 そのためには、要求で短い時間範囲を指定するか、フィルターを使用します。 たとえば、3 年分のコスト データを必要とするシナリオでは、この API は、1 回の呼び出しではなく、さまざまな時間範囲に対して複数の呼び出しを行う方がより効果的です。 この後、データを Excel に読み込んで、さらに分析を行うことができます。

## <a name="automate-retrieval-with-usage-details-api"></a>Usage Details API を使用して取得を自動化する

[Usage Details API](/rest/api/consumption/usageDetails) を使用すると、Azure の請求書に対応する未加工の集計されていないコスト データを簡単に取得できます。 この API は、組織がプログラムによるデータの取得ソリューションを必要とする場合に役立ちます。 小さいコスト データ セットを分析する場合は、この API の使用をご検討ください。 ただし、データ セットが大きい場合は、前述の他のソリューションを使用する必要があります。 Usage Details の詳細データは、1 日あたりの測定ごとに提供されます。 これは、毎月の請求額の計算に使用されます。 この API の一般提供 (GA) バージョンは、`2019-10-01` です。 API を使用して、予約および Azure Marketplace での購入のためにプレビュー バージョンにアクセスするには、`2019-04-01-preview` を使用してください。

エクスポートされたデータを大量かつ定期的に取得する場合には、「[エクスポートを使用してサイズの大きなコスト データセットを繰り返し取得する](ingest-azure-usage-at-scale.md)」を参照してください。

### <a name="usage-details-api-suggestions"></a>Usage Details API に関する推奨事項

**要求のスケジュール**

Usage Details API に対する要求は、1 日あたり "_1 回のみ_" 行うことをお勧めします。 コスト データの更新の頻度および丸め処理の方法の詳細については、「[Cost Management のデータを理解する](./understand-cost-mgt-data.md)」を参照してください。

**フィルター処理を行わずに最上位レベルのスコープを対象とする**

この API を使用して、使用可能な最も高いレベルのスコープで、必要なすべてのデータを取得します。 必要なすべてのデータが取り込まれるまで待ってから、フィルター処理、グループ化、または集計分析を行います。 この API は、特に大量の集計されていない未加工のコスト データを提供するために最適化されています。 Cost Management の使用可能なスコープの詳細については、「[スコープを理解して使用する](./understand-work-scopes.md)」を参照してください。 スコープの必要なデータをダウンロードした後、Excel で、フィルターやピボット テーブルを使用してデータを詳細に分析します。

### <a name="notes-about-pricing"></a>価格に関する注意事項

価格シートまたは請求書を使用して使用量と料金を調整する場合は、次の情報に注意してください。

価格シートの価格の内容 - 価格シートに表示される価格は、Azure からお渡ししている価格です。 これらは、特定の測定ユニットにスケーリングされます。 残念ながら、この測定ユニットは、実際のリソース使用量や料金が発生する測定ユニットとは必ずしも一致しりません。

使用量詳細の価格の内容 - 使用状況ファイルには、価格シートと正確には一致しない可能性のあるスケーリングされた情報が表示されます。 具体的な内容は次のとおりです。

- ユニット価格 - この価格は、Azure リソースが実際に料金を発生させる測定ユニットに合わせてスケーリングされます。 スケーリングが行われると、価格は価格シートに表示される価格とは一致しなくなります。
- 測定ユニット - Azure リソースが実際に料金を発生させる測定ユニットを表します。
- 実効価格またはリソース料金 - この価格は、割引が適用された後の、ユニットあたりのお支払い料金を表します。 これは、数量と併せて価格 x 数量という計算を行って料金を調整する際に使用する必要がある料金です。 この価格は、以下のシナリオと、ファイルに表示されるスケーリングされたユニット価格が考慮されます。 その結果、スケーリングされたユニット価格とは異なる場合があります。
  - 階層化された価格 - たとえば、最初の 100 ユニットは $10、次の 100 ユニットは $8 のような場合です。
  - 含まれる数量 - たとえば、最初の 100 ユニットは無料、ユニットごとに $10 のような場合です。
  - Reservations
  - 計算時に発生する丸め処理 - 丸め処理では、使用量、階層化された数量または含まれる数量の価格、およびスケーリングされたユニット価格が考慮されます。

## <a name="example-usage-details-api-requests"></a>Usage Details API に対する要求の例

次の要求例は、Microsoft のお客様が、直面する可能性のある一般的なシナリオに対処するために使用されています。

### <a name="get-usage-details-for-a-scope-during-specific-date-range"></a>特定の日付範囲内のスコープの使用状況の詳細を取得する

要求によって返されるデータは、使用状況が課金システムによって受信された日付に対応します。 これには、複数の請求書のコストが含まれている場合があります。 使用する呼び出しは、お使いのサブスクリプションの種類によって異なります。

マイクロソフト エンタープライズ契約 (EA) または従量課金制のサブスクリプションをお持ちの従来のお客様の場合は、次の呼び出しを使用します。

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?$filter=properties%2FusageStart%20ge%20'2020-02-01'%20and%20properties%2FusageEnd%20le%20'2020-02-29'&$top=1000&api-version=2019-10-01
```

Microsoft 顧客契約をご利用の新しいお客様の場合は、次の呼び出しを使用します。

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?startDate=2020-08-01&endDate=&2020-08-05$top=1000&api-version=2019-10-01
```

### <a name="get-amortized-cost-details"></a>償却コストの詳細を取得する

未払いの購入を表示するために実際のコストが必要な場合、次の要求の *metric* を `ActualCost` に変更します。 償却コストと実際のコストを使用する場合、`2019-04-01-preview` バージョンを使用する必要があります。 この API の現在のバージョンは、`2019-10-01` と同様に動作します。ただし、新しい型およびメトリック属性が追加され、プロパティ名が変更されています。 Microsoft 顧客契約をお持ちの場合は、次の例の `startDate` と `endDate` がフィルターになります。

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

## <a name="automate-alerts-and-actions-with-budgets"></a>予算を使用してアラートとアクションを自動化する

クラウドへの投資の価値を最大化するための重要な要素が 2 つあります。 1 つは予算の自動作成です。 もう 1 つは、予算アラートに対応するコストベースのオーケストレーションの構成です。 Azure の予算作成を自動化するには、さまざまな方法があります。 構成済みのアラートのしきい値を超えると、さまざまなアラート応答が発生します。

以下のセクションでは、使用可能なオプションについて説明し、予算の自動化を開始するためのサンプル API 要求を示します。

### <a name="how-costs-are-evaluated-against-your-budget-threshold"></a>予算しきい値に対してコストが評価されるしくみ

コストは、1 日に 1 回、予算しきい値に対して評価されます。 新しい予算を作成したとき、または予算のリセット日には、評価が行われていない可能性があるため、しきい値と比較したコストはゼロまたは null になります。

コストがしきい値を超えたことが Azure によって検出されると、検出後 1 時間以内に通知がトリガーされます。

### <a name="view-your-current-cost"></a>現在のコストを表示する

現在のコストを表示するには、[Query API](/rest/api/cost-management/query) を使用して GET 呼び出しを行う必要があります。

Budgets API への GET 呼び出しでは、コスト分析で示される現在のコストは返されません。 代わりに、この呼び出しでは最後に評価されたコストが返されます。

### <a name="automate-budget-creation"></a>予算の作成を自動化する

[Budgets API](/rest/api/consumption/budgets) を使用して、予算の作成を自動化できます。 [予算テンプレート](quick-create-budget-template.md)を使用して予算を作成することもできます。 テンプレートを使用すると、コスト管理を適切に構成し、確実に適用して、Azure デプロイを簡単に標準化できます。

### <a name="supported-locales-for-budget-alert-emails"></a>予算のアラート メールでサポートされているロケール

予算については、コストが設定されたしきい値を超えた場合にアラートを受け取ります。 1 つの予算につき最大 5 つの電子メール受信者を設定できます。 受信者は、予算しきい値を超過してから 24 時間以内に電子メール アラートを受信します。 ただし、受信者は別の言語で電子メールを受信する必要がある場合があります。 次の言語カルチャ コードを、Budgets API で使用できます。 次の例のような `locale` パラメーターを使用して、カルチャ コードを設定します。

```json
{
  "eTag": "\"1d681a8fc67f77a\"",
  "properties": {
    "timePeriod": {
      "startDate": "2020-07-24T00:00:00Z",
      "endDate": "2022-07-23T00:00:00Z"
    },
    "timeGrain": "BillingMonth",
    "amount": 1,
    "currentSpend": {
      "amount": 0,
      "unit": "USD"
    },
    "category": "Cost",
    "notifications": {
      "actual_GreaterThan_10_Percent": {
        "enabled": true,
        "operator": "GreaterThan",
        "threshold": 20,
        "locale": "en-us",
        "contactEmails": [
          "user@contoso.com"
        ],
        "contactRoles": [],
        "contactGroups": [],
        "thresholdType": "Actual"
      }
    }
  }
}

```

カルチャ コードによってサポートされる言語:

| カルチャ コード| 言語 |
| --- | --- |
| ja-JP | 英語 (米国) |
| ja-jp | 日本語 (日本) |
| zh-cn | 中国語 (簡体字、中国) |
| de-de | ドイツ語 (ドイツ) |
| es-es | スペイン語 (スペイン、インターナショナル) |
| fr-fr | フランス語 (フランス) |
| it-it | イタリア語 (イタリア) |
| ko-kr | 韓国語 (韓国) |
| pt-br | ポルトガル語 (ブラジル) |
| ru-ru | ロシア語 (ロシア) |
| zh-tw | 中国語 (繁体字、台湾) |
| cs-cz | チェコ語 (チェコ共和国) |
| pl-pl | ポーランド語 (ポーランド) |
| tr-tr | トルコ語 (トルコ) |
| da-dk | デンマーク語 (デンマーク) |
| dn-gb | 英語 (イギリス) |
| hu-hu | ハンガリー語 (ハンガリー) |
| nb-bo | ノルウェー語ブークモール (ノルウェー) |
| nl-nl | オランダ語 (オランダ) |
| pt-pt | ポルトガル語 (ポルトガル) |
| sv-se | スウェーデン語 (スウェーデン) |

### <a name="common-budgets-api-configurations"></a>Budgets API の一般的な構成

Azure 環境で予算を構成する方法は多数あります。 まず、実際のシナリオを検討してから、それを実現する構成オプションを特定します。 次のオプションを確認します。

- **時間グレイン** - 予算でコストの計上と評価に使用する定期的な期間を表します。 最も一般的なオプションは、Monthly、Quarterly、Annual です。
- **期間** - 予算の有効期間を表します。 予算は、有効な間だけアクティブに監視され、アラートが通知されます。
- **通知**
  - 連絡先のメール - 予算でコストが計上され、予算が定義済みのしきい値を超えると、このメール アドレスにアラートが送信されます。
  - 連絡先のロール - このオプションでは、特定のスコープで一致する Azure ロールが割り当てられているすべてのユーザーに、メール アラートが送信されます。 たとえば、サブスクリプション所有者は、サブスクリプション スコープで作成された予算のアラートを受け取ることができます。
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

この API は、1 日に 1 回だけ呼び出すことをお勧めします。 Cost Management データは、4 時間ごとに Azure リソース プロバイダーから新しい使用状況データを受け取って更新されます。 呼び出す回数を増やしても、提供されるデータが増えるわけではありません。 代わりに、負荷が大きくなります。 データが変更される頻度と、データ待機時間の処理方法の詳細については、「[Cost Management のデータを理解する](understand-cost-mgt-data.md)」を参照してください。

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>エラー コード 429 - 呼び出し回数がレート制限を超えた場合

Cost Management サブスクリプションをお持ちのすべてのユーザーに対して一貫したエクスペリエンスを実現するために、Cost Management API にはレート制限があります。 この制限に達すると、HTTP 状態コード `429: Too many requests` が返されます。 API の現在のスループット制限は、次のとおりです。

- 1 分あたり 30 回の呼び出し - スコープあたりのユーザーあたり、またはアプリケーションあたりの回数です。
- 1 分あたり 200 回の呼び出し - テナントあたりのユーザーあたり、またはアプリケーションあたりの回数です。

## <a name="next-steps"></a>次のステップ

- [Power BI テンプレート アプリを使用して Azure のコストを分析する](./analyze-cost-data-azure-cost-management-power-bi-template-app.md)。
- エクスポートを使用して、[データをエクスポートし、管理する](./tutorial-export-acm-data.md)。
- [Usage Details API](/rest/api/consumption/usageDetails) の詳細情報