---
title: クイックスタート - Azure Spatial Anchors を使用して Xamarin Android アプリを作成する | Microsoft Docs
description: このクイックスタートでは、Spatial Anchors を使用する Android アプリを Xamarin でビルドする方法について説明します。
author: craigktreasure
manager: virivera
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 0958961f1e66be130dd2be816f6002dd34465dc6
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931423"
---
# <a name="quickstart-create-a-xamarin-android-app-with-azure-spatial-anchors"></a>クイック スタート:Azure Spatial Anchors を使用して Xamarin Android アプリを作成する

このクイックスタートでは、[Azure Spatial Anchors](../overview.md) を使用する Android アプリを Xamarin で作成する方法について説明します。 Azure Spatial Anchors は、クロスプラットフォーム対応の開発者向けサービスです。このサービスを使用すると、時間が経過した後でも複数のデバイス間で位置情報を保持するオブジェクトを使用して複合現実エクスペリエンスを作成できます。 完了すると、空間アンカーを保存して呼び戻すことができる Android アプリが作成されます。

学習内容は次のとおりです。

> [!div class="checklist"]
> * Spatial Anchors アカウントを作成する
> * Spatial Anchors アカウント識別子とアカウント キーを構成する
> * Android デバイスに配置して実行する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このクイック スタートを実行するには、以下が必要です。
- Windows または macOS のコンピューター。
  - Windows を使っている場合:
    - 最新バージョンの <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 16.2 以上</a>。
    - <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a>
  - macOS を使っている場合:
    - 最新バージョンの <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio for Mac 8.1 以上</a>。
    - <a href="https://git-scm.com/download/mac" target="_blank">Git for macOS</a>
- 適切なプラットフォームに最新バージョンの Xamarin.Android がインストールされ、実行されていること。 Xamarin.Android のインストールガイドについては、[Xamarin.Android のインストール](https://docs.microsoft.com/xamarin/android/get-started/installation/index)に関するガイドを参照してください。
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">開発者向け</a>の <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore 対応</a> Android デバイス。
  - コンピューターが Android デバイスと通信するには、追加のデバイス ドライバーが必要になることがあります。 詳細については、[このページ](https://developer.android.com/studio/run/device.html)を参照してください。
- アプリでは、ARCore **1.8** をターゲットにする必要があります。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>サンプル プロジェクトを開く

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Visual Studio で `Xamarin/SampleXamarin.sln` を開きます。

## <a name="configure-account-identifier-and-key"></a>アカウント識別子とキーを構成する

次に、自分のアカウント識別子とアカウント キーを使用するようにアプリを構成します。 これらの情報は、[Spatial Anchors リソースを設定](#create-a-spatial-anchors-resource)するときにテキスト エディターにコピーしました。

`Xamarin/SampleXamarin.Common/AccountDetails.cs`を開きます。

`SpatialAnchorsAccountKey` フィールドを見つけ、`Set me` をアカウント キーに置き換えます。

`SpatialAnchorsAccountId` フィールドを見つけ、`Set me` をアカウント識別子に置き換えます。

## <a name="deploy-the-app-to-your-android-device"></a>アプリを Android デバイスに配置する

Android デバイスの電源をオンにしてサインインし、USB ケーブルを使用してコンピューターに接続します。

スタートアップ プロジェクトを **SampleXamarin.Android** に設定し、 **[ソリューション構成]** を **[リリース]** に変更して、デバイス セレクター ドロップダウンからデプロイ先のデバイスを選択します。

# <a name="windowstabdeploy-windows"></a>[Windows](#tab/deploy-windows)

![Visual Studio の構成](./media/get-started-xamarin-android/visual-studio-windows-configuration.png)

**[デバッグ]**  >  **[デバッグの開始]** の順に選択して、アプリをデプロイし、起動します。

# <a name="macostabdeploy-macos"></a>[macOS](#tab/deploy-macos)

![Visual Studio の構成](./media/get-started-xamarin-android/visual-studio-macos-configuration.jpg)

**[実行]**  >  **[デバッグなしで開始]** の順に選択してアプリをデプロイし、起動します。

---

アプリで、 **[Basic]\(基本\)** を選択してデモを実行し、画面の指示に従ってアンカーを配置し、呼び戻します。

> ![スクリーンショット 1](./media/get-started-xamarin-android/screenshot-1.jpg)
> ![スクリーンショット 2](./media/get-started-xamarin-android/screenshot-2.jpg)
> ![スクリーンショット 3](./media/get-started-xamarin-android/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [チュートリアル:デバイス間で Spatial Anchors を共有する](../tutorials/tutorial-share-anchors-across-devices.md)
