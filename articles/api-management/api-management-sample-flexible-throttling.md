---
title: "Azure API Management を使用した高度な要求スロットル"
description: "Azure API management で柔軟なクォータとレートの制限ポリシーを作成して適用する方法について説明します。"
services: api-management
documentationcenter: 
author: darrelmiller
manager: erikre
editor: 
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2a5078b34f74efd5d394587d8ace7f339ecedb5e


---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Azure API Management を使用した高度な要求スロットル
受信要求のスロットルは、Azure API Management の重要な役割の 1 つです。 要求のレートや転送される要求/データの合計を制御できるため、API プロバイダーは API Management を使用して、さまざまな API 成果物階層に対する値の不正使用や作成から API を保護できます。

## <a name="product-based-throttling"></a>成果物ベースのスロットル
これまで、レートのスロットル機能は、特定の成果物のサブスクリプション (実質的にはキー) を範囲とするように制限されていました。この範囲は、API Management パブリッシャー ポータルで定義します。 API プロバイダーからすると、これは API を使用するためにサインアップした開発者に制限を適用する場合には便利ですが、API の個々のエンド ユーザーをスロットルする場合などには役立ちません。 開発者のアプリケーションを使用する 1 人のユーザーがクォータ全体を使用しているために、他の顧客がアプリケーションを使用できないという状況が生じることがあります。 また、複数の顧客が大量の要求を生成したために、不定期に使用するユーザーがアクセスを制限される場合もあります。

## <a name="custom-key-based-throttling"></a>カスタム キー ベースのスロットル
新しい [rate-limit-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) ポリシーと [quota-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) ポリシーを使用すると、トラフィック制御の柔軟性が大幅に向上します。 これらの新しいポリシーでは、トラフィックの使用状況を追跡するために使用されるキーを識別する式を定義できます。 このしくみを理解しやすいように、例を示します。 

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

この例では、Authorization ヘッダーを抽出して `JWT` オブジェクトに変換し、トークンのサブジェクトを使用してユーザーを識別し、レートを制限するキーとして使用しています。 `JWT` で他の要求の 1 つとしてユーザー ID が格納されている場合は、その値を代わりに使用できます。

## <a name="combined-policies"></a>ポリシーの組み合わせ
新しいスロットル ポリシーでは、既存のスロットル ポリシーよりも詳細な制御ができますが、両方の機能を組み合わせることでも価値が得られます。 成果物サブスクリプション キー別のスロットル ([サブスクリプション別の呼び出しレート制限](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate)と[サブスクリプション別の使用量クォータの設定](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota)) を用いて使用レベルに基づいて課金することで、API を収益化できます。 ユーザーごとにスロットルできる細やかな制御を補完的に使用すれば、あるユーザーの行為によって別のユーザーのエクスペリエンスの質が低下する事態を防ぐことができます。 

## <a name="client-driven-throttling"></a>クライアント主導のスロットル
[ポリシー式](https://msdn.microsoft.com/library/azure/dn910913.aspx)を使用してスロットル キーを定義する場合、スロットルの適用範囲は API プロバイダーによって決定されます。 一方で、開発者が自分の顧客に対するレート制限の内容を制御したいと考える場合があります。 これを実現するために、API プロバイダーは、開発者のクライアント アプリケーションから API にキーを伝えるためのカスタム ヘッダーを導入できます。

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

これにより、開発者のクライアント アプリケーションで、レート制限キーを作成する方法を指定できます。 クライアント開発者は、少しの工夫で、キーのセットをユーザーに割り当てたり、キーの使用をローテーションしたりして、独自のレート階層を作成できます。

## <a name="summary"></a>まとめ
Azure API Management では、レートとクォータのスロットルを通じて、API サービスの保護と API サービスへの付加価値の両方を実現します。 新しいスロットル ポリシーをカスタムの範囲設定規則と組み合わせて使用することで、ポリシーをきめ細かく制御でき、顧客はさらに高品質なアプリケーションを構築できるようになります。 この記事の例では、クライアントの IP アドレス、ユーザー ID、クライアントを用いて生成された値を使用してレート制限キーを作成して、新しいポリシーの使用方法の例を示しましたが、 ユーザー エージェント、URL パス フラグメント、メッセージ サイズなど、使用可能なメッセージ要素は他にも多数あります。

## <a name="next-steps"></a>次のステップ
ご意見ご感想をこのトピックの Disqus スレッドでお寄せください。 上記以外で、お客様のシナリオに適した選択肢となりそうなキーの値がありましたら、ぜひお聞かせください。

## <a name="watch-a-video-overview-of-these-policies"></a>これらのポリシーの概要に関するビデオ
この記事で説明した [rate-limit-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) ポリシーと [quota-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) ポリシーの詳細については、次のビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Advanced-Request-Throttling-with-Azure-API-Management/player]
> 
> 




<!--HONumber=Nov16_HO3-->


