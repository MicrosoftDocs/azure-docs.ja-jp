---
title: OneDrive for Business に接続する
description: OneDrive for Business REST API と Azure Logic Apps を使用して、ファイルをアップロードおよび管理します
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 48c819566db43e2f997309d1bea55a9436ec7085
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "74789455"
---
# <a name="get-started-with-the-onedrive-for-business-connector"></a>OneDrive for Business コネクタの使用
OneDrive for Business に接続してファイルを管理します。 ファイルのアップロード、更新、取得、削除など、多様なアクションを実行できます。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

## <a name="create-a-connection-to-onedrive-for-business"></a>OneDrive for Business への接続の作成
OneDrive for Business を使用してロジック アプリを作成するには、まず **接続** を作成してから、次のプロパティの詳細を指定する必要があります。

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| トークン |はい |OneDrive for Business の資格情報を提供します |

接続を作成したら、その接続を使用してアクションを実行し、この記事で説明するトリガーをリッスンできます。

> [!INCLUDE [Steps to create a connection to OneDrive for Business](../../includes/connectors-create-api-onedriveforbusiness.md)]
> 

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/onedriveforbusinessconnector/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="more-connectors"></a>その他のコネクタ
[API リスト](apis-list.md)に戻ります。