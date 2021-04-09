---
title: Azure API Management で外部キャッシュを使用する | Microsoft Docs
description: Azure API Management で外部キャッシュを構成し、使用する方法について説明します。 外部キャッシュを使用すると、組み込みキャッシュに関するいくつかの制限事項を回避することができます。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: 6288a10e111e42629abf5e09b84a6a7791dcfe95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018224"
---
# <a name="use-an-external-redis-compatible-cache-in-azure-api-management"></a>Azure API Management で Redis と互換性がある外部キャッシュを使用する

Azure API Management では、組み込みのキャッシュを利用できるだけでなく、Redis と互換性がある外部キャッシュ (Azure Cache for Redis など) に応答をキャッシュすることもできます。

外部キャッシュを使用すると、組み込みキャッシュに関する次のいくつかの制限事項を回避することができます。

* API Management の更新時にキャッシュが定期的に消去されるのを避ける
* キャッシュの構成をより細かく制御する
* ご利用の API Management レベルより多くのデータをキャッシュする
* API Management の従量課金レベルでキャッシュを使用する
* [API Management セルフホステッド ゲートウェイ](self-hosted-gateway-overview.md)でキャッシュを有効にする

キャッシュの詳細については、「[API Management のキャッシュ ポリシー](api-management-caching-policies.md)」と「[Azure API Management のカスタム キャッシュ](api-management-sample-cache-by-key.md)」を参照してください。

![APIM にお客様のキャッシュを追加する](media/api-management-howto-cache-external/overview.png)

ここでは、次の内容について学習します。

> [!div class="checklist"]
> * API Management に外部キャッシュを追加する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

+ [Azure API Management インスタンスを作成する](get-started-create-service-instance.md)
+ [Azure API Management のキャッシュ](api-management-howto-cache.md)について理解する

## <a name="create-azure-cache-for-redis"></a><a name="create-cache"> </a>Azure Cache for Redis を作成する

このセクションでは、Azure 内に Azure Cache for Redis を作成する方法について説明します。 Azure の内部または外部に Azure Cache for Redis が既にある場合は、次のセクションに<a href="#add-external-cache">スキップ</a>できます。

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="deploy-redis-cache-to-kubernetes"></a><a name="create-cache"> </a> Redis Cache を Kubernetes にデプロイする

キャッシュの場合、セルフホステッド ゲートウェイは外部キャッシュのみに依存します。 キャッシュを有効なセルフホステッド ゲートウェイにし、依存するキャッシュを互いに近接させて、参照と保存の待機時間を最小限にする必要があります。 Redis Cache を同じ Kubernetes クラスターまたは近くの別のクラスターにデプロイすることをお勧めします。 Redis Cache を Kubernetes クラスターにデプロイする方法については、次の[リンク](https://github.com/kubernetes/examples/tree/master/guestbook)を参照してください。

## <a name="add-an-external-cache"></a><a name="add-external-cache"> </a>外部キャッシュの追加

Azure API Management に外部の Azure Cache for Redis を追加するには、次の手順に従います。

![Azure API Management に外部の Azure Cache for Redis を追加する方法が示されているスクリーンショット。](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> **[Use from]\(使用元\)** 設定では、構成されたキャッシュを使用する Azure リージョンまたはセルフホステッド ゲートウェイの場所を指定します。 **[Default]\(規定\)** として構成されているキャッシュは、特定の一致するリージョンまたは場所の値を持つキャッシュによって上書きされます。
>
> たとえば、API Management が米国東部、東南アジア、および西ヨーロッパのリージョンでホストされていて、2 つのキャッシュ (1 つは **既定**、もう 1 つは **東南アジア** 用) が構成されている場合、**東南アジア** の API Management ではそのリージョンのキャッシュが使用され、他の 2 つのリージョンでは **既定** のキャッシュ エントリが使用されます。

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>同じサブスクリプションから Azure Cache for Redis を追加する

1. Azure portal で API Management インスタンスを参照します。
2. 左側のメニューから **[External cache]\(外部キャッシュ\)** タブを選択します。
3. **[+ 追加]** ボタンをクリックします。
4. **[Cache instance]\(キャッシュ インスタンス\)** ドロップダウン フィールドでキャッシュを選択します。
5. **[既定]** を選択するか、**[Use from]\(使用元\)** ドロップダウン フィールドで目的のリージョンを指定します。
6. **[保存]** をクリックします。

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>現在の Azure サブスクリプションまたは一般の Azure の外部でホストされている Azure Cache for Redis を追加します

1. Azure portal で API Management インスタンスを参照します。
2. 左側のメニューから **[External cache]\(外部キャッシュ\)** タブを選択します。
3. **[+ 追加]** ボタンをクリックします。
4. **[Cache instance]\(キャッシュ インスタンス\)** ドロップダウン フィールドで **[カスタム]** を選択します。
5. **[既定]** を選択するか、**[Use from]\(使用元\)** ドロップダウン フィールドで目的のリージョンを指定します。
6. **[接続文字列]** フィールドで Azure Cache for Redis の接続文字列を指定します。
7. **[保存]** をクリックします。

### <a name="add-a-redis-cache-to-a-self-hosted-gateway"></a>セルフホステッド ゲートウェイに Redis Cache を追加する

1. Azure portal で API Management インスタンスを参照します。
2. 左側のメニューから **[External cache]\(外部キャッシュ\)** タブを選択します。
3. **[+ 追加]** ボタンをクリックします。
4. **[Cache instance]\(キャッシュ インスタンス\)** ドロップダウン フィールドで **[カスタム]** を選択します。
5. **[Use from]\(使用元\)** ドロップダウン フィールドで、目的のセルフホステッド ゲートウェイの場所を指定するか、 **[Default]\(規定\)** を指定します。
6. **[接続文字列]** フィールドで Redis Cache の接続文字列を指定します。
7. **[保存]** をクリックします。

## <a name="use-the-external-cache"></a>外部キャッシュの使用

Azure API Management で外部キャッシュが構成されたら、キャッシュ ポリシーを通じてそのキャッシュを使用できます。 詳しい手順については、「[キャッシュを追加して Azure API Management のパフォーマンスを向上させる](api-management-howto-cache.md)」をご覧ください。

## <a name="next-steps"></a><a name="next-steps"> </a>次の手順

* キャッシュ ポリシーの詳細については、[Azure API Management ポリシー リファレンス][Caching policies]の「[Caching policies (キャッシュ ポリシー)][API Management policy reference]」を参照してください。
* ポリシー式を使ってキーごとにアイテムをキャッシュする方法については、「 [Azure API Management のカスタム キャッシュ](api-management-sample-cache-by-key.md)」を参照してください。

[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-caching-policies.md
