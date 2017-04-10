---
title: "車両の状態と運転習慣の予測 - Azure | Microsoft Docs"
description: "Cortana Intelligence の機能を使用して、車両の状態と運転の習慣に関するリアルタイムの予測的な洞察を得ます。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 09fad60b-2f48-488b-8a7e-47d1f969ec6f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: f497366f8e66ba79b0e5978fde54d0b33048aa8d
ms.openlocfilehash: 3467c5549381f1354987fead424646afe847739c
ms.lasthandoff: 01/24/2017


---
# <a name="vehicle-telemetry-analytics-solution-playbook"></a>車両テレメトリ分析ソリューション プレイブック
この **メニュー** は、このプレイブック内の各章にリンクされています。 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## <a name="overview"></a>Overview
スーパー コンピューターをラボからガレージに持ち出しました。 これらの最先端の自動車には、毎秒数百万というイベントを追跡、監視するためのおびただしい数のセンサーが搭載されています。 2020 年までには、これらの車の大半がインターネットに接続されていることが予測されます。 この豊富なデータを利用して、向上された安全性、信頼性、そしてより良い運転体験を実現することを想像してみてください。 Microsoft は Cortana Intelligence により、この夢を現実のものにしました。

Microsoft の Cortana Intelligence は、データをインテリジェントなアクションに変換できる、完全管理型ビッグ データおよび高度な分析一式です。 ここでは、Cortana Intelligence 車両テレメトリ分析ソリューション テンプレートを紹介します。 このソリューションでは、車の販売代理店、自動車メーカー、保険会社が Cortana Intelligence の機能を使用して、車両の状態や運転の習慣などに関するリアルタイムの予測分析をどのように実現できるかについて説明します。 

ソリューションを [ラムダ アーキテクチャ パターン](https://en.wikipedia.org/wiki/Lambda_architecture) として実装することで、リアルタイムのバッチ処理において Cortana Intelligence プラットフォームが持つすべての可能性を示します。 このソリューションでは次のことが可能です。 

* 車両テレメトリ シミュレーターを提供する
* Event Hubs を活用して、数百万におよぶシミュレートされた車両テレメトリ イベントを Azure に取り込む 
* Stream Analytics を使用して、車両の状態に関するリアルタイム情報を取得する
* そのデータを長期的なストレージで維持して、より豊富な一括分析を実行する。 
* リアルタイムのバッチ処理での異常検出に Machine Learning を利用して、予測分析を実行する。
* HDInsight で大規模にデータを変換する。また、Data Factory を利用して、オーケストレーション、スケジュール設定、リソース管理、およびバッチ処理のパイプラインの監視を処理する 
* Power BI を使用して、このソリューションに機能豊富なダッシュボードを提供し、リアルタイム データと予測分析を視覚化する

## <a name="architecture"></a>アーキテクチャ
![ソリューションのアーキテクチャ ダイアグラム](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)
*図 1 – 車両テレメトリ分析ソリューション アーキテクチャ*

このソリューションは、次の **Cortana Intelligence コンポーネント**を含み、エンド ツー エンドの統合を説明します。

* **Event Hubs** 数百万の車両テレメトリ イベントを Azure に取り込みます。
* **Stream Analytics** 車両の状態に関するリアルタイムの洞察を得て、より高度な一括分析のために長期的なストレージにそのデータを保持します。
* **Machine Learning** リアルタイムのバッチ処理での異常検出にこれを活用して予測分析を行います。
* **HDInsight** 大量のデータを変換します。
* **Data Factory** オーケストレーション、スケジュール設定、リソース管理、およびバッチ処理のパイプラインの監視を処理します。
* **Power BI** このソリューションに機能豊富なダッシュボードを提供し、リアルタイムなデータおよび予測分析を視覚化します。

このソリューションでは、2 つの異なる **データ ソース**にアクセスします。 

* **シミュレートされた車両の信号と診断**: 車両テレマティックス シミュレーターは診断情報、車両の状態に対応する信号、特定の時間における運転のパターンを出力します。 
* **車両カタログ**: モデルのマッピングに対する VIN を含む参照データセット。


