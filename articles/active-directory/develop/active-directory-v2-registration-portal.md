---
title: "アプリ登録ポータルのヘルプ トピック | Microsoft Docs"
description: "Microsoft アプリ登録ポータルの各種機能の説明。"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2016
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 3e0bb32a6c60011d71606c896cc506f430bc3c27
ms.openlocfilehash: 19d8d860e39139d3003c216659b7203e2482154e


---
# <a name="app-registration-reference"></a>アプリ登録のリファレンス
このドキュメントでは、Microsoft アプリ登録ポータル ([https://apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) の各種機能のコンテキストと説明を提供します。

## <a name="my-applications"></a>マイ アプリケーション
この一覧には、Azure AD v2.0 エンドポイントで使用するために登録されているすべてのアプリケーションが含まれています。  これらのアプリケーションには、Microsoft アカウントからの個人アカウントと、Azure Active Directory からの職場/学校アカウントの両方を持つユーザーにサインインする機能があります。  Azure AD v2.0 エンドポイントの詳細については、 [v2.0 の概要](active-directory-appmodel-v2-overview.md)に関するページを参照してください。  これらのアプリケーションはまた、Microsoft アカウント認証エンドポイント ( `https://login.live.com`) と統合するために使用することができます。

## <a name="live-sdk-applications"></a>Live SDK アプリケーション
この一覧には、Microsoft アカウントのみでの使用のために登録されたアプリケーションが含まれています。  これらのアプリケーションを Azure Active Directory で使用するようにすることは一切できません。  ここでは、以前に MSA 開発者ポータル ( `https://account.live.com/developers/applications`) に登録したアプリケーションを確認できます。  以前 `https://account.live.com/developers/applications` で実行していた機能はすべて、この新しいポータル (`https://apps.dev.microsoft.com`) で実行できるようになりました。  Microsoft アカウント アプリケーションに関して質問がある場合は、お問い合わせください。

## <a name="application-secrets"></a>アプリケーション シークレット
アプリケーション シークレットは資格情報であり、これを持つアプリケーションは Azure AD で信頼できる[クライアント認証](http://tools.ietf.org/html/rfc6749#section-2.3)を実行することができます。  OAuth と OpenID Connect では、アプリケーション シークレットは一般に `client_secret` として参照されます。  v2.0 プロトコルの場合、Web のアドレス指定可能な場所でセキュリティ トークンを受信するアプリケーションは (`https` スキームを使用)、セキュリティ トークンの引き換え時にアプリケーション シークレットを使用して Azure AD に身元を証明する必要があります。  さらに、デバイス上でトークンを受信するネイティブ クライアントは、アプリケーション シークレットを使用してクライアント認証を実行することが許可されません。これは安全でない環境に機密情報が格納されるのを防ぐためです。

各アプリは、任意の時点で 2 つの有効なアプリケーション シークレットを保持することができます。  2 つのシークレットを管理することにより、アプリケーションの環境全体にわたって定期的にキーのロール オーバーを実行することができます。  アプリケーションの全体を新しいシークレットに移行したら、古いシークレットを削除し、新しいシークレットをプロビジョニングすることができます。

この時点で、アプリ登録ポータルで許可されるアプリケーション シークレットは 2 種類のみです。  **[新しいパスワードを生成]** を選択すると、共有シークレットが作成され個々のデータ ストアに格納されて、アプリケーションで使用できるようになります。  **[新しいキー ペアを生成]** を選択すると、ダウンロードして Azure AD に対するクライアント認証に使用できる新しい公開/秘密キー ペアが作成されます。

## <a name="profile"></a>プロファイル
アプリ登録ポータルのプロファイル セクションを使用すると、アプリケーションのサインイン ページをカスタマイズすることができます。  この時点で、サインイン ページのアプリケーション ロゴ、サービス URL の利用規約、およびプライバシーに関する声明を変更できます。  ロゴは、GIF、PNG、または JPEG ファイル形式の 48 x 48 ピクセルまたは 50 x 50 ピクセルの透明なイメージとし、サイズを 15 KB 以下とする必要があります。  値の変更し、結果として生成されるサインイン ページを表示してみてください。

## <a name="live-sdk-support"></a>Live SDK のサポート
"Live SDK のサポート" を有効にすると、作成するアプリケーション シークレットは、Azure AD データ ストアと Microsoft アカウント データ ストアの両方にプロビジョニングされます。  これにより、アプリケーションを Microsoft アカウント サービス (login.live.com) と直接統合することができます。  Microsoft アカウントを直接使用してアプリを作成する場合 (Azure AD v2.0 を使用するのではなく)、Live SDK のサポートが有効になっていることを確認してください。

Live SDK のサポートを無効にすると、アプリケーション シークレットは Azure AD データ ストアにしか書き込まれません。  Azure AD データ ストアでは、FISMA 準拠など、特定の標準を満たすことができるようにするためのエンタープライズ クラスの規定を取り入れています。  Live SDK のサポートを有効にすると、アプリケーションがこれらの標準のうちのいくつかに準拠しなくなる可能性があります。

Azure AD v2.0 エンドポイントの使用に限定する場合は、Live SDK のサポートを無効にしてかまいません。




<!--HONumber=Nov16_HO3-->


