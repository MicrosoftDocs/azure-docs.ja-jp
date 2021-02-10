---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 2ff600429cefbfe0c9a4f0522ee49274000029ca
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760696"
---
この記事では、さまざまな種類のエラーの概要と、一般的なサインイン エラーを処理するための推奨事項を提供します。

## <a name="msal-error-handling-basics"></a>MSAL のエラー処理の基本

Microsoft Authentication Library (MSAL) での例外は、エンド ユーザーに表示するためのものではなく、アプリ開発者がトラブルシューティングに使うことが意図されています。 例外のメッセージはローカライズされていません。

例外やエラーを処理するときは、例外の種類自体とエラー コードを使って、例外を区別できます。  エラー コードの一覧については、「[Azure AD 認証と承認のエラー コード](../articles/active-directory/develop/reference-aadsts-error-codes.md)」をご覧ください。

サインイン中に、同意、条件付きアクセス (MFA、デバイス管理、場所に基づく制限)、トークンの発行と引き換え、ユーザー プロパティに関するエラーが発生することがあります。

次のセクションでは、アプリのエラー処理の詳細について詳しく説明します。