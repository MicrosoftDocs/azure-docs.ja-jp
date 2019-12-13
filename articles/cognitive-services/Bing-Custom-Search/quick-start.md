---
title: クイック スタート:最初の Bing Custom Search インスタンスの作成 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: この記事では、自分で定義したドメインや Web ページを検索できるカスタム Bing インスタンスを作成します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 2c98395e2671c245d9349a72ed6a964b2ebd28ca
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978545"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>クイック スタート:最初の Bing Custom Search インスタンスの作成

Bing Custom Search を使用するには、Web のビューまたはスライスを定義するカスタム検索のインスタンスを作成する必要があります。 このインスタンスには、検索するパブリック ドメイン、Web サイト、Web ページが、必要なランク付けの調整と共に含まれます。 

インスタンスを作成するには、[Bing Custom Search ポータル](https://customsearch.ai)を使用します。 

![Bing Custom Search ポータルの画像](media/blockedCustomSrch.png)

## <a name="prerequisites"></a>前提条件

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-a-custom-search-instance"></a>カスタム検索インスタンスの作成

Bing Custom Search インスタンスを作成するには:

1. [Bing Custom Search ポータル](https://customsearch.ai) Web ページの **[Get Started]\(作業の開始\)** をクリックして、お使いの Microsoft アカウントでサインインします。

2. **[新しいインスタンス]** をクリックして、わかりやすい名前を入力します。 インスタンスの名前はいつでも変更できます。
 
3. **[検索エクスペリエンス]** で **[アクティブ]** タブをクリックし、検索に含める 1 つまたは複数の Web サイトの URL を入力します。 

    > [!NOTE]
    > Bing Custom Search インスタンスから返されるのは、既に Bing によってインデックスが作成されているパブリックな Web ページおよびドメインの結果だけです。

4. Bing Custom Search ポータルの右側を使用して、クエリを入力したり、検索インスタンスから返された検索結果を観察したりすることができます。 まったく結果が返されない場合は、別の URL を入力してみてください。  

5. **[発行]** をクリックすると、変更内容が運用環境に発行され、インスタンスのエンドポイントが更新されます。

6.  **[エンドポイント]** の下の **[実稼働]** タブをクリックし、自分の**カスタム構成 ID** をコピーします。 Custom Search API を呼び出すには、この ID が必要となります。実際の呼び出しの `customconfig=` クエリ パラメーターにこの ID を追加します。


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [クイック スタート:Bing Custom Search エンドポイントを呼び出す](./call-endpoint-csharp.md)
