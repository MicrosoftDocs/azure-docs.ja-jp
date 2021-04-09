---
title: よく寄せられる質問 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services の Computer Vision API についてよく寄せられる質問とその回答を紹介します。
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: e8ab205a43e5cb1e8e2b96dbd9600e1fceb29403
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "68564594"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Computer Vision API についてよく寄せられる質問

> [!TIP]
> この FAQ で質問に対する回答が見つからない場合は、[StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) の Computer Vision API コミュニティに質問するか、[UserVoice のヘルプとサポート](https://cognitive.uservoice.com/)にご連絡ください

---

**質問**: *カスタム タグを使用するように Computer Vision API をトレーニングできますか?たとえば、ネコの品種の写真をフィードして AI を "トレーニング" し、AI 要求に対して品種値を受け取りたいと思います。*

**回答**: この機能は、現在使用できません。 ただし、マイクロソフトのエンジニアは、Computer Vision へのこの機能の追加に取り組んでいます。

---

**質問**: *Computer Vision をインターネット接続なしでローカルに使用することはできますか?*

**回答**: 現在、オンプレミスまたはローカルのソリューションは提供していません。

---

**質問**: *Computer Vision をナンバー プレートの読み取りに使用できますか?*

**回答**: Vision API は、OCR による優れたテキスト検出を提供しますが、ナンバー プレート用には現在最適化されていません。 マイクロソフトではサービスの向上に努めており、自動ナンバー プレート認識用の OCR を機能要求の一覧に追加しました。

---

**質問**: *手書き認識ではどのような種類の筆記面がサポートされますか?*

**回答**: ホワイトボード、白紙、黄色の付箋など、さまざまな種類の筆記面で動作します。

---

**質問**: *手書き認識操作にかかる時間はどれくらいですか?*

**回答**: 所要時間は、テキストの長さによって異なります。 長いテキストの場合は数秒かかることがあります。 したがって、手書きテキストの認識操作の完了後、手書きテキスト操作の結果の取得操作を使用して結果を取得する前に待機することが必要な場合があります。

---

**質問**: *手書き認識テクノロジは、行の途中にキャレットを使用して挿入されたテキストをどのように処理しますか?*

**回答**: このようなテキストは、手書き認識操作によって別々の行として返されます。

---

**質問**: *手書き認識テクノロジでは、線で消された単語または行はどのように処理されますか?*

**回答**: 単語が認識できないことを示すために複数の線で消されている場合、手書き認識操作はそれらを認識対象としません。 一方、単語が 1 本の線を使用して消されている場合、線はノイズと見なされ、単語は手書き認識操作によって認識対象となります。

---

**質問**: *手書き認識テクノロジではどのようなテキスト方向がサポートされますか?*

**回答**: 最大 30° から 40° の角度が付いたテキストは手書き認識操作によって認識対象となります。

---
