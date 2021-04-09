---
title: カスタム トークン キャッシュのシリアル化 (MSAL for Python) | Azure
titleSuffix: Microsoft identity platform
description: MSAL for Python 用のトークン キャッシュをシリアル化する方法について説明します
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 7aaf59377949101ba8dd5c9454b89229e925e859
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87846192"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>MSAL for Python でのカスタム トークン キャッシュのシリアル化

MSAL Python では、アプリ セッションの期間だけ保持されるメモリ内トークン キャッシュが、[ClientApplication](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication) のインスタンスを作成するときに既定で提供されます。

アプリの異なるセッションでキャッシュにアクセスできるようにする、トークン キャッシュのシリアル化は、"そのままでは" 提供されません。 これは、MSAL Python は、Web アプリなどのファイル システムにアクセスできないアプリの種類で使用できるためです。 MSAL Python アプリで永続的なトークン キャッシュを作成するには、カスタム トークン キャッシュのシリアル化を提供する必要があります。

トークン キャッシュをシリアル化する方法は、パブリック クライアント アプリケーション (デスクトップ) を作成しているのか、それとも機密クライアント アプリケーション (Web アプリ、Web API、デーモン アプリ) を作成しているのかによって異なります。

## <a name="token-cache-for-a-public-client-application"></a>パブリック クライアント アプリケーション用のトークン キャッシュ

パブリック クライアント アプリケーションは、ユーザーのデバイス上で実行され、1 人のユーザー用にトークンを管理します。 この場合、キャッシュ全体をファイルにシリアル化することができます。 自分のアプリまたは別のアプリがキャッシュに同時にアクセスできる場合は、必ずファイル ロックを用意してください。 ロックを行わずにトークン キャッシュをファイルにシリアル化する方法の簡単な例については、[SerializableTokenCache](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) クラスのリファレンス ドキュメントの例を参照してください。

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Web アプリ (機密クライアント アプリケーション) 用のトークン キャッシュ

Web アプリまたは Web API の場合は、セッション、Redis Cache、またはデータベースを使用して、トークン キャッシュを格納することができます。 アカウントごとにトークン キャッシュがシリアル化されるように、ユーザーごと (アカウントごと) に 1 つのトークン キャッシュが必要です。

## <a name="next-steps"></a>次のステップ

Windows または Linux の Web アプリまたは Web API に対してトークン キャッシュを使用する方法の例については、[ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) を参照してください。 この例は、Microsoft Graph API を呼び出す Web アプリに関するものです。
