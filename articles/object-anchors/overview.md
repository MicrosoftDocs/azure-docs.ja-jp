---
title: Azure Object Anchors の概要
description: Azure Object Anchors を使用して、現実世界の物体を検出する方法について説明します。
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: b4284411fbe09a0d0ce8412e6e5a5df464f35564
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462866"
---
# <a name="azure-object-anchors-overview"></a>Azure Object Anchors の概要

Azure Object Anchors を使用すると、アプリケーションで 3D モデルを使用して現実世界の物体を検出し、その 6DoF 姿勢を推定することができます。 6DoF (6 つの自由度) は、3D モデルと、それに物理的に対応するもの (実際のオブジェクト) との間の回転および平行移動として定義されます。

Azure Object Anchors は、モデル変換のためのサービスと、HoloLens 用のランタイム クライアント SDK で構成されています。 このサービスは、3D オブジェクト モデルを受け取り、Azure Object Anchors モデルを出力します。 Azure Object Anchors モデルは、ランタイム SDK と共に使用されます。これにより、HoloLens アプリケーションでオブジェクト モデルを読み込み、現実世界においてそのモデルのインスタンスを検出して追跡できるようになります。

:::image type="content" source="./media/aoa-overview.jpg" alt-text="動作中の Azure Object Anchors":::

## <a name="examples"></a>例

Azure Object Anchors によって実現できるユース ケースの例には、次のようなものがあります。

- **Training**。 マーカーを配置したり、ホログラムの配置の手動調整に時間をかけたりすることなく、作業者向けの Mixed Reality トレーニング エクスペリエンスを作成します。 自動化された検出および追跡によって、Mixed Reality トレーニング エクスペリエンスを強化する場合は、モデルを Azure Object Anchors サービスに取り込むと、マーカーなしのエクスペリエンスに一歩近づきます。

- **タスクのガイダンス**。 Mixed Reality を使用すると、従業員に一連のタスクを経験させることが非常に簡略化できます。 工場の現場にある機械であれ、チームのキッチンにあるコーヒー メーカーであれ、対象物の上にデジタルの指示とベスト プラクティスを重ね合わせると、一連のタスクを完了する難しさを大幅に軽減できます。 これらのエクスペリエンスをトリガーするには、通常、何らかの形式のマーカーまたは手動での配置が必要ですが、Azure Object Anchors を使用すると、目下のタスクに関連する物体を自動的に検出するエクスペリエンスを作成できます。 その後、マーカーや手動のアライメントなしで、Mixed Reality ガイダンスをシームレスに進めます。

- **アセットの検索**。 物理空間に何らかの物体の 3D モデルが既にある場合は、Azure Object Anchors を使用して、物理環境でその物体のインスタンスを特定し、追跡できます。

## <a name="next-steps"></a>次のステップ

次のセクションで、Azure Object Anchors を使用したアプリの使用および作成の概要に関する情報を提供しています。

> [!div class="nextstepaction"]
> [モデルの取り込み](quickstarts/get-started-model-conversion.md)

> [!div class="nextstepaction"]
> [Unity HoloLens](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [ MRTK を使用した Unity HoloLens](quickstarts/get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](quickstarts/get-started-hololens-directx.md)
