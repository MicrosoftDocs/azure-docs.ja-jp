---
title: コンテンツの翻訳を防止する - Translator
titleSuffix: Azure Cognitive Services
description: Translator によるコンテンツの翻訳を防止します。 Translator では、翻訳されないようコンテンツにタグを付けることができます。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: e89ff3f4c1f54d9e137d3dd51e325b908c826b03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897478"
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

2. コンテンツに `translate="no"` でタグ付けします。 これは、入力の textType が HTML に設定されている場合にのみ機能します。

   例:

   ```html
   <span translate="no">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div translate="no">This will not be translated.</div>
   <div>This will be translated. </div>
   ```
   
3. [動的ディクショナリ](dynamic-dictionary.md)を使用して決まった翻訳を指示する。

4. 文字列を翻訳対象として Translator に渡さないでください。

5. カスタム トランスレーター: 語句に対して 100% の確率で決まる翻訳は、[カスタム トランスレーターのディクショナリ](custom-translator/what-is-dictionary.md)を使用して指示します。


## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [翻訳操作を使用してテキストを翻訳する](reference/v3-0-translate.md)
