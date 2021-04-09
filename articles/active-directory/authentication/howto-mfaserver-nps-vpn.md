---
title: Azure MFA Server とサード パーティ VPN - Azure Active Directory
description: Azure MFA Server と Cisco、Citrix、および Juniper を統合するためのステップ バイ ステップの構成ガイドです。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88fe09199cb50d2a3796c3b638dca1a723016dc4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96742019"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>Azure MFA Server とサード パーティの VPN ソリューションの高度なシナリオ

Azure Multi-Factor Authentication Server (Azure MFA Server) を使用して、さまざまなサード パーティの VPN ソリューションにシームレスに接続できます。 この記事では、Cisco&reg; ASA VPN アプライアンス、Citrix NetScaler SSL VPN アプライアンス、および Juniper Networks Secure Access/Pulse Secure Connect Secure SSL VPN アプライアンスに重点を置いて説明します。 これら 3 つの一般的なアプライアンスを対象とした構成ガイドを作成しました。 Azure MFA Server は、RADIUS、LDAP、IIS、または AD FS に対して要求ベースの認証を使用するその他の大半のシステムと統合できます。 詳細については、[Azure MFA Server の構成](howto-mfaserver-deploy.md#next-steps)に関する記事をご覧ください。

> [!IMPORTANT]
> 2019 年 7 月 1 日より、Microsoft では新しいデプロイに対して MFA Server が提供されなくなりました。 サインイン イベント時に多要素認証が必要な新しいお客様は、クラウドベースの Azure AD Multi-Factor Authentication (MFA) を使用していただく必要があります。
>
> クラウドベースの MFA の使用を開始するには、「[チュートリアル: Azure AD Multi-Factor Authentication を使用してユーザーのサインイン イベントのセキュリティを確保する](tutorial-enable-azure-mfa.md)」を参照してください。
>
> クラウドベースの MFA を使用する場合は、[VPN インフラストラクチャと Azure MFA の統合](howto-mfa-nps-extension-vpn.md)に関する記事を参照してください。
>
> 2019 年 7 月 1 日より前に MFA Server をアクティブ化した既存のお客様は、最新バージョンの今後の更新プログラムをダウンロードし、アクティブ化資格情報を通常どおり生成することができます。

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>Cisco ASA VPN アプライアンスと Azure MFA Server
Azure MFA Server と Cisco&reg; ASA VPN アプライアンスを統合すると、Cisco AnyConnect&reg; VPN のログインとポータル アクセスのセキュリティが強化されます。  LDAP または RADIUS プロトコルのいずれかを使用できます。  次のいずれかを選択し、詳しい段階的構成ガイドをダウンロードしてください。

| 構成ガイド | 説明 |
| --- | --- |
| [Anyconnect VPN / Azure MFA 構成の Cisco ASA (LDAP の場合)](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Cisco ASA VPN アプライアンスと Azure MFA を LDAP で統合します |
| [Anyconnect VPN / Azure MFA 構成の Cisco ASA (RADIUS の場合)](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Cisco ASA VPN アプライアンスと Azure MFA を RADIUS で統合します |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>Citrix NetScaler SSL VPN と Azure MFA Server
Azure MFA Server と Citrix NetScaler SSL VPN アプライアンスを統合すると、Citrix NetScaler SSL VPN のログインとポータル アクセスのセキュリティが強化されます。  LDAP または RADIUS プロトコルのいずれかを使用できます。  次のいずれかを選択し、詳しい段階的構成ガイドをダウンロードしてください。

| 構成ガイド | 説明 |
| --- | --- |
| [Citrix NetScaler SSL VPN / Azure MFA 構成 (LDAP の場合)](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Citrix NetScaler SSL VPN と Azure MFA アプライアンスを LDAP で統合します |
| [Citrix NetScaler SSL VPN / Azure MFA 構成 (RADIUS の場合)](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Citrix NetScaler SSL VPN アプライアンスと Azure MFA を RADIUS で統合します |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>Juniper/Pulse Secure SSL VPN アプライアンスと Azure MFA Server
Azure MFA Server と Juniper/Pulse Secure SSL VPN アプライアンスを統合すると、Juniper/Pulse Secure SSL VPN のログインとポータル アクセスのセキュリティが強化されます。  LDAP または RADIUS プロトコルのいずれかを使用できます。  次のいずれかを選択し、詳しい段階的構成ガイドをダウンロードしてください。

| 構成ガイド | 説明 |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN と Azure MFA の構成 (LDAP の場合)](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Juniper/Pulse Secure SSL VPN と Azure MFA アプライアンスを LDAP で統合します |
| [Juniper/Pulse Secure SSL VPN と Azure MFA の構成 (RADIUS の場合)](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Juniper/Pulse Secure SSL VPN アプライアンスと Azure MFA を RADIUS で統合します |

## <a name="next-steps"></a>次のステップ

- [Azure Multi-Factor Authentication の NPS 拡張機能を使用して既存の認証インフラストラクチャを強化する](howto-mfa-nps-extension.md)

- [Azure Multi-Factor Authentication 設定の構成](howto-mfa-mfasettings.md)
