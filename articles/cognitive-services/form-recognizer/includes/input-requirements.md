---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 73a7795629a94395b43bfca191cec7b2c4773611
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68481487"
---
Form Recognizer は、次の要件を満たす入力ドキュメントを処理します。

* 形式は、JPG、PNG、または PDF (テキストまたはスキャン済み) である必要があります。 テキストが埋め込まれている PDF は、文字の抽出と場所にエラーが生じる可能性がないため、最適です。
* PDF がパスワードでロックされている場合は、送信前にロックを解除する必要があります。
* ファイル サイズは 4 メガバイト (MB) 未満である必要があります。
* イメージの場合、寸法は 50 x 50 ピクセルと 4200 x 4200 ピクセルの間に収める必要があります。
* 紙のドキュメントからスキャンする場合、フォームは高品質のスキャンでなければなりません。
* テキストでは、ラテン アルファベット (英語の文字) を使用する必要があります。
* データには、キーと値を含める必要があります。
* キーは値の上または左に配置されていてかまいませんが、下または右に配置されていてはいけません。

Form Recognizer は現在、次の種類の入力データをサポートしていません。

* 複雑なテーブル (入れ子になったテーブル、マージされたヘッダーまたはセルなど)。
* チェックボックスまたはラジオ ボタン。
* 50 ページを超える PDF ドキュメント。