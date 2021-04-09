---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: fc4cc5063f72ff3f0db62cde79f7908add86166e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102434584"
---
:::row:::
    :::column span="3":::
        音声SDKは Windows10 およびWindows Sever 2016以降のバージョンをサポートしています。 これより以前のバージョンはサポートして **いません**。 以前のバージョンの Windows では、音声SDK の一部を使用することもできますが、これは推奨されません。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Windows" src="https://docs.microsoft.com/media/logos/logo_Windows.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### <a name="system-requirements"></a>システム要件

Windowsの音声SDKは、システムで <a href="https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads" target="_blank">Microsoft Visual StudioのVisual C++ 再配布可能パッケージ 2019</a>を必要とします。

- <a href="https://aka.ms/vs/16/release/vc_redist.x86.exe" target="_blank">x86 用にインストールする</a>
- <a href="https://aka.ms/vs/16/release/vc_redist.x64.exe" target="_blank"> x64用にインストールする</a>
- <a href="https://aka.ms/vs/16/release/vc_redist.arm64.exe" target="_blank">x64ARMx用にインストールする </a>

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

マイク入力のために、Media Foundation ライブラリをインストールする必要があります。 これらのライブラリは、Windows 10 および Windows Server 2016 に含まれます。 マイクがオーディオ入力デバイスとして使用されていない場合は、これらのライブラリがなくても、Speech SDK を使用できます。

必要な Speech SDK ファイルは、お使いのアプリケーションと同じディレクトリに展開できます。 この方法で、お使いのアプリケーションはライブラリに直接アクセスできます。 必ずお使いのアプリケーションと一致する正しいバージョン(x86/x64) を選択してください。

| 名前                                            | 機能                                             |
|-------------------------------------------------|------------------------------------------------------|
| `Microsoft.CognitiveServices.Speech.core.dll`   | Core SDK。ネイティブおよびマネージド展開に必要 |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | マネージド展開に必要                      |

> [!NOTE]
> リリース 1.3.0 以降、(以前のリリースで提供されていた) `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` ファイルは不要になりました。 この機能はコア SDK に統合されました。

> [!IMPORTANT]
> Windows フォーム アプリケーション (.NET Framework) の C# プロジェクトの場合は、ライブラリがプロジェクトのデプロイ設定に含まれていることを確認してください。 これは `Properties -> Publish Section` で確認できます。 `Application Files` ボタンをクリックし、一覧を下にスクロールして、対応するライブラリを見つけます。 値が `Included` に設定されていることを確認します。 プロジェクトが発行またはデプロイされると、Visual Studio にこのファイルが組み込まれます。

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
