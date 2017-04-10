---
title: "Azure Active Directory アプリケーション プロキシを使用したリモート デスクトップの公開 | Microsoft Docs"
description: "Azure AD アプリケーション プロキシ コネクタの基本について説明します。"
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
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 0d9f2f5c9ad5c47eb5e1a8a8ec3fee2952caef63
ms.openlocfilehash: 5c01f89c335a6c85b5b50a4c8491ae1b02740578
ms.lasthandoff: 02/27/2017


---

# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシを使用したリモート デスクトップの発行

この記事では、リモート ユーザーが Windows リモート デスクトップのデプロイにアクセスできるようにする方法について説明します。 リモート デスクトップのデプロイは、オンプレミスにもプライベート ネットワーク (IaaS デプロイなど) にも配置できます。

> [!NOTE]
> アプリケーション プロキシ機能は、Azure Active Directory (Azure AD) の Premium または Basic エディションにアップグレードした場合にのみ利用できます。 詳細については、「 [Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。

リモート デスクトップ プロトコル (RDP) のトラフィックは、パススルー プロキシ アプリケーションとして Azure AD アプリケーション プロキシ経由で発行できます。 このソリューションは、接続の問題を解決し、ネットワークのバッファー処理、セキュリティを強化したインターネットのフロントエンド、分散型サービス拒否 (DDoS) 保護などの基本的なセキュリティ保護を提供します。

## <a name="remote-desktop-deployment"></a>リモート デスクトップのデプロイメント

リモート デスクトップのデプロイ内で、リモート デスクトップ ゲートウェイが発行されると、RPC (リモート プロシージャ コール) over HTTPS トラフィックを、RDP over UDP (ユーザー データグラム プロトコル) トラフィックに変換できます。

MSTSC.exe などのリモート デスクトップ クライアントを使用して Azure AD アプリケーション プロキシにアクセスするようクライアントを構成できます。 この方法で、コネクタを使用してリモート デスクトップ ゲートウェイへの新しい HTTPS 接続を作成できます。 その結果、ゲートウェイはインターネットに直接公開されなくなり、すべての HTTPS 要求は最初にクラウドで終了します。

このトポロジを次の図に示します。

 ![Azure AD サービスのローカルの図](./media/application-proxy-publish-remote-desktop/remote-desktop-topology.png)

## <a name="configure-the-remote-desktop-gateway-url"></a>リモート デスクトップ ゲートウェイの URL の構成

ユーザーは、通常どおりに、リモート デスクトップ ゲートウェイの URL を構成し、RDP トラフィックをトリガーすると、ファイルとその他のメソッドにアクセスできます。

アプリケーション プロキシ (msappproxy.net) で指定されるドメイン名、または Azure AD で構成されているカスタム ドメイン名 (rdg.contoso.com など) を使用して発行できます。

クライアント デバイスと RDP ファイルに既にリモート デスクトップ ゲートウェイの URL が構成されている場合は、同じドメイン名を使用して変更を回避することもできます。 この場合、このドメインを対象とする証明書をアプリケーション プロキシに提供し、その証明書失効リスト (CRL) をインターネット経由でアクセスできる必要があります。

リモート デスクトップ ゲートウェイの URL が構成されていない場合は、ユーザーまたは管理者が、次のように、リモート デスクトップ クライアント (MSTSC) で [リモート デスクトップ接続] ダイアログ ボックスを使用して URL を指定できます。

 ![[リモート デスクトップ接続] ダイアログ ボックス](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-advanced.png)

**[詳細設定]** タブの **[設定]** をクリックすると、**[接続設定]** ダイアログ ボックスが表示されます。

 ![[リモート デスクトップ接続] ダイアログ ボックスの [接続設定] ウィンドウ](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-settings.png)

## <a name="remote-desktop-web-access"></a>リモート デスクトップの Web アクセス

リモート デスクトップ Web アクセス (RDWA) ポータルを使用している組織の場合は、Azure AD アプリケーション プロキシを使用して発行することもできます。 事前認証とシングル サインオン (SSO) を使用してこのポータルに公開できます。

RDWA シナリオのトポロジを次の図に示します。

 ![RDWA シナリオの図](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal1.png)

上のケースでは、ユーザーは RDWA にアクセスする前に Azure AD で認証されます。 ユーザーは Azure AD で既に認証されている場合 (Office 365 を使用している場合など)、RDWA のために再認証する必要はありません。

ユーザーが RDP セッションを開始するときには、RDP チャネル経由で再認証する必要があります。 これは、RDWA からリモート デスクトップ ゲートウェイへの SSO が、ActiveX を使用したクライアントでのユーザーの資格情報の格納に基づいているためです。 RDWA フォームベースの認証から、このプロセスがトリガーされます。 RDWA 認証で Kerberos を使用しているときにはフォームベースの認証が行われず、RDWA から RDP への SSO は機能しません。

RDWA で RDP トラフィックへの SSO が必要な場合、または RDWA のフォームベースの認証が大きくカスタマイズされている場合は、事前認証なしで RDWA を発行できます。

このシナリオのトポロジを次の図に示します。

 ![RDWA シナリオの図](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal2.png)

上のケースでは、ユーザーがフォームベースの認証を使用して RDWA に対する認証を行う必要がありますが、RDP 経由で認証する必要はありません。

>[!NOTE]
>上記のどちらの場合も、RDP トラフィックに対する事前認証は必要ありません。 そのため、ユーザーは最初に RDWA を経由せずにアクセスできます。

## <a name="next-steps"></a>次のステップ

[Azure AD アプリケーション プロキシによる SharePoint へのリモート アクセスの有効化](application-proxy-enable-remote-access-sharepoint.md)  
[Azure Portal でアプリケーション プロキシを有効にする](https://github.com/Microsoft/azure-docs-pr/blob/master/articles/active-directory/active-directory-application-proxy-enable.md)

