---
title: MSAL for Java (MSAL4j) を使用したトークン キャッシュのアカウントの取得と削除
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library for Java を使用してトークン キャッシュのアカウントを表示および削除する方法を確認します。
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a40c5f2e272a44727d4da91600093e8dc691a95f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916640"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>MSAL for Java を使用したトークン キャッシュのアカウントの取得と削除

MSAL for Java は、既定でインメモリ トークン キャッシュを備えています。 アプリケーション インスタンスの期間、インメモリ トークン キャッシュは継続します。

## <a name="see-which-accounts-are-in-the-cache"></a>キャッシュ内に存在するアカウントを確認する

次の例に示すように、`PublicClientApplication.getAccounts()` を呼び出して、キャッシュ内に存在するアカウントを確認できます。

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>キャッシュからアカウントを削除する

キャッシュからアカウントを削除するには、次の例に示すように、削除する必要があるアカウントを見つけて、`PublicClientApplicatoin.removeAccount()` を呼び出します。

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>詳細情報

MSAL for Java を使用している場合、[MSAL for Java のカスタム トークン キャッシュのシリアル化](msal-java-token-cache-serialization.md)について学習してください。
