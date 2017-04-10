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
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 87122cb432f90573fdb0a35943ce14c8d62e84a5
ms.openlocfilehash: f8ccf5c0fab1e4aca85b22b99a1a5b48f35dfcbc

---

# <a name="azure-dns-overview"></a>Azure DNS の概要

ドメイン ネーム システム (DNS) は、Web サイトまたはサービスの名前をその IP アドレスに変換する (または解決する) 役割を担います。 Azure DNS は、DNS ドメインのホスティング サービスであり、Microsoft Azure インフラストラクチャを使用した名前解決を提供します。 Azure でドメインをホストすることで、その他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。

Azure DNS 内の DNS ドメインは、DNS ネーム サーバーから成る Azure のグローバル ネットワーク上でホストされます。 エニーキャスト ネットワークが使用されるため、各 DNS クエリには、使用できる最も近い DNS サーバーが応答します。 これによって、ドメインには高速なパフォーマンスと高可用性の両方が提供されます。

この Azure DNS サービスは、Azure Resource Manager に基づいています。 そのため、ロールに基づくアクセス制御、監査ログ、リソース ロックなどの Resource Manager 機能を利用できます。 ドメインとレコードは、Azure ポータル、Azure PowerShell コマンドレット、およびクロス プラットフォームの Azure CLI を使用して管理できます。 DNS の自動管理を必要とするアプリケーションは、REST API および SDK を使用してサービスと統合できます。

Azure DNS では、現在、ドメイン名の購入はサポートされていません。 ドメインを購入する場合、サードパーティのドメイン名レジストラーを利用する必要があります。 レジストラーは、通常、少額の年間料金がかかります。 購入後、ドメインを Azure DNS でホストし、DNS レコードを管理できます。 詳細については、「 [Azure DNS へのドメインの委任](dns-domain-delegation.md) 」を参照してください。

### <a name="next-steps"></a>次のステップ

[DNS ゾーンの作成](./dns-getstarted-create-dnszone-portal.md)




<!--HONumber=Dec16_HO3-->


