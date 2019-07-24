---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: f96c3a693ce8fc099374c998b35ce2fa90f4bb3f
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753737"
---
Bing の応答には、Bing が提供するサムネイル イメージへの URL を含むものがあります。 サムネイル イメージはサイズ変更とトリミングが可能です。 

> [!NOTE]
> Bing Search API の使用要件と表示要件で定められているように、サムネイルのサイズとトリミングでは検索シナリオを提供し、第三者の権利を尊重するようにしてください。


イメージのサイズを変更するには、サムネイルの URL に w (幅) クエリ パラメーター、h (高さ) クエリ パラメーター、またはその両方を含めます。 幅と高さはピクセル単位で指定します。 例:   
  
`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=200&h=200`  
  
幅のクエリ パラメーターだけ、または高さのクエリ パラメーターだけを指定した場合、Bing ではイメージの縦横比を維持します。 幅と高さの両方を指定し、イメージの元の縦横比を維持しない場合、Bing ではイメージの枠線に空白パディングを追加します。 たとえば、480 x 359 のイメージのサイズをトリミングせずに 200 x 200 に変更すると、イメージは幅いっぱいになりますが、高さではイメージの上下に 25 ピクセルの空白パディングが含まれます。 イメージが 359 x 480 の場合にも同じことが当てはまります。ただし、左右の枠線に空白パディングが含まれる点が異なります。 イメージをトリミングする場合は、空白パディングが追加されません。  

 
次の画像は、サムネイル イメージの元のサイズ (480 x 300) を示しています。  
  
![元のイメージ (横)](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape.PNG)  
  
次の画像は、200 x 200 にサイズ変更されたイメージを示しています。 縦横比が維持され、上下の枠線に空白が埋め込まれています (黒の枠線は、パディングを示すために含めています)。  
  
![サイズ変更されたイメージ (横)](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape-resized.PNG)  



イメージの元の幅と高さを超える大きさを指定した場合、イメージの左と上の枠線に空白が埋め込まれます。  
  
イメージをトリミングするには、c (トリミング) クエリ パラメーターを含めます。 指定できる値を次に示します。  
  
- 4&mdash;Blind Ratio  
- 7&mdash;Smart Ratio  
  
Smart Ratio トリミング (c=7) を要求すると、イメージは、イメージの関心領域の中心から外側へトリミングされますが、イメージの縦横比は維持されます。 関心領域とは、Bing によって最も重要な部分が含まれていると判断されたイメージ領域です。 関心領域の例を次に示します。  
  
![関心領域](./media/cognitive-services-bing-resize-crop/bing-resize-crop-regionofinterest.PNG)

イメージのサイズを変更し、Smart Ratio トリミングを要求すると、イメージは、縦横比を維持したまま、要求されたサイズに縮小されます。 その後、サイズ変更された大きさに基づいてトリミングされます。 たとえば、サイズ変更後の幅が高さ以上の場合、イメージは、関心領域の中心の左右でトリミングされます。 それ以外の場合は、関心領域の中心の上下でトリミングされます。  
  
 
次に、Smart Ratio トリミングを使用して 200 x 200 に縮小されたイメージを示します。  
  
![200 x 200 にトリミングされたイメージ (横)](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x200c7.PNG)
  
次に、Smart Ratio トリミングを使用して 200 x 100 に縮小されたイメージを示します。  
   
![200 x 100 にトリミングされたイメージ (横)](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x100c7.PNG)
  
次に、Smart Ratio トリミングを使用して 100 x 200 に縮小されたイメージを示します。  
  
![100 x 200 にトリミングされたイメージ (横)](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape100x200c7.PNG)



Bing がイメージの関心領域を判断できない場合は、Blind Ratio トリミングが使用されます。  
  
Blind Ratio トリミング (c=4) を要求すると、Bing では、次のルールを使用してイメージをトリミングします。  
  
- (元のイメージの幅/元のイメージの高さ) < (要求されたイメージの幅/要求されたイメージの高さ) の場合、イメージは、左上隅から測定され、下部からトリミングされます。  
- (元のイメージの幅/元のイメージの高さ) > (要求されたイメージの幅/要求されたイメージの高さ) の場合、イメージは、中心から測定され、左右でトリミングされます。  



次に、225 x 300 のイメージ (縦) を示します。  
  
![元のひまわりのイメージ](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower.PNG)
  
次に、Blind Ratio トリミングを使用して 200 x 200 に縮小されたイメージを示します。 イメージは、左上隅を基準として測定され、結果として、イメージの下の部分がトリミングされています。  
  
![200 x 200 にトリミングされたひまわりのイメージ](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x200c4.PNG)
  
次に、Blind Ratio トリミングを使用して 200 x 100 に縮小されたイメージを示します。 イメージは、左上隅を基準として測定され、結果として、イメージの下の部分がトリミングされています。  
  
![200 x 100 にトリミングされたひまわりのイメージ](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x100c4.PNG)
  
次に、Blind Ratio トリミングを使用して 100 x 200 に縮小されたイメージを示します。 イメージは、中心を基準として測定され、結果として、イメージの左右の部分がトリミングされています。  
  
![100 x 200 にトリミングされたひまわりのイメージ](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower100x200c4.PNG)

