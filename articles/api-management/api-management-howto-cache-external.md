---
title: Azure API Management で外部キャッシュを使用する | Microsoft Docs
description: Azure API Management で外部キャッシュを構成し、使用する方法について説明します。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: 8e3f34210627fbb455a1eca0b415cdd6de9b3681
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407625"
---
# <a name="use-an-external-azure-cache-for-redis-in-azure-api-management"></a>Azure API Management で外部の Azure Cache for Redis を使用する

Azure API Management では、組み込みのキャッシュを利用できるだけでなく、外部の Azure Cache for Redis に応答をキャッシュすることもできます。

外部キャッシュを使用すると、組み込みキャッシュに関するいくつかの制限事項を回避することができます。 これは、次の目的がある場合は特に便利です。

* API Management の更新時にキャッシュが定期的に消去されるのを避ける
* キャッシュの構成をより細かく制御する
* ご利用の API Management レベルより多くのデータをキャッシュする
* API Management の従量課金レベルでキャッシュを使用する

キャッシュの詳細については、「[API Management のキャッシュ ポリシー](api-management-caching-policies.md)」と「[Azure API Management のカスタム キャッシュ](api-management-sample-cache-by-key.md)」を参照してください。

![APIM にお客様のキャッシュを追加する](media/api-management-howto-cache-external/overview.png)

ここでは、次の内容について学習します。

> [!div class="checklist"]
> * API Management に外部キャッシュを追加する

## <a name="availability"></a>可用性

> [!NOTE]
> この機能は現在、Azure API Management の**従量課金**レベルでのみ使用できます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

+ [Azure API Management インスタンスを作成する](get-started-create-service-instance.md)
+ [Azure API Management のキャッシュ](api-management-howto-cache.md)について理解する

## <a name="create-cache"> </a> Azure Cache for Redis を作成する

このセクションでは、Azure 内に Azure Cache for Redis を作成する方法について説明します。 Azure の内部または外部に Azure Cache for Redis が既にある場合は、次のセクションに<a href="#add-external-cache">スキップ</a>できます。

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="add-external-cache"> </a>外部キャッシュの追加

Azure API Management に外部の Azure Cache for Redis を追加するには、次の手順に従います。

![APIM にお客様のキャッシュを追加する](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> **[Use from]\(使用元\)** 設定では、API Management がマルチリージョン構成である場合に、API Management のどのリージョンのデプロイが構成済みのキャッシュと通信するのかを指定します。 **既定**として指定されたキャッシュは、リージョンの値を使ったキャッシュによって上書きされます。
>
> たとえば、API Management が米国東部、東南アジア、および西ヨーロッパのリージョンでホストされていて、2 つのキャッシュ (1 つは**既定**、もう 1 つは**東南アジア**用) が構成されている場合、**東南アジア**の API Management ではそのリージョンのキャッシュが使用され、他の 2 つのリージョンでは**既定**のキャッシュ エントリが使用されます。

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>同じサブスクリプションから Azure Cache for Redis を追加する

1. Azure portal で API Management インスタンスを参照します。
2. 左側のメニューから **[External cache]\(外部キャッシュ\)** タブを選択します。
3. **[+ 追加]** ボタンをクリックします。
4. **[Cache instance]\(キャッシュ インスタンス\)** ドロップダウン フィールドでキャッシュを選択します。
5. **[既定]** を選択するか、**[Use from]\(使用元\)** ドロップダウン フィールドで目的のリージョンを指定します。
6. **[Save]** をクリックします。

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>現在の Azure サブスクリプションまたは一般の Azure の外部でホストされている Azure Cache for Redis を追加します

1. Azure portal で API Management インスタンスを参照します。
2. 左側のメニューから **[External cache]\(外部キャッシュ\)** タブを選択します。
3. **[+ 追加]** ボタンをクリックします。
4. **[Cache instance]\(キャッシュ インスタンス\)** ドロップダウン フィールドで **[カスタム]** を選択します。
5. **[既定]** を選択するか、**[Use from]\(使用元\)** ドロップダウン フィールドで目的のリージョンを指定します。
6. **[接続文字列]** フィールドで Azure Cache for Redis の接続文字列を指定します。
7. **[Save]** をクリックします。

## <a name="use-the-external-cache"></a>外部キャッシュの使用

Azure API Management で外部キャッシュが構成されたら、キャッシュ ポリシーを通じてそのキャッシュを使用できます。 詳しい手順については、「[キャッシュを追加して Azure API Management のパフォーマンスを向上させる](api-management-howto-cache.md)」をご覧ください。

## <a name="next-steps"> </a>次のステップ
* キャッシュ ポリシーの詳細については、[API Management ポリシー リファレンス][API Management policy reference]の[キャッシュ ポリシー][Caching policies]に関するページを参照してください。
* ポリシー式を使ってキーごとにアイテムをキャッシュする方法については、「 [Azure API Management のカスタム キャッシュ](api-management-sample-cache-by-key.md)」を参照してください。

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
