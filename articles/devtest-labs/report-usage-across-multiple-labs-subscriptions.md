---
title: 複数のラボとサブスクリプションにわたる Azure DevTest Labs の使用状況
description: 複数のラボとサブスクリプションにわたって Azure DevTest Labs の使用状況を報告する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1e4d1f0abb5596c7fd9d22740bf052827c2ca666
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "102452647"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>複数のラボとサブスクリプションにわたって Azure DevTest Labs の使用状況を報告する

ほとんどの大規模な組織では、使用状況の傾向や外れ値を視覚化することで、リソースの使用状況をより効果的に追跡したいと考えています。 ラボの所有者またはマネージャーは、リソースの使用状況に基づいて、ラボをカスタマイズして[リソースの使用状況とコストを向上させる](../cost-management-billing/cost-management-billing-overview.md)ことができます。 Azure DevTest Labs では、ラボごとのリソース使用状況をダウンロードし、使用パターンをより詳しく調べることができます。 これらの使用パターンは、変化を特定し、効率を向上させるために役立ちます。 ほとんどの企業では、個々のラボの使用状況と、[複数のラボとサブスクリプション](/azure/architecture/cloud-adoption/decision-guides/subscriptions/)にわたる全体的な使用状況の両方を求めています。 

この記事では、複数のラボとサブスクリプションにわたるリソース使用状況の情報を処理する方法について説明します。

![レポートの使用](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>個々のラボの使用状況

このセクションでは、1 つのラボのリソース使用状況をエクスポートする方法について説明します。

DevTest Labs のリソース使用率をエクスポートする前に、Azure Storage アカウントを設定し、使用状況データを含むさまざまなファイルを格納できるようにしておく必要があります。 データのエクスポートを実行するには、次の 2 つの一般的な方法があります。

* [DevTest Labs REST API](/rest/api/dtl/labs/exportresourceusage) 
* アクション `exportResourceUsage`、ラボ リソース ID、および必要なパラメーターを指定した PowerShell Az.Resource モジュール [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction)。 

    [個人データのエクスポートまたは削除](personal-data-delete-export.md)に関する記事には、サンプル PowerShell スクリプトとエクスポートされるデータに関する詳細情報が記載されています。 

    > [!NOTE]
    > 日付パラメーターにはタイム スタンプが含まれないので、ラボが配置されているタイム ゾーンに基づいて、午前 0 時からのすべてのデータが含まれます。

エクスポートが完了すると、さまざまなリソース情報を持つ複数の CSV ファイルが BLOB ストレージに格納されます。
  
現在、次の 2 つの CSV ファイルがあります。

* *virtualmachines.csv* - ラボ内の仮想マシンに関する情報が含まれています。
* *disks.csv* - ラボ内のさまざまなディスクに関する情報が含まれています 

これらのファイルは、ラボ名、ラボの一意の ID、実行日に従い、すべて、またはエクスポート要求に基づく開始日のいずれかで、*labresourceusage* BLOB コンテナーに格納されます。 BLOB 構造の例を次に示します。

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>すべてのラボの使用状況をエクスポートする

複数のラボの使用状況情報をエクスポートするには、次の使用を検討します。 

* [Azure Functions](../azure-functions/index.yml)。PowerShell などの多くの言語で使用できます。 
* [Azure Automation Runbook](../automation/index.yml)。PowerShell、Python、またはカスタム グラフィカル デザイナーを使用してエクスポート コードを記述します。

これらのテクノロジを使用して、特定の日時にすべてのラボで個別のラボ エクスポートを実行できます。 

Azure 関数では、長期的なストレージにデータをプッシュする必要があります。 複数のラボのデータをエクスポートする場合、エクスポートには時間がかかることがあります。 パフォーマンスを向上させ、情報の重複の可能性を減らすために、各ラボを並列して実行することをお勧めします。 並列処理を実行するには、Azure Functions を非同期に実行します。 また、Azure Functions に用意されているタイマー トリガーを利用します。

## <a name="using-a-long-term-storage"></a>長期的なストレージの使用

長期的なストレージでは、さまざまなラボからのエクスポート情報が 1 つのデータ ソースに統合されます。 長期的なストレージを使用するもう 1 つの利点は、ストレージ アカウントからファイルを削除して重複とコストを削減できることです。 

次の例のように、長期的なストレージを使用して任意のテキスト操作を行うことができます。 

* フレンドリ名の追加
* 複合グループの作成
* データの集計。

いくつかの一般的なストレージ ソリューションとして、[SQL Server](https://azure.microsoft.com/services/sql-database/)、[Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)、および [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) があります。 どの長期的なストレージ ソリューションを選択するかは、優先事項によって変わります。 データを視覚化するときに、相互作用の可用性に関して提供される機能に応じて、ツールを選択することを検討してください。

## <a name="visualizing-data-and-gathering-insights"></a>データの視覚化と分析情報の収集

任意のデータ視覚化ツールを使用して長期的なストレージに接続し、使用状況データを表示し、分析情報を収集して、使用効率を確認します。 たとえば、[Power BI](/power-bi/power-bi-overview) を使用すると、使用状況データを整理して表示できます。 

[Azure Data Factory](https://azure.microsoft.com/services/data-factory/) を使用すると、1 つの場所のインターフェイス内でリソースの作成、リンク、管理を行うことができます。 より高度な制御が必要な場合は、個々のリソースを 1 つのリソース グループ内に作成し、Data Factory サービスとは別に管理することができます。  

## <a name="next-steps"></a>次の手順

システムを設定し、データを長期的なストレージに移行したら、次の手順では、データで回答する必要がある質問について説明します。 次に例を示します。 

-   VM サイズの使用量はどのくらいですか。

    ユーザーはハイ パフォーマンス (高コスト) の VM サイズを選択していますか。
-   どの Marketplace イメージが使用されていますか。

    カスタム イメージは最も一般的な VM ベースであり、[Shared Image Gallery](../virtual-machines/shared-image-galleries.md) や [Image ファクトリ](image-factory-create.md)などの共通のイメージ ストアを構築する必要があります。
-   使用されているカスタム イメージと使用されていないカスタム イメージ。