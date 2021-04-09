---
title: Visual Studio Code で Azure Stream Analytics ジョブを調べる
description: この記事では、Azure Stream Analytics ジョブをローカル プロジェクトにエクスポートする方法、ジョブを一覧表示する方法、ジョブ エンティティを表示する方法について説明します。
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: how-to
ms.openlocfilehash: 4f1c0650820a419275ade6095344033b6d81a568
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98013874"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Visual Studio Code で使用する Azure Stream Analytics の詳細 (プレビュー)

Visual Studio Code 用 Azure Stream Analytics 拡張機能は、Stream Analytics ジョブを管理するための軽量エクスペリエンスを開発者に提供します。 Windows、Mac、Linux 上で使用できます。 Azure Stream Analytics 拡張機能を使用すると、次のことができます。

- ジョブの[作成](quick-create-visual-studio-code.md)、開始、停止
- 既存のジョブのローカル プロジェクトへのエクスポート
- ジョブを一覧表示してジョブ エンティティを表示する

## <a name="export-a-job-to-a-local-project"></a>ジョブのローカル プロジェクトへのエクスポート

ジョブをローカル プロジェクトにエクスポートするには、Visual Studio Code の **Stream Analytics Explorer** で、エクスポートするジョブを特定します。 次に、プロジェクトのフォルダーを選択します。 プロジェクトが選択したフォルダーにエクスポートされ、Visual Studio Code からジョブの管理を続行できます。 Visual Studio Code を使用した Stream Analytics ジョブの管理の詳細については、Visual Studio Code の[クイック スタート](quick-create-visual-studio-code.md)を参照してください。

![Visual Studio Code での ASA ジョブのエクスポート](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>ジョブを一覧表示してジョブ エンティティを表示する

ジョブ ビューを使用して、Visual Studio から Azure Stream Analytics ジョブとやりとりすることができます。


1. Visual Studio Code のアクティビティ バーの **[Azure]** アイコンをクリックし、**Stream Analytics ノード** を展開します。 ジョブは、サブスクリプションの下に表示されます。

   ![Stream Analytics Explorer を開く](./media/vscode-explore-jobs/open-explorer.png)

2. ジョブ ノードを展開し、ジョブのクエリ、構成、入力、出力、および関数を開いて表示できます。 

3. ジョブ ノードを右クリックし、 **[Open Job View in Portal]\(ポータルでジョブ ビューを開く\)** ノードを選択して、Azure portal でジョブ ビューを開きます。

   ![[Open Job View in Portal]\(ポータルでジョブ ビューを開く\)](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>次のステップ

* [Visual Studio Code で Azure Stream Analytics クラウド ジョブを作成する (プレビュー)](quick-create-visual-studio-code.md)
