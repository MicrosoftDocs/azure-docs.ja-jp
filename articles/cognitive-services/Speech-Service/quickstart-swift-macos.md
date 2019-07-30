---
title: クイック スタート:音声を認識する、Swift - Speech Services
titleSuffix: Azure Cognitive Services
description: macOS 上で Swift と Speech SDK を使用して音声を認識する方法について説明します
services: cognitive-services
author: cbasoglu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/28/2019
ms.author: cbasoglu
ms.openlocfilehash: d81384c4a3b4691f179dc2b11330b25fe7fb42bb
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561533"
---
# <a name="quickstart-recognize-speech-in-swift-on-macos-using-the-speech-sdk"></a>クイック スタート:macOS 上で Swift と Speech SDK を使用して音声を認識する

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

この記事では、Swift と Cognitive Services Speech SDK を使用して、マイクから録音された音声をテキストに文字起こしする macOS アプリを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

開始する前の前提条件の一覧を次に示します。

* Speech Service 用の[サブスクリプション キー](get-started.md)。
* [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) 以降と [CocoaPods](https://cocoapods.org/) 以降がインストールされた macOS コンピューター。

## <a name="get-the-speech-sdk-for-ios"></a>iOS 用の Speech SDK を取得する

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Cognitive Services Speech SDK の現在のバージョンは `1.6.0` です。 SDK の以前のバージョンを変更しないと、このチュートリアルは動作しないことに注意してください。

macOS 用の Cognitive Services Speech SDK は、フレームワーク バンドルとして配布されています。
Xcode プロジェクトで [CocoaPod](https://cocoapods.org/) として使用することも、 https://aka.ms/csspeech/macosbinary からダウンロードして手動でリンクすることも可能です。 このガイドでは CocoaPod を使用します。

## <a name="create-an-xcode-project"></a>Xcode プロジェクトを作成する

Xcode を起動し、 **[File]**  >  **[New]**  >  **[Project]** の順にクリックして新しいプロジェクトを開始します。
テンプレートの選択ダイアログで、"iOS Single View App" テンプレートを選択します。

以降のダイアログで、次のように選択します。

1. [Project Options] ダイアログ
    1. クイック スタート アプリの名前を入力します (例: `helloworld`)。
    1. 既に Apple Developer アカウントをお持ちの場合は、適切な組織名と組織 ID を入力します。 テスト目的の場合は、単に `testorg` のような名前を選択できます。 アプリに署名するためには、適切なプロビジョニング プロファイルが必要です。 詳細については、[Apple の開発者向けサイト](https://developer.apple.com/)を参照してください。
    1. プロジェクトの言語として macOS が選択されていることを確認します。
    1. ストーリーボードを使用するためのチェック ボックスとドキュメントベースのアプリケーションを作成するためのチェック ボックスを無効にします。 サンプル アプリ向けのシンプルな UI がプログラムにより作成されます。
    1. テストとコア データに関するすべてのチェック ボックスを無効にします。
1. プロジェクト ディレクトリの選択
    1. プロジェクトの配置先のディレクトリを選択します。 これにより、選択したディレクトリ内に `helloworld` ディレクトリが作成され、Xcode プロジェクトのすべてのファイルが入れられます。
    1. このサンプル プロジェクトの Git リポジトリの作成を無効にします。
1. アプリによるマイクの使用は、`Info.plist` ファイルでも宣言する必要があります。 対象のファイルを概要でクリックし、[Privacy - Microphone Usage Description]\(プライバシー - マイクの用途に関する説明\) キーを追加して、その値として "音声認識にマイクが必要" などを設定します。
    ![Info.plist の設定](media/sdk/qs-swift-ios-info-plist.png)
1. Xcode プロジェクトを閉じます。 後で CocoaPods の設定後に別のインスタンスを使用します。

## <a name="add-the-sample-code"></a>サンプル コードを追加する

1. `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` という名前の新しいヘッダー ファイルを、helloworld プロジェクト内の `helloworld` ディレクトリに配置し、次のコードをその中に貼り付けます。[!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. helloworld ターゲット用の Swift プロジェクトの設定に対するブリッジ ヘッダーの相対パス `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` を " *[Objective-C ブリッジ ヘッダー]* " フィールドに追加します。![ヘッダーのプロパティ](media/sdk/qs-swift-ios-bridging-header.png)
1. 自動生成された `AppDelegate.swift` ファイルの内容を次のように置き換えます。[!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/AppDelegate.swift#code)]
1. 自動生成された `ViewController.swift` ファイルの内容を次のように置き換えます。[!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/ViewController.swift#code)]
1. `ViewController.swift` で、文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。
1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

## <a name="install-the-sdk-as-a-cocoapod"></a>SDK を CocoaPod としてインストールする

1. CocoaPod 依存関係マネージャーをその[インストールの手順](https://guides.cocoapods.org/using/getting-started.html)に従ってインストールします。
1. サンプル アプリのディレクトリに移動します (`helloworld`)。 そのディレクトリに、次の内容を含んだ `Podfile` という名前のテキスト ファイルを配置します。[!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/Podfile)]
1. ターミナルで `helloworld` ディレクトリに移動して、`pod install` コマンドを実行します。 これにより、サンプル アプリと依存関係としての Speech SDK の両方を含んだ、`helloworld.xcworkspace` という Xcode ワークスペースが生成されます。 以降、このワークスペースを使用します。

## <a name="build-and-run-the-sample"></a>サンプルのビルドと実行

1. Xcode で `helloworld.xcworkspace` ワークスペースを開きます。
1. デバッグ出力が表示されるようにします ( **[View]**  >  **[Debug Area]**  >  **[Activate Console]** )。
1. **[Product]\(製品\)**  >  **[Destination]\(ターゲット\)** メニューの一覧から、お使いの開発マシンに接続された iOS デバイスまたは iOS シミュレーターをアプリのターゲットとして選択します。
1. コード例をビルドし、メニューから **[Product]**  >  **[Run]** の順に選択するか、 **[Play]** ボタンをクリックして、iOS シミュレーターで実行します。
1. アプリの [認識] ボタンをクリックして何か話すと、発話した内容のテキストが画面の下部に表示されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub でサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

