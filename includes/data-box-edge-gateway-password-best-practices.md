---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 648c2e4ffc63dad42a87888e7cb5629fb7301eea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026809"
---
次のベスト プラクティスに注意してください。

- 忘れた場合にパスワードをリセットしなくても済むように、すべてのパスワードをセキュリティで保護された場所に格納することをお勧めします。 管理サービスは既存のパスワードを取得できません。 可能なのは、そのパスワードを Azure Portal 経由でリセットすることだけです。 パスワードをリセットする場合は、リセットする前に、必ずすべてのユーザーに通知してください。
- HTTP 経由でお使いのデバイスの Windows PowerShell インターフェイスにリモートでアクセスすることができます。 セキュリティのベスト プラクティスとして、信頼できるネットワークのみで HTTP を使用する必要があります。
- デバイスのパスワードが強力であり、適切に保護されていることを確認してください。 [パスワードのベスト プラクティス](../articles/security/fundamentals/identity-management-best-practices.md#enable-password-management)に従ってください。