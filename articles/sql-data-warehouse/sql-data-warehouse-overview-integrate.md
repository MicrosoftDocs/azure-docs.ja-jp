---
title: "SQL Data Warehouse を使用した統合ソリューションの作成 | Microsoft Docs"
description: "SQL Data Warehouse と統合されたソリューションを持つツールとパートナー。 "
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: e2dc8f3f-10e3-4589-a4e2-50c67dfcf67f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a4de014f80bb7acfc91fb0c2f4c36fa659463db4


---
# <a name="leverage-other-services-with-sql-data-warehouse"></a>SQL Data Warehouse と他のサービスの併用
SQL Data Warehouse では、コア機能に加えて Azure 内の他のサービスの多くを並行して活用できます。  具体的には、現在のところ、次のサービスと密接に統合するための措置を講じています。

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

今後、Azure エコシステムでさらに多くのサービスと接続できるようになる予定です。

## <a name="power-bi"></a>Power BI
Power BI との統合によって、SQL Data Warehouse のコンピューティング パワーを、Power BI の動的なレポート作成や視覚化と併せて活用することができます。 現在、Power BI との統合には次のものが含まれています。

* **Direct Connect**: SQL Data Warehouse に対する論理的プッシュダウンを備えた、さらに高度な接続です。  これにより、分析の速度と規模を高めることができます。
* **Power BI で開く**: ［Power BI で開く］ ボタンは、Power BI にインスタンス情報を渡し、よりシームレスな接続を可能にします。

詳細については、「[SQL Data Warehouse での Power BI の使用](sql-data-warehouse-integrate-power-bi.md)」または「[Power BI documentation (Power BI ドキュメント)](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx)」をご覧ください。

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory は、抽出 - 読み込みの複雑なパイプラインを作成するための管理されたプラットフォームを提供します。  SQL Data Warehouse と Azure Data Factory の統合には、次が含まれます。

* **ストアド プロシージャ**: SQL Data Warehouse でのストアド プロシージャの実行を調整します。
* **コピー**: ADF を使用して、SQL Data Warehouse にデータを移動します。  この操作では、内部で ADF の標準的なデータ移行メカニズムまたは PolyBase を使用できます。 

詳細については、「[SQL Data Warehouse での Azure Data Factory の使用](sql-data-warehouse-integrate-azure-data-factory.md)」または「[Data Factory のドキュメント](https://azure.microsoft.com/documentation/services/data-factory/)」をご覧ください。

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning は、完全に管理されたを分析サービスで、大規模な予測ツール セットを活用する複雑なモデルを作成できます。  SQL Data Warehouse は、次の機能により、これらのモデルのソースと変換先の両方としてサポートされています。

* **データの読み取り** : T-SQL を使用して、SQL Data Warehouse に対して大規模にモデルを駆動します。
* **データの書き込み** : 任意のモデルから SQL Data Warehouse に変更を戻します。

詳細については、「[SQL Data Warehouse での Azure Machine Learning の使用](sql-data-warehouse-integrate-azure-machine-learning.md)」または「[Machine Learning documentation (Machine Learning ドキュメント)](https://azure.microsoft.com/services/machine-learning/)」をご覧ください。

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics は、Azure Event Hub で生成されたイベント データの処理と使用のための、複雑で完全に管理されたインフラストラクチャです。  SQL Data Warehouse との統合により、ストリーミング データを効率的に処理でき、さらに、関連するデータと並行して格納できることにより、より深く高度な分析が可能になります。  

* **[ジョブの出力** : Stream Analytics ジョブから SQL Data Warehouse に直接出力を送信します。

詳細については、「[SQL Data Warehouse での Azure Stream Analytics の使用](sql-data-warehouse-integrate-azure-stream-analytics.md)」または「[Stream Analytics のドキュメント](https://azure.microsoft.com/documentation/services/stream-analytics/)」をご覧ください。

<!--Image references-->

<!--Article references-->
[開発の概要]: sql-data-warehouse-overview-develop/

[Azure Data Factory]: sql-data-warehouse-integrate-azure-data-factory.md
[Azure Machine Learning]: sql-data-warehouse-integrate-azure-machine-learning.md
[Azure Stream Analytics]: sql-data-warehouse-integrate-azure-stream-analytics.md
[Power BI]: sql-data-warehouse-integrate-power-bi.md
[パートナー]: sql-data-warehouse-partner-business-intelligence.md

<!--MSDN references-->

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


