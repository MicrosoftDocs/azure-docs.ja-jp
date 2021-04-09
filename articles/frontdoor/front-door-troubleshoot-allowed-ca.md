---
title: Azure Front Door でカスタム HTTPS を有効にするために許可された CA
description: 独自の証明書を使用して Azure Front Door のカスタム ドメインで HTTPS を有効にする場合、その証明書を作成するためには許可された証明機関 (CA) を使用する必要があります。
services: frontdoor
documentationcenter: ''
author: duongau
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 20c5d611272ee2159ce8ddcc2865797a225a7ebb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91613681"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Azure Front Door 上でカスタム HTTPS を有効にするために許可された証明機関

Azure Front Door のカスタム ドメインに対して[独自の証明書を使用して HTTPS 機能を有効にする](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)場合。 TLS/SSL 証明書を作成するには、許可された証明機関 (CA) が必要です。 許可されていない CA や自己署名証明書を使用すると、要求が拒否されます。

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
