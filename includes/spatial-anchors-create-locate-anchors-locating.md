---
ms.openlocfilehash: 52dfbfca5f79a7f92848ea39eddc00aa10f05ff1
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631670"
---
## <a name="locate-a-cloud-spatial-anchor"></a>クラウド空間アンカーを見つける

Azure Spatial Anchors ライブラリを使用する主要な理由の 1 つに、以前にアップロードしたクラウド空間アンカーを検索できることが挙げられます。 クラウド空間アンカーを見つけるには、識別子を知っておく必要があります。 アンカー ID はアプリケーションのバックエンド サービスに格納でき、それを適切に認証できるすべてのデバイスにアクセスできます。 この例のついては、「[チュートリアル:デバイス間で空間アンカーを共有する](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/)」をご覧ください。

`AnchorLocateCriteria` オブジェクトをインスタンス化し、検索する識別子を設定します。次に `AnchorLocateCriteria` を指定することでセッション上の `CreateWatcher` メソッドを呼び出します。
