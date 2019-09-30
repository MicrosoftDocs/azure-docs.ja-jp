---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 081155c8984f39cc9cc8e905eb108c07dee98d2e
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034425"
---
## <a name="validate-that-a-container-is-running"></a>コンテナーが実行されていることを検証する 

コンテナーが実行されていることを検証する方法は複数あります。 問題になっているコンテナーの "*外部 IP*" アドレスと公開ポートを特定し、任意の Web ブラウザーを開きます。 以下に示した各種の要求 URL を使用して、コンテナーが実行中であることを確認します。 以下に示した例の要求 URL は `http://localhost:5000` ですが、実際のコンテナーは異なる可能性があります。 ベースとなるのは実際のコンテナーの "*外部 IP*" アドレスと公開ポートであることに注意してください。

| 要求 URL | 目的 |
|--|--|
| `http://localhost:5000/` | コンテナーには、ホーム ページが用意されています。 |
| `http://localhost:5000/status` | HTTP GET で要求され、エンドポイント クエリを発生させずにコンテナーが実行されていることを検証します。 この要求は Kubernetes の [liveness probe と readiness probe](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) に対して使用できます。 |
| `http://localhost:5000/swagger` | コンテナーには、エンドポイントの完全なドキュメント一式と、 **[Try it out]\(試してみる\)** の機能が用意されています。 この機能を使用すると、コードを一切記述することなく、お客様の設定を Web ベースの HTML フォームに入力したりクエリを実行したりできます。 クエリから戻った後、HTTP ヘッダーと HTTP 本文の必要な形式を示すサンプル CURL コマンドが得られます。 |

![コンテナーのホーム ページ](./media/cognitive-services-containers-api-documentation/container-webpage.png)
