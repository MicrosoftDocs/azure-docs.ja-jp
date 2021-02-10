---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: e54301e01d25fb075325ef34522daa5f1b691dd5
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760674"
---
## <a name="conditional-access-and-claims-challenges"></a>条件付きアクセスとクレーム チャレンジ

トークンをサイレントで取得するとき、アクセスしようとしている API で MFA ポリシーなどの[条件付きアクセス クレーム チャレンジ](../articles/active-directory/develop/v2-conditional-access-dev-guide.md)が必要な場合、ご利用のアプリケーションはエラーを受け取ることがあります。

このエラーを処理するパターンは、MSAL を使用して対話形式でトークンを取得することです。 これによりユーザーにメッセージが表示され、ユーザーは必要な条件付きアクセス ポリシーを満たす機会を与えられます。

条件付きアクセスを必要とする API を呼び出す特定のケースでは、API からのエラーでクレーム チャレンジを受け取ることがあります。 たとえば、条件付きアクセス ポリシーがマネージド デバイス (Intune) を使用するものである場合、エラーは [AADSTS53000: Your device is required to be managed to access this resource](../articles/active-directory/develop/reference-aadsts-error-codes.md) (このリソースにアクセスするには、デバイスがマネージドである必要があります) のようなものになります。 この場合、トークン取得呼び出しでクレームを渡して、適切なポリシーを満たすようユーザーに求めることができます。
