---
title: "ロジック アプリに Azure Blob Storage コネクタを追加する | Microsoft Docs"
description: "ロジック アプリで Azure Blob Storage コネクタを使用および構成する"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b5dc3f75-6bea-420b-b250-183668d2848d
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/02/2017
ms.author: mandia; ladocs
ms.openlocfilehash: bc7908868828bd1628633cf9e57f8c44f8000827
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-azure-blob-storage-connector-in-a-logic-app"></a>ロジック アプリで Azure Blob Storage コネクタを使用する
Azure Blob Storage コネクタを使用して、ロジック アプリ内で、ストレージ アカウントの BLOB をアップロード、更新、取得、および削除します。  

Azure Blob Storage では、次の操作を実行できます。

* 新しいプロジェクトをアップロードするか、最近更新されたファイルを取得して、ワークフローを構築します。
* ファイルのメタデータの取得、ファイルの削除、ファイルのコピーなどのアクションを使用します。 たとえば、Azure Web サイトでツールが更新されると (トリガー)、Blob Storage でファイルを更新します (アクション)。 

このトピックでは、ロジック アプリで Blob Storage コネクタを使用する方法について説明します。

Logic Apps の詳細については、「[Logic Apps とは](../logic-apps/logic-apps-what-are-logic-apps.md)」と[ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関するページを参照してください。

Logic Apps の詳細については、「[Logic Apps とは](../logic-apps/logic-apps-what-are-logic-apps.md)」と[ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関するページを参照してください。

## <a name="connect-to-azure-blob-storage"></a>Azure Blob Storage に接続する
ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの "*接続*" を作成します。 接続により、ロジック アプリと別のサービスとの接続が実現します。 たとえば、ストレージ アカウントに接続するには、最初に Blob Storage の "*接続*" を作成します。 接続を作成するには、接続対象のサービスへのアクセスに通常使用する資格情報を入力します。 そのため、Azure Storage の場合は、ストレージ アカウントの資格情報を入力して接続を作成します。 

#### <a name="create-the-connection"></a>接続の作成
> [!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="use-a-trigger"></a>トリガーを使用する
このコネクタにはトリガーがありません。 定期実行のトリガー、HTTP Webhook トリガー、他のコネクタで使用可能なトリガーなど、他のトリガーを使用してロジック アプリを起動します。 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事に例が記載されています。

## <a name="use-an-action"></a>アクションを使用する
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。

1. プラス記号を選択します。 **[アクションの追加]**、**[条件の追加]**、**[More (その他)]** のいずれかのオプションという複数の選択肢があります。
   
    ![](./media/connectors-create-api-azureblobstorage/add-action.png)
2. **[アクションの追加]**を選択します。
3. テキスト ボックスに「blob」と入力して、使用可能なすべてのアクションの一覧を取得します。
   
    ![](./media/connectors-create-api-azureblobstorage/actions.png) 
4. この例では、**[AzureBlob - Get file metadata using path (AzureBlob - パスを使用してファイルのメタデータを取得する)]** を選択します。 接続が既に存在する場合は、**[...]** (表示ピッカー) ボタンを使用してファイルを選択します。
   
    ![](./media/connectors-create-api-azureblobstorage/sample-file.png)
   
    接続情報の入力を求められたら、詳細を入力して接続を作成します。 これらのプロパティについては、このトピックの「[接続の作成](connectors-create-api-azureblobstorage.md#create-the-connection)」を参照してください。 
   
   > [!NOTE]
   > この例では、ファイルのメタデータを取得します。 メタデータを表示するには、別のコネクタを使用して新しいファイルを作成する別のアクションを追加します。 たとえば、メタデータに基づいて新しい "test" ファイルを作成する OneDrive アクションを追加します。 


5. ツール バーの左上隅にある **[保存]** を選択して変更を保存します。 ロジック アプリが保存され、場合によっては、自動的に有効になります。

> [!TIP]
> [ストレージ エクスプローラー](http://storageexplorer.com/)は、複数のストレージ アカウントを管理するための優れたツールです。

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/azureblobconnector/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。 

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成](../logic-apps/logic-apps-create-a-logic-app.md)します。 [API の一覧](apis-list.md)で、Logic Apps で使用できる他のコネクタを確認してください。

