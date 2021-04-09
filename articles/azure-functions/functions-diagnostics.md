---
title: Azure Functions 診断の概要
description: Azure Functions 診断を使用して Function App に関する問題をトラブルシューティングする方法について説明します。
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: fb329273d442c023233fee2e7e01aabe5f5bff8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "83122369"
---
# <a name="azure-functions-diagnostics-overview"></a>Azure Functions 診断の概要

Function App を実行している場合は、4xx エラーからトリガーの失敗まで、発生する可能性のある問題に備えることをお勧めします。 Azure Functions 診断は、Function App のトラブルシューティングを支援するインテリジェントな対話型エクスペリエンスを備え、構成や追加コストは不要です。 Function App の問題が発生した場合、Azure Functions 診断によって何が問題であるかが指摘されます。 適切な情報が示されるため、容易かつ迅速に問題をトラブルシューティングして解決できます。 この記事では、Azure Functions 診断を使用して、一般的な Function App の問題をより迅速に診断および解決する方法の基本について説明します。

## <a name="start-azure-functions-diagnostics"></a>Azure Functions 診断の開始

Azure Functions 診断を開始するには:

1. [Azure portal](https://portal.azure.com) で Function App に移動します。
1. **[問題の診断と解決]** を選択して Azure Functions 診断を開きます。
1. ホームページ タイルのキーワードを使用して、Function App の問題について最もよく説明されているカテゴリを選択します。 問題を最もよく説明するキーワードを検索バーに入力することもできます。 たとえば、"`execution`" と入力すると、Function App の実行に関連する診断レポートの一覧を表示してホームページから直接開くことができます。

   :::image type="content" source="./media/functions-diagnostics/functions-app-search-azure-functions-diagnostics.png" alt-text="Azure Functions 診断の検索" border="true":::

## <a name="use-the-interactive-interface"></a>対話型インターフェイスの使用

Function App の問題に応じた最適なホームページ カテゴリを選択すると、Azure Functions 診断の対話型インターフェイス Genie によって、アプリでの問題を診断して解決する手順へと誘導されます。 Genie で提供されているタイル ショートカットを使用して、関心のある問題カテゴリの診断レポート全体を閲覧できます。 タイル ショートカットでは、診断メトリックにアクセスするための直接的な方法を提供します。

:::image type="content" source="./media/functions-diagnostics/genie.png" alt-text="Genie は Azure Functions 診断のインターフェイスです。" border="false":::

タイルを選択すると、タイルに示されている問題に関連するトピックの一覧を表示できます。 これらのトピックでは、レポート全体から注目すべき情報のスニペットを提示します。 問題をさらに詳しく調査するには、これらのトピックのいずれかを選択します。 また、 **[レポート全体の表示]** を選択して、単一ページ上ですべてのトピックを参照することも可能です。

:::image type="content" source="./media/functions-diagnostics/preview-of-diagnostic-report.png" alt-text="診断レポートのプレビュー" border="false":::

## <a name="view-a-diagnostic-report"></a>診断レポートの表示

トピックを選択した後、対象の Function App に固有の診断レポートを表示できます。 診断レポートは、アプリに特定の問題があるかどうかをステータス アイコンを使用して示します。 問題の詳細な説明、推奨されるアクション、関連メトリック、および役立つドキュメントが表示されます。カスタマイズされた診断レポートは、Function App で実行される一連のチェックから生成されます。 診断レポートは、Function App の問題を特定するのに便利なツールであり、問題の解決方法についての指針となります。

## <a name="find-the-problem-code"></a>問題のあるコードを見つける

スクリプトベースの関数では、 **[Function App がダウンしているか、エラーが報告される]** の **[関数の実行とエラー]** を使用して、例外やエラーを引き起こしているコード行を絞り込むことができます。 このツールを使用して、根本原因を特定し、特定のコード行の問題を修正できます。 このオプションは、プリコンパイル済み C# 関数と Java 関数では使用できません。

:::image type="content" source="./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png" alt-text="関数の実行エラーに関する診断レポート" border="false":::

:::image type="content" source="./media/functions-diagnostics/function-exception.png" alt-text="例外の詳細の表示" border="false":::

## <a name="next-steps"></a>次のステップ

[UserVoice](https://feedback.azure.com/forums/355860-azure-functions) で、Azure Functions 診断に関する質問をしたり、フィードバックを提供したりすることができます。 フィードバックのタイトルに `[Diag]` を含めてください。

> [!div class="nextstepaction"]
> [Function App の監視](functions-monitoring.md)
