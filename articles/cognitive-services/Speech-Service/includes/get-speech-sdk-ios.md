---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 0a9ca21cc14bb87797c962a89cf87ac184e73735
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434565"
---
:::row:::
    :::column span="3":::
        iOS 向けの開発時には、2つの音声Sdk を利用できます。 Objective-C音声SDK は、iOS Cocoアポストロフィパッケージとしてネイティブに使用できます。 また、.NET音声SDK は .NET Standard 2.0 を実装しているため、Xamarin で使用することもできます。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Objective-C-音声SDK と Swift <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">の使用の詳細については、「Objective-C をSwiftにインポートする 」を参照してください</a>。

### <a name="system-requirements"></a>システム要件

- macOS version 10.3以降
- 対象 iOS 9.3以降

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        iOS Cocoアポストロフィ d パッケージは、<a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (またはそれ以降) </a>統合開発環境 (IDE) と共にダウンロードして使用することができます。 <a href="https://aka.ms/csspeech/iosbinary" target="_blank">まず、バイナリ Cocoアポストロフィ </a>をダウンロードします。 使用目的に合わせて同じディレクトリ内のポッドを抽出し、*Podfile* を作成して、`pod`を `target`として一覧表示します。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'AppName' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.10.0'
end
```

# <a name="xamarinios"></a>[Xamarin.iOS](#tab/ios-xamarin)

:::row:::
    :::column span="3":::
        Xamarin iOS は、.NET 開発者向けの完全な iOS SDK を公開しています。 Visual Studio でC#またはF#を使用して、完全にネイティブな iOS アプリをビルドします。 詳細は、<a href="https://docs.microsoft.com/xamarin/ios/" target="_blank">Xamarin.iOS </a>を参照してください。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
            &nbsp; ❤️ &nbsp;         <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

---

#### <a name="additional-resources"></a>その他のリソース

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">iOS音声SDK クイックスタートのObjective-C ソースコード </a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">iOS音声SDK クイックスタート Swift ソースコード</a>