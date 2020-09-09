---
title: 統合されたソリューションの構築
description: Synapse SQL プールと統合されるソリューション ツールとパートナー。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 16cfdfb475ce21ed4b51dc9140e59df701363f27
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85201006"
---
# <a name="integrate-other-services-with-a-synapse-sql-pool-data-warehouse"></a>他のサービスを Synapse SQL プール (データ ウェアハウス) と統合する

Azure Synapse Analytics 内の SQL プール機能を使用すると、ユーザーは Azure の他の多くのサービスと統合することができます。 Synapse SQL を使用すると、SQL プール リソースを使ってデータ ウェアハウスを作成できます。これにより、次のようないくつかの追加サービスを利用できます。

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Azure 全体の統合サービスの詳細については、[統合パートナー](sql-data-warehouse-partner-data-integration.md)に関する記事をご確認ください。

## <a name="power-bi"></a>Power BI

Power BI との統合によって、データ ウェアハウスのコンピューティング パワーを、Power BI の動的なレポート作成や視覚化と組み合わせることができます。 現在、Power BI との統合には次のものが含まれています。

* **Direct Connect**:SQL プールを使用してプロビジョニングされたデータ ウェアハウスに対する論理的プッシュダウンを備えた、さらに高度な接続です。 プッシュダウンにより、分析の速度と規模を高めることができます。
* **Power BI で開く**:[Power BI で開く] ボタンにより、簡単に接続できるように Power BI にインスタンス情報が渡されます。

詳しくは、[Power BI との統合](sql-data-warehouse-get-started-visualize-with-power-bi.md)に関する記事、または[Power BI のドキュメント](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/)をご覧ください。

## <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory は、抽出と読み込みの複雑なパイプラインを作成するための管理されたプラットフォームを提供します。 SQL プールと Azure Data Factory の統合には、以下が含まれます。

* **ストアド プロシージャ**:ストアド プロシージャの実行を調整します。
* **コピー**:ADF を使用して SQL プールにデータを移動します。 この操作では、内部で ADF の標準的なデータ移行メカニズムまたは PolyBase を使用できます。

詳しくは、[Azure Data Factory との統合](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)に関する記事をご覧ください。

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning は、フル マネージドの分析サービスで、大規模な予測ツール セットを使用する複雑なモデルを作成できます。 SQL プールは、これらのモデルのソースと変換先の両方としてサポートされており、次の機能があります。

* **データの読み取り**:T-SQL を使用して、SQL プールに対して大規模にモデルを駆動します。
* **データの書き込み**:任意のモデルから SQL プールに変更を書き戻します。

詳しくは、[Azure Machine Learning との統合](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)に関する記事をご覧ください。

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

Azure Stream Analytics は、Azure Event Hub で生成されたイベント データの処理と使用のための、複雑でフル マネージドのインフラストラクチャです。  SQL プールとの統合により、ストリーミング データを効率的に処理でき、さらに、関連するデータと並行して格納できることにより、より深く高度な分析が可能になります。  

* **ジョブの出力**:Stream Analytics ジョブから SQL プールに出力を直接送信します。

詳しくは、[Azure Stream Analytics との統合](sql-data-warehouse-integrate-azure-stream-analytics.md)に関する記事をご覧ください。
