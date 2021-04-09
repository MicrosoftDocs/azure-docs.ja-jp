---
title: ID の構成 - Azure Event Grid IoT Edge | Microsoft Docs
description: Event Grid モジュールの ID を構成する
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 2418c8518bbf298a102d4f29b4a973f6121de2eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171688"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Event Grid モジュールの ID を構成する

この記事では、Edge で Grid 用の ID を構成する方法について示します。 既定では、Event Grid モジュールによって、IoT セキュリティ デーモンによって構成されたように、その ID 証明書が提示されます。 Edge の Event Grid では、イベントを配信するときに、その発信呼び出しで ID 証明書を提示します。 その後、サブスクライバーは、それが受理前にイベントを送信した Event Grid モジュールであることを検証できます。

可能なすべての構成については、「[セキュリティと認証](security-authentication.md)」のガイドを参照してください。

## <a name="always-present-identity-certificate"></a>常に ID 証明書を提示する
発信呼び出しで ID 証明書を常に提示する構成の例を次に示します。 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>ID 証明書を提示しない
発信呼び出しで ID 証明書を提示しない構成の例を次に示します。 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
