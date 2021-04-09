---
title: Synapse Studio で Apache Spark プールを監視する方法
description: Synapse Studio を使用して Apache Spark プールを監視する方法について説明します。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 8d95897e0c2d58b2a3955918be945800eed9ba56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96465597"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-pools"></a>Synapse Studio を使用して Apache Spark プールを監視する

Azure Synapse Analytics では、Spark を使用して、ワークスペース内の Spark プールでノートブック、ジョブ、その他の種類のアプリケーションを実行できます。

この記事では、Apache Spark プールを監視する方法について説明します。この方法により、さまざまなワークスペース ユーザーによって使用中の仮想コアの数を含め、プールの状態を監視することができます。

## <a name="access-spark-pools-list"></a>Spark プールの一覧にアクセスする

ワークスペースで Apache Spark プールの一覧を表示するには、最初に [Synapse Studio を開き](https://web.azuresynapse.net/)、ワークスペースを選択します。

![ワークスペースへのログイン](./media/common/login-workspace.png)

ワークスペースを開いたら、左側の **[監視]** セクションを選択します。

![[監視] ハブを選択する](./media/common/left-nav.png)

Apache Spark プールの一覧を表示するには、 **[Apache Spark プール]** を選択します。

 ![Apache Spark プールを選択します](./media/how-to-monitor-spark-pools/monitor-hub-nav-spark-pools.png)

## <a name="filter-your-spark-pools"></a>Spark プールをフィルター処理する

Spark プールの一覧をフィルター処理して、関心のあるものに絞ることができます。 画面の上部にあるフィルターを使用して、フィルター処理を行うフィールドを指定できます。

たとえば、ビューをフィルター処理して、"dataprep" という名前を含む Spark プールのみを表示することができます。

![サンプルのフィルター](./media/how-to-monitor-spark-pools/filter-example.png)

## <a name="view-details-about-a-specific-spark-pool"></a>特定の Spark プールに関する詳細を表示する

いずれかの Spark プールに関する詳細を表示するには、その Spark プールを選択して、詳細を表示します。

![Apache Spark プールの詳細](./media/how-to-monitor-spark-pools/spark-pool-details.png)

## <a name="next-steps"></a>次の手順

パイプラインの実行の監視に関する詳細については、[Synapse Studio でのパイプラインの実行の監視](how-to-monitor-pipeline-runs.md)に関する記事をご覧ください。 

Apache Spark アプリケーションの監視に関する詳細については、[Synapse Studio での Apache Spark アプリケーションの監視](how-to-monitor-spark-applications.md)に関する記事をご覧ください。
