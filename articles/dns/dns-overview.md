---
title: "Azure DNS の概要 | Microsoft Docs"
description: "Microsoft Azure の DNS ホスティング サービスの概要です。 Microsoft Azure でドメインをホストします。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 68747a0d-b358-4b8e-b5e2-e2570745ec3f
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 3705457e4c90f8869496f7f5177531bd128d1057
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="azure-dns-overview"></a>Azure DNS の概要

ドメイン ネーム システム (DNS) は、Web サイトまたはサービスの名前をその IP アドレスに変換する (または解決する) 役割を担います。 Azure DNS は、DNS ドメインのホスティング サービスであり、Microsoft Azure インフラストラクチャを使用した名前解決を提供します。 Azure でドメインをホストすることで、その他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。

![DNS の概要](./media/dns-overview/scenario.png)

## <a name="features"></a>Features (機能)

* **信頼性とパフォーマンス** - Azure DNS 内の DNS ドメインは、DNS ネーム サーバーから成る Azure のグローバル ネットワーク上でホストされます。 エニーキャスト ネットワークが使用されるため、各 DNS クエリには、使用できる最も近い DNS サーバーが応答します。 これによって、ドメインには高速なパフォーマンスと高可用性の両方が提供されます。

* **シームレスな統合** - Azure DNS サービスを使用して、Azure サービスの DNS レコードを管理でき、同時に外部リソースに DNS を提供することもできます。 Azure DNS は Azure Portal に統合されており、他の Azure サービスと同じ資格情報、課金、サポート契約を使用します。

* **セキュリティ** - この Azure DNS サービスは、Azure Resource Manager に基づいています。 そのため、ロールに基づくアクセス制御、監査ログ、リソース ロックなどの Resource Manager 機能を利用できます。 ドメインとレコードは、Azure ポータル、Azure PowerShell コマンドレット、およびクロス プラットフォームの Azure CLI を使用して管理できます。 DNS の自動管理を必要とするアプリケーションは、REST API および SDK を使用してサービスと統合できます。

Azure DNS では、現在、ドメイン名の購入はサポートされていません。 ドメインを購入する場合、サードパーティのドメイン名レジストラーを利用する必要があります。 レジストラーは、通常、少額の年間料金がかかります。 購入後、ドメインを Azure DNS でホストし、DNS レコードを管理できます。 詳細については、「 [Azure DNS へのドメインの委任](dns-domain-delegation.md) 」を参照してください。

## <a name="pricing"></a>価格

DNS の課金は、Azure でホストされている DNS ゾーンの数と、DNS クエリの数に基づきます。 価格の詳細については、「[Azure DNS の価格](https://azure.microsoft.com/pricing/details/dns/)」を参照してください。

## <a name="faq"></a>FAQ

Azure DNS に関してよく寄せられる質問については、「[Azure DNS に関する FAQ](dns-faq.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

「[DNS ゾーンとレコードの概要](dns-zones-records.md)」でDNS ゾーンとレコードについて学びます。

Azure DNS に [DNS ゾーンを作成する](./dns-getstarted-create-dnszone-portal.md)方法を学びます。

Azure のその他の重要な[ネットワーク機能](../networking/networking-overview.md)について参照してください。


