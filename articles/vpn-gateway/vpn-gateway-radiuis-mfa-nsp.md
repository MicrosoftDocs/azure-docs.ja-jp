---
title: Multi-Factor Authentication 用の NPS サーバーを使用した Azure VPN ゲートウェイ RADIUS 認証のセキュリティ保護 | Microsoft Docs
description: Azure ゲートウェイ RADIUS 認証と Multi-Factor Authentication 用の NPS サーバーの統合について説明します。
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: willchen
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 8e10151cd117a3400893f94559b2c9892de9f3c7
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67666211"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Azure VPN ゲートウェイ RADIUS 認証と Multi-Factor Authentication 用の NPS サーバーを統合する 

この記事では、ネットワーク ポリシー サーバー (NPS) と Azure VPN ゲートウェイ RADIUS 認証を統合して、ポイント対サイト VPN 接続用の Multi-Factor Authentication (MFA) を実現する方法について説明します。 

## <a name="prerequisite"></a>前提条件

MFA を有効にするには、ユーザーが Azure Active Directory (Azure AD) 内に存在し、それがオンプレミスかクラウド環境から同期されている必要があります。 また、ユーザーが MFA の自動登録プロセスを完了している必要もあります。  詳細については、「[アカウントへの 2 段階認証の設定](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md)」を参照してください。

## <a name="detailed-steps"></a>詳細な手順

### <a name="step-1-create-a-virtual-network-gateway"></a>手順 1:仮想ネットワーク ゲートウェイの作成

1. [Azure Portal](https://portal.azure.com) にログオンします。
2. 仮想ネットワーク ゲートウェイをホストする仮想ネットワークで、 **[サブネット]** 、 **[ゲートウェイ サブネット]** の順に選択して、サブネットを作成します。 

    ![ゲートウェイ サブネットの追加方法に関する画像](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. 次の設定を指定して、仮想ネットワーク ゲートウェイを作成します。

    - **[ゲートウェイの種類]** : **[VPN]** を選択します。
    - **VPN の種類**: **[ルート ベース]** を選択します。
    - **[SKU]** : 要件に基づいて SKU の種類を選択します。
    - **[仮想ネットワーク]** : ゲートウェイ サブネットを作成した仮想ネットワークを選択します。

        ![仮想ネットワーク ゲートウェイの設定に関する画像](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>手順 2. Azure MFA 用の NPS を構成する

1. NPS サーバーで、[Azure MFA 用の NPS 拡張機能をインストール](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension)します。
2. NPS コンソールを開き、 **[RADIUS クライアント]** を右クリックし、 **[新規]** を選択します。 次の設定を指定して、RADIUS クライアントを作成します。

    - **[フレンドリ名]** : 任意の名前を入力します。
    - **[アドレス (IP または DNS)]** : 手順 1 で作成したゲートウェイ サブネットを入力します。
    - **[共有シークレット]** : 任意のシークレット キーを入力します。後で使用するので覚えておいてください。

      ![RADIUS クライアントの設定に関する画像](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  **[詳細設定]** タブで、 **[RADIUS Standard]\(RADIUS 標準\)** にベンダー名を設定し、 **[追加オプション]** チェック ボックスがオフになっていることを確認します。

    ![RADIUS クライアントの詳細設定に関する画像](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. **[ポリシー]**  >  **[ネットワーク ポリシー]** の順に移動します。 **[Microsoft ルーティングとリモート アクセス サーバーへの接続]** ポリシーをダブルクリックして **[アクセスを許可する]** を選択し、 **[OK]** をクリックします。

### <a name="step-3-configure-the-virtual-network-gateway"></a>手順 3. 仮想ネットワーク ゲートウェイを構成する

1. [Azure Portal](https://portal.azure.com) にログオンします。
2. 作成した仮想ネットワーク ゲートウェイを開きます。 [ゲートウェイの種類] が **[VPN]** 、[VPN の種類] が **[ルート ベース]** に設定されていることを確認します。
3. **[ポイント対サイトの構成]**  >  **[今すぐ構成]** の順にクリックし、次の設定を指定します。

    - **[アドレス プール]** : 手順 1 で作成したゲートウェイ サブネットを入力します。
    - **[認証の種類]** : **[RADIUS 認証]** を選択します。
    - **[サーバーの IP アドレス]** : NPS サーバーの IP アドレスを入力します。

      ![ポイント対サイトの設定に関する画像](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>次の手順

- [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)
- [Azure Multi-Factor Authentication と既存の NPS インフラストラクチャの統合](../active-directory/authentication/howto-mfa-nps-extension.md)
