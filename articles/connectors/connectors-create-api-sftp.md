---
title: "ロジック アプリで SFTP コネクタを使用する方法 | Microsoft Docs"
description: "Azure App Service を使用してロジック アプリを作成します。 SFTP API に接続してファイルを送受信します。 ファイルの作成、更新、取得、削除など、さまざまな操作を実行できます。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/20/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 31253d8daee1581167a96a20ba8ad529a04b3e92
ms.contentlocale: ja-jp
ms.lasthandoff: 05/26/2017


---
# <a name="get-started-with-the-sftp-connector"></a>SFTP コネクタの使用
SFTP コネクタを使用すると、SFTP アカウントにアクセスしてファイルを送受信できます。 ファイルの作成、更新、取得、削除など、さまざまな操作を実行できます。  

[任意のコネクタ](apis-list.md)を使用するには、まずロジック アプリを作成する必要があります。 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)から始めることができます。

## <a name="connect-to-sftp"></a>SFTP への接続
ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの*接続*を作成する必要があります。 [接続](connectors-overview.md)により、ロジック アプリと別のサービスとの接続が実現します。  

### <a name="create-a-connection-to-sftp"></a>SFTP への接続を作成する
> [!INCLUDE [Steps to create a connection to SFTP](../../includes/connectors-create-api-sftp.md)]
> 
> 

## <a name="use-an-sftp-trigger"></a>SFTP トリガーの使用
トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。 トリガーの詳細については[こちら](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)を参照してください。  

この例では、**[SFTP - When a file is added or modified]\(SFTP - ファイルの追加または変更時\)** トリガーを使用して、SFTP サーバー上でファイルが追加または変更されたときにロジック アプリ ワークフローを開始します。 また、新しいファイルまたは変更されたファイルの内容をチェックする際の条件を追加し、ファイルの内容が、内容を使用する前にファイルを抽出する必要があることを示している場合にファイルの抽出を決定します。 最後に、ファイルの内容を抽出し、抽出した内容を SFTP サーバー上のフォルダーに配置するアクションを追加します。 

企業での使用例として、このトリガーを使用して、SFTP フォルダーに顧客からの注文を表す新しいファイルがあるかどうかを監視できます。  その後、**Get file content (ファイルの内容を取得する)** などの SFTP コネクタ アクションを使用して注文の内容を取得し、後続の処理や注文データベースへの格納を行うことができます。

> [!INCLUDE [Steps to create an SFTP trigger](../../includes/connectors-create-api-sftp-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>条件を追加する
> [!INCLUDE [Steps to add a condition](../../includes/connectors-create-api-sftp-condition.md)]
> 
> 

## <a name="use-an-sftp-action"></a>SFTP アクションの使用
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 アクションの詳細については[こちら](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)を参照してください。  

> [!INCLUDE [Steps to create an SFTP action](../../includes/connectors-create-api-sftp-action.md)]
> 
> 

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/sftpconnector/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="more-connectors"></a>その他のコネクタ
[API リスト](apis-list.md)に戻ります。
