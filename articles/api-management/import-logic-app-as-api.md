---
title: Azure Portal を使用してロジック アプリを API としてインポートする | Microsoft Docs
description: このチュートリアルでは、API Management (APIM) を使用してロジック アプリを API としてインポートする方法を示します。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: 76a509c1cb9277ac72f99ec9ebfc239bfd71390c
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969184"
---
# <a name="import-a-logic-app-as-an-api"></a>ロジック アプリを API としてインポートする

この記事では、ロジック アプリを API としてインポートする方法について説明します。 また、APIM API をテストする方法についても説明します。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * ロジック アプリを API としてインポート
> * Azure Portal での API のテスト
> * 開発者ポータルでの API のテスト

## <a name="prerequisites"></a>前提条件

* 次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)
* HTTP エンドポイントが公開されるサブスクリプションにロジック アプリがあることを確認します。 詳しくは、[HTTP エンドポイントでのワークフローのトリガー](../logic-apps/logic-apps-http-endpoint.md)に関する記事をご覧ください

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>バックエンド API のインポートと公開

1. **[API Management]** で **[API]** を選択します。
2. **[Add a new API]\(新しい API の追加\)** の一覧から **[ロジック アプリ]** を選択します。

    ![ロジック アプリ](./media/import-logic-app-as-api/logic-app-api.png)
3. **[参照]** をクリックして、サブスクリプション内の呼び出し可能なロジック アプリの一覧を表示します。
4. アプリを選びます。 APIM では、選択したアプリに関連付けられている Swagger が検索されてフェッチされ、インポートされます。 
5. API URL サフィックスを追加します。 サフィックスは、この APIM インスタンスでこの特定の API を識別する名前です。 この APIM インスタンス内で一意である必要があります。
6. API を成果物に関連付けることで API を公開します。 この場合、"*無制限*" の成果物が使用されます。  API を公開して開発者が利用できるようにするには、その API を成果物に追加します。 API の作成時に行うことも、後で設定することもできます。

    成果物には、1 つまたは複数の API が関連付けられています。 複数の API を組み込み、開発者ポータルを通じてそれらを開発者に提供できます。 開発者は、まず成果物をサブスクライブして API へのアクセス権を取得する必要があります。 サブスクライブすると、その成果物の API に適したサブスクリプション キーを受け取ります。 APIM インスタンスを作成した場合は、既に管理者になっているため、既定ですべての製品をサブスクライブしています。

    すべての API Management インスタンスは、2 つのサンプル成果物を既定で備えています。

    * **スターター**
    * **無制限**   
7. **作成**を選択します。

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Azure Portal での新しい APIM API のテスト

Azure Portal には、API の操作を表示およびテストするための便利な環境が用意されており、操作を直接呼び出すことができます。  

1. 前の手順で作成した API を選びます。
2. **[テスト]** タブをクリックします。
3. いくつかの操作を選びます。

    ページに、クエリ パラメーターのフィールドとヘッダーのフィールドが表示されます。 この API に関連付けられている成果物のサブスクリプション キーの場合、ヘッダーの 1 つは "Ocp-Apim-Subscription-Key" です。 APIM インスタンスを作成した場合は、既に管理者になっているので、キーが自動的に入力されます。 
1. **[送信]** をクリックします。

    バックエンドは **200 OK** といくつかのデータで応答します。

## <a name="call-operation"></a>開発者ポータルから操作を呼び出す

操作を**開発者ポータル**から呼び出して API をテストすることもできます。 

1. "バックエンド API のインポートと公開" の手順で作成した API を選びます。
2. **[開発者ポータル]** をクリックします。

    "開発者ポータル" サイトが開きます。
3. 作成した **API** を選びます。
4. テストする操作をクリックします。
5. **[テスト]** をクリックします。
6. **[送信]** をクリックします。
    
    操作を呼び出すと、**応答のステータス**、**応答ヘッダー**、**応答内容**が開発者ポータルに表示されます。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

>[!NOTE]
> すべてのロジック アプリには、**手動呼び出し**操作があります。 複数ロジック アプリの API を含める場合は、競合を回避するために、関数の名前を変更する必要があります。

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [公開された API の変換と保護](transform-api.md)