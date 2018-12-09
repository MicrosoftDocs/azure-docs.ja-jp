---
title: Azure API Management のサブスクリプション | Microsoft Docs
description: Azure API Management のサブスクリプションの概念について説明します。
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 45a65c7a94f3e6917b2882f27c9ad7d764ef97d7
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621759"
---
# <a name="subscriptions-in-azure-api-management"></a>Azure API Management のサブスクリプション

サブスクリプションは、Azure API Management (APIM) の重要な概念です。 これは、API ユーザーが APIM インスタンスを介して公開されている API へのアクセス権を取得するための最も一般的な方法です。 この記事では、この概念の概要について説明します。

## <a name="what-is-subscriptions"></a>サブスクリプションとは

APIM を介して API を公開する場合、これらの API へのアクセスをセキュリティで保護する最も簡単で最も一般的な方法は、サブスクリプション キーを使用することです。 つまり、公開された API を使用する必要のある開発者は、このような API を呼び出すときに有効なサブスクリプション キーを HTTP 要求に含める必要があります。 そうしないと、呼び出しは APIM ゲートウェイによって直ちに拒否され、バックエンド サービスに転送されません。

API にアクセスするためのサブスクリプション キーを取得するには、サブスクリプションが必要です。 サブスクリプションは、基本的にサブスクリプション キーのペアの名前付きコンテナーです。 API の公開元の承認の有無にかかわらず、公開されている API を使用する必要がある開発者は、サブスクリプションを取得できます。 API の公開元は、API ユーザーの代理でサブスクリプションを直接作成することもできます。

> [!TIP]
> APIM は、[OAuth2.0](api-management-howto-protect-backend-with-aad.md)、[クライアント証明書](api-management-howto-mutual-certificates-for-clients.md)、[IP のホワイトリスト登録](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)など、API へのアクセスをセキュリティで保護するための他のメカニズムもサポートしています。

## <a name="scope-of-subscriptions"></a>サブスクリプションの範囲

サブスクリプションは、製品、すべての API、または個々の API というさまざまな範囲に関連付けることができます。

### <a name="subscriptions-for-a-product"></a>製品のサブスクリプション

従来、APIM のサブスクリプションは、常に 1 つの [API 製品](api-management-terminology.md)の範囲に関連付けられていました。 開発者は、開発者ポータルで製品の一覧を探し、使用する製品のサブスクリプション要求を送信します。 サブスクリプション要求が (自動的にまたは API パブリッシャーによって) 承認されると、開発者はそのキーを使用して製品内のすべての API にアクセスできます。

![製品のサブスクリプション](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> シナリオによっては、API の公開元はサブスクリプションを必要としない API 製品を公開することがあります。 Azure portal の製品の **[設定]** ページで **[サブスクリプションを要求する]** オプションをオフにすることができます。 その結果、API キーを使用せずに製品のすべての API にアクセスできるようになります。

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>すべての API または個々の API のサブスクリプション

> [!NOTE]
> 現在、この機能は API Management の従量課金レベルでのみ使用できます。

APIM の[従量課金](https://aka.ms/apimconsumptionblog)レベルを導入したときに、キー管理を合理化するためにいくつかの変更を行いました。 まず、すべての API と単一の API という 2 つのサブスクリプション範囲を追加しました。 サブスクリプションの範囲は、API 製品に限定されなくなりました。 最初に製品を作成して API を追加することなく、1 つの API (または APIM インスタンス内のすべての API) へのアクセスを許可するキーを作成できるようになりました。 さらに、各 APIM インスタンスには不変の全 API サブスクリプションが付属しているため、テスト コンソール内での API のテストとデバッグがより簡単に、わかりやすくなりました。

次に、APIM では "スタンドアロン" のサブスクリプションが許可されました。 サブスクリプションを開発者アカウントに関連付ける必要はなくなりました。 これは、1 つのサブスクリプションを複数の開発者またはチームで共有する場合などのシナリオで役立ちます。

最後に、API の公開元は Azure portal で[サブスクリプションを作成](api-management-howto-create-subscriptions.md)できるようになりました。

![柔軟なサブスクリプション](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>次の手順
API Management の詳細情報:

+ API Management の他の[概念](api-management-terminology.md)について学びます
+ [チュートリアル](import-and-publish.md)に従って API Management の詳細を学びます
+ [FAQ ページ](api-management-faq.md)で一般的な質問を確認します