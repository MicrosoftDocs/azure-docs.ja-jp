---
title: キャッシュ規則で Azure CDN キャッシュの動作を制御する | Microsoft Docs
description: CDN のキャッシュ規則を使って、グローバルと、URL パスやファイル拡張子などの条件付きの両方で、既定のキャッシュ期限切れ動作を設定または変更することができます。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/19/2019
ms.author: allensu
ms.openlocfilehash: a5f4f6a6e72b57638688069111071a6e0a035c49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018666"
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>キャッシュ規則で Azure CDN キャッシュの動作を制御する

> [!NOTE] 
> キャッシュ規則は、**Azure CDN Standard from Verizon** および **Azure CDN Standard from Akamai** プロファイルでのみ使用できます。 **Microsoft の Azure CDN** プロファイルの場合は、[Standard ルール エンジン](cdn-standard-rules-engine-reference.md)を使用する必要があります。**Azure CDN Premium from Verizon** プロファイルの場合は、同様の機能に対して **管理** ポータルで [Verizon Premium ルール エンジン](./cdn-verizon-premium-rules-engine.md)を使用する必要があります。
 
Azure Content Delivery Network (CDN) には、ファイルのキャッシュ方法を制御する方法が 2 つあります。 

- キャッシュ規則: この記事で説明する方法であり、コンテンツ配信ネットワーク (CDN) のキャッシュ規則を使って、グローバルと、URL パスやファイル拡張子などのカスタム条件の両方で、既定のキャッシュ期限切れ動作を設定または変更することができます。 Azure CDN では、2 種類のキャッシュ規則が提供されます。

   - グローバル キャッシュ規則:プロファイルのエンドポイントごとに 1 つのグローバル キャッシュ規則を設定することができます。この規則は、エンドポイントに対するすべての要求に適用されます。 HTTP キャッシュ ディレクティブ ヘッダーが設定されている場合、グローバル キャッシュ規則はそれをオーバーライドします。

   - カスタム キャッシュ規則:プロファイルのエンドポイントごとに、1 つ以上のカスタム キャッシュ規則を設定できます。 カスタム キャッシュ規則は、特定のパスおよびファイル拡張子と一致し、順番に処理され、グローバル キャッシュ規則が設定されている場合はそれをオーバーライドします。 

- クエリ文字列キャッシュ: Azure CDN が要求のキャッシュを処理する方法を、クエリ文字列で調整することができます。 詳しくは、「[クエリ文字列による Azure CDN キャッシュ動作の制御](cdn-query-string.md)」をご覧ください。 ファイルがキャッシュ可能でない場合は、クエリ文字列のキャッシュ設定に効果はなく、キャッシュ規則と CDN の既定の動作に基づきます。

既定のキャッシュ動作およびキャッシュ ディレクティブ ヘッダーについては、「[キャッシュのしくみ](cdn-how-caching-works.md)」をご覧ください。 


## <a name="accessing-azure-cdn-caching-rules"></a>Azure CDN のキャッシュ規則へのアクセス

1. Azure Portal を開き、CDN プロファイルを選んで、エンドポイントを選びます。

2. 左側のウィンドウの [設定] で、 **[キャッシュ規則]** を選択します。

   ![CDN の [キャッシュ規則] ボタン](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   **[キャッシュ規則]** ページが表示されます。

   ![CDN の [キャッシュ規則] ページ](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>キャッシュ動作の設定
グローバル キャッシュ規則およびカスタム キャッシュ規則に対しては、次の **キャッシュ動作** の設定を指定できます。

- **[キャッシュのバイパス]**: キャッシュを行わず、もともと指定されているキャッシュ ディレクティブ ヘッダーを無視します。

- **オーバーライド**: もともと指定されているキャッシュ期間を無視し、代わりに指定したキャッシュ期間を使います。 これは、cache-control の no-cache をオーバーライドしません。

- **[存在しない場合に設定]**: キャッシュ ディレクティブ ヘッダーがもともと指定されていた場合はそれに従い、指定されていなかった場合は、設定したキャッシュ期間を使います。

![グローバル キャッシュ ルール](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![カスタム キャッシュ ルール](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>キャッシュの有効期間
グローバル キャッシュ規則とカスタム キャッシュ規則では、日数、時間、分、および秒でキャッシュの有効期間を指定できます。

- **[オーバーライド]** と **[存在しない場合に設定] の** **キャッシュ動作** 設定の場合、有効なキャッシュ期間は 0 秒から 366 日の範囲です。 値が 0 秒の場合は、CDN はコンテンツをキャッシュしますが、要求ごとに送信元のサーバーを再検証する必要があります。

- **[キャッシュのバイパス]** 設定の場合は、キャッシュ期間は自動的に 0 秒に設定されて、変更できません。

## <a name="custom-caching-rules-match-conditions"></a>カスタム キャッシュ規則の一致条件

カスタム キャッシュ規則では、次の 2 つの一致条件を使うことができます。
 
- **[パス]**: この条件は、ドメイン名を除く URL のパスと一致し、ワイルドカード文字 (\*) をサポートしています。 たとえば、_/myfile.html_、_/my/folder/*_、_/my/images/*.jpg_ です。 最大長は 260 文字です。

- **[拡張子]**: この条件は、要求されたファイルのファイル拡張子と一致します。 コンマで区切ってファイル拡張子のリストを指定できます。 たとえば、_.jpg_、_.mp3_、または _.png_ です。 拡張子の最大数は 50、各拡張子の最大文字数は 16 です。 

## <a name="global-and-custom-rule-processing-order"></a>グローバル規則とカスタム規則の処理順序
グローバル キャッシュ規則とカスタム キャッシュ規則は、次の順序で処理されます。

- 既定の CDN キャッシュ動作 (HTTP キャッシュ ディレクティブ ヘッダーの設定) より、グローバル キャッシュ規則の方が優先されます。 

- グローバル キャッシュ規則より、カスタム キャッシュ規則の方が優先されます (同じ適用対象について)。 カスタム キャッシュ規則は上から下へ順番に処理されます。 つまり、要求が両方の条件と一致する場合、一覧で上にある規則より下にある規則の方が優先されます。 したがって、固有性の高い規則ほど一覧の下に置く必要があります。

**例**:
- グローバル キャッシュ規則: 
   - キャッシュ動作: **オーバーライド**
   - キャッシュの有効期間: 1 日

- カスタム キャッシュ規則 1:
   - 一致条件: **パス**
   - 一致する値: _/home/*_
   - キャッシュ動作: **オーバーライド**
   - キャッシュの有効期間: 2 日

- カスタム キャッシュ規則 2:
   - 一致条件: **拡張子**
   - 一致する値: _.html_
   - キャッシュ動作: **存在しない場合に設定**
   - キャッシュの有効期間: 3 日

これらの規則が設定されている場合、 _&lt;endpoint hostname&gt;_ .azureedge.net/home/index.html に対する要求は、**存在しない場合に設定** および 3 日に設定されているカスタム キャッシュ規則 2 をトリガーします。 したがって、*index.html* ファイルに `Cache-Control` または `Expires` HTTP ヘッダーがある場合はそれらが適用され、これらのヘッダーが設定されていない場合は、ファイルは 3 日間キャッシュされます。

> [!NOTE] 
> 規則が変更される前にキャッシュされたファイルは、元のキャッシュ期間の設定のままになります。 キャッシュ期間をリセットするには、[ファイルを消去する](cdn-purge-endpoint.md)必要があります。 
>
> Azure CDN 構成の変更がネットワーク全体に反映されるまでには時間がかかる可能性があります。 
> - **Azure CDN Standard from Akamai** プロファイルの場合、通常、反映は 1 分以内で完了します。 
> - **Azure CDN Standard from Verizon** プロファイルの場合、通常、反映は 10 分で完了します。  
>

## <a name="see-also"></a>関連項目

- [キャッシュのしくみ](cdn-how-caching-works.md)
- [チュートリアル:Azure CDN キャッシュ規則の設定](cdn-caching-rules-tutorial.md)