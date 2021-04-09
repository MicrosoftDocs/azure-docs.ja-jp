---
title: Azure Portal を使用して SOAP API をインポートする | Microsoft Docs
description: SOAP API の標準的な XML 表現をインポートし、この API を Azure portal と開発者ポータルでテストする方法について説明します。
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
ms.openlocfilehash: 81ded79ee72fb7c2d89898595602cb3e6d7ae5e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011066"
---
# <a name="import-soap-api"></a>SOAP API のインポート

この記事では、SOAP API の標準的な XML 表現をインポートする方法について説明します。 また、API Management API をテストする方法についても説明します。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * SOAP API のインポート
> * Azure Portal での API のテスト
> * 開発者ポータルでの API のテスト

## <a name="prerequisites"></a>前提条件

[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関するクイックスタートを完了します

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>バックエンド API のインポートと公開

1. Azure portal で API Management サービスに移動し、メニューから **[API]** を選択します。
2. **[Add a new API]\(新しい API の追加\)** の一覧から **[WSDL]** を選択します。

    ![SOAP API](./media/import-soap-api/wsdl-api.png)
3. **[WSDL 仕様]** で、SOAP API が存在する場所の URL を入力します。
4. **[SOAP パススルー]** が既定で選択されています。 この場合、API は SOAP として公開されます。 コンシューマーは SOAP 規則を使用する必要があります。 API を "復元・改良" する場合は、[SOAP API のインポートと REST への変換](restify-soap-api.md)に関する記事の手順に従います。

    ![スクリーンショットは、WSDL 仕様を入力できる [Create from WSDL]\(WSDL から作成\) ダイアログ ボックスを示しています。](./media/import-soap-api/pass-through.png)
5. Tab キーを押します。

    SOAP API からの情報が次のフィールドに入力されます: [表示名]、[名前]、[説明]。
6. API URL サフィックスを追加します。 サフィックスは、この API Management インスタンスでこの特定の API を識別する名前です。 この API Management インスタンス内で一意である必要があります。
7. API を成果物に関連付けることで API を公開します。 この場合、"*無制限*" の成果物が使用されます。  API を公開して開発者が利用できるようにするには、その API を成果物に追加します。 API の作成時に行うことも、後で設定することもできます。

    製品には、1 つまたは複数の API が関連付けられています。 複数の API を組み込み、開発者ポータルを通じてそれらを開発者に提供できます。 開発者は、まず製品をサブスクライブして API へのアクセス権を取得する必要があります。 サブスクライブすると、その製品の API に適したサブスクリプション キーを受け取ります。 API Management インスタンスを作成した場合は、既に管理者になっているため、既定ですべての製品をサブスクライブしています。

    すべての API Management インスタンスは、2 つのサンプル成果物を既定で備えています。

    * **スターター**
    * **無制限**   
8. 他の API 設定を入力します。 値は、作成時に設定することも、後で **[設定]** タブに移動して構成することもできます。設定については、「[最初の API のインポートと発行](import-and-publish.md#import-and-publish-a-backend-api)」のチュートリアルで説明されています。
9. **［作成］** を選択します

### <a name="test-the-new-api-in-the-administrative-portal"></a>管理ポータルでの新しい API のテスト

管理ポータルには、API の操作を表示およびテストするための便利な環境が用意されており、操作を直接呼び出すことができます。  

1. 前の手順で作成した API を選びます。
2. **[テスト]** タブをクリックします。
3. いくつかの操作を選びます。

    ページに、クエリ パラメーターのフィールドとヘッダーのフィールドが表示されます。 この API に関連付けられている成果物のサブスクリプション キーの場合、ヘッダーの 1 つは "Ocp-Apim-Subscription-Key" です。 API Management インスタンスを作成した場合は、既に管理者になっているので、キーが自動的に入力されます。 
1. **[送信]** をクリックします。

    バックエンドは **200 OK** といくつかのデータで応答します。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [公開された API の変換と保護](transform-api.md)