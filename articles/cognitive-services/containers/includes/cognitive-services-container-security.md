---
title: コンテナーのセキュリティ
titleSuffix: Azure Cognitive Services
description: コンテナーをセキュリティで保護する方法について説明します
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/24/2019
ms.author: dapine
ms.openlocfilehash: 7322d356d972635b81bc1bdd4b329bd3d5ac02df
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71842570"
---
## <a name="azure-cognitive-services-container-security"></a>Azure Cognitive Services コンテナーのセキュリティ

アプリケーションを開発するときは常にセキュリティを重視する必要があります。 セキュリティの重要性は成功の指標です。 Cognitive Services コンテナーを含むソフトウェア ソリューションを設計する場合は、制限と使用できる機能を理解しておくことが重要です。 詳細については、[Azure Security][az-security] に関する記事を参照してください。

> [!IMPORTANT]
> 既定では、Cognitive Services コンテナー API に "*セキュリティはありません*"。 この理由は、ほとんどの場合、ネットワーク ブリッジによって外部から保護されるポッドの一部としてコンテナーが実行されるためです。 ただし、[クラウドベースの Cognitive Services][request-authentication] にアクセスするときに使用する認証と同じように動作する認証を有効にすることができます。

次の図は、**セキュリティで保護されていない**既定のアプローチを示しています。

![コンテナーのセキュリティ](../media/container-security.svg)

別の "*セキュリティで保護された*" アプローチとして、Cognitive Services コンテナーのコンシューマーでは、コンテナー エンドポイントを非公開に保ちながら、前面のコンポーネントを使用してコンテナーを補強できます。 イングレス ゲートウェイとして [Istio][istio] を使用するシナリオについて考えてみましょう。 Istio は、HTTPS/SSL およびクライアント証明書の認証をサポートします。 このシナリオでは、Istio のフロントエンドでコンテナー アクセスが公開され、事前に Istio でホワイトリストに登録されているクライアント証明書が提示されています。

[Nginx][nginx] は、同じカテゴリにあるもう 1 つの一般的な選択肢です。 Istio と Nginx はどちらもサービス メッシュとして機能し、負荷分散、ルーティング、レート制御などの追加機能が提供されます。

### <a name="container-networking"></a>コンテナー ネットワーク

Cognitive Services コンテナーは、課金のために使用状況測定情報を送信する必要があります。 唯一の例外は、*オフライン コンテナー*です。それらは、異なる課金方法に従っているからです。 Cognitive Services コンテナーが依存しているさまざまなネットワーク チャネルの一覧表示を許可しないと、コンテナーは動作しなくなります。

#### <a name="allow-list-cognitive-services-domains-and-ports"></a>Cognitive Services のドメインとポートの一覧表示を許可する

ホストは、**ポート 443** と次のドメインの一覧表示を許可する必要があります。

* `*.cognitive.microsoft.com`
* `*.cognitiveservices.azure.com`

#### <a name="disable-deep-packet-inspection"></a>ディープ パケット インスペクションを無効にする

> [ディープ パケット インスペクション](https://en.wikipedia.org/wiki/Deep_packet_inspection) (DPI) は、コンピューター ネットワーク経由で送信されるデータを詳細に検査するデータ処理の一種であり、通常は、状況に応じてブロック、再ルーティング、またはログ記録を行うことによってアクションを実行します。

Cognitive Services コンテナーによって Microsoft サーバーに作成される、セキュリティで保護されたチャネルでは DPI を無効にします。 そうしないと、コンテナーは正しく機能しません。

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../../security/fundamentals/overview.md
