---
title: Synapse Studio で SQL プールを監視する方法
description: Synapse Studio を使用して SQL プールを監視する方法について説明します。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 63b63526333435387c3ff5b5c9d5599ec851c1a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96465626"
---
# <a name="use-synapse-studio-to-monitor-your-sql-pools"></a>Synapse Studio を使用して SQL プールを監視する

Synapse Studio では、ワークスペース内の SQL プールで SQL スクリプトを実行できます。

この記事では、SQL プールを監視する方法について説明します。この方法により、プールの状態とアクティビティを監視することができます。

## <a name="access-sql-pools-list"></a>SQL プールの一覧にアクセスする

ワークスペースで SQL プールの一覧を表示するには、最初に [Synapse Studio を開き](https://web.azuresynapse.net/)、ワークスペースを選択します。

![ワークスペースへのログイン](./media/common/login-workspace.png)

ワークスペースを開いたら、左側の **[監視]** セクションを選択します。

![[監視] ハブを選択する](./media/common/left-nav.png)

**[SQL プール]** を選択して、SQL プールの一覧を表示します。

 ![SQL プールを選択します](./media/how-to-monitor-sql-pools/monitor-hub-nav-sql-pools.png)

## <a name="filter-your-sql-pools"></a>SQL プールをフィルター処理する

SQL プールの一覧をフィルター処理して、関心のあるものに絞ることができます。 画面の上部にあるフィルターを使用して、フィルター処理を行うフィールドを指定できます。

たとえば、ビューをフィルター処理して、"salesrecords" という名前を含む SQL プールのみを表示することができます。

![サンプルのフィルター](./media/how-to-monitor-sql-pools/filter-example.png)

## <a name="view-details-about-a-specific-sql-pool"></a>特定の SQL プールに関する詳細を表示する

いずれかの SQL プールに関する詳細を表示するには、その SQL プールを選択して、詳細を表示します。

![SQL プールの詳細](./media/how-to-monitor-sql-pools/sql-pool-details.png)

## <a name="next-steps"></a>次の手順

パイプラインの実行の監視に関する詳細については、[Synapse Studio でのパイプラインの実行の監視](how-to-monitor-pipeline-runs.md)に関する記事をご覧ください。 

SQL 要求の監視に関する詳細については、[Synapse Studio での SQL 要求の監視](how-to-monitor-sql-requests.md)に関する記事をご覧ください。