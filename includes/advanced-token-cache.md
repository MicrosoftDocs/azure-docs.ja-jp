---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.date: 11/05/2020
ms.author: kkrishna
ms.openlocfilehash: 174946667885debc348370ef2c6f93206890e9c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94628024"
---
不在のユーザーに代わって操作を続けられるよう、アクセス トークン キャッシュの使用をバックグラウンドのアプリ、API、サービスに許可するため、MSAL のトークン キャッシュ実装を利用できます。 これは特に、ユーザーがフロントエンド Web アプリを終了した後、バックグラウンドのアプリやサービスがユーザーの代わりに作業を続けなければならない場合に便利です。

現在、ほとんどのバックグラウンド プロセスにおいて、ユーザーの不在時にユーザーのデータを使用して認証または再認証しなければならないとき、[アプリケーションのアクセス許可](/graph/auth/auth-concepts#microsoft-graph-permissions)が使用されます。 アプリケーションのアクセス許可は多くの場合、特権の昇格を必要とする管理者の同意を必要とするため、不要な衝突が発生します。その理由は、開発者のアプリに対してユーザーが最初に同意した以上のアクセス権を取得することを開発者が意図しなかったことにあります。

GitHub にあるこのコード サンプルからは、バックグラウンド アプリから MSAL のトークン キャッシュにアクセスすることでこの不要な衝突を回避する方法を確認できます。

 [バックグラウンドのアプリ、API、サービスからログイン ユーザーのトークン キャッシュにアクセスする](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache)