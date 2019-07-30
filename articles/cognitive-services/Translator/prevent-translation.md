---
title: コンテンツの翻訳を防止する - Translator Text API
titlesuffix: Azure Cognitive Services
description: Translator Text API によるコンテンツの翻訳を防止します。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 02c4e90879b81e29f3972d262f36d5c6b6d8e841
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448282"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Translator Text API によるコンテンツの翻訳を防止する方法

Translator Text API では、翻訳されないようコンテンツにタグを付けることができます。 たとえば、コードやブランド名、ローカライズしても意味がない語句にタグを付けることが考えられます。

## <a name="methods-for-preventing-translation"></a>翻訳を防止する方法
1. Twitter タグ (@somethingtopassthrough または #somethingtopassthrough) でエスケープする。 翻訳後にエスケープを解除します。

2. コンテンツに `notranslate` でタグ付けします。

   例:

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. [動的ディクショナリ](dynamic-dictionary.md)を使用して決まった翻訳を指示する。

4. Translator Text API に翻訳対象の文字列を渡さない。

5. カスタム トランスレーター: 語句に対して 100% の確率で決まる翻訳は、[カスタム トランスレーターのディクショナリ](custom-translator/what-is-dictionary.md)を使用して指示します。


## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [Translator API 呼び出しでの翻訳を防止する](reference/v3-0-translate.md)
