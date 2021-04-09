---
title: チュートリアル:パイプラインとの統合の概要
description: このチュートリアルでは、Synapse Studio を使用してパイプラインとアクティビティを統合する方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 2ea7c3c440fcf95e4512464333efe8461788bceb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98219404"
---
# <a name="integrate-with-pipelines"></a>パイプラインと統合する

このチュートリアルでは、Synapse Studio を使用してパイプラインとアクティビティを統合する方法について説明します。 

## <a name="overview"></a>概要

Azure Synapse では、さまざまなタスクを統合できます。

1. Synapse Studio で、 **[統合]** ハブに移動します。
1. **[+]**  >  **[パイプライン]** を選択して新しいパイプラインを作成します。 新しいパイプライン オブジェクトをクリックして、パイプライン デザイナーを開きます。
1. **[アクティビティ]** で **[Synapse]** フォルダーを展開し、**Notebook** オブジェクトをデザイナーにドラッグします。
1. Notebook アクティビティのプロパティの **[設定]** タブを選択します。 ドロップダウン リストを使用して、現在の Synapse ワークスペースから任意のノートブックを選択します。 
1. パイプラインで、 **[トリガーの追加]**  >  **[New/edit]\(新規作成/編集\)** を選択します。
1. **[Choose trigger]\(トリガーの選択\)** で **[新規]** を選択し、 **[Recurrence]\(繰り返し\)** を "1 時間ごと" に設定します。
1. **[OK]** を選択します。 
1. **[すべて公開]** を選択します。 


## <a name="monitor-pipeline"></a>パイプラインを監視する

1. パイプラインが公開された後、次の 1 時間を待たずに、パイプラインをすぐに実行するには、 **[トリガーの追加]**  >  **[Trigger now]\(今すぐトリガー\)** の順に選択します。
1. Synapse Studio で、 **[監視]** ハブに移動し、 **[パイプラインの実行]** を選択して、パイプラインの実行の進行状況を監視します。



## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Power BI でデータを視覚化する](get-started-visualize-power-bi.md)
