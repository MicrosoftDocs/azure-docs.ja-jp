---
title: クイック スタート:Unity を使用して HoloLens アプリを作成する
description: このクイックスタートでは、Object Anchors を使用する HoloLens Unity アプリを作成する方法について説明します。
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 4f85a258042430d58690ef578db6d21a6c831d50
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102044811"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity"></a>クイックスタート: Azure Object Anchors を使用する HoloLens アプリを Unity で作成する

このクイックスタートでは、[Azure Object Anchors](../overview.md) を使用する Unity HoloLens アプリを作成します。 Azure Object Anchors は、3D アセットを HoloLens の物体認識 Mixed Reality エクスペリエンスを実現する AI モデルに変換するマネージド クラウド サービスです。 作業を終えると、Unity で作成された HoloLens アプリで、物理世界のオブジェクトを検出できるようになります。

学習内容は次のとおりです。

> [!div class="checklist"]
> * Unity のビルド設定を準備する。
> * HoloLens Visual Studio プロジェクトをエクスポートする。
> * HoloLens 2 デバイスにアプリを配置して実行する。

[!INCLUDE [Unity quickstart prerequisites](../../../includes/object-anchors-quickstart-unity-prerequisites.md)]

[!INCLUDE [Unity device setup](../../../includes/object-anchors-quickstart-unity-device-setup.md)]

## <a name="open-the-sample-project"></a>サンプル プロジェクトを開く

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

[!INCLUDE [Download Unity Package](../../../includes/object-anchors-quickstart-unity-download-package.md)]

Unity で `quickstarts/apps/unity/basic` プロジェクトを開きます。

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

### <a name="run-the-sample-app"></a>サンプル アプリを実行する

デバイスをオンにし、 **[すべてのアプリ]** を選択してから、アプリを見つけて起動します。 Unity のスプラッシュ スクリーンの後に、オブジェクト オブザーバーが初期化されたことを示すメッセージが表示されます。 ただし、モデルをアプリに追加する必要があります。

[!INCLUDE [Unity setup Windows Device Portal](../../../includes/object-anchors-quickstart-unity-setup-device-portal.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

アプリによって、ビューの現在のフィールドでオブジェクトが検索され、検出されるとそれらが追跡されます。 インスタンスは、ユーザーの場所から 6 メートル離れると削除されます。 デバッグ テキストには、ID、更新されたタイムスタンプ、サーフェス カバレッジの比率など、インスタンスの詳細が表示されます。

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Unity HoloLens と MRTK](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [概念: SDK の概要](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [FAQ](../faq.md)
