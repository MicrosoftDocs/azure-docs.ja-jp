---
title: クイック スタート - Azure Spatial Anchors を使用する Android Unity アプリを作成する | Microsoft Docs
description: このクイック スタートでは、Spatial Anchors を使用する Android アプリを Unity でビルドする方法について説明します。
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: c5647aa20f444d5efd36f03d813ee87ef199cc41
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621874"
---
# <a name="quickstart-create-an-android-unity-app-with-azure-spatial-anchors"></a>クイック スタート:Azure Spatial Anchors を使用する Android Unity アプリを作成する

このクリック スタートでは、[Azure Spatial Anchors](../overview.md) を使用する Android Unity アプリを作成する方法について説明します。 Azure Spatial Anchors は、クロスプラットフォーム対応の開発者向けサービスです。このサービスを使用すると、時間が経過した後でも複数のデバイス間で位置情報を保持するオブジェクトを使用して複合現実エクスペリエンスを作成できます。 完了すると、Unity でビルドされた、空間アンカーを保存して呼び戻すことができる ARCore Android アプリが作成されます。

学習内容は次のとおりです。

> [!div class="checklist"]
> * Spatial Anchors アカウントを作成する
> * Unity のビルド設定を準備する
> * ARCore SDK for Unity をダウンロードしてインポートする
> * Spatial Anchors アカウント識別子とアカウント キーを構成する
> * Android Studio プロジェクトをエクスポートする
> * Android デバイスに配置して実行する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このクイック スタートを実行するには、以下が必要です。

- <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3 以降</a>および <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3 以降</a>がインストールされている Windows または macOS コンピューター。
  - Windows で実行する場合は、<a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a> も必要になります。
  - MacOS で実行する場合は、HomeBrew から Git をインストールします。 `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` コマンドをターミナルに 1 行で入力します。 次に、`brew install git` を実行します。
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">開発者向け</a>の <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore 対応</a> Android デバイス。
- アプリでは Unity 用 ARCore SDK のバージョン **1.5** を使用する必要があります (ARCore 1.6 以降のサポートは後日提供される予定です)。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Unity でサンプル プロジェクトを開く

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>アカウント識別子とキーを構成する

**[Project]\(プロジェクト\)** ウィンドウで `Assets/AzureSpatialAnchorsPlugin/Examples` に移動し、シーン ファイル `AzureSpatialAnchorsBasicDemo.unity` を開きます。

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

**[File]\(ファイル\)** -> **[Save]\(保存\)** の順に選択してシーンを保存します。

## <a name="export-the-android-studio-project"></a>Android Studio プロジェクトをエクスポートする

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

**[Export Project]\(プロジェクトのエクスポート\)** チェック ボックスがオフになっていることを確認してください。 **[Build And Run]\(ビルドして実行\)** をクリックします。 `.apk` ファイルを保存するよう求められたら、任意の名前を選択できます。

アプリの指示に従って、アンカーを配置し、呼び戻します。

> [!NOTE]
> アプリを実行したとき、背景としてカメラが表示されない場合は (たとえば、代わりに空白、青、または他のテクスチャが表示される場合)、Unity に資産を再インポートすることが必要な場合があります。 アプリを停止します。 Unity の上部のメニューで、**[Assets]\(資産\) -> [Reimport all]\(すべて再インポート\)** を選択します。 その後、アプリをもう一度実行します。

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [チュートリアル:デバイス間で Spatial Anchors を共有する](../tutorials/tutorial-share-anchors-across-devices.md)
