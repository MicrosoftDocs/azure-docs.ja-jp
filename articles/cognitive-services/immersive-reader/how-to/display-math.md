---
title: イマーシブ リーダーで数式を表示する
titleSuffix: Azure Cognitive Services
description: この記事では、イマーシブ リーダーで数式を表示する方法について説明します。
author: nitinme
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: 75b748604e8faa502970ac71e3fec4fae2774c19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91334516"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>イマーシブ リーダーで数式を表示する方法

イマーシブ リーダーは、数学用マークアップ言語 ([MathML](https://developer.mozilla.org/docs/Web/MathML)) の形式で指定された数式を表示できます。
MIME の種類は、イマーシブ リーダーの[チャンク](../reference.md#chunk)で設定できます。 詳細については、[サポートされる MIME の種類](../reference.md#supported-mime-types)に関するセクションを参照してください。

## <a name="send-math-to-the-immersive-reader"></a>イマーシブ リーダーに数式を送信する
イマーシブ リーダーに数式を送信するには、MathML を含んだチャンクを指定し、MIME の種類を ```application/mathml+xml``` に設定します。

たとえば、次のようなコンテンツがあるとします。

```html
<div id='ir-content'>
    <math xmlns='http://www.w3.org/1998/Math/MathML'>
        <mfrac>
            <mrow>
                <msup>
                    <mi>x</mi>
                    <mn>2</mn>
                </msup>
                <mo>+</mo>
                <mn>3</mn>
                <mi>x</mi>
                <mo>+</mo>
                <mn>2</mn>
            </mrow>
            <mrow>
                <mi>x</mi>
                <mo>−</mo>
                <mn>3</mn>
            </mrow>
        </mfrac>
        <mo>=</mo>
        <mn>4</mn>
    </math>
</div>
```

この場合、次の JavaScript を使用してコンテンツを表示できます。

```javascript
const data = {
    title: 'My Math',
    chunks: [{
        content: document.getElementById('ir-content').innerHTML.trim(),
        mimeType: 'application/mathml+xml'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

イマーシブ リーダーを起動すると、次のように表示されます。

![イマーシブ リーダーにおける数式](../media/how-tos/1-math.png)

## <a name="next-steps"></a>次のステップ

* [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) と [Immersive Reader SDK リファレンス](../reference.md)を探索する