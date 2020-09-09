---
title: コンテンツの翻訳を防止する - Translator
titleSuffix: Azure Cognitive Services
description: Translator によるコンテンツの翻訳を防止します。 Translator では、翻訳されないようコンテンツにタグを付けることができます。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 37d2e4c0131569ab50ebf49ff73b6adf7a420713
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996178"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>Translator によるコンテンツの翻訳を防止する方法

Translator では、翻訳されないようコンテンツにタグを付けることができます。 たとえば、コードやブランド名、ローカライズしても意味がない語句にタグを付けることが考えられます。

## <a name="methods-for-preventing-translation"></a>翻訳を防止する方法

1. コンテンツに `notranslate` でタグ付けします。 入力の textType が HTML に設定されている場合にのみ機能するのは仕様です。

   例:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. [動的ディクショナリ](dynamic-dictionary.md)を使用して決まった翻訳を指示する。

3. 文字列を翻訳対象として Translator に渡さないでください。

4. カスタム トランスレーター: 語句に対して 100% の確率で決まる翻訳は、[カスタム トランスレーターのディクショナリ](custom-translator/what-is-dictionary.md)を使用して指示します。


## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [Translator 呼び出しでの翻訳を防止する](reference/v3-0-translate.md)
