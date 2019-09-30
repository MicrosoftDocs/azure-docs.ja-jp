---
title: Azure Portal を使用した SOAP API のインポートと REST への変換 | Microsoft Docs
description: API Management を使用して SOAP API をインポートし、REST に変換する方法を説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: e1534a562c81fc92fdf0c21452458c51b10f0bc9
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074238"
---
# <a name="import-a-soap-api-and-convert-to-rest"></a>SOAP API のインポートと REST への変換

この記事では、SOAP API をインポートし、REST に変換する方法を説明します。 また、APIM API をテストする方法についても説明します。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * SOAP API のインポートと REST への変換
> * Azure Portal での API のテスト
> * 開発者ポータルでの API のテスト

## <a name="prerequisites"></a>前提条件

次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>バックエンド API のインポートと公開

1. **[API MANAGEMENT]** で **[API]** を選択します。
2. **[Add a new API]\(新しい API の追加\)** の一覧から **[WSDL]** を選択します。

    ![SOAP API](./media/restify-soap-api/wsdl-api.png)
3. **[WSDL 仕様]** で、SOAP API が存在する場所の URL を入力します。
4. **[SOAP から REST]** ラジオ ボタンをクリックします。 このオプションをクリックすると、APIM は XML と JSON の間で自動変換を実行しようとします。 この場合、コンシューマーは JSON を返す API を RESTful API として呼び出す必要があります。 APIM は各要求を SOAP 呼び出しに変換します。

    ![SOAP から REST](./media/restify-soap-api/soap-to-rest.png)

5. Tab キーを押します。

    SOAP API からの情報が次のフィールドに入力されます: [表示名]、[名前]、[説明]。
6. API URL サフィックスを追加します。 サフィックスは、この APIM インスタンスでこの特定の API を識別する名前です。 この APIM インスタンス内で一意である必要があります。
9. API を成果物に関連付けることで API を公開します。 この場合、"*無制限*" の成果物が使用されます。  API を公開して開発者が利用できるようにするには、その API を成果物に追加します。 API の作成時に行うことも、後で設定することもできます。

    製品には、1 つまたは複数の API が関連付けられています。 複数の API を組み込み、開発者ポータルを通じてそれらを開発者に提供できます。 開発者は、まず成果物をサブスクライブして API へのアクセス権を取得する必要があります。 サブスクライブすると、その製品の API に適したサブスクリプション キーを受け取ります。 APIM インスタンスを作成した場合は、既に管理者になっているため、既定ですべての製品をサブスクライブしています。

    すべての API Management インスタンスは、2 つのサンプル成果物を既定で備えています。

    * **スターター**
    * **無制限**   
10. **作成** を選択します。

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

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [公開された API の変換と保護](transform-api.md)