---
title: Azure Communication Services に対する認証
titleSuffix: An Azure Communication Services concept document
description: アプリケーションまたはサービスが Communication Services に対して認証を行うさまざまな方法について説明します。
author: GrantMeStrength
manager: jken
services: azure-communication-services
ms.author: jken
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: b702afe9c4359d9f8711846d93fd79df9fc2f42e
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485963"
---
# <a name="authenticate-to-azure-communication-services"></a>Azure Communication Services に対する認証

クライアントと Azure Communication Services とのやり取りはすべて認証される必要があります。 一般的なアーキテクチャでは、ユーザーの作成やトークンの発行に、信頼できるユーザー アクセス サービスの [クライアントとサーバーのアーキテクチャ](./client-and-server-architecture.md)、*アクセス キー*、または *マネージド ID* が使用されます。 また、信頼できるユーザー アクセス サービスによって発行された *ユーザー アクセス トークン* は、クライアント アプリケーションが他の通信サービス (チャットや通話サービスなど) にアクセスするために使用されます。

Azure Communication Services の SMS サービスは、認証のために *アクセス キー* や *マネージド ID* も受け入れます。 これは通常、信頼できるサービス環境で実行されているサービス アプリケーションで発生します。

ここでは、各認証オプションについて簡単に説明します。

- SMS および ID 操作用の **アクセス キー** 認証。 アクセス キー認証は、信頼できるサービス環境で実行されているサービス アプリケーションに適しています。 アクセス キーは、Azure Communication Services ポータルで確認できます。 アクセスキーを使用して認証するには、サービス アプリケーションがアクセス キーを資格情報として使用して、対応する SMS または ID クライアント ライブラリを初期化します。「[アクセス トークンを作成して管理する](../quickstarts/access-tokens.md)」を参照してください。 アクセス キーはリソースの接続文字列の一部であるため、「[Communication Services のリソースを作成して管理する](../quickstarts/create-communication-resource.md)」を参照してください。接続文字列を使用した認証は、アクセス キーを使用した認証に相当します。
- SMS および ID 操作用の **マネージド ID** 認証。 マネージド ID については、「[マネージド ID](../quickstarts/managed-identity.md)」を参照してください。これは、信頼できるサービス環境で実行されているサービス アプリケーションに適しています。 マネージド ID を使用して認証するために、サービス アプリケーションは、ID およびマネージド ID のシークレットを使用して資格情報を作成し、対応する SMS または ID クライアント ライブラリを初期化します。「[アクセス トークンを作成して管理する](../quickstarts/access-tokens.md)」を参照してください。
- チャットおよび通話用の **ユーザー アクセス トークン** 認証。 ユーザー アクセス トークンを使用すると、クライアント アプリケーションが Azure Communication のチャットおよび通話サービスに対して認証を行うことができます。 これらのトークンは、作成する "信頼できるユーザー アクセス サービス" で生成されます。 その後、クライアント デバイスに提供され、クライアント デバイスはそのトークンを使用して、チャットおよび通話のクライアント ライブラリを初期化します。 詳細については、「[チャットをアプリに追加する](../quickstarts/chat/get-started.md)」などを参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Communication Services のリソースを作成して管理する](../quickstarts/create-communication-resource.md)
> [Azure CLI から Azure Active Directory マネージド ID アプリケーションを作成する](../quickstarts/managed-identity-from-cli.md) 
> [ユーザー アクセス トークンの作成](../quickstarts/access-tokens.md)

詳細については、次の記事を参照してください。
- [クライアントとサーバーのアーキテクチャについて学習する](../concepts/client-and-server-architecture.md)
