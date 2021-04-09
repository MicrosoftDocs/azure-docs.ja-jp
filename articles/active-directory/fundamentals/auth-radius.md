---
title: Azure Active Directory を使用した RADIUS 認証
description: Azure Active Directory を使用した RADIUS 認証の取得に関するアーキテクチャ ガイダンス。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97c524e1f4c05787f1dd61dea5a463e8fa83511a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96168629"
---
# <a name="radius-authentication-with-azure-active-directory"></a>Azure Active Directory を使用した RADIUS 認証

リモート認証ダイヤルイン ユーザー サービス (RADIUS) は、ダイヤルイン ユーザーの一元的な認証と認可を有効にすることで、ネットワークを保護するネットワーク プロトコルです。 多くのアプリケーションは、ユーザーを認証するために RADIUS プロトコルに依存しています。

Microsoft Windows Server には、RADIUS サーバーとして機能し、RADIUS 認証をサポートできるネットワーク ポリシー サーバー (NPS) と呼ばれる役割があります。

Azure Active Directory (Azure AD) では、RADIUS ベースのシステムを使用した多要素認証が有効になっています。 顧客が前述の RADIUS ワークロードのいずれかに Azure AD Multi-Factor Authentication を適用する場合は、Azure AD Multi-Factor Authentication の NPS 拡張機能を Windows NPS サーバーにインストールできます。 

Windows NPS サーバーでは、Active Directory を照合してユーザーの資格情報が認証され、多要素認証要求が Azure に送信されます。 その後、ユーザーが自分のモバイル認証システムでチャレンジを受信します。 成功すると、クライアント アプリケーションからサービスへの接続が許可されます。 

## <a name="use-when"></a>次の場合に使用します。 

次のようなアプリケーションに Multi-Factor Authentication を追加する必要があります。
* 仮想プライベート ネットワーク (VPN)
* WiFi アクセス
* リモート デスクトップ ゲートウェイ (RDG)
* 仮想デスクトップ インフラストラクチャ (VDI)
* サービスへのユーザー認証を行うために RADIUS プロトコルに依存しているその他のアプリケーション。 

> [!NOTE]
> Azure AD Multi-Factor Authentication を VPN ワークロードに適用するために、RADIUS、および Azure AD Multi-Factor Authentication の NPS 拡張機能に依存するのではなく、VPN を SAML にアップグレードし、Azure AD で直接 VPN のフェデレーションを行うことをお勧めします。 これにより、完全な Azure AD 保護 (条件付きアクセス、多要素認証、デバイス コンプライアンス、ID 保護など) が VPN で実現されます。

![アーキテクチャの図](./media/authentication-patterns/radius-auth.png)


## <a name="components-of-the-system"></a>システムのコンポーネント 

* **クライアント アプリケーション (VPN クライアント)** :RADIUS クライアントに認証要求を送信します。

* **RADIUS クライアント**:クライアント アプリケーションからの要求を変換し、それを NPS 拡張機能がインストールされている RADIUS サーバーに送信します。

* **RADIUS サーバー**:Active Directory に接続して、RADIUS 要求のプライマリ認証を実行します。 成功すると、要求を Azure AD Multi-Factor Authentication の NPS 拡張機能に渡します。

* **NPS 拡張機能**:セカンダリ認証を行うために、Azure AD Multi-Factor Authentication への要求をトリガーします。 成功すると、NPS 拡張機能によって、Azure のセキュリティ トークン サービスで発行された多要素認証要求が含まれるセキュリティ トークンが RADIUS サーバーに渡され、認証要求が完了します。

* **Azure AD Multi-Factor Authentication**: Azure AD と通信してユーザーの詳細を取得し、ユーザーが構成した検証メソッドを使用してセカンダリ認証を行います。

## <a name="implement-radius-with-azure-ad"></a>Azure AD を使用した RADIUS を実装する 

* [NPS を使用した Azure AD Multi-Factor Authentication 機能を提供する](../authentication/howto-mfa-nps-extension.md) 

* [Azure AD Multi-Factor Authentication の NPS 拡張機能を構成する](../authentication/howto-mfa-nps-extension-advanced.md) 

* [NPS 拡張機能を使用する Azure AD Multi-Factor Authentication を備えた VPN](../authentication/howto-mfa-nps-extension-vpn.md) 

  
‎ 

