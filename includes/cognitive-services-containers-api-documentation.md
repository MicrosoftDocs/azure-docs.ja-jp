---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2019
ms.openlocfilehash: 94e95864d8bac2d6dc0ff690a2a8f53bd2db5a40
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522517"
---
## <a name="validate-container-is-running"></a>コンテナーが実行されていることを検証する 

コンテナーが実行されていることを検証する方法は複数あります。 

|Request|目的|
|--|--|
|`http://localhost:5000/`|コンテナーには、ホーム ページが用意されています。|
|`http://localhost:5000/status`|GET で要求され、エンドポイント クエリを発生させずにコンテナーが実行されていることを検証します。 これは Kubernetes の [liveness probe と readiness probe](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) に対して使用できます。|
|`http://localhost:5000/swagger`|コンテナーには、エンドポイントの完全なドキュメント一式のほか、`Try it now` 機能が用意されています。 この機能を使用すると、コードを一切記述することなく、お客様の設定を Web ベースの HTML フォームに入力したりクエリを実行したりすることができます。 クエリから戻ると、HTTP ヘッダーと HTTP 本文の必要な形式を示すサンプル CURL コマンドが得られます。 |

![コンテナーのホーム ページ](./media/cognitive-services-containers-api-documentation/container-webpage.png)
