---
title: クエリ文字列による Azure CDN キャッシュ動作の制御 - Premium レベル
description: Azure CDN クエリ文字列のキャッシュにより、Web 要求にクエリ文字列が含まれる場合のファイルのキャッシュ方法を制御します。 この記事では、Azure CDN Premium from Verizon 製品でのクエリ文字列のキャッシュについて説明します。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/11/2018
ms.author: allensu
ms.openlocfilehash: a799309b6e5d00db3b6c206187eec7097c9dc11a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018598"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>クエリ文字列による Azure CDN キャッシュ動作の制御 - Premium レベル
> [!div class="op_single_selector"]
> * [Standard レベル](cdn-query-string.md)
> * [Premium レベル](cdn-query-string-premium.md)
> 

## <a name="overview"></a>概要
Azure コンテンツ配信ネットワーク (CDN) を使用すると、クエリ文字列を含む Web 要求のためにファイルをキャッシュする方法を制御できます。 クエリ文字列を含む Web 要求で、クエリ文字列は要求の疑問符 (?) の後に指定されます。 クエリ文字列には、フィールド名とその値を等号 (=) で区切って指定される、キーと値のペア (複数可) を含めることができます。 キーと値のペアはそれぞれ、アンパサンド (&) で区切られます。 たとえば、http:\//www.contoso.com/content.mov?field1=value1&field2=value2 のようになります。 要求のクエリ文字列にキーと値のペアを複数指定する場合、どのような順序で指定してもかまいません。 

> [!IMPORTANT]
> Standard および Premium CDN 製品では同じクエリ文字列キャッシュ機能が提供されますが、ユーザー インターフェイスは異なります。 ここでは、**Azure CDN Premium from Verizon** のインターフェイスについて説明します。 Azure CDN Standard 製品でのクエリ文字列のキャッシュについては、「[クエリ文字列による Azure CDN キャッシュ動作の制御 - Standard レベル](cdn-query-string.md)」を参照してください。
>


次の 3 つのクエリ文字列モードを使用できます。

- **standard-cache**: 既定のモードです。 このモードでは、CDN Point-of-Presence (POP) ノードは、クエリ文字列を要求元から最初の要求の配信元サーバーに渡して、資産をキャッシュします。 POP サーバーから提供される資産の後続の要求はすべて、キャッシュされた資産の有効期限が切れるまで、クエリ文字列を無視します。

    >[!IMPORTANT] 
    > このアカウントのいずれかのパスでトークン認証が有効になっている場合は、standard-cache モードだけを使うことができます。 

- **no-cache**: このモードでは、クエリ文字列のある要求は CDN POP ノードでキャッシュされません。 POP ノードは配信元サーバーから直接資産を取得し、それを各要求により要求元に渡します。

- **unique-cache**: このモードでは、クエリ文字列を含む一意の URL が指定された各要求は、独自のキャッシュがある一意の資産として扱われます。 たとえば、example.ashx?q=test1 の要求の配信元サーバーからの応答は POP ノードでキャッシュされ、後続のキャッシュではその同じクエリ文字列により返されます。 example.ashx?q=test2 の要求は、独自の有効期限設定を持つ個別の資産としてキャッシュされます。
   
    >[!IMPORTANT] 
    > セッション ID やユーザー名など、要求ごとに変わるパラメーターがクエリ文字列に含まれる場合は、キャッシュ ヒット率が低くなるので、このモードを使わないでください。

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Premium CDN プロファイル用にクエリ文字列のキャッシュ設定を変更する
1. CDN プロファイルを開き、**[管理]** をクリックします。
   
    ![[CDN プロファイル] の [管理] ボタン](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    CDN 管理ポータルが開きます。
2. **[HTTP Large]\(HTTP ラージ\)** タブ、**[キャッシュの設定]** フライアウトの順にマウスのカーソルを合わせます。 **[クエリ文字列のキャッシュ]** をクリックします。
   
    クエリ文字列のキャッシュ オプションが表示されます。
   
    ![CDN クエリ文字列のキャッシュ オプション](./media/cdn-query-string-premium/cdn-query-string.png)
3. クエリ文字列モードを選択し、**[更新]** をクリックします。

> [!IMPORTANT]
> 設定の変更が CDN に反映されるまでに時間がかかるので、変更がすぐに表示されないことがあります。 通常、反映は 10 分で完了します。
 

