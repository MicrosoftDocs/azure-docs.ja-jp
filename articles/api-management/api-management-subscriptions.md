---
title: Azure API Management のサブスクリプション | Microsoft Docs
description: Azure API Management のサブスクリプションの概念について説明します。 コンシューマーは、Azure API Management のサブスクリプションを使用して API にアクセスします。
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: cdc7668b06308bd5532f4885d0e1365fc34ae989
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97511248"
---
# <a name="subscriptions-in-azure-api-management"></a>Azure API Management のサブスクリプション

サブスクリプションは、Azure API Management の重要な概念です。 これは、API ユーザーが API Management インスタンスを介して公開されている API へのアクセス権を取得するための最も一般的な方法です。 この記事では、この概念の概要について説明します。

## <a name="what-are-subscriptions"></a>サブスクリプションとは

API Management を介して API を公開する場合、これらの API へのアクセスをセキュリティで保護する簡単で一般的な方法は、サブスクリプション キーを使用することです。 公開された API を使用する必要がある開発者は、これらの API を呼び出すときに、有効なサブスクリプション キーを HTTP 要求に含める必要があります。 そうしないと、呼び出しは API Management ゲートウェイによってすぐに拒否されます。 それらはバックエンド サービスに転送されません。

API にアクセスするためのサブスクリプション キーを取得するには、サブスクリプションが必要です。 サブスクリプションは、基本的にサブスクリプション キーのペアの名前付きコンテナーです。 公開されている API を使用する必要がある開発者は、サブスクリプションを取得できます。 API の公開元による承認は必要ありません。 API の公開元は、API ユーザー用のサブスクリプションを直接作成することもできます。

> [!TIP]
> API Management では、API へのアクセスをセキュリティで保護するための他のメカニズムもサポートしています。たとえば、以下があります。
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [クライアント証明書](api-management-howto-mutual-certificates-for-clients.md)
> - [呼び出し元 IP を制限する](./api-management-access-restriction-policies.md#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>サブスクリプションの範囲

サブスクリプションは、製品、すべての API、または個々の API というさまざまな範囲に関連付けることができます。

### <a name="subscriptions-for-a-product"></a>製品のサブスクリプション

従来、API Management のサブスクリプションは、常に 1 つの [API 製品](api-management-terminology.md)の範囲に関連付けられていました。 開発者は、開発者ポータルで製品の一覧を確認していました。 その後、使用する製品のサブスクリプション要求を送信します。 サブスクリプション要求が (自動的にまたは API の公開元によって) 承認されると、開発者はそのキーを使用して製品内のすべての API にアクセスできます。 現時点では、製品をスコープとするサブスクリプションは、開発者ポータルのユーザー プロファイル セクションにのみ表示されます。 

![製品のサブスクリプション](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> シナリオによっては、API の公開元は、サブスクリプションを必要としない API 製品を公開することがあります。 Azure portal の製品の **[設定]** ページで **[サブスクリプションを要求する]** オプションをオフにすることができます。 その結果、API キーを使用せずに製品のすべての API にアクセスできるようになります。

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>すべての API または個々の API のサブスクリプション

API Management の[従量課金](https://aka.ms/apimconsumptionblog)レベルの導入時に、キー管理を合理化するためにいくつかの変更が行われました。
- まず、すべての API と単一の API という 2 つのサブスクリプション範囲が追加されました。 サブスクリプションの範囲は、API 製品に限定されなくなりました。 1 つの API (または API Management インスタンス内のすべての API) へのアクセスを許可するキーを作成できるようになりました。先に製品を作成して API を追加しておく必要はありません。 さらに、各 API Management インスタンスには、不変の全 API サブスクリプションが付属するようになりました。 このサブスクリプションにより、テスト コンソール内での API のテストとデバッグがより簡単に、わかりやすくなりました。

- 次に、API Management で、**スタンドアロン** のサブスクリプションが許可されるようになりました。 サブスクリプションを開発者アカウントに関連付ける必要はなくなりました。 この機能は、1 つのサブスクリプションを複数の開発者またはチームで共有する場合などのシナリオで役立ちます。

- 最後に、API の公開元は、Azure portal で直接[サブスクリプションを作成](api-management-howto-create-subscriptions.md)できるようになりました。

    ![柔軟なサブスクリプション](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>次の手順
API Management の詳細情報:

+ API Management の他の[概念](api-management-terminology.md)を確認します。
+ [チュートリアル](import-and-publish.md)に従って API Management の詳細を確認します。
+ [FAQ ページ](api-management-faq.md)で一般的な質問を確認します。
