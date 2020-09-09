---
title: Azure Data Factory のコストを計画および管理する
description: この記事では、Azure Data Factory のコストを計画および管理できる方法について説明します
documentationcenter: ''
author: shirleywangmsft
ms.author: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: ca76563475dfbf8d35595c1de3cdee37f80e3ce1
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83690238"
---
# <a name="plan-and-manage-costs-for-azure-data-factory"></a>Azure Data Factory のコストを計画および管理する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory は、クラウド規模用に構築されたサーバーレスのエラスティック データ統合サービスです。  これは、ピーク時の負荷に対して計画する必要がある固定サイズのコンピューティングがないことを意味します。代わりに、必要に応じて操作ごとに割り当てるリソースの量を指定します。これにより、非常にスケーラブルに ETL プロセスを設計できます。 さらに、ADF は使用量ベースのプランに対して課金されます。これは、使用した分だけを支払うことを意味します。

この記事では、Azure Data Factory のコストを計画および管理できる方法について説明します。

*   まず、ETL プロジェクトの開始時に概念実証を行い、パイプラインごとの使用量と料金計算ツールを組み合わせて、コストを見積もることができます。
*   パイプラインを運用環境にデプロイしたら、コスト管理機能を使用して、予算を設定し、コストを監視します。 また、予想コストを確認し、支出の傾向を特定できます。
*   さらに、パイプラインごとの使用量とアクティビティごとの使用量の情報を表示して、どのパイプラインとどのアクティビティが最もコストがかかるかを把握し、コスト削減候補を特定できます。

## <a name="estimate-costs-using-pipeline-and-activity-run-consumption-and-pricing-calculator"></a>パイプラインおよびアクティビティの実行使用量および料金計算ツールを使用してコストを見積もります

[ADF 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=data-factory)を使用して、Azure Data Factory で ETL ワークロードを実行するコストを見積もることができます。  計算ツールを使用するには、アクティビティの実行回数、データ統合単位時間数、Data Flow に使用されるコンピューティングの種類、コア数、インスタンス数、実行期間などの詳細を入力する必要があります。

料金計算ツールについてよくある質問の 1 つが、どの値を入力として使用する必要があるかというものです。  概念実証フェーズでは、サンプル データセットを使用した試験を行って、さまざまな ADF メーターの使用量を把握できます。  続いて、サンプル データセットの使用量に基づいて、完全なデータセットおよび運用化スケジュールの使用量を算定できます。

> [!NOTE]
> 次の例で使用されている価格は仮定的なものであり、実際の価格を暗示することを意図するものではありません。

たとえば、毎日 1 TB のデータを AWS S3 から Azure Data Lake Gen2 に移動する必要があるとします。  100 GB のデータを移動して、データ インジェストのスループットを測定し、それに応じて課金される使用料金を測定するという概念実証を実行できます。

コピー アクティビティの実行の詳細のサンプルを次に示します (実際のマイレージは、特定のデータセットの形状、ネットワーク速度、S3 アカウントの送信制限、ADLS Gen2 の受信制限、その他の要因によって異なります)。

![S3 コピーの実行](media/plan-manage-costs/s3-copy-run-details.png)

[パイプライン実行レベルでの使用量の監視](#monitor-consumption-at-pipeline-run-level)を利用することで、対応するデータ移動メーター使用量を確認できます。

![S3 コピー パイプラインの使用量](media/plan-manage-costs/s3-copy-pipeline-consumption.png)

そのため、1 日に 1 TB を移動するために必要な DIU 時間の 1 か月の合計は次のようになります。

1.2667 (DIU 時間数) * (1 TB / 100 GB) * 30 (1 か月の日数) = 380 DIU 時間

次に、30 回のアクティビティの実行と 380 DIU 時間を、ADF 料金計算ツールに入力し、月額の見積もりを得ることができます。

![S3 コピー料金計算ツール](media/plan-manage-costs/s3-copy-pricing-calculator.png)

## <a name="use-budgets-and-cost-alerts"></a>予算とコストのアラートを使用する

[予算](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets)を作成してコストを管理し、異常な支出や超過出費のリスクについて関係者に自動的に通知するアラートを作成できます。  アラートは、予算とコストのしきい値と比較した支出に基づきます。  予算を作成する場合は、サブスクリプションレベルで作成することも、リソース ID やメーター名などのフィルターを追加することで細分性を低くして作成することもできます。  ただし、ファクトリ内の個々のパイプラインの予算は作成できません。

## <a name="monitor-costs-at-factory-level"></a>ファクトリ レベルでコストを監視する

Azure Data Factory の使用を開始すると、Azure portal の [[コスト分析]](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis) ウィンドウに、発生したコストが表示されます。

1. [[コスト分析]](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis) を表示するには、 **[Cost Management + Billing]\(コストの管理と請求\)** ウィンドウを開き、メニューから **[Cost management]\(コスト管理\)** を選択し、 **[コスト分析を開く]** を選択します。
2. 既定のビューには、当月の累積コストが表示されます。  別の時間範囲と、日単位や月単位などのさまざまな細分性に切り替えることができます。
3. Azure Data Factory など、1 つのサービスのコストを絞り込むには、 **[フィルターの追加]** を選択してから、 **[サービス名]** を選択します。  続いて、一覧から **[Azure Data Factory V2]** を選択します。
4. 特定のファクトリ インスタンスおよび特定の ADF メーター細分性のコストを分析する追加のフィルターを追加できます。

   ![コスト分析](media/plan-manage-costs/cost-analysis.png)

## <a name="monitor-consumption-at-pipeline-run-level"></a>パイプライン実行レベルでの使用量を監視する

パイプラインに含まれているアクティビティの種類、移動および変換しているデータの量、変換の複雑さに応じて、パイプラインを実行すると Azure Data Factory でのさまざまな課金メーターが動作します。

個々のパイプラインの実行に関するさまざまなメーターの使用量は、Azure Data Factory のユーザー エクスペリエンスで確認できます。 監視エクスペリエンスを開くには、[Azure portal](https://portal.azure.com/) の [データ ファクトリ] ブレードで **[監視と管理]** タイルを選択します。 ADF UX を既に使用している場合は、左側のサイドバーにある **[監視]** アイコンをクリックします。 既定の監視ビューはパイプラインの実行の一覧です。

パイプライン名の横にある **[消費]** ボタンをクリックすると、パイプライン内のすべてのアクティビティに対して集計されたパイプライン実行の使用量を示すポップアップ ウィンドウが表示されます。

![パイプライン実行の使用量](media/plan-manage-costs/pipeline-run-consumption.png)

![パイプライン使用量の詳細](media/plan-manage-costs/pipeline-consumption-details.png)

[pipeline run consumption]\(パイプラインの実行使用量\) ビューには、特定のパイプライン実行の ADF メーターごとに消費された量は表示されますが、課金される実際の料金は表示されません。課金される金額は、所有している Azure アカウントの種類と使用されている通貨の種類によって異なります。  サポートされているアカウントの種類の完全な一覧については、「[Understand Cost Management data (Cost Management データの概要)](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data)」を参照してください。

## <a name="monitor-consumption-at-activity-run-level"></a>アクティビティ実行レベルでの使用量を監視する
パイプライン実行レベルでの集計された使用量を把握した後、シナリオによっては、さらにドリルダウンして、パイプライン内で最もコストのかかるアクティビティを特定する必要があります。

アクティビティ実行レベルでの使用量を表示するには、データ ファクトリの **[作成と監視]** UI に移動します。 パイプライン実行の一覧が表示される **[監視]** タブから、**パイプライン名**のリンクをクリックして、パイプライン実行でのアクティビティの実行の一覧にアクセスします。  アクティビティ名の横にある **[出力]** ボタンをクリックし、JSON 出力で **billableDuration** プロパティを探します。

コピー アクティビティの実行のサンプルを次に示します。

![出力をコピーする](media/plan-manage-costs/copy-output.png)

マッピング データ フロー アクティビティの実行のサンプルを次に示します。

![データフローの出力](media/plan-manage-costs/dataflow-output.png)

## <a name="next-steps"></a>次のステップ

Azure Data Factory での課金のしくみの詳細については、以下の記事をご覧ください。

- [Azure Data Factory の価格ページ](https://azure.microsoft.com/pricing/details/data-factory/ssis/)
- [Azure Data Factory を実例から理解する](https://docs.microsoft.com/azure/data-factory/pricing-concepts)
- [Azure Data Factory の領域計算ツール](https://azure.microsoft.com/pricing/calculator/?service=data-factory)
