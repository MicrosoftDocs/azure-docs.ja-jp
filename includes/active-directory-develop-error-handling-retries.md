---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: d0377e3e918a8431c35c1f5b15a1c4b54eeae3b4
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760638"
---
## <a name="retrying-after-errors-and-exceptions"></a>エラーおよび例外の後の再試行

MSAL を呼び出すときには、独自の再試行ポリシーを実装することが求められます。 MSAL では Azure AD サービスに対して HTTP 呼び出しが行われ、場合によってはエラーが発生することがあります。 たとえば、ネットワークがダウンしたり、サーバーが過負荷になったりする可能性があります。  

### <a name="http-429"></a>HTTP 429

サービス トークン サーバー (STS) が過剰な要求で過負荷になると、HTTP エラー 429 が返され、`Retry-After` 応答フィールドで再試行できるまでの期間に関するヒントが示されます。