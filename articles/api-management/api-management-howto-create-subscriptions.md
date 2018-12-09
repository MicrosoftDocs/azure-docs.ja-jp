---
title: Azure API Management でサブスクリプションを作成する方法 | Microsoft Docs
description: Azure API Management でサブスクリプションを作成する方法について説明します。
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
ms.openlocfilehash: 3f4e113125ec9644aac974e47996afe290e57cee
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621738"
---
# <a name="how-to-create-subscriptions-in-azure-api-management"></a>Azure API Management でサブスクリプションを作成する方法

Azure API Management (APIM) を介して API を公開する場合、これらの API へのアクセスをセキュリティで保護する最も簡単で最も一般的な方法は、サブスクリプション キーを使用することです。 つまり、公開された API を使用する必要のあるクライアント アプリケーションには、このような API を呼び出すときに有効なサブスクリプション キーを HTTP 要求に含める必要があります。 API にアクセスするためのサブスクリプション キーを取得するには、サブスクリプションが必要です。 サブスクリプションの詳細については、「[Subscriptions in Azure API Management](api-management-subscriptions.md)」(Azure API Management のサブスクリプション) を参照してください。

この記事では、Azure portal でサブスクリプションを作成する手順について説明します。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、以下を実行する必要があります。

+ [APIM インスタンスを作成する](get-started-create-service-instance.md)
+ [APIM のサブスクリプションを理解する](api-management-subscriptions.md)

## <a name="create-a-new-subscription"></a>新しいサブスクリプションを作成する

1. 左側のメニューの **[サブスクリプション]** をクリックします
2. **[サブスクリプションの追加]** をクリックします
3. サブスクリプションの名前を指定し、範囲を選択します
4. **[保存]** をクリックします

![柔軟なサブスクリプション](./media/api-management-subscriptions/flexible-subscription.png)

サブスクリプションが作成されると、API にアクセスするための 1 組の API キー (プライマリとセカンダリ) がプロビジョニングされます。

## <a name="next-steps"></a>次の手順
API Management の詳細情報:

+ API Management の他の[概念](api-management-terminology.md)について学びます
+ [チュートリアル](import-and-publish.md)に従って API Management の詳細を学びます
+ [FAQ ページ](api-management-faq.md)で一般的な質問を確認します