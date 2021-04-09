---
title: クイック スタート:DirectX を使用して HoloLens アプリを作成する
description: このクイック スタートでは、Object Anchors を使用する HoloLens アプリを作成する方法について説明します。
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 8a02bb7c70df4fed55c354638fe6662b85e6c164
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102049642"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-cwinrt-and-directx"></a>クイック スタート: Azure Object Anchors を使用する HoloLens アプリを C++/WinRT と DirectX で作成する

このクイック スタートでは、[Azure Object Anchors](../overview.md) を使用する HoloLens アプリを C++/WinRT と DirectX で作成する方法について説明します。 Azure Object Anchors は、3D アセットを HoloLens の物体認識 Mixed Reality エクスペリエンスを実現する AI モデルに変換するマネージド クラウド サービスです。 作業を終えると、Holographic DirectX 11 (ユニバーサル Windows) アプリケーション内で物体とその姿勢を検出できる HoloLens アプリが完成します。

学習内容は次のとおりです。

> [!div class="checklist"]
> * HoloLens アプリケーションを作成してサイドロードする
> * 物体を検出し、そのモデルを視覚化する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このクイック スタートを実行するには、以下が必要です。

* ご利用環境にある対象物と、その 3D モデル (CAD またはスキャン済みのいずれか)。
* 以下がインストールされている Windows マシン。
  * <a href="https://git-scm.com" target="_blank">Git for Windows</a>
  * **ユニバーサル Windows プラットフォーム開発** ワークロードを含む <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> と **Windows 10 SDK (10.0.18362.0 以降)** コンポーネント
* [開発者モード](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio#enabling-developer-mode)が有効になっている最新の HoloLens 2 デバイス。
  * HoloLens を最新のリリースに更新するには、 **[設定]** アプリを開き、 **[更新とセキュリティ]** を選択し、 **[更新プログラムの確認]** を選択します。

## <a name="open-the-sample-project"></a>サンプル プロジェクトを開く

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

Visual Studio で `quickstarts/apps/directx/DirectXAoaSampleApp.sln` を開きます。

**ソリューション構成** を **[リリース]** に変更し、**ソリューション プラットフォーム** を **[ARM64]** に変更し、配置先オプションから **[デバイス]** を選択します。 次に、**AoaSampleApp** プロジェクトを右クリックし、 **[ビルド]** を選択して、プロジェクトをビルドします。

:::image type="content" source="./media/vs-deploy-to-device.png" alt-text="配置する Visual Studio プロジェクトを構成する":::

## <a name="deploy-the-app-to-hololens"></a>アプリを HoloLens に配置する

サンプル プロジェクトを正常にコンパイルしたら、アプリを HoloLens に配置できます。

HoloLens デバイスの電源をオンにしてサインインし、USB ケーブルを使用して PC に接続します。 **[デバイス]** が、選択された配置先であることを確認します (上記を参照)。

**AoaSampleApp** プロジェクトを右クリックし、ポップアップ メニューの **[配置]** をクリックして、アプリをインストールします。 Visual Studio の **出力ウィンドウ** にエラーが表示されなければ、アプリは HoloLens にインストールされます。

:::image type="content" source="./media/vs-deploy-app.png" alt-text="アプリを HoloLens に配置する":::

アプリを起動する前に、オブジェクト モデルをアップロードする必要があります。 以下の「**オブジェクト モデルを取り込み、そのインスタンスを検出する**」セクションの手順に従ってください。

アプリを起動してデバッグするには、 **[デバッグ] > [デバッグの開始]** の順に選択します。 アプリを停止するには、 **[デバッグの停止]** を選択するか、**Shift + F5** キーを押します。

## <a name="ingest-object-model-and-detect-its-instance"></a>オブジェクト モデルを取り込み、そのインスタンスを検出する

サンプル アプリを実行するには、オブジェクト モデルを作成する必要があります。 空間にある物体の CAD またはスキャンされた 3D メッシュ モデルが既にあるものとします。 モデルの作成方法については、[クイック スタート: 3D モデルの取り込み](./get-started-model-conversion.md)に関する記事を参照してください。

このモデル (今回の場合は **chair.ou**) を自分のコンピューターにダウンロードします。 次に HoloLens デバイス ポータルで **[システム] > [エクスプローラー] > [LocalAppData] > [AoaSampleApp] > [LocalState]** の順に選択し、 **[参照...]** を選択します。次に、モデル ファイル (たとえば、**chair.ou**) を選択し、 **[アップロード]** を選択します。 すると、ローカル キャッシュにモデル ファイルが表示されます。

:::image type="content" source="../../../includes/media/object-anchors-quickstarts/portal-upload-model.png" alt-text="ポータルでのモデルのアップロード":::

HoloLens から **AoaSampleApp** アプリを起動します (既に開いていた場合は、閉じてから再度開きます)。 対象の物体 (椅子) の近く (2 メートル以内の距離) を歩き、複数の視点からそれを見てスキャンします。 その物体の周囲にピンク色の境界ボックスが表示され、いくつかの黄色の点が物体の表面の近くにレンダリングされています。これは、それが検出されたことを示しています。

:::image type="content" source="./media/chair-detection.png" alt-text="椅子の検出":::

図: 検出された椅子。その境界ボックス (ピンク色)、点群 (黄色)、および検索領域 (大きな黄色のボックス) でレンダリングされています。

アプリ内で物体の検索スペースを定義するには、左右のどちらかの手を使用して空中で指をパチンと鳴らします。 検索スペースは、半径 2 メートルの球、4 m^3 の境界ボックス、ビューの視錐台の間で切り替わります。 自動車などの大きい物体の場合、通常、約 2 メートルの距離で物体の角の方を向いて立ちながら、ビューの視錐台の選択範囲を使用することが最適な方法です。
検索領域が変更されるたびに、アプリは現在追跡されているインスタンスを削除してから、新しい検索領域で再度それらの検索を試行します。

このアプリは、一度に複数の物体を追跡できます。 これを行うには、複数のモデルを **Localstate** フォルダーにアップロードし、対象とするすべての物体を範囲に含む検索領域を設定します。 複数の物体の検出と追跡には時間がかかることがあります。

このアプリでは、3D モデルをそれに物理的に対応するものと密接に整合させます。 ユーザーは、左手を使用してエアタップを行って、高精度の追跡モードを有効にすることができます。これにより、さらに正確な姿勢が計算されます。 これはまだ試験段階の機能であり、より多くのシステム リソースを消費します。また、推定される姿勢に高いジッターが発生する恐れがあります。 通常の追跡モードに戻すには、左手で再度エアタップを行います。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [クイック スタート: 3D モデルの取り込み](./get-started-model-conversion.md)

> [!div class="nextstepaction"]
> [概念: SDK の概要](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [FAQ](../faq.md)
