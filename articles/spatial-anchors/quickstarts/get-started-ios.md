---
title: クイック スタート:iOS アプリの作成
description: Swift または Objective-C のプログラムで Azure Spatial Anchors を使用して iOS アプリを作成する方法について説明します。
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.custom: has-adal-ref
ms.openlocfilehash: 2df66979d1e8f400f2630f5e531cd201244fdc6d
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87809988"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>クイック スタート:Azure Spatial Anchors を使用する iOS アプリを Swift または Objective-C で作成する

このクイック スタートでは、[Azure Spatial Anchors](../overview.md) を使用する iOS アプリを Swift または Objective-C で作成する方法について説明します。 Azure Spatial Anchors は、クロスプラットフォーム対応の開発者向けサービスです。このサービスを使用すると、時間が経過した後でも複数のデバイス間で位置情報を保持するオブジェクトを使用して複合現実エクスペリエンスを作成できます。 完了すると、空間アンカーを保存して再呼び出しできる ARKit iOS アプリが作成されます。

学習内容は次のとおりです。

> [!div class="checklist"]
> * Spatial Anchors アカウントを作成する
> * Spatial Anchors アカウント識別子とアカウント キーを構成する
> * iOS デバイスにデプロイして実行する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このクイック スタートを実行するには、以下が必要です。

- 最新バージョンの <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> と <a href="https://cocoapods.org" target="_blank">CocoaPods</a> がインストールされた、開発者向けの macOS コンピューター。
- HomeBrew を介してインストールされた Git:
  1. コマンド `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` をターミナルに 1 行で入力します。 
  1. `brew install git` および `brew install git-lfs` を実行します。
  1. `git lfs install` (現在のユーザーに対して) または `git lfs install --system` (システム全体に対して) で git 構成を更新します。
- 開発者向けの <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit 対応</a> iOS デバイス。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>サンプル プロジェクトを開く

ターミナルを使用して、以下のアクションを実行します。

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

CocoaPods を使用して必要なポッドをインストールします。

# <a name="swift"></a>[Swift](#tab/openproject-swift)

`iOS/Swift/` に移動します。

```bash
cd ./iOS/Swift/
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

`iOS/Objective-C/` に移動します。

```bash
cd ./iOS/Objective-C/
```

---

`pod install --repo-update` を実行して、プロジェクトの CocoaPods をインストールします。

Xcode で `.xcworkspace` を開きます。

> [!NOTE]
> macOS Catalina (10.15) にアップグレードした後に CocoaPod に関する問題が発生している場合は、[こちら](#cocoapods-issues-on-macos-catalina-1015)のトラブルシューティングの手順を参照してください。

# <a name="swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>アカウント識別子とキーを構成する

次に、自分のアカウント識別子とアカウント キーを使用するようにアプリを構成します。 これらの情報は、[Spatial Anchors リソースを設定](#create-a-spatial-anchors-resource)するときにテキスト エディターにコピーしました。

# <a name="swift"></a>[Swift](#tab/openproject-swift)

`iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`を開きます。

`spatialAnchorsAccountKey` フィールドを見つけ、`Set me` をアカウント キーに置き換えます。

`spatialAnchorsAccountId` フィールドを見つけ、`Set me` をアカウント識別子に置き換えます。

`spatialAnchorsAccountDomain` フィールドを見つけ、`Set me` をアカウント ドメインに置き換えます。

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

`iOS/Objective-C/SampleObjC/BaseViewController.m`を開きます。

`SpatialAnchorsAccountKey` フィールドを見つけ、`Set me` をアカウント キーに置き換えます。

`SpatialAnchorsAccountId` フィールドを見つけ、`Set me` をアカウント識別子に置き換えます。

`SpatialAnchorsAccountDomain` フィールドを見つけ、`Set me` をアカウント ドメインに置き換えます。

---

## <a name="deploy-the-app-to-your-ios-device"></a>アプリを iOS デバイスにデプロイする

iOS デバイスを Mac に接続し、**アクティブ スキーム**を iOS デバイスに設定します。

![デバイスを選択する](./media/get-started-ios/select-device.png)

**[Build and then run the current scheme]\(ビルドしてから現在のスキームを実行する\)** を選択します。

![デプロイして実行する](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> `library not found for -lPods-SampleObjC` エラーが表示される場合は、`.xcworkspace` ではなく `.xcodeproj` ファイルを開いた可能性があります。 `.xcworkspace` を開き、もう一度試してください。

Xcode で、 **[Stop]\(停止\)** を押してアプリを停止します。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>macOS Catalina (10.15) での CocoaPods に関する問題

最近 macOS Catalina (10.15) に更新し、そのとき事前に CocoaPods がインストールされていると、CocoaPods が破損した状態になり、ポッドや `.xcworkspace` プロジェクト ファイルを正しく構成できないことがあります。 この問題を解決するには、次のコマンドを実行して CocoaPods を再インストールする必要があります。

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="app-crashes-when-deploying-to-ios-1031-from-a-personal-provisioning-profiledeveloper-account"></a>アプリを個人のプロビジョニング プロファイル、または開発者アカウントから iOS 10.3.1 に展開するとクラッシュする 

iOS 10.3.1 上の iOS アプリを個人のプロビジョニング プロファイルまたは開発者アカウントから展開すると、エラー `Library not loaded: @rpath/ADAL...` が表示されることがあります。 

この問題を解決するには:

- Personal Team プロファイル (有料開発者アカウント) ではないプロビジョニング プロファイルを使用します。
- iOS 13.3 以前を実行している iOS デバイスまたは iOS 13.4 ベータ版またはリリース版を実行している iOS デバイスにアプリを展開します。
- この問題の詳細については[スタック オーバーフロー](https://stackoverflow.com/questions/60015309/running-ios-apps-causes-runtime-error-for-frameworks-code-signature-invalid)を参照してください。


[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [チュートリアル:デバイス間で Spatial Anchors を共有する](../tutorials/tutorial-share-anchors-across-devices.md)
