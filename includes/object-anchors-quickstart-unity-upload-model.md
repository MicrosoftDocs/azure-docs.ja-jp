---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 03/02/2021
ms.author: crtreasu
ms.openlocfilehash: d8dfc3d4b7a8447250481b98c1adadc865a29da1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102532765"
---
### <a name="upload-your-model"></a>モデルをアップロードする

Object Anchors モデルをまだお持ちでない場合は、[モデルの作成](/azure/object-anchors/quickstarts/get-started-model-conversion)の手順に従ってモデルを作成します。 その後、ここに戻ります。

HoloLens を Windows デバイス ポータルに接続した状態で、次の手順に従って、アプリに使用するモデルをアップロードします。

1. Windows デバイス ポータルで、 **[システム] > [エクスプローラー] > [LocalAppData]** に移動します。 アプリの一覧にアプリケーションが表示されます。

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localappdata.png" alt-text="エクスプローラー":::

2. アプリケーションを開き、`LocalState` フォルダーをクリックします。

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localstate.png" alt-text="LocalState フォルダーを開く":::

3. モデル ファイルを `LocalState` フォルダーにアップロードします。

    :::image type="content" source="./media/object-anchors-quickstarts/portal-upload-model.png" alt-text="ポータルでモデルをアップロードする":::

    HoloLens からアプリケーションを再起動します。 これで、モデルに一致するオブジェクトを検出できるようになりました。
