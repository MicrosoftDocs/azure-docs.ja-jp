---
title: Azure CDN エンドポイント上のアセットを事前に読み込む | Microsoft Docs
description: Azure Content Delivery Network エンドポイントでキャッシュされたコンテンツを事前に読み込む方法について説明します。 この機能は特定のバージョンの製品で利用できます。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/12/2018
ms.author: allensu
ms.openlocfilehash: 186ded90b504420a2f315d054551d97821cf8465
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100385049"
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Azure CDN エンドポイント上の資産を事前に読み込む
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

既定では、アセットは要求された場合にのみキャッシュされます。 エッジ サーバーはまだコンテンツをキャッシュしておらず、要求を配信元サーバーに転送する必要があるため、各リージョンからの最初の要求は、後続の要求よりも時間がかかることがあります。 この初回待ち時間をなくすには、アセットを事前に読み込みます。 キャッシュされたアセットを事前に読み込んでおくと、カスタマー エクスペリエンスが向上するだけでなく、配信元サーバーのネットワーク トラフィックを減少させることもできます。

> [!NOTE]
> 資産の事前読み込みは、大規模なイベントや多数のユーザーが同時に利用できるコンテンツで役立ちます。たとえば、新作映画の公開やソフトウェアの更新などがあります。
> 
> 

このチュートリアルでは、すべての Azure CDN エッジ ノード上のキャッシュされたコンテンツを事前に読み込む方法について説明します。

## <a name="to-pre-load-assets"></a>アセットを事前に読むには
1. [Azure Portal](https://portal.azure.com) で、事前に読み込むエンドポイントを含む CDN プロファイルを参照します。 プロファイルのウィンドウが開きます。
    
2. リスト内のエンドポイントをクリックします。 エンドポイントのウィンドウが開きます。
3. CDN エンドポイント ウィンドウで、**[読み込み]** を選択します。
   
    ![CDN エンドポイント ウィンドウ](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    **[読み込み]** ウィンドウが開きます。
   
    ![CDN 読み込みウィンドウ](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. **[コンテンツ パス]** で、読み込む各アセットの完全なパスを入力します (たとえば、`/pictures/kitten.png`)。
   
   > [!TIP]
   > テキストの入力を開始すると、**[コンテンツ パス]** テキスト ボックスが追加されるため、複数のアセットの一覧を作成できます。 一覧からアセットを削除するには、省略記号 (...) ボタンを選択し、**[削除]** を選択します。
   > 
   > 各コンテンツ パスは、次の[正規表現](/dotnet/standard/base-types/regular-expression-language-quick-reference)に準拠する相対 URL にする必要があります。  
   > - 1 つのファイル パスの読み込み: `^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - クエリ文字列を使用した 1 つのファイルの読み込み: `^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > 各アセットは独自のパスを持つ必要があるため、アセットの事前読み込みにワイルドカードの機能はありません。
   > 
   > 
   
    ![[読み込み] ボタン](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. コンテンツ パスの入力が終わったら、**[読み込み]** を選択します。
   

> [!NOTE]
> CDN プロファイルごとに 1 分あたり 10 個の読み込み要求という制限があり、同時に処理することができるパスは 50 個です。 各パスの、パスの長さの上限値は 1024 文字です。
> 
> 

## <a name="see-also"></a>関連項目
* [Azure CDN エンドポイントの消去](cdn-purge-endpoint.md)
* [Azure CDN REST API リファレンス: エンドポイント上のコンテンツの事前読み込み](/rest/api/cdn/cdn/endpoints/loadcontent)
* [Azure CDN REST API リファレンス: エンドポイントからのコンテンツの消去](/rest/api/cdn/cdn/endpoints/purgecontent)