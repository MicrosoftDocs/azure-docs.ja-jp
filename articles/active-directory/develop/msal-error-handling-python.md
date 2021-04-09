---
title: Python 用の MSAL におけるエラーと例外の処理
titleSuffix: Microsoft identity platform
description: Python 用の MSAL アプリケーションで、エラーと例外、条件付きアクセス、クレーム チャレンジ、再試行を処理する方法について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, rayluo
ms.custom: aaddev
ms.openlocfilehash: 3046787393d38ed60b54236f33acc065db90321d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98761090"
---
# <a name="handle-errors-and-exceptions-in-msal-for-python"></a>Python 用の MSAL におけるエラーと例外の処理

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-python"></a>Python 用の MSAL でのエラー処理

MSAL for Python では、ほとんどのエラーは API 呼び出しからの戻り値として伝達されます。 このエラーは、Microsoft ID プラットフォームからの JSON 応答を含むディクショナリとして表されます。

* 成功時の応答には `"access_token"` が含まれています。 応答の形式は OAuth2 プロトコルで定義されています。 詳細については、「[5.1 Successful Response](https://tools.ietf.org/html/rfc6749#section-5.1)」(5.1 成功応答) を参照してください。
* エラー応答には `"error"` が含まれ、通常は `"error_description"` も含まれています。 応答の形式は OAuth2 プロトコルで定義されています。 詳細については、「[5.2 Error Response](https://tools.ietf.org/html/rfc6749#section-5.2)」(5.2 エラー応答) を参照してください。

エラーが返されると、`"error_description"` キーには人間が読めるメッセージが含まれています。このメッセージには通常、Microsoft ID プラットフォーム エラー コードが含まれています。 さまざまなエラー コードの詳細については、「[認証と承認エラー コード](./reference-aadsts-error-codes.md)」をご覧ください。

MSAL for Python では、ほとんどのエラーはエラー値を返すことで処理されるため、例外はまれです。 `ValueError` 例外は、API パラメーターの形式が正しくない場合など、ライブラリの使用方法に問題がある場合にのみスローされます。

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>次のステップ

問題の診断とデバッグに役立つように、[Python 用の MSAL でログ記録](msal-logging-python.md)を有効にすることを検討してください。
