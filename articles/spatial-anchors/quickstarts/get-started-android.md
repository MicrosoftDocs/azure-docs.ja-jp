---
title: クイック スタート - Azure Spatial Anchors を使用する Android アプリを作成する | Microsoft Docs
description: このクイック スタートでは、Spatial Anchors を使用する Android アプリを構築する方法について説明します。
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 8f2a5fdaf2222de7a802e8ff2a1f6fdb37dae4c3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880042"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>クイック スタート:Azure Spatial Anchors を使用する Android アプリを作成する

このクイック スタートでは、[Azure Spatial Anchors](../overview.md) を使用する Android アプリを Java または C++/NDK で作成する方法について説明します。 Azure Spatial Anchors は、クロスプラットフォーム対応の開発者向けサービスです。このサービスを使用すると、時間が経過した後でも複数のデバイス間で位置情報を保持するオブジェクトを使用して複合現実エクスペリエンスを作成できます。 完了すると、空間アンカーを保存して呼び戻すことができる ARCore Android アプリが作成されます。

学習内容は次のとおりです。

> [!div class="checklist"]
> * Spatial Anchors アカウントを作成する
> * Spatial Anchors アカウント識別子とアカウント キーを構成する
> * Android デバイスに配置して実行する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このクイック スタートを実行するには、以下が必要です。

- <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3 以降</a>がインストールされている Windows または macOS コンピューター。
  - Windows で実行する場合は、<a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a> も必要になります。
  - MacOS で実行する場合は、HomeBrew から Git をインストールします。 `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` コマンドをターミナルに 1 行で入力します。 次に、`brew install git` を実行します。
  - NDK サンプルを構築するには、Android Studio の NDK と CMake 3.6 SDK Tools もインストールする必要があります。
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">開発者向けオプションが有効化</a>されている、<a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore 対応</a>の Android デバイス。
- アプリでは ARCore 1.5 をターゲットにする必要があります (ARCore 1.6 以降は間もなくサポートされるようになります)

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>サンプル プロジェクトを開く

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Android NDK サンプルを構築している場合は、`arcore_c_api.h` を[こちら](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.5.0/libraries/include/arcore_c_api.h)からダウンロードし、`Android\NDK\libraries\include` に配置する必要があります。

Android Studio を起動します。

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

**[Open an existing Android Studio project]\(既存の Android Studio プロジェクトを開く\)**  を選択し、`Android/Java/` にあるプロジェクトを選択します。

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

**[Open an existing Android Studio project]\(既存の Android Studio プロジェクトを開く\)**  を選択し、`Android/NDK/` にあるプロジェクトを選択します。

***

## <a name="configure-account-identifier-and-key"></a>アカウント識別子とキーを構成する

次に、自分のアカウント識別子とアカウント キーを使用するようにアプリを構成します。 これらの情報は、[Spatial Anchors リソースを設定](#create-a-spatial-anchors-resource)するときにテキスト エディターにコピーしました。

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

`Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsActivity.java`を開きます。

`SpatialAnchorsAccountKey` フィールドを見つけ、`Set me` をアカウント キーに置き換えます。

`SpatialAnchorsAccountId` フィールドを見つけ、`Set me` をアカウント識別子に置き換えます。

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

`Android/NDK/app/src/main/cpp/spatial_services_application.cc`を開きます。

`SpatialAnchorsAccountKey` フィールドを見つけ、`Set me` をアカウント キーに置き換えます。

`SpatialAnchorsAccountId` フィールドを見つけ、`Set me` をアカウント識別子に置き換えます。

***

## <a name="deploy-the-app-to-your-android-device"></a>アプリを Android デバイスに配置する

Android デバイスの電源をオンにしてサインインし、USB ケーブルを使用して PC に接続します。

Android Studio のツール バーから **[Run]\(実行\)** を選択します。

![Android Studio での配置と実行](./media/get-started-android/android-studio-deploy-run.png)

**[Select Deployment Target]\(配置ターゲットの選択\)** ダイアログで Android デバイスを選択し、**[OK]** を選択して Android デバイス上でアプリを実行します。

アプリの指示に従って、アンカーを配置し、呼び戻します。

Android Studio ツール バーの **[Stop]\(停止\)** を選択して、アプリを停止します。

![Android Studio での停止](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [チュートリアル:デバイス間で Spatial Anchors を共有する](../tutorials/tutorial-share-anchors-across-devices.md)
