---
title: Synapse Studio を使用してパイプラインの実行を監視する
description: Synapse Studio を使用して、ワークスペースのパイプラインの実行を監視します。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: dbd890117c78274392d5745e0563332371b404c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96452579"
---
# <a name="use-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Synapse Studio を使用してワークスペースのパイプラインの実行を監視する

Azure Synapse Analytics では、ソリューション内のデータ移動、データ変換、コンピューティングのアクティビティを自動化および統合する複雑なパイプラインを作成できます。 これらのパイプラインを作成して監視するには、Synapse Studio を使用します。

この記事では、パイプラインの実行を監視する方法を説明します。これにより、パイプラインの最新の状態、問題、進捗状況を監視することができます。

## <a name="access-pipeline-runs-list"></a>パイプラインの実行リストにアクセスする

ワークスペースでパイプラインの実行の一覧を表示するには、最初に [Synapse Studio を開き](https://web.azuresynapse.net/)、ワークスペースを選択します。

![ワークスペースへのログイン](./media/common/login-workspace.png)

ワークスペースを開いたら、左側の **[監視]** セクションを選択します。

![[監視] ハブを選択する](./media/common/left-nav.png)

**[パイプラインの実行]** を選択して、パイプラインの実行の一覧を表示します。

![[パイプラインの実行] を選択する](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filter-your-pipeline-runs"></a>パイプラインの実行をフィルター処理する

パイプラインの実行の一覧をフィルター処理して、関心のあるものだけに絞り込むことができます。 画面の上部にあるフィルターを使用して、フィルター処理を行うフィールドを指定できます。

たとえば、ビューをフィルター処理して、"holiday" という名前のパイプラインのパイプラインの実行のみを表示できます。

![サンプルのフィルター](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="view-details-about-a-specific-pipeline-run"></a>特定のパイプラインの実行の詳細を表示する

パイプラインの実行の詳細を表示するには、そのパイプラインの実行を選択します。 パイプラインの実行に関連付けられたアクティビティの実行が表示されます。 パイプラインがまだ実行中であれば、進捗状況を監視することができます。 
  
## <a name="next-steps"></a>次のステップ

アプリケーションの監視の詳細については、[Apache Spark アプリケーションの監視](how-to-monitor-spark-applications.md)に関する記事をご覧ください。 
