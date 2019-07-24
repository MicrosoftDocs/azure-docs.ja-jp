---
title: OneDrive for Business に接続する - Azure Logic Apps | Microsoft Docs
description: OneDrive for Business REST API と Azure Logic Apps を使用して、ファイルをアップロードおよび管理します
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 08/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 63cb02c52b5def12675ccca4f2cd292a8ad4bb66
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295644"
---
# <a name="get-started-with-the-onedrive-for-business-connector"></a>OneDrive for Business コネクタの使用
OneDrive for Business に接続してファイルを管理します。 ファイルのアップロード、更新、取得、削除など、多様なアクションを実行できます。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

## <a name="create-a-connection-to-onedrive-for-business"></a>OneDrive for Business への接続の作成
OneDrive for Business を使用してロジック アプリを作成するには、まず**接続**を作成してから、次のプロパティの詳細を指定する必要があります。

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