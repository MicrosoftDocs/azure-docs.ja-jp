---
title: 開発する認証と認可のアプリケーションの回復性を向上させる
titleSuffix: Microsoft identity platform
description: Azure Active Directory と Microsoft ID プラットフォームを使用したアプリケーション開発向けの回復性ガイダンスの概要
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: d06e851390537bf94b59e656f84bf58fe7216410
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96317354"
---
# <a name="increase-resilience-of-authentication-and-authorization-applications-you-develop"></a>開発する認証と認可のアプリケーションの回復性を向上させる

Microsoft ID では、トークンベースの最新の認証と認可が使用されます。 これは、アプリケーションが ID プロバイダーからトークンを取得して、ユーザーを認証し、保護された API を呼び出すことをアプリケーションに認可することを意味します。

トークンは、アプリで新しいものを取得することが必要になるまで、一定の期間有効です。 まれに、ネットワークやインフラストラクチャの障害、認証サービスの停止などの問題が原因で、トークンを取得するための呼び出しが失敗することがあります。 このドキュメントでは、トークンの取得エラーが発生した場合に、アプリケーションの回復性を向上させるために開発者が実行できる手順について説明します。

これらの記事では、Microsoft ID プラットフォームと Azure Active Directory を使用してアプリの回復性を向上させるためのガイダンスを提供します。 サインインしているユーザーに代わって動作するクライアント アプリケーションと、それ自体のために動作するデーモン アプリケーションの両方についてのガイダンスがあります。 こらには、トークンの使用と、リソースの呼び出しに関するベストプラクティスが含まれます。

- [ユーザーのサインインを行う アプリケーションの回復性を強化する](resilience-client-app.md)
- [ユーザーなしのアプリケーションの回復性を強化する](resilience-daemon-app.md)
- [ID およびアクセス管理インフラストラクチャで回復性を強化する](resilience-in-infrastructure.md)
- [CIAM システムで回復性を強化する](resilience-b2c.md)
