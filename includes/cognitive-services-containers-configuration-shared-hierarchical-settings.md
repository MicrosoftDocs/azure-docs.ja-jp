---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 03ec8740a4cf36bf3d09dade8a24b155c09d1299
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56740794"
---
コンテナーの設定は階層になっていて、共有された階層がホスト コンピューター上のすべてのコンテナーによって使用されます。

次のいずれかを使用して設定を指定できます。

* [環境変数](#environment-variable-settings)
* [コマンドライン引数](#command-line-argument-settings)

環境変数の値によってコマンドライン引数の値がオーバーライドされ、さらにコンテナー イメージの既定値がオーバーライドされます。 同じ構成設定に、環境変数とコマンドライン引数で異なる値を指定した場合、環境変数の値が、インスタンス化されたコンテナーによって使用されます。

|優先順位|設定の場所|
|--|--|
|1|環境変数| 
|2|コマンド ライン|
|3|コンテナー イメージの既定値|

### <a name="environment-variable-settings"></a>環境変数の設定

環境変数を使用する利点は次のとおりです。

* 複数の設定を構成できる。
* 複数のコンテナーで同じ設定を使用できる。

### <a name="command-line-argument-settings"></a>コマンドライン引数の設定

コマンドライン引数を使用する利点は、各コンテナーで異なる設定を使用できることです。
