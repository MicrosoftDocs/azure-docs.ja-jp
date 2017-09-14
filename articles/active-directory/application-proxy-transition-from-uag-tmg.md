---
title: "Azure AD アプリケーション プロキシへのアップグレード | Microsoft Docs"
description: "Microsoft Forefront または Unified Access Gateway からアップグレードする場合に最適なプロキシ ソリューションを選択します。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: 6c9f70493155de6989b26fd4e8bcf1dff01c835c
ms.contentlocale: ja-jp
ms.lasthandoff: 08/01/2017

---
# <a name="compare-remote-access-solutions"></a>リモート アクセス ソリューションを比較します

Azure Active Directory アプリケーション プロキシは、Microsoft が提供する 2 つのリモート アクセス ソリューションの 1 つです。 もう 1 つは、オンプレミスのバージョンである Web アプリケーション プロキシです。 これら 2 つのソリューションは、Microsoft がこれまで提供していた Microsoft Forefront Threat Management Gateway (TMG) と Unified Access Gateway (UAG) の後継となるソリューションです。 この記事で、これら 4 つのソリューションを相互に比較する方法を把握できます。 非推奨の TMG または UAG ソリューションを使用しているユーザーは、この記事を活用して、どちらかのアプリケーション プロキシへの移行を計画してください。 


## <a name="feature-comparison"></a>機能の比較

この表を使用して、Threat Management Gateway (TMG)、Unified Access Gateway (UAG)、Web アプリケーション プロキシ (WAP)、および Azure AD アプリケーション プロキシ (AP) を相互に比較する方法を把握します。

| 機能 | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| 証明書認証 | あり | あり | - | - |
| ブラウザー アプリの選択的な発行 | あり | あり | あり | あり |
| 事前認証とシングル サインオン | あり | あり | あり | あり | 
| レイヤー 2/3 ファイアウォール | あり | あり | - | - |
| 転送プロキシ機能 | あり | - | - | - |
| VPN 機能 | あり | あり | - | - |
| プロトコルの豊富なサポート | - | あり | HTTP 経由で実行される場合はあり | HTTP 経由でまたはリモート デスクトップ ゲートウェイ経由で実行する場合はあり |
| ADFS プロキシ サーバーとしてのサーバー | - | あり | あり | - |
| アプリケーションにアクセスするための 1 つのポータル | - | あり | - | あり |
| 応答本文のリンク変換 | あり | あり | - | あり | 
| ヘッダーによる認証 | - | あり | - | あり。PingAccess を使用 | 
| クラウド規模のセキュリティ | - | - | - | あり | 
| 条件付きアクセス | - | あり | - | あり |
| 非武装地帯 (DMZ) にコンポーネントが存在しない | - | - | - | あり |
| 受信接続がない | - | - | - | あり |

ほとんどのシナリオでは、最新のソリューションとして Azure AD アプリケーションをお勧めします。 Web アプリケーション プロキシは AD FS 用のプロキシ サーバーを必要とするシナリオにのみ推奨され、Azure Active Directory のカスタム ドメインを使用することはできません。 

類似製品に比べ、Azure AD アプリケーション プロキシには、次に示す独自のメリットがあります。

- オンプレミスのリソースへの Azure AD の拡張
   - クラウド規模のセキュリティと保護
   - 簡単に有効にできる条件付きアクセスや Multi-Factor Authentication などの機能
- 非武装地帯にコンポーネントが存在しない
- 受信接続は不要
- ユーザーがすべてのアプリケーション (O365、Azure AD に統合された SaaS アプリ、オンプレミスの Web アプリなど) に 移動できる 1 つのアクセス パネル 


## <a name="next-steps"></a>次のステップ

- [Azure AD アプリケーション プロキシを使用してセキュリティで保護されたオンプレミス アプリケーションへのリモート アクセスを提供する](active-directory-application-proxy-get-started.md)
- [Forefront TMG と UAG からアプリケーション プロキシに移行する](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/)

