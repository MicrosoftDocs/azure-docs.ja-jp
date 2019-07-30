---
title: Speaker Recognition API とは
titleSuffix: Azure Cognitive Services
description: Cognitive Services の Speaker Recognition API には、高度な話者認証および話者識別アルゴリズムが用意されています。
services: cognitive-services
author: dwlin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speaker-recognition
ms.topic: overview
ms.date: 10/01/2018
ms.author: nitinme
ms.openlocfilehash: 15fc320a5b76a50def634d937a02fa639dce3739
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67845870"
---
# <a name="speaker-recognition-api"></a>Speaker Recognition API

Azure Cognitive Services の Speaker Recognition API へようこそ 話者認識 API は、最も高度な話者認証および話者識別アルゴリズムを提供するクラウド ベースの API です。 Speaker Recognition は、話者認証と話者識別の 2 つのカテゴリに分類できます。


## <a name="speaker-verification"></a>話者認証

人の声には固有の特性があるため、指紋と同じように、ユーザーを一意に識別することができます。  アクセス制御および認証シナリオの信号として音声を使用する、新しい革新的なツールが登場しました。これにより、セキュリティ水準を高め、お客様の認証エクスペリエンスを簡素化します。

話者認証 API は人の声や会話でユーザーを自動的に確認し、認証できます。

### <a name="enrollment"></a>加入

話者認証の登録はテキストに依存します。つまり、登録フェーズと検証フェーズの両方で、話者が使用する具体的なパス フレーズを選択する必要があります。

登録フェーズでは、具体的なフレーズを発話することで話者の音声を記録し、その後いくつかの特徴が抽出され、選択したフレーズが認識されます。 抽出された特徴と選択したフレーズの両方を合わせることで、一意の声紋を形成します。

### <a name="verification"></a>確認

検証フェーズでは、入力された音声とフレーズが、登録された声紋および語句と照合され、同一人物によって発話されているかどうか、また発話された語句が正しいかどうかが検証されます。

話者認証の詳細については、 [話者 - 検証](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652)の API に関するページを参照してください。

## <a name="speaker-identification"></a>話者識別

Speaker Identification API は、オーディオ ファイル内の話者を、指定した見込み話者のグループから自動的に識別します。 入力音声を指定した話者のグループと照合し、一致があった場合に話者の ID を返します。

すべての話者が、登録プロセスでそれぞれの声をシステムに登録し、声紋を作成しておく必要があります。


### <a name="enrollment"></a>加入

話者識別の登録はテキストに依存しません。つまり、オーディオ内で話者が話す内容に制限はありません。 話者の音声が記録され、いくつかの特徴が抽出されて一意の声紋を形成します。


### <a name="recognition"></a>認識

認識中は、一連の予測される話者と共に、不明話者のオーディオが提供されます。 入力された音声は、それが誰の声かを特定するためにすべての話者と照合され、一致が見つかった場合は、その話者の ID が返されます。

話者識別の詳細については、 [話者 - 識別](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e)の API に関するページを参照してください。
