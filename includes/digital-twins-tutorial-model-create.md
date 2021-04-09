---
author: baanders
description: Azure Digital Twins のインクルード ファイル - コマンドライン チュートリアルのモデル手順
ms.topic: include
ms.date: 3/5/2021
ms.author: baanders
ms.openlocfilehash: a94b9304ecd6c6630f6ad45652e76d2879bbc1b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463787"
---
1. **バージョン番号を更新する**。このモデルのバージョンを更新することを示します。 そのためには、`@id` 値の末尾にある *1* を *2* に変更します。 現在のバージョン番号よりも大きい数値であれば、何でもかまいません。
1. **プロパティを編集する**。 `Humidity` プロパティの名前を *HumidityLevel* に変更します (必要であれば、他の名前でもかまいません。 *HumidityLevel* 以外の名前を使用した場合は、その名前を覚えておいて、このチュートリアル全体の *HumidityLevel* の代わりにそれを使用し続けてください)。
1. **プロパティを追加する**。 `HumidityLevel` プロパティの最後 (15 行目) に続けて、次のコードを貼り付け、`RoomName` プロパティを room に追加します。

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="16-20":::

1. **リレーションシップを追加する**。 先ほど追加した `RoomName` プロパティの下に次のコードを貼り付けて、このタイプのツインが他のツインとの間で *contains* のリレーションシップを形成できるようにします。

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="21-24":::

作業が終わったら、更新後のモデルは次と一致します。

:::code language="json" source="~/digital-twins-docs-samples/models/Room.json":::

次に進む前に、必ずファイルを保存してください。