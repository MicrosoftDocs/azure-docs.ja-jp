---
title: 'クイック スタート: Unity と MRTK を使用して HoloLens アプリを作成する'
description: このクイック スタートでは、MRTK と Object Anchors を使用する HoloLens アプリを作成する方法について説明します。
author: craigktreasure
manager: virivera
services: azure-object-anchors
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 1fd42f7b2da82da17dc19f2a57ea9b64f78f3fe0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102049747"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity-with-mrtk"></a>クイック スタート: Azure Object Anchors を使用する HoloLens アプリを MRKT を使用した Unity で作成する

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

Unity で `quickstarts/apps/unity/mrtk` プロジェクトを開きます。

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

[TMP Importer] ダイアログで TextMesh Pro リソースをインポートするように求めるメッセージが表示されたら、[Import TMP Essentials]\(TMP Essentials のインポート\) を選択して、それを実行します。
:::image type="content" source="./media/textmesh-pro-importer-dialog.png" alt-text="TextMesh Pro リソースをインポートする":::

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

### <a name="run-the-sample-app"></a>サンプル アプリを実行する

デバイスをオンにし、 **[すべてのアプリ]** を選択してから、アプリを見つけて起動します。 Unity スプラッシュ スクリーンの後に、白い境界ボックスが表示されます。 手を使用して、境界ボックスの移動、拡大縮小、または回転を行うことができます。 検出したい物体を範囲に含むボックスを配置します。

<a href="https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_HandMenu.html" target="_blank">ハンド メニュー</a>を開き、 **[Lock Search Area]\(検索領域のロック\)** を選択して、境界ボックスがこれ以上移動しないようにします。 **[検索開始]** を選択して、物体検出を開始します。 物体が検出されると、メッシュがその物体に対してレンダリングされます。 検出されたインスタンスの詳細が画面に表示されます。たとえば、更新されたタイムスタンプやサーフェス カバレッジ比率などです。 **[検索の停止]** を選択して追跡を停止すると、検出されたすべてのインスタンスが削除されます。

#### <a name="the-app-menus"></a>アプリのメニュー

<a href="https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_HandMenu.html" target="_blank">ハンド メニュー</a>を使用して他の操作を行うこともできます。

##### <a name="primary-menu"></a>主なメニュー

* **検索開始/検索の停止** - 物体検出プロセスを開始または停止します。
* **Toggle Spatial Mapping (空間マッピングの切り替え)** – 空間マッピングのレンダリングの表示/非表示を切り替えます。 このオプションは、スキャンが完了したかどうかのデバッグに使用できます。
* **Tracker Settings (トラッカー設定)** – トラッカー設定メニューをアクティブにするか、しないかを切り替えます。
* **Search Area Settings (検索領域の設定)** – 検索領域の設定メニューをアクティブにするか、しないかを切り替えます。
* **Start Tracing (トレースの開始)** – 診断データを取得し、デバイスに保存します。 詳細については **検出に関する問題のデバッグと診断の取得** に関するセクションを参照してください。
* **Upload Tracing (トレースのアップロード)** – 診断データを Object Anchors サービスにアップロードします。 ユーザーは、`subscription.json` にサブスクリプション アカウントを指定し、`LocalState` フォルダーにそれをアップロードする必要があります。 サンプルの `subscription.json` ファイルは以下に記載されています。

    :::image type="content" source="./media/mrtk-hand-menu-primary.png" alt-text="Unity の主なハンド メニュー":::

##### <a name="tracker-settings-menu"></a>[Tracker Settings]\(トラッカー設定\) メニュー

* **High Accuracy (高精度)** – より正確な姿勢の取得に使用される試験的な機能です。 このオプションを有効にすると、物体検出時により多くのシステム リソースが必要になります。 このモードでは、物体のメッシュはピンク色でレンダリングされます。 通常の追跡モードに戻すには、このボタンをもう一度選択します。
* **Relaxed Vertical Alignment (緩やかな垂直方向の整合)** – 有効にすると、物体を非鉛直角で検出できます。 傾斜状態の物体を検出する場合に役立ちます。
* **Allow Scale Change (スケールの変更を許可)** - 環境情報に基づいて、検出された物体のサイズをトラッカーが変更できるようにします。
* **Coverage Ratio (カバレッジ比率) スライダー** – トラッカーが物体を検出するために一致する必要があるサーフェス ポイントの割合を調整します。  値を小さくすると、暗い物体や高反射性の物体など、HoloLens センサーで検出することが困難な物体を、トラッカーがより適切に検出できるようになります。 値を大きくすると、誤検出の頻度が低下します。

    :::image type="content" source="./media/mrtk-hand-menu-tracker.png" alt-text="Unity トラッカーのハンド メニュー":::

##### <a name="search-area-settings-menu"></a>[Search Area Settings]\(検索領域の設定\) メニュー

* **Lock Search Area (検索領域のロック)** – 領域の境界ボックスをロックして、手で誤って移動しないようにします。
* **Auto-Adjust Search Area (検索領域の自動調整)** - 物体検出中に検索領域によるそれ自体の位置変更を可能にします。
* **Cycle Mesh (メッシュの循環)** – 検索領域内で読み込まれたメッシュの視覚化を順番に切り替えます。  このオプションを使用すると、物体を検出するためにユーザーが検索ボックスを厳格に調整しやすくなります。

    :::image type="content" source="./media/mrtk-hand-menu-search-area.png" alt-text="Unity 検索領域のハンド メニュー":::

例 `subscription.json`

```json
{
  "AccountId": "<your account id>",
  "AccountKey": "<your account key>",
  "AccountDomain": "<your account domain>"
}
```

[!INCLUDE [Unity setup Windows Device Portal](../../../includes/object-anchors-quickstart-unity-setup-device-portal.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [概念: SDK の概要](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [FAQ](../faq.md)
