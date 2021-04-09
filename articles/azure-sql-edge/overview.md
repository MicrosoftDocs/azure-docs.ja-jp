---
title: Azure SQL Edge とは
description: Azure SQL Edge の詳細
keywords: SQL Edge の紹介, SQL Edge とは, SQL Edge の概要
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a74e12a5e9bdd2dfdbc6ac07b66798e517f6f426
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395072"
---
# <a name="what-is-azure-sql-edge"></a>Azure SQL Edge とは

Azure SQL Edge は、IoT および IoT Edge のデプロイ向けに最適化されたリレーショナル データベース エンジンです。 これは、IoT のアプリケーションおよびソリューションに向けて、パフォーマンスの高いデータ ストレージと処理レイヤーを作成する機能を提供します。 Azure SQL Edge は、JSON、グラフ、時系列データなどのリレーショナルおよび非リレーショナル データのストリーミング、処理、および分析を行う機能を提供します。これにより、さまざまな最新の IoT アプリケーションに適した選択肢となっています。

Azure SQL Edge は、業界最高水準のパフォーマンス、セキュリティ、クエリ処理機能を提供する最新バージョンの [SQL Server データベース エンジン](/sql/sql-server/sql-server-technical-documentation)に基づいて構築されています。 Azure SQL Edge は [SQL Server](/sql/sql-server/sql-server-technical-documentation) や [Azure SQL](../azure-sql/index.yml) と同じエンジンに基づいて構築されているため、同じ Transact-SQL (T-SQL) プログラミング サーフェス領域が提供されます。これにより、アプリケーションまたはソリューションの開発がより簡単かつ迅速になると同時に、IoT Edge デバイス、データ センター、クラウドの間のアプリケーションの移植が簡単になります。

チャネル 9 上の「Azure SQL Edge とは」のビデオ:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/What-is-Azure-SQL-Edge/player]

## <a name="deployment-models"></a>デプロイ モデル

Azure SQL Edge では、2 つのデプロイ モードがサポートされます。

- Azure IoT Edge を使用して接続されたデプロイ:Azure SQL Edge は Azure Marketplace で提供されており、[Azure IoT Edge](../iot-edge/about-iot-edge.md) のモジュールとしてデプロイできます。 詳細については、[Azure SQL Edge のデプロイ](deploy-portal.md)に関するページを参照してください。<br>

![SQL Edge の概要図](media/overview/overview.png)

- 切断されたデプロイ:Azure SQL Edge コンテナー イメージは Docker Hub からプルし、スタンドアロンの Docker コンテナーとして、または Kubernetes クラスター上にデプロイできます。 詳細については、[Docker を使用した Azure SQL Edge のデプロイ](disconnected-deployment.md)に関するページおよび [Kubernetes での Azure SQL Edge コンテナーのデプロイ](deploy-kubernetes.md)に関するページを参照してください。

## <a name="editions-of-sql-edge"></a>SQL Edge のエディション

SQL Edge は、2 つの異なるエディション (ソフトウェア プラン) で提供されています。 これらのエディションの機能セットはまったく同じで、使用権限と、サポートされる CPU とメモリの量についてのみ違いがあります。

   |**プラン**  |**説明**  |
   |---------|---------|
   |Azure SQL Edge Developer  |  開発専用 SKU です。各 SQL Edge コンテナーは、最大 4 コアおよび 32 GB メモリまでに制限されます。  |
   |Azure SQL Edge    |  運用 SKU。各 SQL Edge コンテナーは、最大 8 コアおよび 64 GB メモリに制限されます。 |

## <a name="pricing-and-availability"></a>価格と可用性

Azure SQL Edge は現在、一般公開されています。 特定のリージョンでの価格と可用性の詳細については、[Azure SQL Edge](https://azure.microsoft.com/services/sql-edge/) に関するページを参照してください。

> [!IMPORTANT]
> Azure SQL Edge と SQL Server の機能面の違いや、Azure SQL Edge のさまざまなオプション間の違いについては、[Azure SQL Edge でサポートされる機能](features.md)に関するページを参照してください。

## <a name="streaming-capabilities"></a>ストリーミング機能  

Azure SQL Edge は、リアルタイム分析や複雑なイベント処理のための、組み込みのストリーミング機能を備えています。 ストリーミング機能は、[Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) と同じ構成要素および [Azure Stream Analytics on IoT Edge](../stream-analytics/stream-analytics-edge.md) と同様の機能を使用して構築されています。

Azure SQL Edge のストリーミング エンジンは、低待機時間、回復性、帯域幅の効率的な使用、およびコンプライアンスを実現するように設計されています。 

SQL Edge でのデータ ストリーミングの詳細については、[データ ストリーミング](stream-data.md)に関するページを参照してください。

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>機械学習および人工知能の機能

Azure SQL Edge は、オープン形式の ONNX (Open Neural Network Exchange) ランタイムを統合することにより、機械学習と分析の組み込み機能を提供します。これを使用すると、さまざまなフレームワーク間でディープ ラーニングとニューラル ネットワークのモデルを交換できます。 ONNX の詳細については、[こちら](https://onnx.ai/)をご覧ください。 ONNX ランタイムにより、お好みの言語やツールでモデルを柔軟に開発した後で、SQL Edge 内で実行するために ONNX 形式に変換できるようになります。 詳細については、[SQL Edge での ONNX を使用した機械学習と人工知能](onnx-overview.md)に関するページを参照してください。

## <a name="working-with-azure-sql-edge"></a>Azure SQL Edge の利用

Azure SQL Edge は、アプリケーションの開発と管理をより簡単かつ生産的にします。 ユーザーは、使い慣れたツールやスキルをすべて使用して、IoT Edge のニーズに応じた優れたアプリとソリューションを構築できます。 ユーザーは次のようなツールを使用して、SQL Edge での開発を行うことができます。

- [Azure portal](https://portal.azure.com/): すべての Azure サービスを管理するための Web ベースのアプリケーションです。
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/): SQL Server から SQL Database まで、あらゆる SQL インフラストラクチャを管理するための、無料でダウンロードできるクライアント アプリケーションです。
- [Visual Studio の SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt/): SQL Server リレーショナル データベース、SQL データベース、Integration Services パッケージ、Analysis Services データ モデル、および Reporting Services レポートを開発するための、無料でダウンロードできるクライアント アプリケーションです。
- [Azure Data Studio](/sql/azure-data-studio/what-is/): Windows、macOS、Linux 上で Microsoft ファミリのオンプレミスおよびクラウド データ プラットフォームを使用するデータ プロフェッショナルに向けた、無料でダウンロードできるクロス プラットフォーム データベース ツールです。
- [Visual Studio Code](https://code.visualstudio.com/docs): Windows、macOS、Linux 向けの無料でダウンロードできるオープンソースのコード エディターです。 Microsoft SQL Server、Azure SQL Database、Azure Synapse Analytics にクエリを実行するための [mssql 拡張機能](https://aka.ms/mssql-marketplace)を含む拡張機能をサポートします。


## <a name="next-steps"></a>次のステップ

- [Azure portal を使用した SQL Edge のデプロイ](deploy-portal.md)
- [SQL Edge での機械学習と人工知能](onnx-overview.md)
- [SQL Edge でのエンドツーエンドの IoT ソリューションの構築](tutorial-deploy-azure-resources.md)