---
title: "Azure Logic Apps での Wunderlist コネクタ | Microsoft Docs"
description: "Wunderlist への接続を作成し、この接続を使用してロジック アプリでワークフローを構築します。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 899110992cc52ca5edf1706320fd5570473de784
ms.contentlocale: ja-jp
ms.lasthandoff: 05/26/2017


---
# <a name="get-started-with-the-wunderlist-connector"></a>Wunderlist コネクタの使用
Wunderlist では、ユーザーの作業を支援する todo リストとタスク マネージャーを提供します。  買い物メモを家族と共有したり、プロジェクトで作業したり、休暇の計画を立てたりするときも、Wunderlist を使えばタスクを簡単に確認、共有、完了できます。 Wunderlist は携帯電話、タブレット、コンピューター間で即時に同期するため、すべてのタスクにどこからでもアクセスできます。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事を参照してください。

## <a name="create-a-connection-to-wunderlist"></a>Wunderlist への接続を作成する
Wunderlist を使用してロジック アプリを作成するには、まず**接続**を作成してから、次のプロパティの詳細を指定する必要があります。

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| トークン |はい |Wunderlist の資格情報を提供します |

接続を作成したら、その接続を使用してアクションを実行し、トリガーをリッスンできます。

> [!INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)]
> 

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/wunderlist/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="more-connectors"></a>その他のコネクタ
[API リスト](apis-list.md)に戻ります。
