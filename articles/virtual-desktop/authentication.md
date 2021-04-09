---
title: Windows Virtual Desktop 認証 - Azure
description: Windows Virtual Desktop の認証方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/26/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 04a4366bfee6b1d9c5f52d649910163269962684
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709259"
---
# <a name="supported-authentication-methods"></a>サポートされている認証方法

この記事では、Windows Virtual Desktop で使用できる認証の種類の概要について説明します。

## <a name="session-host-authentication"></a>セッション ホスト認証

Windows Virtual Desktop では、セッション ホスト認証に NT LAN Manager (NTLM) と Kerberos の両方がサポートされています。 ただし、クライアントで Kerberos を使用するには、ドメイン コントローラーで実行されているキー配布センター (KDC) サービスから Kerberos セキュリティ チケットを取得する必要があります。 チケットを取得するには、クライアントからドメイン コントローラーへ向かう直接の通信経路が必要になります。 企業ネットワークを使用することで、直接の通信経路を確保できます。 また、企業ネットワークに対して VPN 接続を使用することや、[KDC プロキシ サーバー](key-distribution-center-proxy.md)を設定することもできます。

現在サポートされているサインイン方法は次のとおりです。

- Windows デスクトップクライアント
    - ユーザー名とパスワード
    - スマート カード
    - Windows Hello for Business (証明書信頼のみ)
- Microsoft Store クライアント
    - ユーザー名とパスワード
- Web クライアント
    - ユーザー名とパスワード
- Android
    - ユーザー名とパスワード
- iOS
    - ユーザー名とパスワード
- macOS
    - ユーザー名とパスワード

>[!NOTE]
>スマートカードと Windows Hello for Business では、サインインに Kerberos のみが使用できます。 Kerberos でサインインするには、ドメイン コントローラーまたは [KDC プロキシ サーバー](key-distribution-center-proxy.md)への通信経路が必要になります。

## <a name="hybrid-identity"></a>ハイブリッド ID

Windows Virtual Desktop では、[ハイブリッド ID](../active-directory/hybrid/whatis-hybrid-identity.md) がサポートされています。これを実現するのが Azure Active Directory (AD) で、対象には Active Directory フェデレーション サービス (AD FS) を使用してフェデレーションされているものも含まれます。 ユーザーは Azure AD で検出可能である必要があるため、ADFS を使用したスタンドアロンの Active Directory デプロイは Windows Virtual Desktop ではサポートされません。

## <a name="single-sign-on-sso"></a>シングル サインオン (SSO)

現在、Windows Virtual Desktop では SSO に対する Active Directory フェデレーション サービス (ADFS) はサポートされていません。

セッション ホストの認証情報を要求されないようにする唯一の方法は、クライアントに保存することです。 他のユーザーがリソースにアクセスできないようにするため、これはセキュリティで保護されたデバイスを使用している場合にのみ行うことをお勧めします。

## <a name="next-steps"></a>次のステップ

デプロイをセキュリティで保護するための他の方法については、 「[セキュリティの運用方法](security-guide.md)」を確認してください。
