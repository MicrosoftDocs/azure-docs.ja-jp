---
title: MSAL for Python でのエラーと例外のログ記録
titleSuffix: Microsoft identity platform
description: MSAL for Python でエラーと例外をログに記録する方法について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8488325613b05d54b352a19a06860e08f1779877
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063116"
---
# <a name="logging-in-msal-for-python"></a>Python 用の MSAL でのログ記録

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>MSAL for Python でのログ記録

MSAL Python でのログ記録には、`logging.info("msg")` などの標準的な Python ログ記録メカニズムが使用されます。次のように MSAL ログを構成できます ([username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32) で動作を確認できます)。

### <a name="enable-debug-logging-for-all-modules"></a>すべてのモジュールのデバッグ ログを有効にする

既定では、すべての Python スクリプトでログ記録は無効になっています。 Python スクリプト全体のすべてのモジュールに対してデバッグ ログを有効にする場合は、次のようにします。

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>MSAL ログ記録のみをサイレント状態にする

MSAL ライブラリのログ記録のみをサイレント状態にするには、Python スクリプト内の他のすべてのモジュールでデバッグ ログを有効にするときに、MSAL Python で使用されるロガーを無効にします。

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Python での個人と組織のデータ

MSAL for Python では、個人データおよび組織データはログに記録されません。 個人または組織のデータのログ記録を有効または無効にするプロパティはありません。

標準的な Python ログ記録を使用して任意のログを記録することができますが、機密データを安全に処理し、規制要件に従う責任があります。

Python でのログ記録の詳細については、Python の「[Logging: how-to](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)」を参照してください。

## <a name="next-steps"></a>次のステップ

追加のコード サンプルについては、「[Microsoft ID プラットフォームのコード サンプル](sample-v2-code.md)」を参照してください。
