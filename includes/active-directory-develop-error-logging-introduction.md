---
author: mmacy
ms.author: marsma
ms.date: 12/16/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 53198c663d318a2eb47bcb3207939bbcb1fdd59c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98763268"
---
Microsoft Authentication Library (MSAL) アプリは、問題の診断に役立つログ メッセージを生成します。 アプリは数行のコードでログ記録を構成でき、詳細レベルと、個人データと組織のデータをログ記録するかどうかを制御するカスタム コントロールを備えています。 ユーザーが認証の問題を抱えている場合は、MSAL ログ コールバックを作成して、ユーザーがログを送信できるようにすることをお勧めします。

## <a name="logging-levels"></a>ログ記録のレベル

MSAL では、いくつかのレベルのログ記録の詳細が提供されます。

- エラー:問題の発生し、エラーが生成されたことを示します。 問題をデバッグおよび特定するために使用されます。
- 警告:必ずしもエラーや障害があったわけではありませんが、診断や問題の特定の対象となっています。
- 情報:MSAL は、情報目的でイベントをログ記録し、必ずしもデバッグ目的ではありません。
- 詳細:既定値。 MSAL では、ライブラリの動作の詳細がログに記録されます。

## <a name="personal-and-organizational-data"></a>個人と組織のデータ

既定では、MSAL ロガーによって、機密性の高い個人または組織のデータはキャプチャされません。 ライブラリには、個人と組織のデータをログ記録することにした場合に、そのログ記録を有効にするオプションが用意されています。

次のセクションでは、アプリケーションの MSAL エラー ログ記録の詳細について説明します。