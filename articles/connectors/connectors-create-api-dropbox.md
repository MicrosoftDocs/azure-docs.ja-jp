---
title: "Azure Logic Apps での Dropbox コネクタ | Microsoft Docs"
description: "Azure App Service を使用してロジック アプリを作成します。 Dropbox に接続してファイルを管理します。 Dropbox のファイルのアップロード、更新、取得、削除など、多様なアクションを実行できます。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: cb0ae033-aba7-4ac9-beaa-be561a0f0cac
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 0d09580c60fd620811b539147439d0922839fe7e
ms.contentlocale: ja-jp
ms.lasthandoff: 05/26/2017


---
# <a name="get-started-with-the-dropbox-connector"></a>Dropbox コネクタの使用
Dropbox に接続してファイルを管理します。 Dropbox のファイルのアップロード、更新、取得、削除など、多様なアクションを実行できます。

[任意のコネクタ](apis-list.md)を使用するには、まずロジック アプリを作成する必要があります。 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)から始めることができます。

## <a name="connect-to-dropbox"></a>Dropbox に接続する
ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの*接続*を作成する必要があります。 接続により、ロジック アプリと別のサービスとの接続が実現します。 たとえば、Dropbox に接続するには、最初に Dropbox "*接続*" を作成する必要があります。 接続を作成するには、接続先のサービスへのアクセスに通常使用する資格情報を入力する必要があります。 たとえば、Dropbox の場合は、Dropbox への接続を作成するために Dropbox アカウントの資格情報が必要になります。 接続の詳細については、[こちら]()をご覧ください。

### <a name="create-a-connection-to-dropbox"></a>Dropbox への接続を作成する
> [!INCLUDE [Steps to create a connection to Dropbox](../../includes/connectors-create-api-dropbox.md)]
> 
> 

## <a name="use-a-dropbox-trigger"></a>Dropbox トリガーを使用する
トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。 トリガーの詳細については[こちら](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)を参照してください。

この例では、**[When a file is created (ファイルの作成時)]** トリガーを使用します。 このトリガーが発生したら、**[Get file content using path (パスを使用してファイルの内容を取得する)]** Dropbox アクションを呼び出します。 

1. Logic Apps デザイナーの検索ボックスに「*dropbox*」と入力し、**[Dropbox - When a file is created (Dropbox - ファイルの作成時)]** トリガーを選択します。      
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)  
2. ファイルの作成をトラッキングするフォルダーを選択します。 [...] (赤で囲まれている部分) を選択し、トリガーの入力用に選択するフォルダーを参照します。  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)  

## <a name="use-a-dropbox-action"></a>Dropbox アクションを使用する
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 アクションの詳細については[こちら](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)を参照してください。

トリガーを追加した後は、次の手順に従って、新しいファイルの内容を取得するアクションを追加します。

1. **[+ 新しいステップ]** を選択し、新しいファイルの作成時に実行するアクションを追加します。  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)
2. **[アクションの追加]**を選択します。 これにより検索ボックスが開き、行う操作について検索できます。  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)
3. 「*dropbox*」と入力して、Dropbox に関連するアクションを検索します。  
4. 選択した Dropbox フォルダーに新しいファイルが作成されたときに実行するアクションとして、**[Dropbox - Get file content using path (Dropbox - パスを使用してファイルの内容を取得する)]** を選択します。 アクションの制御ブロックが表示されます。 ロジック アプリによる Dropbox アカウントへのアクセスをまだ承認していない場合は、承認を求められます。  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)  
5. (**[ファイル パス]** コントロールの右側にある) [...] を選択し、使用するファイル パスを参照します。 または、**ファイル パス** トークンを使用してロジック アプリをすばやく作成することもできます。  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)  
6. 作業内容を保存し、Dropbox に新しいファイルを作成してワークフローをアクティブ化します。  

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/dropbox/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="more-connectors"></a>その他のコネクタ
[API リスト](apis-list.md)に戻ります。
