---
title: コンテナーのセキュリティ
titleSuffix: Azure Cognitive Services
description: コンテナーをセキュリティで保護する方法について説明します
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: dapine
ms.openlocfilehash: d8d069dddbce6ab6ddb541db460634ad3f6fa067
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2019
ms.locfileid: "70994949"
---
## <a name="azure-cognitive-services-container-security"></a>Azure Cognitive Services コンテナーのセキュリティ

アプリケーションを開発するときは常にセキュリティを重視する必要があります。 セキュリティの重要性は成功の指標です。 Cognitive Services コンテナーを含むソフトウェア ソリューションを設計する場合は、制限と使用できる機能を理解しておくことが重要です。 詳細については、[Azure Security][az-security] に関する記事を参照してください。

> [!IMPORTANT]
> 既定では、Cognitive Services コンテナー API に "*セキュリティはありません*"。 この理由は、ほとんどの場合、ネットワーク ブリッジによって外部から保護されるポッドの一部としてコンテナーが実行されるためです。 ただし、[クラウドベースの Cognitive Services][request-authentication] にアクセスするときに使用する認証と同じように動作する認証を有効にすることができます。

次の図は、既定の**セキュリティで保護されていない**アプローチを示しています。

![コンテナーのセキュリティ](../media/container-security.svg)

別の "*セキュリティで保護された*" アプローチとして、Cognitive Services コンテナーのコンシューマーでは、コンテナー エンドポイントを非公開に保ちながら、前面のコンポーネントを使用してコンテナーを補強できます。 イングレス ゲートウェイとして [Istio][istio] を使用するシナリオについて考えてみましょう。 Istio は、HTTPS/SSL およびクライアント証明書の認証をサポートします。 このシナリオでは、Istio のフロントエンドでコンテナー アクセスが公開され、事前に Istio でホワイトリストに登録されているクライアント証明書が提示されています。

[Nginx][nginx] は、同じカテゴリにあるもう 1 つの一般的な選択肢です。 Istio と Nginx はどちらもサービス メッシュとして機能し、負荷分散、ルーティング、レート制御などの追加機能が提供されます。

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../../security/fundamentals/overview.md
