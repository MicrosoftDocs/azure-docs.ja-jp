---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 4079b0cab0126a9750bbb91ca7046aa18dbe9ece
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657817"
---
Form Recognizer は、次の要件を満たす入力ドキュメントを処理します。

* 形式は、JPG、PNG、または PDF (テキストまたはスキャン済み) である必要があります。 テキストが埋め込まれている PDF は、文字の抽出と場所にエラーが生じる可能性がないため、最適です。
* PDF がパスワードでロックされている場合は、送信前にロックを解除する必要があります。
* ファイル サイズは 4 MB 未満である必要があります。
* 画像の場合、寸法は 600 x 100 ピクセルと 4,200 x 4,200 ピクセルの間に収める必要があります。
* 紙のドキュメントからスキャンする場合、フォームは高品質のスキャンでなければなりません。
* テキストでは、ラテン アルファベット (英語の文字) を使用する必要があります。
* データには、キーと値を含める必要があります。
* キーは値の上または左に配置されていてかまいませんが、下または右に配置されていてはいけません。

Form Recognizer は現在、次の種類の入力データをサポートしていません。

* 複雑なテーブル (入れ子になったテーブル、マージされたヘッダーまたはセルなど)。
* チェックボックスまたはラジオ ボタン。
* 50 ページを超える PDF ドキュメント。
