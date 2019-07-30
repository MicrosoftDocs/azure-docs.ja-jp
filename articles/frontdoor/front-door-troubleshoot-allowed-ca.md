---
title: Azure Front Door Service でカスタム HTTPS を有効にするために許可された認証機関 | Microsoft Docs
description: 独自の証明書を使用してカスタム ドメインで HTTPS を有効にする場合、その証明書を作成するためには許可された証明機関 (CA) を使用する必要があります。
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: e2bab9f9e1ae099952b34e66f7250e7ecbc8e689
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330746"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>Azure Front Door Service でカスタム HTTPS を有効にするために許可された認証機関

Azure Front Door Service のカスタム ドメインでは、[独自の証明書を使用して HTTPS 機能を有効にする](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)場合、許可された証明機関 (CA) を使用して SSL 証明書を作成する必要があります。 許可されていない CA や自己署名証明書を使用すると、要求が拒否されます。

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
