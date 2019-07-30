---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 86d1cf5e103bcbb13782aa7a2a84092aa426d670
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181814"
---
次のベスト プラクティスに注意してください。

- 忘れた場合にパスワードをリセットしなくても済むように、すべてのパスワードをセキュリティで保護された場所に格納することをお勧めします。 管理サービスは既存のパスワードを取得できません。 可能なのは、そのパスワードを Azure Portal 経由でリセットすることだけです。 パスワードをリセットする場合は、リセットする前に、必ずすべてのユーザーに通知してください。
- HTTP 経由でお使いのデバイスの Windows PowerShell インターフェイスにリモートでアクセスすることができます。 セキュリティのベスト プラクティスとして、信頼できるネットワークのみで HTTP を使用する必要があります。
- デバイスのパスワードが強力であり、適切に保護されていることを確認してください。 [パスワードのベスト プラクティス](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices#enable-password-management)に従ってください。