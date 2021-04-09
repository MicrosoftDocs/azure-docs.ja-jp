---
title: アプリケーションの設定 - LUIS
description: Azure Cognitive Services の言語を理解するアプリのアプリケーション設定は、アプリとポータルに格納されています。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/04/2020
ms.openlocfilehash: 0578e3c3c952a475c6beb01ffcf354e19eda6e26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91319165"
---
# <a name="app-and-version-settings"></a>アプリとバージョンの設定

これらの設定は[エクスポートされた](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)アプリに格納され、REST API または LUIS ポータルを使用して更新されます。

アプリのバージョン設定を変更すると、アプリのトレーニング状態がトレーニングなしにリセットされます。

[!INCLUDE [App and version settings](includes/app-version-settings.md)]


テキストのリファレンスと例を次に示します。

* [句読点](#punctuation-normalization)
* [分音記号](#diacritics-normalization)

## <a name="diacritics-normalization"></a>分音文字の正規化

次の発話は、分音記号の正規化が発話に与える影響を示しています。

|分音記号を false に設定した場合|分音記号を true に設定した場合|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>分音記号の言語サポート

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>ポルトガル語 (ブラジル) `pt-br` の分音記号

|分音記号を false に設定|分音記号を true に設定|
|-|-|
|`á`|`a`|
|`â`|`a`|
|`ã`|`a`|
|`à`|`a`|
|`ç`|`c`|
|`é`|`e`|
|`ê`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ô`|`o`|
|`õ`|`o`|
|`ú`|`u`|
|||

#### <a name="dutch-nl-nl-diacritics"></a>オランダ語 `nl-nl` の分音記号

|分音記号を false に設定|分音記号を true に設定|
|-|-|
|`á`|`a`|
|`à`|`a`|
|`é`|`e`|
|`ë`|`e`|
|`è`|`e`|
|`ï`|`i`|
|`í`|`i`|
|`ó`|`o`|
|`ö`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|||

#### <a name="french-fr--diacritics"></a>フランス語 `fr-` の分音記号

これには、フランスとカナダ両方のサブカルチャが含まれます。

|分音記号を false に設定|分音記号を true に設定|
|--|--|
|`é`|`e`|
|`à`|`a`|
|`è`|`e`|
|`ù`|`u`|
|`â`|`a`|
|`ê`|`e`|
|`î`|`i`|
|`ô`|`o`|
|`û`|`u`|
|`ç`|`c`|
|`ë`|`e`|
|`ï`|`i`|
|`ü`|`u`|
|`ÿ`|`y`|

#### <a name="german-de-de-diacritics"></a>ドイツ語 `de-de` の分音記号

|分音記号を false に設定|分音記号を true に設定|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>イタリア語 `it-it` の分音記号

|分音記号を false に設定|分音記号を true に設定|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`|
|`í`|`i`|
|`î`|`i`|
|`ò`|`o`|
|`ó`|`o`|
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>スペイン語 `es-` の分音記号

これには、スペインとカナダ両方のメキシコ人が含まれます。

|分音記号を false に設定|分音記号を true に設定|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|

## <a name="punctuation-normalization"></a>句読点の正規化

次の発話は、句読点が発話に与える影響を示しています。

|句読点が False に設定されている場合|句読点が True に設定されている場合|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>句読点を削除

`NormalizePunctuation` が true に設定されている場合、次の句読点は削除されます。

|句読点|
|--|
|`-`|
|`.`|
|`'`|
|`"`|
|`\`|
|`/`|
|`?`|
|`!`|
|`_`|
|`,`|
|`;`|
|`:`|
|`(`|
|`)`|
|`[`|
|`]`|
|`{`|
|`}`|
|`+`|
|`¡`|

## <a name="next-steps"></a>次のステップ

* 分音記号と句読点の[概念](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation)について説明します。
