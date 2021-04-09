---
title: Azure Portal を使用して API アプリを API としてインポートする | Microsoft Docs
description: この記事では、API Management (APIM) を使用して API アプリを API としてインポートする方法を示します。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: 41209233ec59f578db4ff7fd344bb96aefeb975e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994742"
---
# <a name="import-an-api-app-as-an-api"></a>API アプリを API としてインポートする

この記事では、API アプリを API としてインポートする方法を示します。 また、APIM API をテストする方法についても説明します。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * API アプリを API としてインポートする
> * Azure Portal での API のテスト
> * 開発者ポータルでの API のテスト

## <a name="prerequisites"></a>前提条件

+ 次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)
+ サブスクリプションに API アプリがあることを確認します。 詳細については、[App Service のドキュメント](../app-service/index.yml)に関するページを参照してください

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>バックエンド API のインポートと公開

1. Azure portal で API Management サービスに移動し、メニューから **[API]** を選択します。
2. **[Add a new API]\(新しい API の追加\)** の一覧から **[API アプリ]** を選択します。

    ![API アプリ](./media/import-api-app-as-api/api-app.png)
3. **[参照]** をクリックしてサブスクリプション内の API Apps の一覧を表示します。
4. アプリを選びます。 APIM では、選択したアプリに関連付けられている Swagger が検索されてフェッチされ、インポートされます。 

    Swagger が見つからない場合、APIM は API を "パススルー" API として公開します。 
5. API URL サフィックスを追加します。 サフィックスは、この APIM インスタンスでこの特定の API を識別する名前です。 この APIM インスタンス内で一意である必要があります。
6. API を成果物に関連付けることで API を公開します。 この場合、"*無制限*" の成果物が使用されます。  API を公開して開発者が利用できるようにするには、その API を成果物に追加します。 API の作成時に行うことも、後で設定することもできます。

    製品には、1 つまたは複数の API が関連付けられています。 複数の API を組み込み、開発者ポータルを通じてそれらを開発者に提供できます。 開発者は、まず製品をサブスクライブして API へのアクセス権を取得する必要があります。 サブスクライブすると、その製品の API に適したサブスクリプション キーを受け取ります。 APIM インスタンスを作成した場合は、既に管理者になっているため、既定ですべての製品をサブスクライブしています。

    すべての API Management インスタンスは、2 つのサンプル成果物を既定で備えています。

    * **スターター**
    * **無制限**   
7. 他の API 設定を入力します。 値は、作成時に設定することも、後で **[設定]** タブに移動して構成することもできます。設定については、「[最初の API のインポートと発行](import-and-publish.md#import-and-publish-a-backend-api)」のチュートリアルで説明されています。
8. **［作成］** を選択します

## <a name="test-the-new-api-in-the-azure-portal"></a>Azure portal での新しい API のテスト

Azure Portal には、API の操作を表示およびテストするための便利な環境が用意されており、操作を直接呼び出すことができます。  

1. 前の手順で作成した API を選びます。
2. **[テスト]** タブをクリックします。
3. いくつかの操作を選びます。

    ページに、クエリ パラメーターのフィールドとヘッダーのフィールドが表示されます。 この API に関連付けられている成果物のサブスクリプション キーの場合、ヘッダーの 1 つは "Ocp-Apim-Subscription-Key" です。 APIM インスタンスを作成した場合は、既に管理者になっているので、キーが自動的に入力されます。 
1. **[送信]** をクリックします。

    バックエンドは **200 OK** といくつかのデータで応答します。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [公開された API の変換と保護](transform-api.md)
