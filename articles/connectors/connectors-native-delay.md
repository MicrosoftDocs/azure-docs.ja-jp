---
title: ロジック アプリに遅延を追加する | Microsoft Docs
description: 遅延アクションおよび延期期限アクションの概要と、Azure ロジック アプリでの使用方法
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 915f48bf-3bd8-4656-be73-91a941d0afcd
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: 15e581454b60319ab734f2fa5faf0d90e0a7c8bf
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58893726"
---
# <a name="get-started-with-the-delay-and-delay-until-actions"></a>遅延アクションと延期期限アクションの概要
遅延アクションと "延期期限" アクションを使用すると、さまざまなワークフロー シナリオに対応できます。

たとえば、次のようなことができます。

* 電子メールで状態の更新情報を送信するのを平日まで待つ。
* HTTP 呼び出しが終了するまでワークフローを遅らせてから、再開して結果を取得する。

ロジック アプリで遅延アクションの使用を開始する方法については、 [ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事をご覧ください。

## <a name="use-the-delay-actions"></a>遅延アクションの使用

アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 
アクションの詳細については[こちら](../connectors/apis-list.md)を参照してください。

ロジック アプリで遅延ステップを使用する方法の例を次に示します。

1. トリガーを追加したら、 **[新しいステップ]** をクリックしてアクションを追加します。
2. 「 **遅延** 」を検索して、遅延アクションを表示します。 この例では、 **[遅延]** を選択します。
   
    ![Delay actions](./media/connectors-native-delay/using-action-1.png)
3. 遅延を構成するアクション プロパティをすべて設定します。
   
    ![Delay config](./media/connectors-native-delay/using-action-2.png)
4. **[保存]** をクリックし、ロジック アプリを発行してアクティブ化します。

## <a name="action-details"></a>アクションの詳細
定期実行のトリガーには、構成可能な次のプロパティがあります。

### <a name="delay-action"></a>遅延アクション
一定期間、実行を遅延します。
\* は、必須フィールドであることを示しています。

| Display name | プロパティ名 | 説明 |
| --- | --- | --- |
| カウント* |count |遅延する時間の単位数 |
| 単位* |unit |時間の単位: `Second`、`Minute`、`Hour`、`Day` のいずれか |

<br>

### <a name="delay-until-action"></a>延期期限アクション
指定した日付/時刻まで実行を遅延します。
* は、必須フィールドであることを示しています。

| Display name | プロパティ名 | 説明 |
| --- | --- | --- |
| 年* |timestamp |遅延後の年 (GMT) |
| 月* |timestamp |遅延後の月 (GMT) |
| 日* |timestamp |遅延後の日 (GMT) |

<br>

## <a name="next-steps"></a>次の手順
プラットフォームを試用し、 [ロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)してください。 [API リスト](apis-list.md)を参照すると、Logic Apps で使用可能な他のコネクタについて確認できます。

