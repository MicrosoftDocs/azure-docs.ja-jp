---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 4a4705647b90d29f47e37b88531f3432c6a2f448
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434560"
---
MacOS 用に開発する場合は、3つの音声Sdk を利用できます。

- Objective-C S音声SDK は、Cocoアポストロフィ d パッケージとしてネイティブに使用できます。
- .NET音声SDKは **.NET Standard 2.0** を実装しているため Xamarin. Mac と共に使用できます
- Python音声SDK は PyPI モジュールとして提供されています。

> [!TIP]
> Objective-C-音声SDK と Swift <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">の使用の詳細については、「Objective-C をSwiftにインポートする 」を参照してください</a>。

### <a name="system-requirements"></a>システム要件

- macOS version 10.13以降

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        macOS Cocoアポストロフィ d パッケージは、 <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (またはそれ以降) </a>統合開発環境 (IDE) と共にダウンロードして使用することができます。 <a href="https://aka.ms/csspeech/macosbinary" target="_blank">まず、バイナリ Cocoアポストロフィ </a>をダウンロードします。 使用目的に合わせて同じディレクトリ内のポッドを抽出し、*Podfile* を作成して、`pod`を `target`として一覧表示します。
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

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.15.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin. Mac は、.NET 開発者向けの完全な macOS SDK を公開しC#を使用してネイティブ Mac アプリケーションを構築します。 詳細については、<a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Xamarin.Mac</a>を参照してください。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>その他のリソース

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">macOS音声 SDK クイックスタートのObjective-Cソースコード</a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">macOS音声SDK クイックスタート Swift ソースコード</a>