---
title: Azure API Management でサブスクリプションを作成する | Microsoft Docs
description: Azure API Management でサブスクリプションを作成する方法について説明します。
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
ms.openlocfilehash: f8b2238eb0fab9aeeb42d11b4176c0d681b5f8e5
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073541"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Azure API Management でサブスクリプションを作成する

Azure API Management を介して API を公開する場合、これらの API へのアクセスをセキュリティで保護する最も簡単で最も一般的な方法は、サブスクリプション キーを使用することです。 公開された API を使用する必要のあるクライアント アプリケーションには、このような API を呼び出すときに有効なサブスクリプション キーを HTTP 要求に含める必要があります。 API にアクセスするためのサブスクリプション キーを取得するには、サブスクリプションが必要です。 サブスクリプションの詳細については、「[Subscriptions in Azure API Management](api-management-subscriptions.md)」(Azure API Management のサブスクリプション) を参照してください。

この記事では、Azure portal でサブスクリプションを作成する手順について説明します。

## <a name="prerequisites"></a>前提条件

この記事の手順を実行するための前提条件は次のとおりです。

+ [API Management インスタンスを作成する](get-started-create-service-instance.md)。
+ [API Management のサブスクリプション](api-management-subscriptions.md)を理解する。

## <a name="create-a-new-subscription"></a>新しいサブスクリプションを作成する

1. 左側のメニューで **[サブスクリプション]** を選択します。
2. **[サブスクリプションの追加]** を選択します。
3. サブスクリプションの名前を指定し、範囲を選択します。
4. 必要に応じて、サブスクリプションをユーザーに関連付けるかどうかを選択します。
5. **[保存]** を選択します。

![柔軟なサブスクリプション](./media/api-management-subscriptions/flexible-subscription.png)

サブスクリプションを作成した後、API にアクセスするために 2 つの API キーが提供されます。 一方のキーはプライマリで、他方のキーはセカンダリです。 

## <a name="next-steps"></a>次の手順
API Management の詳細情報:

+ API Management の他の[概念](api-management-terminology.md)を確認します。
+ [チュートリアル](import-and-publish.md)に従って API Management の詳細を確認します。
+ [FAQ ページ](api-management-faq.md)で一般的な質問を確認します。