---
title: チュートリアル:写真を撮影してイマーシブ リーダーで起動する iOS アプリを作成する (Swift)
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、iOS アプリを一から作成し、イマーシブ リーダー機能に画像を追加します。
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: metan
ms.openlocfilehash: bdaee97c8c5d7e19076847c5f1f7c07c528c1747
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899374"
---
# <a name="tutorial-create-an-ios-app-that-launches-the-immersive-reader-with-content-from-a-photo-swift"></a>チュートリアル:写真のコンテンツを含むイマーシブ リーダーを起動する iOS アプリを作成する (Swift)

[イマーシブ リーダー](https://www.onenote.com/learningtools)は、読解力向上のために実証済みの手法を実装する、包括的に設計されたツールです。

[Computer Vision Cognitive Services Read API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text) では、Microsoft の最新の認識モデルを使用して画像内のテキスト コンテンツが検出され、識別されたテキストはマシンで読み取り可能な文字ストリームに変換されます。

このチュートリアルでは、iOS アプリを一から作成し、イマーシブ リーダー SDK を使用して Read API とイマーシブ リーダーを統合します。 このチュートリアルの完全に動作するサンプルは[こちら](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/samples/picture-to-immersive-reader-swift)で入手できます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* Azure Active Directory (Azure AD) 認証用に構成されたイマーシブ リーダー リソース。 設定するには、[これらの手順](./azure-active-directory-authentication.md)に従ってください。 サンプル プロジェクトのプロパティを構成するときに、ここで作成した値の一部が必要になります。 後で参照するために、実際のセッションの出力をテキスト ファイルに保存します。
* このサンプルを使用するには、Computer Vision Cognitive Services に対する Azure サブスクリプションが必要です。 [Azure portal で Computer Vision Cognitive Services リソースを作成します](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)。

## <a name="create-an-xcode-project"></a>Xcode プロジェクトを作成する

Xcode で新しいプロジェクトを作成します。

![[新しいプロジェクト]](./media/ios/xcode-create-project.png)

**[単一ビュー アプリ]** を選択します。

![新しい単一ビュー アプリ](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>SDK CocoaPods を入手する
イマーシブ リーダー SDK を使用する最も簡単な方法は、CocoaPods を使用することです。 CocoaPods 経由でインストールするには:
1. [CocoaPods をインストールする](http://guides.cocoapods.org/using/getting-started.html) - ファースト ステップ ガイドに従って CocoaPods をインストールします。
2. Xcode プロジェクトのルート ディレクトリで `pod init` を実行して Podfile を作成します。
3.  `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'` を追加して、自分の Podfile に CocoaPods を追加します。 Podfile は次のようになります。picture-to-immersive-reader-swift は実際のターゲットの名前に置き換えておきます。
 ```ruby
  platform :ios, '9.0'

  target 'picture-to-immersive-reader-swift' do
  use_frameworks!
  # Pods for picture-to-immersive-reader-swift
  pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'
  end
```
4. ターミナルの Xcode プロジェクトのディレクトリで `pod install` コマンドを実行して、イマーシブ リーダー SDK ポッドをインストールします。
5. この SDK を参照する必要があるすべてのファイルに `import immersive_reader_sdk` を追加します。
6. `.xcodeproj` ファイルではなく `.xcworkspace` ファイルを開くことで、このプロジェクトが開くようにします。

## <a name="acquire-an-azure-ad-authentication-token"></a>Azure AD 認証トークンを取得する

この部分については、上の Azure AD 認証構成の前提条件の手順におけるいくつかの値が必要です。 そのセッションで保存したテキスト ファイルを参照します。

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

ViewController.swift ファイルが含まれているメイン プロジェクト フォルダー内に、Constants.swift という名前の Swift クラス ファイルを作成します。 このクラスを次のコードに置き換え、必要に応じて実際の値を追加します。 このファイルは、お使いのマシンのみに存在するローカル ファイルとして保持してください。また、公開すべきでないシークレットを含んでいるため、このファイルをソース管理にコミットしないでください。 アプリではシークレットを保持しないことをお勧めします。 代わりに、バックエンド サービスを使用してトークンを取得することをお勧めします。この場合、シークレットはアプリやデバイスの外部で保持することができます。 バックエンド API エンドポイントは、なんらかの認証形式 ([OAuth](https://oauth.net/2/) など) の背後で保護して、未承認のユーザーがお使いのイマーシブ リーダー サービスと請求に対して使用するトークンを取得できないようにする必要があります。この作業は、このチュートリアルの範囲外です。

[!code-swift[Constants](~/ImmersiveReaderSdk/iOS/samples/picture-to-immersive-reader-swift/picture-to-immersive-reader-swift/Constants.swift)]

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>ストーリーボードを使用せずに実行するようアプリを設定する

AppDelegate.swift を開き、このファイルを次のコードに置き換えます。

[!code-swift[AppDelegate](~/ImmersiveReaderSdk/iOS/samples/picture-to-immersive-reader-swift/picture-to-immersive-reader-swift/AppDelegate.swift)]

## <a name="add-functionality-for-taking-and-uploading-photos"></a>写真を撮影してアップロードするための機能を追加する

ViewController.swift の名前を PictureLaunchViewController.swift に変更し、このファイルを次のコードに置き換えます。

[!code-swift[PictureLaunchViewController](~/ImmersiveReaderSdk/iOS/samples/picture-to-immersive-reader-swift/picture-to-immersive-reader-swift/PictureLaunchViewController.swift)]

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

シミュレーターまたはデバイスのターゲットを選択して、Xcode でアーカイブ スキームを設定します。
![アーカイブ スキーム](./media/ios/xcode-archive-scheme.png)<br/>
![ターゲットの選択](./media/ios/xcode-select-target.png)

Xcode で、Ctrl キーを押しながら R キーを押すか再生ボタンをクリックしてプロジェクトを実行すると、指定したシミュレーターまたはデバイスでアプリが起動します。

お使いのアプリでは次のように表示されます。

![サンプル アプリ](./media/ios/picture-to-immersive-reader-ipad-app.png)

アプリ内で、[Take Photo] ボタンまたは [Choose Photo from Library] ボタンを押してテキストの写真を撮影またはアップロードすると、イマーシブ リーダーが起動し、写真のテキストが表示されます。

![Immersive Reader](./media/ios/picture-to-immersive-reader-ipad.png)

## <a name="next-steps"></a>次の手順

* [イマーシブ リーダー iOS SDK](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS) と[イマーシブ リーダー iOS SDK リファレンス](./ios-reference.md)を探索する
