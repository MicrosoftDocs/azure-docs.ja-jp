---
title: カスタム HTTPS を有効にするための許可された CA
titleSuffix: Azure Content Delivery Network
description: カスタム ドメインで HTTPS を有効にするために独自の証明書を使用する場合、それを作成するには許可された証明機関 (CA) を使用する必要があります。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: troubleshooting
ms.date: 02/04/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: f98e28c89fa70831108cfbbbaca6e2f316d1b039
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99573400"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https"></a>カスタム HTTPS を有効にするための許可された証明機関

Azure CDN (Content Delivery Network) カスタム ドメイン用の[独自の証明書を使用して HTTPS 機能を有効にする](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates)場合は、特定の証明書要件が必要です。 

* **Azure CDN Standard from Microsoft** プロファイルでは、次の一覧にある承認されたいずれかの証明機関 (CA) の証明書が必要です。 未承認の CA からの証明書、または自己署名証明書が使用されている場合、要求は拒否されます。 

* **Azure CDN Standard from Verizon** プロファイルと **Azure CDN Premium from Verizon** プロファイルは、すべての有効な CA からの任意の有効な証明書を受け入れます。 Verizon プロファイルは自己署名証明書をサポートしていません。

> [!NOTE]
> 独自の証明書を使用してカスタム ドメイン HTTPS 機能を有効にするオプションは、*Azure CDN Standard from Akamai* プロファイルでは利用 "**できません**"。 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

