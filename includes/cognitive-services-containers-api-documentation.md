---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 00cc63f53388ab7bea05a0b55784247f63477684
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704305"
---
## <a name="validate-that-a-container-is-running"></a>コンテナーが実行されていることを検証する 

コンテナーが実行されていることを検証する方法は複数あります。 

|Request|目的|
|--|--|
|`http://localhost:5000/`|コンテナーには、ホーム ページが用意されています。|
|`http://localhost:5000/status`|GET で要求され、エンドポイント クエリを発生させずにコンテナーが実行されていることを検証します。 この要求は Kubernetes の [liveness probe と readiness probe](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) に対して使用できます。|
|`http://localhost:5000/swagger`|コンテナーには、エンドポイントの完全なドキュメント一式と、`Try it now` 機能が用意されています。 この機能を使用すると、コードを一切記述することなく、お客様の設定を Web ベースの HTML フォームに入力したりクエリを実行したりできます。 クエリから戻った後、HTTP ヘッダーと HTTP 本文の必要な形式を示すサンプル CURL コマンドが得られます。 |

![コンテナーのホーム ページ](./media/cognitive-services-containers-api-documentation/container-webpage.png)
