---
title: クイック スタート:マイクから音声を認識する、C# (Xamarin) - Speech サービス
titleSuffix: Azure Cognitive Services
description: この記事では、Cognitive Services Speech SDK を使用して、ユニバーサル Windows プラットフォーム (UWP)、Android、および iOS 向けのクロスプラットフォーム C# Xamarin アプリケーションを作成します。 デバイスまたはシミュレーターのマイクからの音声を、リアルタイムでテキストに変換します。 このアプリケーションは、Speech SDK NuGet パッケージと Microsoft Visual Studio 2019 を使用して作成します。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 48a80d7367943c2c86fd3822a5c236b0cdf11846
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88926437"
---
## <a name="prerequisites"></a>前提条件

開始する前に、以下の操作を行います。

> [!div class="checklist"]
> * [Azure Speech リソースを作成する](../../../../get-started.md)
> * [開発環境を設定し、空のプロジェクトを作成する](../../../../quickstarts/setup-platform.md?tabs=xamarin&pivots=programming-language-csharp)
> * オーディオ キャプチャ用のマイクにアクセスできることを確認する

既にこれを行っている場合は、これで完了です。 このまま続けましょう。

## <a name="add-sample-code-for-the-common-helloworld-project"></a>共通の helloworld プロジェクトのサンプル コードを追加する

共通の helloworld プロジェクトには、クロスプラットフォーム アプリケーション向けのプラットフォーム非依存の実装が含まれています。 ここで、アプリケーションのユーザー インターフェイスを定義する XAML コードを追加し、実装の C# コード ビハインドを追加します。

1. **ソリューション エクスプローラー**で、共通の helloworld プロジェクトの下にある `MainPage.xaml` を開きます。

1. デザイナーの XAML ビューで、次の XAML スニペットを `<StackLayout>` と `</StackLayout>` の間の **Grid** タグに挿入します。

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml)]

1. **ソリューション エクスプローラー**で、コードビハインドのソース ファイル `MainPage.xaml.cs` を開きます `MainPage.xaml` にグループ化されています。

1. その中のすべてのコードを次のスニペットに置き換えます。

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. ソース ファイルの `OnRecognitionButtonClicked` ハンドラーから `YourSubscriptionKey` という文字列を見つけて、ご利用のサブスクリプション キーに置き換えます。


1. `OnRecognitionButtonClicked` ハンドラーから `YourServiceregion` という文字列を見つけて、自分のサブスクリプションに関連付けられている[リージョン](https://aka.ms/speech/sdkregion)の**リージョン識別子**に置き換えます。 

1. 次に、異なるプラットフォーム プロジェクト (UWP、Android、iOS など) からマイクへのアクセス許可を照会するための [Xamarin サービス](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/)を作成する必要があります。 これを行うには、helloworld プロジェクトの下に *Services* という名前の新しいフォルダーを追加し、その下に新しい C# ソース ファイルを作成します。 *Services* フォルダーを右クリックし、 **[追加]**  >  **[新しい項目]**  >  **[コード ファイル]** の順に選択します。 ファイルの名前を `IMicrophoneService.cs` に変更し、次のスニペットのすべてのコードをそのファイルに配置します。

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

#### <a name="android"></a>[Android](#tab/x-android)
## <a name="add-sample-code-for-the-helloworldandroid-project"></a>`helloworld.Android` プロジェクトのサンプル コードを追加する

ここで、アプリケーションの Android 固有の部分を定義した C# コードを追加します。

1. **ソリューション エクスプローラー**で、helloworld.Android プロジェクトの下の `MainActivity.cs` を開きます。

1. その中のすべてのコードを次のスニペットに置き換えます。

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. 次に、`MicrophoneService` の Android 固有の実装を追加するために、helloworld.Android プロジェクトの下に新しい *Services* フォルダーを作成します。 その後、その下に新しい C# ソース ファイルを作成します。 ファイルの名前を `MicrophoneService.cs` に変更します。 次のコード スニペットをコピーしてそのファイルに貼り付けます。

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. その後、*Properties* フォルダーの下の `AndroidManifest.xml` を開きます。 `<manifest>` と `</manifest>` の間に、次のマイクの uses-permission 設定を追加します。

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```
   
#### <a name="ios"></a>[iOS](#tab/ios)
## <a name="add-sample-code-for-the-helloworldios-project"></a>`helloworld.iOS` プロジェクトのサンプル コードを追加する

ここで、アプリケーションの iOS 固有の部分を定義した C# コードを追加します。 さらに、helloworld.iOS プロジェクトに Apple デバイス固有の構成を作成します。

1. **ソリューション エクスプローラー**で、helloworld.iOS プロジェクトの下の `AppDelegate.cs` を開きます。

1. その中のすべてのコードを次のスニペットに置き換えます。

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. 次に、`MicrophoneService` の iOS 固有の実装を追加するために、helloworld.iOS プロジェクトの下に新しい *Services* フォルダーを作成します。 その後、その下に新しい C# ソース ファイルを作成します。 ファイルの名前を `MicrophoneService.cs` に変更します。 次のコード スニペットをコピーしてそのファイルに貼り付けます。

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. テキスト エディターで、helloworld.iOS プロジェクトの下の `Info.plist` を開きます。 次のキーと値のペアを dict セクションの下に追加します。

   <key>NSMicrophoneUsageDescription</key>
   <string>このサンプル アプリではマイクへのアクセスが必要です</string>

   > [!NOTE]
   > iPhone デバイス用にビルドする場合は、`Bundle Identifier` がお使いのデバイスのプロビジョニング プロファイル アプリ ID と一致していることを確認します。 そうでないと、ビルドは失敗します。 iPhoneSimulator を使用する場合は、そのままにしてかまいません。

1. Windows PC 上でビルドする場合は、 **[ツール]**  >  **[iOS]**  >  **[Mac とペアリング]** で、ビルド用に Mac デバイスとの接続を確立します。 Visual Studio で表示されるウィザードの指示に従って、Mac デバイスとの接続を有効にします。

#### <a name="uwp"></a>[UWP](#tab/helloworlduwp)
## <a name="add-sample-code-for-the-helloworlduwp-project"></a>`helloworld.UWP` プロジェクトのサンプル コードを追加する

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>helloworld.UWP プロジェクトのサンプル コードを追加する

ここでは、アプリケーションの UWP 固有の部分を定義した C# コードを追加します。

1. **ソリューション エクスプローラー**で、helloworld.UWP プロジェクトの下の `MainPage.xaml.cs` を開きます。

1. その中のすべてのコードを次のスニペットに置き換えます。

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. 次に、`MicrophoneService` の UWP 固有の実装を追加するために、helloworld.UWP プロジェクトの下に新しい *Services* フォルダーを作成します。 その後、その下に新しい C# ソース ファイルを作成します。 ファイルの名前を `MicrophoneService.cs` に変更します。 次のコード スニペットをコピーしてそのファイルに貼り付けます。

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. 次に、Visual Studio 内の helloworld.UWP プロジェクトの下にある `Package.appxmanifest` ファイルをダブルクリックします。 **[Capabilities]\(機能\)** で、 **[マイク]** が選択されていることを確認し、ファイルを保存します。

1. 次に、Visual Studio 内で `helloworld.UWP` プロジェクト下にある `Package.appxmanifest` ファイルをダブルクリックし、 **[機能]**  >  **[マイク]** をオンにして、ファイルを保存します。
   > 注:次のような警告が表示された場合: "証明書ファイルが存在しません: helloworld.UWP_TemporaryKey.pfx"、[音声テキスト変換](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)のサンプルで詳細を確認してください。

1. メニュー バーから **[ファイル]**  >  **[すべて保存]** を選択して変更内容を保存します。

## <a name="build-and-run-the-uwp-application"></a>UWP アプリケーションをビルドして実行する

1. helloworld.UWP をスタートアップ プロジェクトとして設定します。 helloworld.UWP プロジェクトを右クリックし、 **[ビルド]** を選択してアプリケーションをビルドします。

1. **[デバッグ]**  >  **[デバッグの開始]** の順に選択して (または **F5** キーを押して) アプリケーションを起動します。 **[helloworld]** ウィンドウが表示されます。

   ![サンプル UWP 音声認識アプリケーション (C#) - クイックスタート](../../../../media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. **[Enable Microphone]\(マイクを有効にする\)** を選択します。 アクセス許可要求が表示されたら、 **[はい]** を選択します。

   ![マイクへのアクセス許可要求](../../../../media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. **[Start Speech recognition]\(音声認識を開始する\)** を選択し、デバイスのマイクに向かって英語のフレーズを話します。 音声が Speech サービスに送信されてテキストに変換され、ウィンドウに表示されます。

   ![音声認識ユーザー インターフェイス](../../../../media/sdk/qs-csharp-xamarin-uwp-ui-result.png)
* * *

## <a name="build-and-run-the-android-and-ios-applications"></a>Android アプリケーションと iOS アプリケーションをビルドして実行する

デバイスまたはシミュレーターで Android および iOS アプリケーションをビルドおよび実行する方法は、UWP の場合と同様です。 この記事の「前提条件」セクションで必須とされているすべての SDK が適切にインストールされていることを確認してください。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
