---
title: アプリ登録ポータルのヘルプ トピック | Microsoft Docs
description: Microsoft アプリ登録ポータルの各種機能の説明。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: celested
ms.reviewer: lenalepa
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b77b5185056329113ee1fd17fa3ed3f364380ca2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58095876"
---
# <a name="app-registration-reference"></a>アプリ登録のリファレンス
このドキュメントでは、[アプリケーション登録ポータル](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/)の各種機能のコンテキストと説明を提供します。

> [!NOTE]
> 2019 年 5 月より、[アプリケーション登録ポータル](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/)での集中型アプリケーションと Azure AD アプリケーションの登録と管理はサポートされなくなります。 既存のアプリケーションを管理し、新しいアプリケーションを登録するには、Azure portal の[アプリの登録 (プレビュー)](https://aka.ms/appregistrations) エクスペリエンスを使用することをお勧めします。

## <a name="my-applications-or-converged-applications"></a>マイ アプリケーションまたは集中型アプリケーション
この一覧には、Azure AD v2.0 エンドポイントで使用するために登録されているすべてのアプリケーションが含まれています。 これらのアプリケーションには、個人の Microsoft アカウントと、Azure Active Directory の職場/学校アカウントの両方を持つユーザーをサインインする機能があります。 Azure AD v2.0 エンドポイントの詳細については、 [v2.0 の概要](active-directory-appmodel-v2-overview.md)に関するページを参照してください。 これらのアプリケーションはまた、Microsoft アカウント認証エンドポイント ( `https://login.live.com`) と統合するために使用することができます。

## <a name="azure-ad-only-applications"></a>Azure AD 専用アプリケーション
この一覧には、Azure AD v1.0 エンドポイントで使用するために登録されているすべてのアプリケーションが含まれています。 これらのアプリケーションには、Azure Active Directory の職場/学校アカウントを持つユーザーのみがサインインできます。 この一覧には、[Azure portal](https://portal.azure.com) の**アプリ登録**エクスペリエンスを使用して登録されたアプリケーションが含まれます。

## <a name="live-sdk-applications"></a>Live SDK アプリケーション
この一覧には、Microsoft アカウントのみでの使用のために登録されたアプリケーションが含まれています。 これらのアプリケーションを Azure Active Directory で使用するようにすることはできません。 ここでは、以前に MSA 開発者ポータル (`https://account.live.com/developers/applications`) に登録したアプリケーションを確認できます。 以前 `https://account.live.com/developers/applications` で実行していた機能はすべて、この新しいポータル (`https://apps.dev.microsoft.com`) で実行できるようになりました。

## <a name="application-secrets"></a>アプリケーション シークレット
アプリケーション シークレットは資格情報であり、これを持つアプリケーションは Azure AD で信頼できる[クライアント認証](https://tools.ietf.org/html/rfc6749#section-2.3)を実行することができます。 OAuth と OpenID Connect では、アプリケーション シークレットは一般に `client_secret` として参照されます。 v2.0 プロトコルの場合、Web のアドレス指定可能な場所でセキュリティ トークンを受信するアプリケーションは (`https` スキームを使用)、セキュリティ トークンの引き換え時にアプリケーション シークレットを使用して Azure AD に身元を証明する必要があります。 さらに、デバイス上でトークンを受信するネイティブ クライアントは、アプリケーション シークレットを使用してクライアント認証を実行することが許可されません。 これは安全でない環境に機密情報が格納されるのを防ぐためです。

各アプリは、常に 2 つの有効なアプリケーション シークレットを保持することができます。 2 つのシークレットを管理することにより、アプリケーションの環境全体にわたって定期的にキーのロール オーバーを実行することができます。 アプリケーションの全体を新しいシークレットに移行したら、古いシークレットを削除し、新しいシークレットをプロビジョニングすることができます。

この時点で、アプリ登録ポータルで許可されるアプリケーション シークレットは 2 種類のみです。 **[新しいパスワードを生成]** を選択すると、共有シークレットが作成され、個々のデータ ストアに格納されて、アプリケーションで使用できるようになります。 **[新しいキー ペアを生成]** を選択すると、ダウンロードして Azure AD に対するクライアント認証に使用できる新しい公開/秘密キー ペアが作成されます。 **[公開キーのアップロード]** を選択すると、独自の公開/秘密キー ペアを使用することができます。
公開キーを含む証明書をアップロードする必要があります。

## <a name="profile"></a>プロファイル
アプリ登録ポータルのプロファイル セクションを使用すると、アプリケーションのサインイン ページをカスタマイズすることができます。 この時点で、サインイン ページのアプリケーション ロゴ、サービス利用規約 URL、およびプライバシーに関する声明 URL を変更できます。 ロゴは、GIF、PNG、または JPEG ファイル形式の 48 x 48 ピクセルまたは 50 x 50 ピクセルの透明なイメージとし、サイズを 15 KB 以下とする必要があります。 値を変更して、結果として生成されるサインイン ページを表示してみてください。

## <a name="live-sdk-support"></a>Live SDK のサポート
"Live SDK のサポート" を有効にすると、作成するアプリケーション シークレットは、Azure AD データ ストアと Microsoft アカウント データ ストアの両方にプロビジョニングされます。 これにより、アプリケーションを Microsoft アカウント サービス (login.live.com) と直接統合することができます。 Microsoft アカウントを直接使用してアプリを作成する場合 (Azure AD v2.0 を使用するのではなく)、Live SDK のサポートが有効になっていることを確認してください。

Live SDK のサポートを無効にすると、アプリケーション シークレットは Azure AD データ ストアにしか書き込まれません。 Azure AD データ ストアでは、FISMA 準拠など、特定の標準を満たすことができるようにするためのエンタープライズ クラスの規定を取り入れています。 Live SDK のサポートを有効にすると、アプリケーションがこれらの標準のうちのいくつかに準拠しなくなる可能性があります。

Azure AD v2.0 エンドポイントの使用に限定する場合は、Live SDK のサポートを無効にしてかまいません。

