---
title: Azure Data Factory のシステム変数 | Microsoft Docs
description: この記事では、Azure Data Factory でサポートされているシステム変数について説明します。 Data Factory エンティティを定義するときに、式でこれらの変数を使用できます。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: shlo
ms.openlocfilehash: 9a4d5acfe16a2fdbb3b631cb8baf6cb8e90a7d58
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016291"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Azure Data Factory でサポートされているシステム変数
この記事では、Azure Data Factory でサポートされているシステム変数について説明します。 Data Factory エンティティを定義するときに、式でこれらの変数を使用できます。

## <a name="pipeline-scope"></a>パイプラインのスコープ
これらのシステム変数は、パイプライン JSON 内の任意の場所で参照できます。

| 変数名 | 説明 |
| --- | --- |
| @pipeline().DataFactory |パイプライン実行が実行されているデータ ファクトリの名前 |
| @pipeline().Pipeline |パイプラインの名前 |
| @pipeline().RunId | 特定のパイプライン実行の ID |
| @pipeline().TriggerType | パイプラインを呼び出したトリガーの種類 (手動、Scheduler) |
| @pipeline（）.TriggerId| パイプラインを呼び出すトリガーの ID |
| @pipeline（）.TriggerName| パイプラインを呼び出すトリガーの名前 |
| @pipeline（）.TriggerTime| トリガーがパイプラインを呼び出した時間。 トリガーの時間は、実際の起動時間であり、スケジュールされた時間ではありません。 たとえば、`13:20:00.00Z` ではなく、`13:20:08.0149599Z` が返されます |

## <a name="schedule-trigger-scope"></a>スケジュール トリガーのスコープ
トリガーのタイプが "ScheduleTrigger" の場合、これらのシステム変数は、トリガー JSON 内の任意の場所で参照できます。

| 変数名 | 説明 |
| --- | --- |
| @trigger().scheduledTime |トリガーがパイプライン実行を呼び出すようにスケジュールされた時間。 たとえば、5 分ごとに起動されるトリガーの場合、この変数はそれぞれ `2017-06-01T22:20:00Z`、`2017-06-01T22:25:00Z`、`2017-06-01T22:29:00Z` を返します。|
| @trigger().startTime |トリガーが**実際に**パイプライン実行を呼び出すために起動した時間。 たとえば、5 分ごとに起動されるトリガーの場合、この変数はそれぞれ、`2017-06-01T22:20:00.4061448Z`、`2017-06-01T22:25:00.7958577Z`、`2017-06-01T22:29:00.9935483Z` のような値を返します。|

## <a name="tumbling-window-trigger-scope"></a>タンブリング ウィンドウ トリガーのスコープ
トリガーのタイプが "TumblingWindowTrigger" の場合、これらのシステム変数は、トリガー JSON 内の任意の場所で参照できます。

| 変数名 | 説明 |
| --- | --- |
| @trigger().outputs.windowStartTime |トリガーがパイプライン実行を呼び出すようにスケジュールされたウィンドウの開始時間。 タンブリング ウィンドウ トリガーの頻度が [毎時間] の場合、この時間は、1 時間の開始時点となります。|
| @trigger().outputs.windowEndTime |トリガーがパイプライン実行を呼び出すようにスケジュールされたウィンドウの終了時間。 タンブリング ウィンドウ トリガーの頻度が [毎時間] の場合、この時間は、1 時間の終了時点となります。|
## <a name="next-steps"></a>次の手順
これらの変数を式で使用する方法については、[式言語と関数](control-flow-expression-language-functions.md)に関するページを参照してください。
