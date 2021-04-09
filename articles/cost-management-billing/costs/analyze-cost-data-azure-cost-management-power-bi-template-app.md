---
title: Power BI アプリを使用して Azure のコストを分析する
description: この記事では、Azure Cost Management Power BI アプリをインストールして使用する方法について説明します。
author: bandersmsft
ms.author: banders
ms.date: 02/19/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: b08ff57f964ef7bc3712c930c222a10ed0f89ef4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045100"
---
# <a name="analyze-cost-with-the-azure-cost-management-power-bi-app-for-enterprise-agreements-ea"></a>Enterprise Agreement (EA) 用の Azure Cost Management Power BI アプリを使用してコストを分析する

この記事では、Azure Cost Management Power BI アプリをインストールして使用する方法について説明します。 このアプリは、Power BI で Azure のコストを分析および管理するのに役立ちます。 このアプリを使用して、コストや使用傾向を監視し、支出を削減するためのコスト最適化オプションを特定することができます。

Azure Cost Management Power BI アプリは現在、[マイクロソフト エンタープライズ契約](https://azure.microsoft.com/pricing/enterprise-agreement/)をお持ちのお客様のみサポートしています。

このアプリは、カスタマイズ性が制限されています。 独自のニーズに合わせてカスタマイズするために、既定のフィルターやビュー、視覚エフェクトを変更して拡張したい場合は、[Power BI Desktop の Azure Cost Management コネクタ](/power-bi/connect-data/desktop-connect-azure-cost-management)を使用してください。 Azure Cost Management コネクタを使用すると、他のソースからのデータを別途結合してレポートをカスタマイズし、ビジネス コスト全体を総合的に把握することができます。 このコネクタでは、Microsoft 顧客契約もサポートされます。

> [!NOTE]
> Power BI テンプレート アプリでは、PBIX ファイルのダウンロードをサポートしていません。

## <a name="prerequisites"></a>前提条件

- アプリをインストールして使用するためには、[Power BI Pro ライセンス](/power-bi/service-self-service-signup-for-power-bi)が必要です。
- データに接続するには、[エンタープライズ管理者](../manage/understand-ea-roles.md)アカウントを使用する必要があります エンタープライズ管理者 (読み取り専用) ロールがサポートされます。

## <a name="installation-steps"></a>インストール手順

アプリをインストールするには:

1. [Azure Cost Management Power BI アプリ](https://aka.ms/costmgmt/ACMApp)を開きます。
1. [Power BI AppSource] ページで、 **[今すぐ入手]** を選択します。
1. **[続行]** を選択して、使用条件とプライバシー ポリシーに同意します。
1. **[この Power BI アプリをインストールしますか]** ボックスで、 **[インストール]** を選択します。
1. 必要に応じて、ワークスペースを作成して **[続行]** を選択します。
1. インストールが完了すると、新しいアプリの準備ができたことを示す通知が表示されます。
1. インストールしたアプリを選択します。
1. [作業の開始] ページで、 **[データを接続]** を選択します。
    :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/connect-your-data.png" alt-text="[データを接続] リンクが強調表示されたスクリーンショット。" lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/connect-your-data.png" :::
1. 表示されるダイアログで、**BillingProfileIdOrEnrollmentNumber** の EA 登録番号を入力します。 取得するデータの月数を指定します。 既定の **[範囲]** 値である **[Enrollment Number]\(登録番号\)** のままにして、 **[次へ]** を選択します。  
    :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png" alt-text="E A 登録情報を入力する場所を示すスクリーンショット。" lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png" :::
1. 次のダイアログで Azure に接続し、データを取得します。 "*構成された既定値のままにして*"、 **[サインインして続行する]** を選択します。  
    :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit.png" alt-text="既定値が表示されている [Connect to Azure Cost Management App]\(Azure Cost Management アプリに接続する\) ダイアログ ボックスを示すスクリーンショット。" lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit.png" :::
1. 最後のインストール手順では、EA 登録に接続し、[エンタープライズ管理者](../manage/understand-ea-roles.md)アカウントが必要です。 すべて既定値のままにします。 **[サインインして接続する]** を選択します。  
    :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-auth.png" alt-text="接続に使用する既定値が表示されている [Connect to Azure Cost Management App]\(Azure Cost Management アプリに接続する\) ダイアログ ボックスを示すスクリーンショット。" lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-auth.png" :::
1. EA 登録で認証するよう求められます。 Power BI を使用して認証します。 認証が完了すると、Power BI のデータ更新が開始されます。
    > [!NOTE]
    > データ更新処理が完了するまでにかなりの時間がかかることがあります。 この長さは、指定された月数と同期に必要なデータの量によって異なります。

データの更新が完了したら、Azure Cost Management アプリを選択して、事前に作成されたレポートを表示します。

## <a name="reports-available-with-the-app"></a>アプリで使用可能なレポート

次のレポートをアプリで使用できます。

**[Getting Started]\(はじめに\)** - ドキュメントへの便利なリンクと、フィードバックを提供するリンクが用意されています。

**[アカウントの概要]** - このレポートには、次のような情報の月別の概要が表示されます。

- クレジット請求金額
- 新規購入
- Azure Marketplace の料金
- 超過分と合計料金

**[Usage by Subscriptions and Resource Groups]\(サブスクリプションとリソース グループ別の使用状況\)** - 時間の経過に伴うコストと、サブスクリプションとリソース グループ別のコストを示すグラフが表示されます。

**[Usage by Services]\(サービス別の使用状況\)** - MeterCategory による使用状況を時間の経過に沿って表示します。 使用状況データを追跡し、異常をドリル ダウンして、使用量の急激な上昇や低下を把握することができます。

**[Top 5 Usage drivers]\(上位 5 つの使用量ドライバー)** - このレポートには、上位 5 つの MeterCategory および対応する MeterName によるフィルター処理されたコストの概要が表示されます。

**[Windows Server AHB Usage]/(Windows Server AHB 使用量/)** - このレポートには、Azure ハイブリッド特典が有効になっている仮想マシンの数が表示されます。 また、仮想マシンによって使用されるコア/vCPU の数も表示されます。

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report-full.png" alt-text="Azure ハイブリッド特典の詳細なレポートを示すスクリーンショット。" lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report-full.png" :::

このレポートでは、ハイブリッド特典が **有効になっているが**、vCPU が 8 つ "_未満_" の Windows VM も識別されます。 また、vCPU が 8 つ "_以上_" あり、ハイブリッド特典が **有効になっていない** 場合も表示されます。 この情報は、ハイブリッド特典を最大限に活用するのに役立ちます。 最もコストのかかる仮想マシンに特典を適用して、削減可能なコストを最大化します。

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report.png" alt-text="Azure ハイブリッド特典レポートの vCPU が 8 つ未満およびハイブリッド特典が有効になっていない vCPU の領域を示すスクリーンショット。" lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report.png" :::

**[RI Chargeback]\(RI チャージバック\)** - このレポートは、リージョン、サブスクリプション、リソース グループ、またはリソースごとに、予約インスタンス (RI) 特典がどこでどの程度適用されるかを把握するのに役立ちます。 このレポートでは、償却された使用状況データを使用してビューが表示されます。

_chargetype_ にフィルターを適用して、RI 過小使用データを表示することができます。

償却されたデータについて詳しくは、「[Enterprise Agreement の予約のコストと使用状況を取得する](../reservations/understand-reserved-instance-usage-ea.md)」をご覧ください。

**[RI Saving]\(RI の節約\)** - このレポートには、サブスクリプション、リソース グループ、およびリソース レベルの予約によって生じた節約額が表示されます。 次の情報が表示されます。

- 予約によるコスト
- 使用に対して予約が適用されなかった場合のオンデマンドの推定コスト
- 予約によって生じたコスト削減

 このレポートでは、使用率が低い予約の無駄なコストが合計の節約額から差し引かれます。 この無駄は、予約がなければ発生しません。

償却された使用状況データを使用して、データに基づいて構築できます。

<a name="shared-recommendation"></a>
 **[VM RI Coverage (shared recommendation)]\(VM RI カバレッジ (共有の推奨事項)\)** - このレポートは、選択した期間のオンデマンドの VM 使用量と RI の VM 使用量に分割されます。 共有スコープでの VM RI の購入に関する推奨事項を示します。

レポートを使用するには、ドリルダウン フィルターを選択します。

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-drill-down2.png" alt-text="VM RI カバレッジ レポートのドリルダウンの選択オプションを示すスクリーンショット。" lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-drill-down2.png" :::

分析するリージョンを選択します。 次に、インスタンス サイズの柔軟性グループなどを選択します。

ドリルダウン レベルごとに、次のフィルターがレポートに適用されます。

- 右側にあるカバレッジ データは、オンデマンド レートを使用して課金される使用量と、予約によってカバーされる使用量を示すフィルターです。
- 推奨事項もフィルター処理されます。

推奨事項の表には、使用する VM のサイズに基づいた予約購入の推奨事項が示されます。

_[Normalized Size]\(正規化されたサイズ\)_ と _[Recommended Quantity Normalized]\(推奨される正規化数量\)_ の値は、インスタンス サイズの柔軟性グループの最小サイズに購入を正規化するのに役立ちます。 この情報は、インスタンス サイズの柔軟性グループのすべてのサイズに対して予約を 1 つのみ購入する場合に役立ちます。

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-recommendations.png" alt-text="RI の推奨事項レポートを示すスクリーンショット。" lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-recommendations.png" :::

**[VM RI Coverage (single recommendation)]\(VM RI カバレッジ (1 つの推奨事項)\)** - このレポートは、選択した期間のオンデマンドの VM 使用量と RI の VM 使用量に分割されます。 サブスクリプション スコープでの VM RI の購入に関する推奨事項を示します。

このレポートの使用方法の詳細については、「[VM RI カバレッジ (共有の推奨事項)](#shared-recommendation)」セクションを参照してください。

**[RI purchases]\(RI の購入\)** - このレポートには、指定した期間における RI の購入が表示されます。

**[Price sheet]\(価格シート\)** - このレポートには、課金アカウントまたは EA 登録に固有の価格の詳細な一覧が表示されます。

## <a name="troubleshoot-problems"></a>問題のトラブルシューティング

Power BI アプリに問題がある場合は、次のトラブルシューティング情報が役立つ場合があります。

### <a name="error-processing-the-data-in-the-dataset"></a>データセット内のデータの処理中のエラー

次のエラーが表示される場合があります。

```
There was an error when processing the data in the dataset.
Data source error: {"error":{"code":"ModelRefresh_ShortMessage_ProcessingError","pbi.error":{"code":"ModelRefresh_ShortMessage_ProcessingError","parameters":{},"details":[{"code":"Message","detail":{"type":1,"value":"We cannot convert the value \"Required Field: 'Enr...\" to type List."}}],"exceptionCulprit":1}}} Table: <TableName>.
```

`<TableName>` ではなくテーブル名が表示されます。

#### <a name="cause"></a>原因

`Enrollment Number` の既定の **[範囲]** 値が、Cost Management への接続で変更されました。

#### <a name="solution"></a>解決策

Cost Management に再接続し、 **[範囲]** 値を `Enrollment Number` に設定します。 組織の登録番号を入力するのではなく、次の図に示すように正確に「`Enrollment Number`」と入力します。

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number-troubleshoot.png" alt-text="登録番号の既定のテキストを変更してはいけないことを示すスクリーンショット。" lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number-troubleshoot.png" :::

### <a name="budgetamount-error"></a>BudgetAmount エラー

次のエラーが表示される場合があります。

```
Something went wrong
There was an error when processing the data in the dataset.
Please try again later or contact support. If you contact support, please provide these details.
Data source error: The 'budgetAmount' column does not exist in the rowset. Table: Budgets.
```

#### <a name="cause"></a>原因

このエラーは、基になるメタデータのバグが原因で発生します。 この問題が発生するのは、Azure portal 内の **[コスト管理] > [予算]** で使用できる予算がないためです。 バグ修正プログラムは、Power BI Desktop と Power BI サービスにデプロイするプロセスにあります。

#### <a name="solution"></a>解決策

- バグが修正されるまでは、Azure portal 内で課金アカウントまたは EA 登録レベルでテスト予算を追加することで、この問題を回避できます。 テスト予算により、Power BI との接続がブロック解除されます。 予算の作成方法の詳細については、「[チュートリアル: Azure の予算を作成して管理する](tutorial-acm-create-budgets.md)」を参照してください。

### <a name="invalid-credentials-for-azureblob-error"></a>AzureBlob の無効な資格情報エラー

次のエラーが表示される場合があります。

```
Failed to update data source credentials: The credentials provided for the AzureBlobs source are invalid.
```

#### <a name="cause"></a>原因

このエラーは、データ ソース接続の認証方法を変更した場合に発生します。

#### <a name="solution"></a>解決策

1. 対象のデータに接続します。
1. EA 登録と月数を入力した後、認証方法に既定値の **[匿名]** をそのまま使用し、プライバシー レベルの設定に **[なし]** を使用します。  
  :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit-troubleshoot.png" alt-text="[匿名] と [なし] の各値が入力されている [Connect to Azure Cost Management App]\(Azure Cost Management アプリに接続する\) ダイアログ ボックスを示すスクリーンショット。" lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit-troubleshoot.png" :::
1. 次のページで、認証方法に **[OAuth2]** を設定し、プライバシー レベルに **[なし]** を設定します。 次に、サインインしてお客様の登録で認証します。 この手順により、Power BI データの更新操作も開始されます。

## <a name="data-reference"></a>データ参照

次の情報は、アプリで使用できるデータの概要を示しています。 データ フィールドと値の詳細な情報を提供する API へのリンクも用意されています。

| **テーブル参照** | **説明** |
| --- | --- |
| **AutoFitComboMeter** | RI の推奨事項と使用量をインスタンス ファミリ グループの最小サイズに正規化するためにアプリに含まれるデータ。 |
| [**Balance summary**](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary#response) | Enterprise Agreement の残高の概要。 |
| [**Budgets**](/rest/api/consumption/budgets/get#definitions) | 既存の予算目標に対する実績のコストまたは使用量を表示するための予算の詳細。 |
| [**Pricesheets**](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet#see-also) | 指定された課金プロファイルまたは EA 登録に適用可能な測定レート。 |
| [**RI charges**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges#response) | 過去 24 か月間の予約インスタンスに関連する料金。 |
| [**RI recommendations (shared)**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response) | 過去 7 日間のすべてのサブスクリプション使用傾向に基づく予約インスタンス購入の推奨事項。 |
| [**RI recommendations (single)**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response-1) | 過去 7 日間の単一のサブスクリプション使用傾向に基づく予約インスタンス購入の推奨事項。 |
| [**RI usage details**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#response) | 過去 1 か月間の既存の予約インスタンスに関する消費量の詳細。 |
| [**RI usage summary**](/rest/api/consumption/reservationssummaries/list) | 日次の Azure の予約使用率。 |
| [**Usage details**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | EA 登録の指定された課金プロファイルでの消費量と推定料金の内訳。 |
| [**Usage details amortized**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | EA 登録の指定された課金プロファイルでの消費量と推定償却料金の内訳。 |

## <a name="next-steps"></a>次のステップ

データの構成、更新、レポートの共有、および追加のレポートのカスタマイズの詳細については、次の記事を参照してください。

- [スケジュールされた更新を構成する](/power-bi/refresh-scheduled-refresh)
- [Power BI ダッシュボードとレポートを仕事仲間や他のユーザーと共有する](/power-bi/service-share-dashboards)
- [Power BI サービスのレポートとダッシュボードを自分および他のユーザーがサブスクライブする](/power-bi/service-report-subscribe)
- [Power BI サービスから Power BI Desktop にレポートをダウンロードする](/power-bi/service-export-to-pbix)
- [Power BI サービスおよび Power BI Desktop でレポートを保存する](/power-bi/service-report-save)
- [データセットをインポートして Power BI サービスでレポートを作成する](/power-bi/service-report-create-new)