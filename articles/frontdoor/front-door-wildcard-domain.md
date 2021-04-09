---
title: Azure Front Door - ワイルドカード ドメインのサポート
description: この記事では、カスタム ドメインの一覧におけるワイルドカード ドメインのマッピングや管理が、Azure Front Door でどのようにサポートされるかについて説明します。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: 18504f1ed4200889b20c9608c9c0ad2c13c9aaa5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94425764"
---
# <a name="wildcard-domains"></a>ワイルドカード ドメイン

頂点ドメインとサブドメイン以外に、Azure Front Door プロファイルのフロントエンド ホストやカスタム ドメインに、ワイルドカード ドメインをマップすることもできます。 Azure Front Door の構成でワイルドカード ドメインを使用すると、API、アプリケーション、または Web サイト用の複数のサブドメインに対するトラフィック ルーティングの動作を、同じルーティング規則によって簡素化することができます。 各サブドメインを個別に追加または指定するように構成を変更する必要はありません。 たとえば、同じルーティング規則を使用し、ワイルドカード ドメイン `*.contoso.com` を追加することで、`customer1.contoso.com`、`customer2.contoso.com`、および `customerN.contoso.com` のルーティングを定義することができます。

ワイルドカード ドメインがサポートされたことで解決される主なシナリオとしては、次のようなものがあります。

- 各サブドメインを Azure Front Door プロファイルにオンボードし、HTTPS を有効にして各サブドメインの証明書をバインドする必要はありません。
- アプリケーションで新しいサブドメインが追加された場合に、運用中の Front Door 構成を変更する必要がなくなりました。 以前は、サブドメインを追加し、それに証明書をバインドし、Web アプリケーション ファイアウォール (WAF) ポリシーをアタッチして、ドメインを個別のルーティング規則に追加する必要がありました。

> [!NOTE]
> 現在のところ、Azure DNS を使用したワイルドカード ドメインの追加は、API、PowerShell、Azure CLI でのみサポートされています。 Azure portal でワイルドカード ドメインを追加、管理することはサポートされていません。

## <a name="adding-wildcard-domains"></a>ワイルドカード ドメインの追加

ワイルドカード ドメインは、フロントエンド ホストまたはドメインのセクションでオンボードできます。 Azure Front Door では、サブドメインと同様、ワイルドカード ドメインについて CNAME レコード マッピングがあることが検証されます。 この DNS マッピングは、`contoso.azurefd.net` にマッピングされた `*.contoso.com` のような、CNAME レコードの直接マッピングにすることができます。 または、afdverify 一時マッピングを使用することもできます。 たとえば、`afdverify.contoso.azurefd.net` にマッピングされた `afdverify.contoso.com` は、ワイルドカードの CNAME レコードマップを検証します。

> [!NOTE]
> Azure DNS は、ワイルドカード レコードをサポートしています。

フロントエンド ホストには、フロントエンド ホストの上限に達するまで、ワイルドカード ドメインの単一レベルのサブドメインを追加できます。 この機能は、次の場合に必要になることがあります。

- 他のドメイン (ワイルドカード ドメインから) とは異なる、サブドメインの別のルートを定義する。

- 特定のサブドメインに対して異なる WAF ポリシーを持つ。 たとえば、`*.contoso.com` では、ドメインの所有権を再度証明しなくても `foo.contoso.com` を追加できます。 ただし、`*.contoso.com` の単一レベルのサブドメインではないため、`foo.bar.contoso.com` は許可されません。 ドメイン所有権の追加検証を行わずに `foo.bar.contoso.com` を追加するには、`*.bar.contosonews.com` を追加する必要があります。

次の制限事項を踏まえ、ワイルドカード ドメインと、そのサブドメインを追加できます。

- ワイルドカード ドメインが Azure Front Door プロファイルに追加された場合:
  - ワイルドカード ドメインを、その他の Azure Front Door プロファイルに追加することはできません。
  - ワイルドカード ドメインの第 1 レベルのサブドメインは、別の Azure Front Door プロファイル、または Azure Content Delivery Network プロファイルに追加することはできません。
- ワイルドカード ドメインのサブドメインが Azure Front Door プロファイルまたは Azure Content Delivery Network プロファイルに既に追加されている場合、そのワイルドカード ドメインを他の Azure Front Door プロファイルに使用することはできません。
- 2 つのプロファイル (Azure Front Door または Azure Content Delivery Network) にルート ドメインのサブドメインが複数ある場合、ワイルドカード ドメインはいずれのプロファイルにも追加できません。

## <a name="certificate-binding"></a>証明書バインド

ワイルドカード ドメインで HTTPS トラフィックを受け入れるには、ワイルドカード ドメインで HTTPS を有効にする必要があります。 ワイルドカード ドメインの証明書バインドには、ワイルドカード証明書が必要です。 つまり、証明書のサブジェクト名にもワイルドカード ドメインが必要です。

> [!NOTE]
> 現在のところ、ワイルドカード ドメインに対して HTTPS を有効にするには、独自のカスタム SSL 証明書を使用するオプションしかありません。 Azure Front Door で管理される証明書は、ワイルドカード ドメインには使用できません。

キー コンテナーから同じワイルドカード証明書を使用することも、サブドメインの Azure Front Door で管理される証明書から使用することもできます。

既に証明書が関連付けられているワイルドカード ドメインにサブドメインが追加されている場合、このサブドメインの HTTPS を無効にすることはできません。 サブドメインは、別の Key Vault または Azure Front Door で管理されている証明書で上書きされない限り、ワイルドカード ドメインの証明書バインドを使用します。

## <a name="waf-policies"></a>WAF ポリシー

WAF ポリシーは、他のドメインと同様に、ワイルドカード ドメインにもアタッチできます。 ワイルドカード ドメインのサブドメインに、別の WAF ポリシーを適用することもできます。 サブドメインについては、ワイルドカード ドメインと同じポリシーを使用する場合であっても、使用する WAF ポリシーを指定する必要があります。 サブドメインでは、ワイルドカード ドメインから WAF ポリシーが自動的に継承されることは *ありません*。

サブドメインに対して WAF ポリシーを実行したくない場合は、管理されていないルールセット、またはカスタム ルールセットを使用せずに、空の WAF ポリシーを作成します。

## <a name="routing-rules"></a>ルーティング ルール

ルーティング規則を構成する際には、フロントエンド ホストとしてワイルドカード ドメインを選択できます。 また、ワイルドカード ドメインとサブドメインに対し、それぞれ異なるルート動作を適用することもできます。 「[Azure Front Door が要求をルーティング規則と照合する方法](front-door-route-matching.md)」で説明されているように、ドメインに対してどのルーティング規則が最も適合するかは、実行時に選択されます。

> [!IMPORTANT]
> ルーティング規則全体でパス パターンが一致している必要があります。一致しない場合、クライアントにエラーが表示されます。 たとえば、ルート 1 (バックエンド プール A にマッピング済み`*.foo.com/*`) とルート 2 (バックエンド プール B にマッピング済み`/bar.foo.com/somePath/*`) などの 2 つのルーティング規則があるとします。 次に、`bar.foo.com/anotherPath/*` の要求が到着します。 Azure Front Door は、より具体的なドメイン一致に基づいてルート 2 を選択しますが、ルート全体で一致するパス パターンはみつかりません。

## <a name="next-steps"></a>次のステップ

- [Azure Front Door プロファイルを作成する](quickstart-create-front-door.md)方法について学習します。
- [Azure Front Door にカスタム ドメインを追加する](front-door-custom-domain.md)方法について学習します。
- [カスタム ドメインで HTTPS を有効にする方法](front-door-custom-domain-https.md)について学習します。
