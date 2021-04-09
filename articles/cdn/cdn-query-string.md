---
title: クエリ文字列による Azure CDN キャッシュ動作の制御 - Standard レベル
description: Azure CDN クエリ文字列のキャッシュにより、Web 要求にクエリ文字列が含まれる場合のファイルのキャッシュ方法を制御します。 この記事では、Azure CDN Standard 製品でのクエリ文字列のキャッシュについて説明します。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/11/2018
ms.author: allensu
ms.openlocfilehash: 1521d08ef9d431bbe8b3fd3a578297d440ed56b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018581"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>クエリ文字列による Azure CDN キャッシュ動作の制御 - Standard レベル
> [!div class="op_single_selector"]
> * [Standard レベル](cdn-query-string.md)
> * [Premium レベル](cdn-query-string-premium.md)
> 

## <a name="overview"></a>概要
Azure コンテンツ配信ネットワーク (CDN) を使用すると、クエリ文字列を含む Web 要求のためにファイルをキャッシュする方法を制御できます。 クエリ文字列を含む Web 要求で、クエリ文字列は要求の疑問符 (?) の後に指定されます。 クエリ文字列には、フィールド名とその値を等号 (=) で区切って指定される、キーと値のペア (複数可) を含めることができます。 キーと値のペアはそれぞれ、アンパサンド (&) で区切られます。 たとえば、http:\//www.contoso.com/content.mov?field1=value1&field2=value2 のようになります。 要求のクエリ文字列にキーと値のペアを複数指定する場合、どのような順序で指定してもかまいません。 

> [!IMPORTANT]
> Azure CDN の Standard および Premium 製品では同じクエリ文字列キャッシュ機能が提供されますが、ユーザー インターフェイスは異なります。 この記事では、**Azure CDN Standard from Microsoft**、**Azure CDN Standard from Akamai**、および **Azure CDN Standard from Verizon** のインターフェイスについて説明します。 **Azure CDN Premium from Verizon** でのクエリ文字列のキャッシュについては、「[クエリ文字列による Azure CDN キャッシュ動作の制御 - Premium レベル](cdn-query-string-premium.md)」をご覧ください。

次の 3 つのクエリ文字列モードを使用できます。

- **クエリ文字列を無視**: 既定モード。 このモードでは、CDN Point-of-Presence (POP) ノードは、クエリ文字列を要求元から最初の要求の配信元サーバーに渡して、資産をキャッシュします。 POP から提供される資産の後続の要求はすべて、キャッシュされた資産の有効期限が切れるまで、クエリ文字列を無視します。

- **クエリ文字列のキャッシュをバイパス**: このモードでは、クエリ文字列のある要求は CDN POP ノードでキャッシュされません。 POP ノードは配信元サーバーから直接資産を取得し、それを各要求により要求元に渡します。

- **一意の URL をすべてキャッシュ**: このモードでは、クエリ文字列を含む一意の URL が指定された各要求は、独自のキャッシュがある一意の資産として扱われます。 たとえば、example.ashx?q=test1 の要求の配信元サーバーからの応答は POP ノードでキャッシュされ、後続のキャッシュではその同じクエリ文字列により返されます。 example.ashx?q=test2 の要求は、独自の有効期限設定を持つ個別の資産としてキャッシュされます。
   
    >[!IMPORTANT] 
    > セッション ID やユーザー名など、要求ごとに変わるパラメーターがクエリ文字列に含まれる場合は、キャッシュ ヒット率が低くなるので、このモードを使わないでください。

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Standard CDN プロファイル用にクエリ文字列のキャッシュ設定を変更する
1. CDN プロファイルを開き、管理する CDN エンドポイントを選択します。
   
   ![CDN プロファイル エンドポイント](./media/cdn-query-string/cdn-endpoints.png)
   
2. 左側のウィンドウの [設定] で、**[キャッシュ規則]** をクリックします。
   
    ![CDN の [キャッシュ規則] ボタン](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. **[クエリ文字列のキャッシュ動作]** 一覧でクエリ文字列モードを選択し、**[保存]** をクリックします。
   
   ![CDN クエリ文字列のキャッシュ オプション](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> 登録内容が Azure CDN に反映されるまで時間がかかるため、キャッシュ文字列設定の変更がすぐに表示されないことがあります。
> - **Azure CDN Standard from Microsoft** プロファイルの場合、通常、反映は 10 分以内で完了します。 
> - **Azure CDN Standard from Akamai** プロファイルの場合、通常、反映は 1 分以内で完了します。 
> - **Azure CDN Standard from Verizon** プロファイルおよび **Azure CDN Premium from Verizon** プロファイルの場合、通常、反映は 10 分で完了します。 



