---
title: Azure API Management の 用語集 | Microsoft Docs
description: この記事では、API Management に固有の用語の定義について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/11/2017
ms.author: apimpm
ms.openlocfilehash: 002ae9f99865114dd8bf52b53efc9303a0706a82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99491826"
---
# <a name="azure-api-management-terminology"></a>Azure API Management の用語

この記事では、API Management (APIM) に固有の用語の定義について説明します。

## <a name="term-definitions"></a>用語の定義

* **バックエンド API** - API とその操作を実装する HTTP サービスです。 詳細については、[バックエンド](backends.md)に関するページを参照してください。
* **フロントエンド API**/**APIM API** - APIM API は API をホストしません。ご使用の API のファサードが作成されます。 ファサードは、バックエンド API に触れることなく、ニーズに合わせてカスタマイズできます。 詳細については、[API のインポートと発行](import-and-publish.md)に関する記事をご覧ください。
* **APIM 成果物** - 成果物には、少なくとも 1 つの API と、使用量クォータや使用条件が含まれます。 複数の API を含めて、開発者ポータルを通じてそれらを開発者に提供できます。 詳細については、[成果物の作成と発行](api-management-howto-add-products.md)に関する記事をご覧ください。
* **APIM API 操作** - 各 APIM API は、開発者が利用できる一連の操作を表します。 各 APIM API には、その API を実装しているバックエンド サービスへの参照が含まれています。API の操作は、バックエンド サービスによって実装されている操作にマッピングされます。 詳細については、[API のモック応答](mock-api-responses.md)に関する記事をご覧ください。
* **バージョン** - 新しい API 機能や異なる API 機能を一部のユーザーに公開する一方で、他のユーザーは現在役立っている API を引き続き使用することを望む場合があります。 詳細については、[複数のバージョンの API の公開](api-management-get-started-publish-versions.md)に関する記事をご覧ください。
* **リビジョン** - API の準備が整って開発者に使用され始めたら、その API に変更を加える際には、API の呼び出し元を混乱させないように注意する必要があります。 また、行った変更内容を開発者に知らせると有効です。 詳細については、[リビジョンの使用](api-management-get-started-revise-api.md)に関する記事をご覧ください。
* **開発者ポータル** - 顧客 (開発者) は、開発者ポータルを使用して API にアクセスする必要があります。 開発者ポータルはカスタマイズできます。 詳細については、[開発者ポータルのカスタマイズ](api-management-customize-styles.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [インスタンスの作成](get-started-create-service-instance.md)

