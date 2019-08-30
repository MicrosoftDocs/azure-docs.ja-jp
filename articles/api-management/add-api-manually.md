---
title: Azure Portal を使用して手動で API を追加する | Microsoft Docs
description: このチュートリアルでは、API Management (APIM) を使用して手動で API を追加する方法を示します。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/27/2018
ms.author: apimpm
ms.openlocfilehash: 5440333360549c5df2da57c97b24dcc77436ba4b
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072701"
---
# <a name="add-an-api-manually"></a>手動による API の追加

この記事の手順では、Azure portal を使用して API Management (APIM) インスタンスに手動で API を追加する方法を示します。 空の API を作成して手動で定義する必要のある一般的なシナリオは、モック API を作成する場合です。 モック API の作成について詳しくは、[API の応答の模擬テストの実行](mock-api-responses.md)に関するページをご覧ください。

既存の API をインポートする場合は、「[関連トピック](#related-topics)」セクションをご覧ください。

この記事では、空の API を作成し、バックエンド API として [httpbin.org](https://httpbin.org) (パブリック テスト サービス) を指定します。

## <a name="prerequisites"></a>前提条件

次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>API の作成

1. **[API Management]** で **[API]** を選びます。
2. 左側のメニューで、 **[+ API の追加]** を選びます。
3. 一覧から **[空の API]** を選びます。

    ![空の API](media/add-api-manually/blank-api.png)
4. API の設定を入力します。

    |**Name**|**値**|**説明**|
    |---|---|---|
    |**[表示名]**|*空の API*|この名前は開発者ポータルに表示されます。|
    |**Name**|*blank-api*|API の一意の名前を指定します。|
    |**[Web サービスの URL]** (省略可能)|*https://httpbin.org*| モック API を作成する場合は、何も入力しない場合があります。 <br/>この例では、「[https://httpbin.org](https://httpbin.org)」と入力します。これは、パブリック テスト サービスです。 <br/>自動的にバックエンドにマッピングされる API をインポートする場合は、「[関連トピック](#related-topics)」セクションのいずれかのトピックをご覧ください。|
    |**[URL スキーム]**|*HTTP*|この場合、バックエンドはセキュリティで保護されていない HTTP でアクセスしますが、バックエンドに対してはセキュリティで保護された HTTPS APIM アクセスを指定します。 <br/>このようなシナリオ (HTTPS から HTTP へ) は、HTTPS の終了と呼ばれます。 API が仮想ネットワーク内にある場合は、このようにすることがあります (仮想ネットワーク内では、HTTPS が使用されていなくても、アクセスが安全であることが分かります)。 <br/>CPU サイクルを節約するために "HTTPS の終了" を使用する場合があります。|
    |**[URL suffix]\(URL サフィックス\)**|*hbin*| サフィックスは、この APIM インスタンスでこの特定の API を識別する名前です。 この APIM インスタンス内で一意である必要があります。|
    |**成果物**|*無制限*|API を成果物に関連付けることで API を公開します。 API を公開して開発者が利用できるようにするには、その API を成果物に追加します。 API の作成時に行うことも、後で設定することもできます。<br/><br/>製品には、1 つまたは複数の API が関連付けられています。 複数の API を組み込み、開発者ポータルを通じてそれらを開発者に提供できます。 <br/>開発者は、まず成果物をサブスクライブして API へのアクセス権を取得する必要があります。 サブスクライブすると、その製品の API に適したサブスクリプション キーを受け取ります。 APIM インスタンスを作成した場合は、既に管理者になっているため、既定ですべての製品をサブスクライブしています。<br/><br/> すべての API Management インスタンスは、2 つのサンプル成果物を既定で備えています。**スターター**と**無制限**。| 
5. **作成** を選択します。

この時点では、バックエンド API の操作にマッピングされる APIM の操作はありません。 APIM ではなくバックエンドを介して公開される操作を呼び出すと、**404** が返されます。

>[!NOTE] 
> 既定では、追加する API がバックエンド サービスに接続されていても、ホワイトリストに登録するまでは APIM ではいかなる操作も公開されません。 バックエンド サービスの操作をホワイトリストに登録するには、バックエンドの操作にマッピングされる APIM の操作を作成します。

## <a name="add-and-test-an-operation"></a>操作の追加とテスト

このセクションでは、バックエンドの "http://httpbin.org/get" 操作にマッピングするために "/get" 操作を追加する方法を示します。

### <a name="add-an-operation"></a>操作の追加

1. 前の手順で作成した API を選びます。
2. **[+ 操作の追加]** を選択します。
3. **[URL]** で、**GET** を選び、リソースに「 */get*」と入力します。
4. **[表示名]** に「*FetchData*」と入力します。
5. **[保存]** を選択します。

### <a name="test-an-operation"></a>操作のテスト

Azure Portal で操作をテストします。 または、**開発者ポータル**内でテストすることもできます。

1. **[テスト]** タブを選びます。
2. **FetchData** を選びます。
3. **[送信]** をクリックします。

"http://httpbin.org/get" 操作によって生成される応答が表示されます。 操作を変換する場合は、「[API を変換および保護する](transform-api.md)」をご覧ください。

## <a name="add-and-test-a-parameterized-operation"></a>パラメーター化された操作の追加とテスト

このセクションでは、パラメーターを受け取る操作を追加する方法を示します。 この場合、操作を "http://httpbin.org/status/200" にマッピングします。

### <a name="add-the-operation"></a>操作の追加

1. 前の手順で作成した API を選びます。
2. **[+ 操作の追加]** を選択します。
3. **[URL]** で、**GET** を選び、リソースに「 */status/{code}* 」と入力します。 必要に応じて、このパラメーターに関連付けられている情報を指定できます。 たとえば、 **[種類]** に「*Number*」を、 **[値]** に「*200*」(既定値) を入力します。
4. **[表示名]** に「GetStatus」と入力します。
5. **[保存]** を選択します。

### <a name="test-the-operation"></a>操作のテスト 

Azure Portal で操作をテストします。  または、**開発者ポータル**内でテストすることもできます。

1. **[テスト]** タブを選びます。
2. **GetStatus** を選びます。 既定では、コード値は "*200*" に設定されます。 値を変更して他の値をテストできます。 たとえば、「*418*」と入力します。
3. **[送信]** をクリックします。

    "http://httpbin.org/status/200" 操作によって生成される応答が表示されます。 操作を変換する場合は、「[API を変換および保護する](transform-api.md)」をご覧ください。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [公開された API の変換と保護](transform-api.md)
