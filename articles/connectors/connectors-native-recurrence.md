---
title: "ロジック アプリに定期実行のトリガーを追加する | Microsoft Docs"
description: "定期実行のトリガーの概要と Azure ロジック アプリでの使用方法"
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 51dd4f22-7dc5-41af-a0a9-e7148378cd50
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: fe558958c316c8dba42163e277ae01451f712e5a
ms.lasthandoff: 02/16/2017


---
# <a name="get-started-with-the-recurrence-trigger"></a>定期実行のトリガーの概要
定期実行のトリガーを使用すると、クラウドに強力なワークフローを作成できます。

たとえば、次のようなことができます。

* SQL ストアド プロシージャを毎日実行するようにワークフローをスケジュールする。
* 特定のハッシュタグに関する&1; 週間以内のすべてのツイートの概要を電子メールで送信する。

ロジック アプリでの定期実行のトリガーの使用を開始する方法については、 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事をご覧ください。

## <a name="use-a-recurrence-trigger"></a>定期実行のトリガーの使用
トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。 [トリガーの詳細についてはこちらを参照してください](connectors-overview.md)。

ロジック アプリで定期実行のトリガーをセットアップする方法の例を次に示します。

1. ロジック アプリの最初の手順として、 **定期実行** のトリガーを追加します。
2. 繰り返し間隔のパラメーターを入力します。

これで、ロジック アプリは、この時間間隔ごとに実行が開始されます。

![HTTP trigger](./media/connectors-native-recurrence/using-trigger.png)

## <a name="trigger-details"></a>トリガーの詳細
定期実行のトリガーには、構成可能な次のプロパティがあります。

指定された期間の後にロジック アプリを起動します。
A * は、必須フィールドであることを示しています。

| 表示名 | プロパティ名 | Description |
| --- | --- | --- |
| 頻度* |frequency |時間の単位: `Second`、`Minute`、`Hour`、`Day`、`Year` のいずれか |
| 間隔* |interval |指定した頻度で繰り返し実行する間隔 |
| タイム ゾーン |timeZone |開始時刻が UTC オフセットなしで指定されている場合、このタイム ゾーンが使用されます。 |
| 開始時刻 |startTime |開始時刻は [ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)です。 |

<br>

## <a name="next-steps"></a>次のステップ
プラットフォームを試用し、 [ロジック アプリを作成](../logic-apps/logic-apps-create-a-logic-app.md)してください。 [API リスト](apis-list.md)を参照すると、Logic Apps で使用可能な他のコネクタについて確認できます。


