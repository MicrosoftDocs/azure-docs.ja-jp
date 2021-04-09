---
title: Azure Front Door アーキテクチャのルール エンジンと用語
description: この記事では、Azure Front Door のルール エンジン機能の概要を示します。
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/29/2020
ms.author: duau
ms.openlocfilehash: 8e478cebcf8c5c9365100ade23c3d610c24930ba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91569751"
---
# <a name="what-is-rules-engine-for-azure-front-door"></a>Azure Front Door のルール エンジンとは 

ルール エンジンにより、エッジでの HTTP 要求の処理方法をカスタマイズし、Web アプリケーションの動作をより細かく制御することができます。 Azure Front Door のルール エンジンは、以下のようないくつかの重要な機能で構成されています。

* HTTPS を適用することで、すべてのエンド ユーザーがセキュリティで保護された接続でコンテンツを利用するようにします。
* HTTP Strict-Transport-Security (HSTS)、X-XSS-Protection、Content-Security-Policy、X-Frame-Options のようなブラウザーベースの脆弱性を防ぐためのセキュリティ ヘッダー、およびクロスオリジン リソース共有 (CORS) シナリオのための Access-Control-Allow-Origin ヘッダーを実装します。 セキュリティベースの属性は、Cookie でも定義できます。
* 要求ヘッダーの内容、Cookie、またはクエリ文字列のパターンに基づいて、モバイル版またはデスクトップ版のアプリケーションに要求をルーティングします。
* リダイレクト機能を使用して 301、302、307、308 リダイレクトをクライアントに返し、新しいホスト名、パス、またはプロトコルに直接送信します。
- 受信要求に基づき、ルートのキャッシュ構成を動的に変更します。
- 要求 URL パスを書き換え、構成されているバックエンド プールの適切なバックエンドに要求を転送します。

## <a name="architecture"></a>アーキテクチャ 

ルール エンジンは、エッジで要求を処理します。 要求が Front Door エンドポイントに到達すると、最初に WAF が実行され、次にフロントエンドまたはドメインに関連付けられているルール エンジン構成が実行されます。 ルール エンジン構成が実行される場合、親のルーティング規則が既に一致していることを意味します。 各ルール内のすべてのアクションが実行されるようにするには、ルール内のすべての一致条件が満たされる必要があります。 要求がルール エンジン構成のどの条件にも一致しない場合は、次に既定のルーティング規則が実行されます。 

たとえば、次の図では、応答ヘッダーを追加するようにルール エンジンが構成されます。 一致条件が満たされた場合、ヘッダーのキャッシュ制御の最長有効期間が変更されます。 

![応答ヘッダーのアクション](./media/front-door-rules-engine/rules-engine-architecture-3.png)

別の例として、このルール エンジンは、一致条件であるデバイスの種類が true の場合にサイトのモバイル バージョンにユーザーを送るように構成されています。 

![ルート構成のオーバーライド](./media/front-door-rules-engine/rules-engine-architecture-1.png)

どちらの例でも、一致条件が満たされない場合は、指定されたルート ルールが実行されます。 

## <a name="terminology"></a>用語 

AFD ルール エンジンでは、それぞれが一連のルールで構成されるルール エンジン構成の組み合わせを作成できます。 ここでは、ルール エンジンを構成する際に役立つ用語をいくつか紹介します。 

- "*ルール エンジン構成*": 単一のルート ルールに適用されるルールのセット。 各構成は 25 のルールに制限されます。 最大 10 個の構成を作成できます。 
- "*ルール エンジン ルール*": 最大 10 個の一致条件と 5 個のアクションで構成されるルール。
- "*一致条件*": 受信要求を解析するために利用できる一致条件は多数あります。 1 つのルールに最大 10 個の一致条件を含めることができます。 一致条件は、**AND** 演算子で評価されます。 一致条件の完全な一覧については、[こちら](front-door-rules-engine-match-conditions.md)を参照してください。 
- *アクション*:アクションは、受信要求に対する処理を示します。要求または応答のヘッダー アクション、転送、リダイレクト、および書き換えが、現在使用できるすべてのアクションです。 1 つのルールには、最大 5 つのアクションを含めることができます。ただし、1 つのルールに含めることができるルート構成のオーバーライドは 1 つだけです。  アクションの完全な一覧については、[こちら](front-door-rules-engine-actions.md)を参照してください。


## <a name="next-steps"></a>次のステップ

- 最初の[ルール エンジン構成](front-door-tutorial-rules-engine.md)を構成する方法について学習します。 
- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
- [Front Door のしくみ](front-door-routing-architecture.md)について学習します。
