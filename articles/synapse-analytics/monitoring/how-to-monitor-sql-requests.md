---
title: Synapse Studio で SQL 要求を監視する方法
description: Synapse Studio を使用して SQL 要求を監視する方法について説明します。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 7296fec91decaf1bd80829f9f3a743a518fbb7a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96465623"
---
# <a name="use-synapse-studio-to-monitor-your-sql-requests"></a>Synapse Studio を使用して SQL 要求を監視する

Synapse Studio では、ワークスペース内の SQL プールで SQL スクリプトを実行できます。

この記事では、SQL 要求を監視する方法について説明します。この方法により、実行中の要求の状態を監視し、履歴要求の詳細を確認することができます。

## <a name="access-sql-requests-list"></a>SQL 要求の一覧にアクセスする

ワークスペースで SQL 要求の一覧を表示するには、最初に [Synapse Studio を開き](https://web.azuresynapse.net/)、ワークスペースを選択します。

![ワークスペースへのログイン](./media/common/login-workspace.png)

ワークスペースを開いたら、左側の **[監視]** セクションを選択します。

![[監視] ハブを選択する](./media/common/left-nav.png)

**[SQL 要求]** を選択して、SQL 要求を表示します。

 ![SQL 要求を選択します](./media/how-to-monitor-sql-requests/monitor-hub-nav-sql-requests.png)

## <a name="select-a-sql-pool"></a>SQL プールを選択する

既定では、組み込みのサーバーレス SQL プールの SQL 要求履歴が、このビューに表示されます。 いずれかの専用 SQL プールを選択すると、そのプールの SQL 要求履歴を確認できます。

![SQL プールを選択します](./media/how-to-monitor-sql-requests/select-pool.png)

## <a name="filter-your-sql-requests"></a>SQL 要求をフィルター処理する

SQL 要求の一覧をフィルター処理して、関心のあるものに絞ることができます。 画面の上部にあるフィルターを使用して、フィルター処理を行うフィールドを指定できます。

たとえば、ビューをフィルター処理して、`maria@contoso.com` によって送信された SQL 要求のみを表示することができます。

![サンプルのフィルター](./media/how-to-monitor-sql-requests/filter-example.png)

## <a name="view-details-about-a-specific-sql-request"></a>特定の SQL 要求に関する詳細を表示する

いずれかの SQL 要求に関する詳細を表示するには、その SQL 要求を開き、詳細ビューに移動します。 専用 SQL プールで実行される複雑な要求については、進行状況を監視できます。

## <a name="next-steps"></a>次のステップ

パイプラインの実行の監視に関する詳細については、[Synapse Studio でのパイプラインの実行の監視](how-to-monitor-pipeline-runs.md)に関する記事をご覧ください。 

Apache Spark アプリケーションの監視に関する詳細については、[Synapse Studio での Apache Spark アプリケーションの監視](how-to-monitor-spark-applications.md)に関する記事をご覧ください。