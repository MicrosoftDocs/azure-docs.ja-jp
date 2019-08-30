---
title: Azure API Management を使用した高度な要求スロットル
description: Azure API management で柔軟なクォータとレートの制限ポリシーを作成して適用する方法について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2018
ms.author: apimpm
ms.openlocfilehash: f0f89275006498c0ff4883f259d7e76f723b922b
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851333"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Azure API Management を使用した高度な要求スロットル
受信要求のスロットルは、Azure API Management の重要な役割の 1 つです。 要求のレートや転送される要求/データの合計を制御できるため、API プロバイダーは API Management を使用して、さまざまな API 成果物階層に対する値の不正使用や作成から API を保護できます。

## <a name="product-based-throttling"></a>成果物ベースのスロットリング
これまで、レート調整機能は、特定の成果物のサブスクリプションを範囲とするように制限されていました。この範囲は、Azure Portal で定義します。 API プロバイダーからすると、これは API を使用するためにサインアップした開発者に制限を適用する場合には便利ですが、API の個々のエンド ユーザーをスロットルする場合などには役立ちません。 開発者のアプリケーションを使用する 1 人のユーザーがクォータ全体を使用しているために、他の顧客がアプリケーションを使用できないという状況が生じることがあります。 また、複数の顧客が大量の要求を生成したために、不定期に使用するユーザーがアクセスを制限される場合もあります。

## <a name="custom-key-based-throttling"></a>カスタム キー ベースのスロットル

> 注:`rate-limit-by-key` と `quota-by-key` のポリシーは、Azure API Management の従量課金レベルでは使用できません。 

新しい [rate-limit-by-key](/azure/api-management/api-management-access-restriction-policies#LimitCallRateByKey) ポリシーと [quota-by-key](/azure/api-management/api-management-access-restriction-policies#SetUsageQuotaByKey) ポリシーを使用すると、トラフィック制御の柔軟性が向上します。 これらの新しいポリシーでは、トラフィックの使用状況を追跡するために使用されるキーを識別する式を定義できます。 このしくみを理解しやすいように、例を示します。 

## <a name="ip-address-throttling"></a>IP アドレスのスロットル
次のポリシーは、1 つのクライアント IP アドレスに対して、呼び出し数を毎分最大 10 回に制限し、1 か月あたりの呼び出しの合計を 1,000,000 回に、帯域幅を 10,000 KB に制限します。 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

このポリシーは、インターネット上のすべてのクライアントが一意の IP アドレスを使用しているような状況では、ユーザーごとに使用量を制限する手段として効果的です。 ただし、複数のユーザーが NAT デバイス経由でインターネットにアクセスすることで 1 つのパブリック IP アドレスを共有している可能性もあります。 このような状況でも、認証されていないアクセスを許可する API では、 `IpAddress` の使用が最適な選択である場合があります。

## <a name="user-identity-throttling"></a>ユーザー ID のスロットル
エンド ユーザーが認証されている場合は、そのユーザーを一意に識別する情報に基づいてスロットル キーを生成できます。

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

この例は、Authorization ヘッダーを抽出して `JWT` オブジェクトに変換し、トークンのサブジェクトを使用してユーザーを識別し、レートを制限するキーとして使用する方法を示しています。 `JWT` で他の要求の 1 つとしてユーザー ID が格納されている場合は、その値を代わりに使用できます。

## <a name="combined-policies"></a>ポリシーの組み合わせ
新しいスロットル ポリシーでは、既存のスロットル ポリシーよりも詳細な制御ができますが、両方の機能を組み合わせることでも価値が得られます。 成果物サブスクリプション キー別のスロットル ([サブスクリプション別の呼び出しレート制限](/azure/api-management/api-management-access-restriction-policies#LimitCallRate)と[サブスクリプション別の使用量クォータの設定](/azure/api-management/api-management-access-restriction-policies#SetUsageQuota)) を用いて使用レベルに基づいて課金することで、API を収益化できます。 ユーザーごとにスロットルできる細やかな制御を補完的に使用すれば、あるユーザーの行為によって別のユーザーのエクスペリエンスの質が低下する事態を防ぐことができます。 

## <a name="client-driven-throttling"></a>クライアント主導のスロットル
[ポリシー式](/azure/api-management/api-management-policy-expressions)を使用してスロットル キーを定義する場合、スロットルの適用範囲は API プロバイダーによって決定されます。 一方で、開発者が自分の顧客に対するレート制限の内容を制御したいと考える場合があります。 これを実現するために、API プロバイダーは、開発者のクライアント アプリケーションから API にキーを伝えるためのカスタム ヘッダーを導入できます。

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

これにより、開発者のクライアント アプリケーションで、レート制限キーを作成する方法を指定できます。 クライアント開発者は、キーのセットをユーザーに割り当てたり、キーの使用をローテーションしたりして、独自のレート階層を作成できます。

## <a name="summary"></a>まとめ
Azure API Management では、レートとクォータのスロットルを通じて、API サービスの保護と API サービスへの付加価値の両方を実現します。 新しいスロットル ポリシーをカスタムの範囲設定規則と組み合わせて使用することで、ポリシーをきめ細かく制御でき、顧客はさらに高品質なアプリケーションを構築できるようになります。 この記事の例では、クライアントの IP アドレス、ユーザー ID、クライアントを用いて生成された値を使用してレート制限キーを作成して、新しいポリシーの使用方法の例を示しましたが、 ユーザー エージェント、URL パス フラグメント、メッセージ サイズなど、使用可能なメッセージ要素は他にも多数あります。

## <a name="next-steps"></a>次の手順
ご意見ご感想をこのトピックの Disqus スレッドでお寄せください。 上記以外で、お客様のシナリオに適した選択肢となりそうなキーの値がありましたら、ぜひお聞かせください。

