---
author: patrickfarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 816fa4b0df2fab77bcde261f30d8bad584257fd7
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592607"
---
Form Recognizer は、次の要件を満たす入力ドキュメントを処理します。

* 形式は、JPG、PNG、または PDF (テキストまたはスキャン済み) である必要があります。 テキストが埋め込まれている PDF は、文字の抽出と場所にエラーが生じる可能性がないため、最適です。
* ファイル サイズは 4 メガバイト (MB) 未満である必要があります。
* イメージの場合、寸法は 50 x 50 ピクセルと 4200 x 4200 ピクセルの間に収める必要があります。
* 紙のドキュメントからスキャンする場合、フォームは高品質のスキャンでなければなりません。
* テキストでは、ラテン アルファベット (英語の文字) を使用する必要があります。
* データは (手書きではなく) 印刷する必要があります。
* データには、キーと値を含める必要があります。
* キーは値の上または左に配置されていてかまいませんが、下または右に配置されていてはいけません。

Form Recognizer は現在、次の種類の入力データをサポートしていません。

* 複雑なテーブル (入れ子になったテーブル、マージされたヘッダーまたはセルなど)。
* チェックボックスまたはラジオ ボタン。
* 50 ページを超える PDF ドキュメント。