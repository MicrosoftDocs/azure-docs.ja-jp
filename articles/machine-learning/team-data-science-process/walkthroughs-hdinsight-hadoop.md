---
title: Hive を用いた Azure HDInsight Hadoop での分析 - Team Data Science Process
description: Azure HDInsight Hadoop で Hive を使用して予測分析を行う Team Data Science Process の例を紹介します。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f72ea6ed5f0eec076d181ef56c99c4f1308a7741
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "75864164"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>Azure 上の Hive を使用した HDInsight Hadoop データ サイエンスのチュートリアル 

ここでは、Hive と HDInsight Hadoop クラスターを使って予測分析を行うチュートリアルを紹介します。 Team Data Science Process で概説された手順を踏襲しています。 Team Data Science Process の概要については、[データ サイエンス プロセス](overview.md)に関するページを参照してください。 Azure HDInsight の概要については、「[Azure HDInsight、Hadoop テクノロジ スタック、Hadoop クラスターの概要](../../hdinsight/hadoop/apache-hadoop-introduction.md)」を参照してください。

Team Data Science Process を実行するデータ サイエンスのチュートリアルは他にも存在し、使用する **プラットフォーム** ごとにグループ化されています。 これらの例の箇条書きについては、[Team Data Science Process を実行するチュートリアル](walkthroughs.md)に関するページをご覧ください。


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Hive と HDInsight Hadoop を使ってタクシーのチップを予測する

[HDInsight Hadoop クラスター](hive-walkthrough.md)のチュートリアルでは、NYC タクシーのデータを使って次の予測を行っています。 

- チップが支払われるかどうか 
- チップの金額の分布

このシナリオは、[Azure HDInsight Hadoop クラスター](https://azure.microsoft.com/services/hdinsight/)と Hive を使って実行されています。 公開されている NYC タクシー乗車および料金データセットのデータを格納、調査し、特徴エンジニアリングを行う方法を身に付けることができます。 また、Azure Machine Learning を使ったモデルの構築とデプロイも行います。

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Hive と HDInsight Hadoop を使って広告クリックを予測する

[1 TB データセットでの Azure HDInsight Hadoop クラスターの使用](hive-criteo-walkthrough.md)に関するチュートリアルでは、公開されている [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) のクリック データセットを使用して、チップが支払われるかどうかと、想定される金額を予測します。 このシナリオでは、[Azure HDInsight Hadoop クラスター](https://azure.microsoft.com/services/hdinsight/)と Hive を使って、データの格納、調査、特徴エンジニアリング、ダウン サンプリングが実行されています。 ユーザーが広告をクリックするかどうかを予測する二項分類モデルの構築、トレーニング、スコア付けを、Azure Machine Learning を使って行います。 そのモデルの 1 つを Web サービスとして公開する方法が、チュートリアルの最後に紹介されています。


## <a name="next-steps"></a>次のステップ

Team Data Science Process を構成する主な要素については、[Team Data Science Process の概要](overview.md)に関するページを参照してください。

データ サイエンス プロジェクトの構築に使用できる Team Data Science Process のライフサイクルについては、「[Team Data Science Process ライフサイクル](lifecycle.md)」を参照してください。 このライフサイクルは、プロジェクトを実行する際に、その開始から終了までにわたって進められる通常のステップを大まかにまとめたものです。 

