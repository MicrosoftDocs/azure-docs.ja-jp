---
title: "Azure Application Gateway でのマルチテナント バックエンドの概要 | Microsoft Docs"
description: "このページでは、Application Gateway によるマルチテナント バックエンドのサポートの概要について説明します。"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: 
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.openlocfilehash: c29ff60a50e68c75b4e8f62713d6d1fffd2123d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="application-gateway-support-for-multi-tenant-back-ends"></a>Application Gateway によるマルチテナント バックエンドのサポート

Azure Application Gateway では、仮想マシン スケール セット、ネットワーク インターフェイス、パブリック/プライベート IP、または完全修飾ドメイン名 (FQDN) が、バックエンド プールの一部としてサポートされます。 既定では、クライアントからの受信 HTTP ホスト ヘッダーは、変更されることなくそのままバックエンドに送信されます。 [Azure Web Apps](../app-service/app-service-web-overview.md) や [API Management](../api-management/api-management-key-concepts.md) など、多くのサービスが本質的にマルチテナントであり、適切なエンドポイントに解決するために特定のホスト ヘッダーまたは SNI 拡張機能を使用します。 Application Gateway では、バックエンド HTTP 設定に基づいて受信 HTTP ホスト ヘッダーを上書きできるようになりました。 これにより、マルチテナント バックエンドの Azure Web Apps と API Management がサポートされます。 この機能は、Standard SKU と WAF SKU の両方で利用できます。 マルチテナント バックエンドのサポートはさらに、SSL 終了とエンド ツー エンド SSL のシナリオにも対応しています。

![Web アプリのシナリオ](./media/application-gateway-web-app-overview/scenario.png)

ホスト上書きを指定する機能は、HTTP 設定で定義されます。また、この機能はルールの作成中にバックエンド プールに適用できます。 マルチテナント バックエンドでは、ホスト ヘッダーの上書きと SNI 拡張機能について次の 2 とおりの方法がサポートされます。

1. HTTP 設定でホスト名を固定値に設定する機能。 この機能では、HTTP 設定が適用されるバックエンド プールへのすべてのトラフィックに関して、ホスト ヘッダーが特定の値に上書きされるようにします。 エンド ツー エンド SSL を使用している場合、上書きされたこのホスト名は SNI 拡張機能で使用されます。 この機能を使用すれば、顧客の受信ホスト ヘッダーと異なるホスト ヘッダーがバックエンド プール ファームによって想定されるシナリオに対応できます。

2. バックエンド プール メンバーの IP または FQDN からホスト名を取得する機能。 HTTP 設定では、個々のバックエンド プール メンバーからホスト名を取得するよう構成されている場合に、バックエンド プール メンバーの FQDN からホスト名を選択するオプションも使用できます。 エンド ツー エンド SSL を使用している場合、このホスト名は FQDN から取得され、SNI 拡張機能で使用されます。 この機能を使用すれば、バックエンド プールに 2 つ以上のマルチテナント PaaS サービス (Azure Web Apps など) を使用でき、各メンバーへの要求のホスト ヘッダーに FQDN から取得されたホスト名を含めるシナリオに対応できます。

> [!NOTE]
> 前述のどちらの場合でも、設定が影響するのはライブ トラフィックの動作のみであり、正常性プローブの動作に影響はありません。 既にカスタム プローブでは、プローブ構成でホスト ヘッダーを指定する機能がサポートされています。 さらに、現在構成されている HTTP 設定からホスト ヘッダーの動作を取得する機能もサポートされるようになりました。 この構成は、プローブ構成の `PickHostNameFromback endAddress` パラメーターを使用して指定できます。 エンド ツー エンドの機能が動作するには、正しい構成を反映するようプローブと HTTP 設定の両方を変更する必要があります。

顧客はこの機能を使用して、HTTP 設定とカスタム プローブのオプションを適切な構成に指定します。 さらに、この設定は、ルールを使用してリスナーとバックエンド プールに関連付けられます。

## <a name="next-steps"></a>次のステップ

アプリケーション ゲートウェイを設定して Web アプリケーションをバックエンド プール メンバーにする方法を学習するために、「[Application Gateway を使用した App Service Web Apps の構成](application-gateway-web-app-powershell.md)」を参照してください。
