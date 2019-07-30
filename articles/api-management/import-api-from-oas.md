---
title: Azure Portal を使用して OpenAPI 仕様をインポートする | Microsoft Docs
description: API Management で OpenAPI 仕様をインポートする方法について説明します。
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
ms.date: 07/12/2019
ms.author: apimpm
ms.openlocfilehash: a2d71330dcdac7054b20e0a6222b542fd8815705
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849563"
---
# <a name="import-an-openapi-specification"></a>OpenAPI 仕様のインポート

この記事では、 https://conferenceapi.azurewebsites.net?format=json に存在する "OpenAPI の仕様" のバックエンド API をインポートする方法を示します。 このバックエンド API は Microsoft によって提供され、Azure でホストされています。 また、APIM API をテストする方法についても説明します。

> [!IMPORTANT]
> OpenAPI のインポートに関する重要な情報とヒントについては、この[ドキュメント](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/)を参照してください。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * "OpenAPI の仕様" バックエンド API のインポート
> * Azure Portal での API のテスト
> * 開発者ポータルでの API のテスト

## <a name="prerequisites"></a>前提条件

次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>バックエンド API のインポートと公開

1. **[API Management]** で **[API]** を選びます。
2. **[Add a new API]\(新しい API の追加\)** の一覧から **[OpenAPI の仕様]** を選択します。
    ![OpenAPI の仕様](./media/import-api-from-oas/oas-api.png)
3. 適切な設定を入力します。 作成時に API のすべての値を設定できます。 また、 **[設定]** タブに移動することで、一部の値を後で設定することもできます。 <br/> **Tab** キーを押すと、一部 (またはすべて) のフィールドに、指定したバックエンド サービスの情報が入力されます。

    ![API の作成](./media/api-management-get-started/create-api.png)

    |Setting|値|説明|
    |---|---|---|
    |**OpenAPI の仕様**|https://conferenceapi.azurewebsites.net?format=json|API を実装しているサービスを参照します。 要求は、API Management によってこのアドレスに転送されます。|
    |**[表示名]**|*Demo Conference API\(デモ会議 API\)*|サービス URL の入力後に Tab キーを押すと、json の内容に基づいてこのフィールドに値が入力されます。 <br/>この名前は開発者ポータルに表示されます。|
    |**Name**|*demo-conference-api*|API の一意の名前を指定します。 <br/>サービス URL の入力後に Tab キーを押すと、json の内容に基づいてこのフィールドに値が入力されます。|
    |**説明**|API の任意の説明を指定します。|サービス URL の入力後に Tab キーを押すと、json の内容に基づいてこのフィールドに値が入力されます。|
    |**API URL サフィックス**|*conference*|サフィックスは、API Management サービスのベース URL に付加されます。 API Management では API がサフィックスによって識別されるため、サフィックスは、特定の発行者のすべての API で一意である必要があります。|
    |**[URL スキーム]**|*HTTPS*|API へのアクセスに使用できるプロトコルを決定します。 |
    |**成果物**|*無制限*| API を成果物に関連付けることで API を公開します。 必要に応じてこの新しい API を成果物に追加するには、成果物名を入力します。 この手順を複数回繰り返して、API を複数の成果物に追加できます。<br/>成果物には、1 つまたは複数の API が関連付けられています。 複数の API を組み込み、開発者ポータルを通じてそれらを開発者に提供できます。 開発者は、まず成果物をサブスクライブして API へのアクセス権を取得する必要があります。 サブスクライブすると、その成果物の API に適したサブスクリプション キーを受け取ります。 APIM インスタンスを作成した場合は、既に管理者になっているため、既定ですべての製品をサブスクライブしています。<br/> すべての API Management インスタンスは、2 つのサンプル成果物を既定で備えています。**スターター**と**無制限**。 |

4. **作成** を選択します。

> [!NOTE]
> API インポートの制限事項については、[別の記事](api-management-api-import-restrictions.md)で説明されています。

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Azure Portal での新しい APIM API のテスト

Azure Portal には、API の操作を表示およびテストするための便利な環境が用意されており、操作を直接呼び出すことができます。

![API のテスト](./media/api-management-get-started/01-import-first-api-01.png)

1. 前の手順で作成した API を選びます。
2. **[テスト]** タブをクリックします。
3. **[GetSpeakers]** をクリックします。

    ページにはクエリ パラメーターのフィールドが表示されますが、この例では何もありません。 ページには、ヘッダーのフィールドも表示されます。 この API に関連付けられている成果物のサブスクリプション キーの場合、ヘッダーの 1 つは "Ocp-Apim-Subscription-Key" です。 APIM インスタンスを作成した場合は、既に管理者になっているので、キーが自動的に入力されます。
4. **[送信]** をクリックします。

    バックエンドは "**200 OK**" といくつかのデータで応答します。

## <a name="call-operation"> </a>開発者ポータルからの操作の呼び出し

操作を**開発者ポータル**から呼び出して API をテストすることもできます。

1. "バックエンド API のインポートと公開" の手順で作成した API を選びます。
2. **[開発者ポータル]** をクリックします。

    ![開発者ポータルでのテスト](./media/api-management-get-started/developer-portal.png)

    "開発者ポータル" サイトが開きます。
3. **[API]** を選択します。
4. **[Demo Conference API]\(デモ会議 API)** を選択します。
5. **[GetSpeakers]** をクリックします。

    ページにはクエリ パラメーターのフィールドが表示されますが、この例では何もありません。 ページには、ヘッダーのフィールドも表示されます。 この API に関連付けられている成果物のサブスクリプション キーの場合、ヘッダーの 1 つは "Ocp-Apim-Subscription-Key" です。 APIM インスタンスを作成した場合は、既に管理者になっているので、キーが自動的に入力されます。
6. **[テスト]** をクリックします。
7. **[送信]** をクリックします。

    操作を呼び出すと、**応答のステータス**、**応答ヘッダー**、**応答内容**が開発者ポータルに表示されます。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [公開された API の変換と保護](transform-api.md)
