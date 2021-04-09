---
title: Azure DNS とは
description: Microsoft Azure の DNS ホスティング サービスの概要です。 Microsoft Azure でドメインをホストします。
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 3/15/2021
ms.author: rohink
ms.openlocfilehash: 5d81e24d344d04ea59bc6c9e6141109bf66da3c3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491097"
---
# <a name="what-is-azure-dns"></a>Azure DNS とは

Azure DNS は、DNS ドメインのホスティング サービスであり、Microsoft Azure インフラストラクチャを使用した名前解決を提供します。 Azure でドメインをホストすることで、その他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。

Azure DNS を使用してドメイン名を購入することはできません。 年会費をお支払いになると、[App Service のドメイン](../app-service/manage-custom-dns-buy-domain.md#buy-an-app-service-domain)またはサードパーティのドメイン名レジストラーを使用して、ドメイン名を購入できます。 購入したドメインは、Azure DNS でホストし、レコードを管理できます。 詳細については、「[Azure DNS へのドメインの委任](dns-domain-delegation.md)」を参照してください。

Azure DNS には、次の機能が含まれています。

## <a name="reliability-and-performance"></a>信頼性とパフォーマンス

Azure DNS 内の DNS ドメインは、DNS ネーム サーバーから成る Azure のグローバル ネットワーク上でホストされます。 Azure DNS には、エニーキャスト ネットワークが使用されています。 各 DNS クエリには、使用できる最も近い DNS サーバーが応答します。これによって、ドメインには高速なパフォーマンスと高可用性が提供されます。

## <a name="security"></a>Security

 Azure DNS は、Azure Resource Manager に基づいており、次のような機能が利用できます。

* [Azure ロールベースのアクセス制御 (Azure RBAC)](../azure-resource-manager/management/overview.md): 組織の特定のアクションにアクセスできるユーザーを制御できます。

* [アクティビティ ログ](../azure-resource-manager/management/overview.md): 組織のユーザーどのようにリソースを変更したかを監視したり、トラブルシューティング時にエラーを見つけたりできます。

* [リソースのロック](../azure-resource-manager/management/lock-resources.md): サブスクリプション、リソース グループ、またはリソースをロックできます。 ロックすることによって、組織内の他のユーザーが重要なリソースを誤って削除したり変更したりするのを防ぎます。

詳細については、「[DNS ゾーンとレコードを保護する方法](dns-protect-zones-recordsets.md)」を参照してください。 

## <a name="dnssec"></a>DNSSEC

現在、Azure DNS で DNSSEC はサポートされません。 ほとんどの場合は、アプリケーションで常に HTTPS/TLS を使用することによって DNSSEC の必要性を減らすことができます。 DNSSEC が DNS ゾーンにとって重要な要件である場合は、サード パーティの DNS ホスティング プロバイダーを使用してこれらのゾーンをホストすることができます。

## <a name="ease-of-use"></a>使いやすさ

 Azure DNS では、Azure サービスの DNS レコードを管理したり、外部リソースに DNS を提供したりできます。 Azure DNS は Azure Portal に統合されており、他の Azure サービスと同じ資格情報、サポート契約、課金を使用します。 

DNS の課金は、Azure でホストされている DNS ゾーンの数と、受信した DNS クエリの数に基づきます。 価格の詳細については、「[Azure DNS の価格](https://azure.microsoft.com/pricing/details/dns/)」を参照してください。

ドメインとレコードは、Azure portal、Azure PowerShell コマンドレット、およびクロス プラットフォームの Azure CLI を使用して管理できます。 DNS の自動管理を必要とするアプリケーションは、REST API および SDK を使用してサービスと統合できます。

## <a name="customizable-virtual-networks-with-private-domains"></a>プライベート ドメインを持つカスタマイズ可能な仮想ネットワーク

Azure DNS は、プライベート DNS ドメインもサポートします。 この機能は、現在利用可能な Azure 提供の名前ではなく、独自のカスタム ドメイン名をプライベート仮想ネットワーク内で使用できます。

詳細については、「[プライベート ドメインに Azure DNS を使用する](private-dns-overview.md)」を参照してください。

## <a name="alias-records"></a>エイリアス レコード

Azure DNS では、エイリアス レコード セットをサポートします。 エイリアス レコード セットは、Azure パブリック IP アドレス、Azure Traffic Manager プロファイル、または Azure Content Delivery Network (CDN) エンドポイントなどの Azure リソースを参照するために使用できます。 基になるリソースの IP アドレスが変更された場合、エイリアス レコード セットは DNS の解決時にシームレスに自動更新されます。 エイリアス レコード セットはサービス インスタンスをポイントし、サービス インスタンスは IP アドレスに関連付けられます。

さらに、エイリアス レコードを使用することにより、頂点のドメインやネイキッド ドメインで Traffic Manager プロファイルまたは CDN エンドポイントを参照できるようになりました。 一例として contoso.com があります。

詳細については、「[Azure DNS エイリアス レコード](dns-alias.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

* DNS ゾーンとレコードについて学びます ([DNS ゾーンとレコードの概要](dns-zones-records.md)に関するページを参照)。

* Azure DNS 内にゾーンを作成する方法を学びます ([DNS ゾーンの作成](./dns-getstarted-portal.md)に関するページを参照)。

* Azure DNS に関してよく寄せられる質問については、「[Azure DNS に関する FAQ](dns-faq.md)」を参照してください。
