---
title: クイック スタート:音声を合成する、Swift - Speech サービス
titleSuffix: Azure Cognitive Services
description: iOS 上で Swift と Speech SDK を使用して音声を合成する方法について説明します
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: 5fd7f125037777c55b748b45df49f8f5cd2d0409
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975892"
---
# <a name="quickstart-synthesize-speech-in-swift-on-ios-using-the-speech-sdk"></a>クイック スタート:iOS 上で Swift と Speech SDK を使用して音声を合成する

この記事では、Swift と Cognitive Services Speech SDK を使用して、テキストから音声を合成する iOS アプリを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

開始する前の前提条件の一覧を次に示します。

* Speech サービス用の[サブスクリプション キー](~/articles/cognitive-services/Speech-Service/get-started.md)。
* [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) 以降と [CocoaPods](https://cocoapods.org/) 以降がインストールされた macOS マシン。

## <a name="get-the-speech-sdk-for-ios"></a>iOS 用の Speech SDK を取得する

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

このチュートリアルは 1.7.0 より前のバージョンの SDK では動作しないことに注意してください。

iOS 用の Cognitive Services Speech SDK は、フレームワーク バンドルとして配布されています。
Xcode プロジェクトで [CocoaPod](https://cocoapods.org/) として使用することも、 https://aka.ms/csspeech/macosbinary からダウンロードして手動でリンクすることも可能です。 このガイドでは CocoaPod を使用します。

## <a name="create-an-xcode-project"></a>Xcode プロジェクトを作成する

Xcode を起動し、 **[File]**  >  **[New]**  >  **[Project]** の順にクリックして新しいプロジェクトを開始します。
テンプレートの選択ダイアログで、"iOS Single View App" テンプレートを選択します。

以降のダイアログで、次のように選択します。

1. [Project Options] ダイアログ
    1. クイック スタート アプリの名前を入力します (例: `helloworld`)。
    1. 既に Apple Developer アカウントをお持ちの場合は、適切な組織名と組織 ID を入力します。 テスト目的の場合は、単に `testorg` のような名前を選択できます。 アプリに署名するためには、適切なプロビジョニング プロファイルが必要です。 詳細については、[Apple の開発者向けサイト](https://developer.apple.com/)を参照してください。
    1. プロジェクトの言語として Swift が選択されていることを確認します。
    1. ストーリーボードを使用するためのチェック ボックスとドキュメントベースのアプリケーションを作成するためのチェック ボックスを無効にします。 サンプル アプリ向けのシンプルな UI がプログラムにより作成されます。
    1. テストとコア データに関するすべてのチェック ボックスを無効にします。
1. プロジェクト ディレクトリの選択
    1. プロジェクトの配置先のディレクトリを選択します。 これにより、選択したディレクトリ内に `helloworld` ディレクトリが作成され、Xcode プロジェクトのすべてのファイルが入れられます。
    1. このサンプル プロジェクトの Git リポジトリの作成を無効にします。
1. Xcode プロジェクトを閉じます。 後で CocoaPods の設定後に別のインスタンスを使用します。

## <a name="add-the-sample-code"></a>サンプル コードを追加する

1. `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` という名前の新しいヘッダー ファイルを、helloworld プロジェクト内の `helloworld` ディレクトリに配置し、次のコードをその中に貼り付けます。  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/text-to-speech/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. ![[ヘッダーのプロパティ]](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-bridging-header.png) で、helloworld ターゲット用の Swift プロジェクトの設定に対するブリッジ ヘッダーの相対パス `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` を *[Objective-C ブリッジ ヘッダー]* フィールドに追加します。
1. 次の操作によって、自動生成された `AppDelegate.swift` ファイルの内容を置き換えます。  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/text-to-speech/helloworld/helloworld/AppDelegate.swift#code)]
1. 次の操作によって、自動生成された `ViewController.swift` ファイルの内容を置き換えます。  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/text-to-speech/helloworld/helloworld/ViewController.swift#code)]
1. `ViewController.swift` で、文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。
1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](~/articles/cognitive-services/Speech-Service/regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

## <a name="install-the-sdk-as-a-cocoapod"></a>SDK を CocoaPod としてインストールする

1. CocoaPod 依存関係マネージャーをその[インストールの手順](https://guides.cocoapods.org/using/getting-started.html)に従ってインストールします。
1. サンプル アプリのディレクトリに移動します (`helloworld`)。 そのディレクトリに、次の内容を含んだ `Podfile` という名前のテキスト ファイルを配置します。  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/text-to-speech/helloworld/Podfile)]
1. ターミナルで `helloworld` ディレクトリに移動して、`pod install` コマンドを実行します。 これにより、サンプル アプリと依存関係としての Speech SDK の両方を含んだ、`helloworld.xcworkspace` という Xcode ワークスペースが生成されます。 以降、このワークスペースを使用します。

## <a name="build-and-run-the-sample"></a>サンプルのビルドと実行

1. Xcode で `helloworld.xcworkspace` ワークスペースを開きます。
1. デバッグ出力が表示されるようにします ( **[View]**  >  **[Debug Area]**  >  **[Activate Console]** )。
1. **[Product]\(製品\)**  >  **[Destination]\(ターゲット\)** メニューの一覧から、お使いの開発マシンに接続された iOS デバイスまたは iOS シミュレーターをアプリのターゲットとして選択します。
1. コード例をビルドし、メニューから **[Product]**  >  **[Run]** の順に選択するか、 **[Play]** ボタンをクリックして、iOS シミュレーターで実行します。
1. テキストを入力してアプリのボタンをクリックすると、合成された音声が再生されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub でサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)
