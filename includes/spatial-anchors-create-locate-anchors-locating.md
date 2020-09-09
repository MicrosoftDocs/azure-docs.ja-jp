---
ms.openlocfilehash: 4df49b4e68769a907423c4edef9f8820df4d20b7
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006484"
---
## <a name="locate-a-cloud-spatial-anchor"></a>クラウド空間アンカーを見つける

Azure Spatial Anchors を使用する主な理由の 1 つに、以前に保存したクラウド空間アンカーを検索できることが挙げられます。 クラウド空間アンカーは、さまざまな方法で検索できます。 Watcher では一度に 1 つの方法を使用することができます。
- ID でアンカーを検索します。
- 以前検索したアンカーに接続されているアンカーを検索します。 アンカーの関係については、[こちら](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/)をご覧ください。
- [粗い再局在化](/azure/spatial-anchors/concepts/coarse-reloc/)を使用してアンカーを検索します。

> [!NOTE]
> アンカーを配置するたびに、Azure Spatial Anchors は、収集された環境データを使用して、アンカーのビジュアル情報を増強しようとします。 アンカーを配置するのに問題がある場合は、アンカーを作成した後、さまざまな角度や照明条件から何度か配置してみるといいでしょう。

クラウド空間アンカーを ID で検索する場合、そのクラウド空間アンカー ID をアプリケーションのバックエンド サービスに格納しておけば、そのバックエンド サービスに対して適切に認証を行うことができるあらゆるデバイスからアクセスさせることができます。 この例のついては、「[チュートリアル: デバイス間で空間アンカーを共有する](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/)」をご覧ください。

`AnchorLocateCriteria` オブジェクトをインスタンス化し、検索する識別子を設定します。次に `AnchorLocateCriteria` を指定することでセッション上の `CreateWatcher` メソッドを呼び出します。