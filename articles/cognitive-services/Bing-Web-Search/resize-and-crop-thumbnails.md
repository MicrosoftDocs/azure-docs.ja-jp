---
title: 画像サムネイルのサイズを変更し、トリミングする - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Bing Search API からの回答には、Bing によって提供されるサムネイル イメージへの URL が含まれています。これは、サイズ変更とトリミングが可能で、クエリ パラメーターを含めることができます。
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: a85c5b2333418367742678a529b69c95164eda53
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96350485"
---
# <a name="resize-and-crop-thumbnail-images"></a>サムネイル画像のサイズ変更とトリミング

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

Bing Search API からの回答には、Bing によって提供されるサムネイル イメージへの URL が含まれています。これは、サイズ変更とトリミングが可能で、クエリ パラメーターを含めることができます。 次に例を示します。

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

これらのサムネイルのサブセットを表示する場合は、残りのイメージを表示するためのオプションを指定します。

> [!NOTE]
> Bing Search API の[使用要件と表示要件](use-display-requirements.md)で定められているように、サムネイル イメージのサイズ変更とトリミングでは、第三者の権利を尊重する検索シナリオを提供するようにしてください。

## <a name="resize-a-thumbnail"></a>サムネイルをサイズ変更する 

サムネイルのサイズを変更するために、Bing では、サムネイルの URL に `w` (幅) または `h` (高さ) のクエリ パラメーターを 1 つだけ指定することをお勧めします。 高さまたは幅のみを指定すると、Bing はイメージの元の縦横比を維持できます。 幅と高さはピクセル単位で指定します。 

たとえば、元のサムネイルが 480 x 620 の場合、次のようになります。

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

そして、サイズを小さくする場合は、`w` パラメーターを新しい値 (たとえば `336`) に設定して、`h` パラメーターを削除します。

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

サムネイルの高さまたは幅のみを指定すると、イメージの元の縦横比が維持されます。 両方のパラメーターが指定され、縦横比が維持されない場合、Bing はイメージの枠線に空白パディングを追加します。

たとえば、480 x 359 のイメージのサイズをトリミングせずに 200 x 200 に変更すると、イメージは幅いっぱいになりますが、高さではイメージの上下に 25 ピクセルの空白パディングが含まれます。 イメージが 359 x 480 の場合は、左右の枠線に空白パディングが含まれます。 イメージをトリミングする場合は、空白パディングが追加されません。  

次の画像は、サムネイル イメージの元のサイズ (480 x 300) を示しています。  
  
![元のイメージ (横)](./media/resize-crop/bing-resize-crop-landscape.png)  
  
次の画像は、200 x 200 にサイズ変更されたイメージを示しています。 縦横比が維持され、上下の枠線に空白が埋め込まれています (こちらの黒の枠線は、パディングを示すために含められています)。  
  
![サイズ変更されたイメージ (横)](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

イメージの元の幅と高さを超える大きさを指定した場合、Bing は左と上の枠線に空白パディングを追加します。  

## <a name="request-different-thumbnail-sizes"></a>異なるサムネイル サイズを要求する

異なるサムネイル イメージ サイズを要求するには、`id` パラメーターと `pid` パラメーターを除き、サムネイルの URL からすべてのクエリ パラメーターを削除します。 次に、ピクセルで希望するイメージ サイズを使って `&w` (幅) または `&h` (高さ) のクエリ パラメーターのいずれか (両方ではない) を追加します。 Bing で、イメージの元の縦横比が維持されます。 

上記の URL で指定されたイメージの幅を 165 ピクセルに増加するには、次の URL を使用します。

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

イメージの元のサイズよりも大きいイメージを要求すると、Bing は必要に応じてイメージの周りにパディングを追加します。 たとえば、イメージの元のサイズが 474 x 316 で、`&w` を 500 に設定した場合、Bing は 500 x 333 のイメージを返します。 このイメージには、上端と下端に 8.5 ピクセルの空白パディングがあり、左端と右端に 13 ピクセルのパディングがあります。

要求されたサイズがイメージの元のサイズより大きい場合に Bing が空白パディングを追加しないようにするには、`&p` クエリ パラメーターを 0 に設定します。 たとえば、上記の URL に `&p=0` パラメーターを含めると、Bing は 500 x 333 のイメージではなく、474 x 316 のイメージを返します。

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

`&w` と `&h` の両方のクエリ パラメーターを指定すると、Bing はイメージの縦横比を維持し、必要に応じて空白パディングを追加します。 たとえば、イメージの元のサイズが 474 x 316 で、幅と高さのパラメーターを 200 x 200 (`&w=200&h=200`) に設定した場合、Bing は、上下に 33 ピクセルの空白パディングを含むイメージを返します。 `&p` クエリ パラメーターを含めると、Bing は 200 x 134 のイメージを返します。

## <a name="crop-a-thumbnail"></a>サムネイルをトリミングする 

イメージをトリミングするには、`c` (トリミング) クエリ パラメーターを含めます。 次の値を使用できます。
  
- `4` &mdash; Blind Ratio  
- `7` &mdash; Smart Ratio  

### <a name="smart-ratio-cropping"></a>Smart Ratio トリミング

Smart Ratio トリミングを要求 (`c` パラメーターを `7` に設定) すると、Bing は、イメージの縦横比を維持したまま、イメージの関心領域の中心から外側へとトリミングします。 関心領域とは、Bing によって最も重要な部分が含まれていると判断されたイメージ領域です。 関心領域の例を次に示します。  
  
![関心領域](./media/resize-crop/bing-resize-crop-regionofinterest.png)

イメージのサイズを変更し、Smart Ratio トリミングを要求すると、Bing は、縦横比を維持したまま、要求されたサイズにイメージを縮小します。 その後、Bing は、サイズ変更された大きさに基づいてイメージをトリミングします。 たとえば、サイズ変更後の幅が高さ以上の場合、Bing は、関心領域の中心の左右でイメージをトリミングします。 それ以外の場合、Bing は、関心領域の中心の上下でトリミングします。  
  
 
次に、Smart Ratio トリミングを使用して 200 x 200 に縮小されたイメージを示します。 Bing は、左上隅を基準としてイメージを測定するため、イメージの下の部分がトリミングされます。 
  
![200 x 200 にトリミングされたイメージ (横)](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
次に、Smart Ratio トリミングを使用して 200 x 100 に縮小されたイメージを示します。 Bing は、左上隅を基準としてイメージを測定するため、イメージの下の部分がトリミングされます。 
   
![200 x 100 にトリミングされたイメージ (横)](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
次に、Smart Ratio トリミングを使用して 100 x 200 に縮小されたイメージを示します。 Bing は中心を基準としてイメージを測定するため、イメージの左右の部分がトリミングされます。
  
![100 x 200 にトリミングされたイメージ (横)](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Bing がイメージの関心領域を判断できない場合、Blind Ratio トリミングが使用されます。  

### <a name="blind-ratio-cropping"></a>Blind Ratio トリミング

Blind Ratio トリミングを要求 (`c` パラメーターを `4` に設定) すると、Bing では、次のルールを使用してイメージをトリミングします。  
  
- `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)` の場合、イメージは左上隅から測定され、下部がトリミングされます。  
- `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)` の場合、イメージは中心から測定され、左右にトリミングされます。  

次に、225 x 300 のイメージ (縦) を示します。  
  
![元のひまわりのイメージ](./media/resize-crop/bing-resize-crop-sunflower.png)
  
次に、Blind Ratio トリミングを使用して 200 x 200 に縮小されたイメージを示します。 イメージは、左上隅を基準として測定され、結果として、イメージの下の部分がトリミングされています。  
  
![200 x 200 にトリミングされたひまわりのイメージ](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
次に、Blind Ratio トリミングを使用して 200 x 100 に縮小されたイメージを示します。 イメージは、左上隅を基準として測定され、結果として、イメージの下の部分がトリミングされています。  
  
![200 x 100 にトリミングされたひまわりのイメージ](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
次に、Blind Ratio トリミングを使用して 100 x 200 に縮小されたイメージを示します。 イメージは、中心を基準として測定され、結果として、イメージの左右の部分がトリミングされています。  
  
![100 x 200 にトリミングされたひまわりのイメージ](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>次のステップ

* [Bing Search API とは](bing-api-comparison.md)
* [Bing Search API の利用と表示の要件](use-display-requirements.md)