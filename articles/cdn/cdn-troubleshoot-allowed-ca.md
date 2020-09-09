---
title: Azure CDN でカスタム HTTPS を有効にするために許可された CA
description: 独自の証明書を使用してカスタム ドメインで HTTPS を有効にする場合、その証明書を作成するためには許可された証明機関 (CA) を使用する必要があります。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/18/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 29b6cb25e021e86ce6663b4db5c89217aaf70a37
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84887398"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Azure CDN でカスタム HTTPS を有効にするために許可された認証機関

Azure Content Delivery Network (CDN) カスタム ドメイン用の[独自の証明書を使用して HTTPS 機能を有効にする](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates)場合は、特定の証明書の要件を満たす必要があります。 **Azure CDN Standard from Microsoft** プロファイルでは、次の一覧にある承認されたいずれかの証明機関 (CA) の証明書が必要です。 未承認の CA からの証明書、または自己署名証明書が使用されている場合、要求は拒否されます。 **Azure CDN Standard from Verizon** プロファイルと **Azure CDN Premium from Verizon** プロファイルは、すべての有効な CA からの任意の有効な証明書を受け入れます。

> [!NOTE]
> 独自の証明書を使用してカスタム ドメイン HTTPS 機能を有効にするオプションは、*Azure CDN Standard from Akamai* プロファイルでは利用 "**できません**"。 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

