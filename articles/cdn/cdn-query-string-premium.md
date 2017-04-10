---
title: "クエリ文字列による Azure CDN キャッシュ動作の制御 - Premium | Microsoft Docs"
description: "Azure CDN クエリ文字列のキャッシュにより、ファイルにクエリ文字列が含まれている場合のファイルのキャッシュ方法を制御します。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
translationtype: Human Translation
ms.sourcegitcommit: 57d00f2192fed7a2e89ac94e110ebb7e84c83b72
ms.openlocfilehash: 145067c2ce50b41c4783f4de4052c0e7cb529fc7


---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium"></a>クエリ文字列による Azure CDN キャッシュ動作の制御 - Premium
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [Azure CDN Premium from Verizon](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>概要
クエリ文字列のキャッシュにより、ファイルにクエリ文字列が含まれている場合のファイルのキャッシュ方法を制御します。

> [!IMPORTANT]
> Standard および Premium CDN 製品では同じクエリ文字列キャッシュ機能が提供されますが、ユーザー インターフェイスは異なります。  ここでは、 **Azure CDN Premium from Verizon**のインターフェイスについて説明します。  **Azure CDN Standard from Akamai** と **Azure CDN Standard from Verizon** のクエリ文字列のキャッシュについては、「[クエリ文字列による CDN 要求のキャッシュ動作の制御](cdn-query-string.md)」を参照してください。
> 
> 

次の&3; つのモードを使用できます。

* **standard-cache**: これは、既定のモードです。  CDN エッジ ノードは、クエリ文字列を要求元から最初の要求の配信元に渡して、資産をキャッシュします。  エッジ ノードから提供される資産の後続の要求はすべて、キャッシュされた資産の有効期限が切れるまで、クエリ文字列を無視します。
* **no-cache**: このモードでは、クエリ文字列のある要求は CDN エッジ ノードでキャッシュされません。  エッジ ノードは配信元から直接資産を取得し、それを各要求により要求元に渡します。
* **unique-cache**: このモードでは、クエリ文字列のある各要求は、独自のキャッシュを持つ一意の資産として処理されます。  たとえば、 *foo.ashx?q=bar* の要求の配信元からの応答はエッジ ノードでキャッシュされ、後続のキャッシュではその同じクエリ文字列により返されます。  *foo.ashx?q=somethingelse* の要求は、独自の有効期限を持つ個別の資産としてキャッシュされます。

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Premium CDN プロファイル用にクエリ文字列のキャッシュ設定を変更する
1. [CDN プロファイル] ブレードで、 **[管理]** をクリックします。
   
    ![[CDN プロファイル] ブレードの [管理] ボタン](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    CDN 管理ポータルが開きます。
2. **[HTTP Large (HTTP ラージ)]** タブ、**[キャッシュの設定]** フライアウトの順にマウスのカーソルを合わせます。  **[クエリ文字列のキャッシュ]**をクリックします。
   
    クエリ文字列のキャッシュ オプションが表示されます。
   
    ![CDN クエリ文字列のキャッシュ オプション](./media/cdn-query-string-premium/cdn-query-string.png)
3. 選択したら、 **[更新]** をクリックします。

> [!IMPORTANT]
> 設定の変更が CDN に反映されるまでに時間がかかるので、変更がすぐに表示されないことがあります。  <b>Azure CDN from Verizon</b> プロファイルの場合、通常、反映は 90 分以内に完了しますが、もっと時間がかかる場合もあります。
> 
> 




<!--HONumber=Jan17_HO4-->


