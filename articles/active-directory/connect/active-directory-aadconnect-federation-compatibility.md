---
title: "Azure AD のフェデレーション互換性リスト"
description: "このページには、シングル サインオンの実装に使用できる Microsoft 以外の ID プロバイダーが記載されています。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: e68d39c4591341c706fcd89a584768cba07de7e1
ms.lasthandoff: 03/22/2017


---
# <a name="azure-ad-federation-compatibility-list"></a>Azure AD のフェデレーション互換性リスト
Azure Active Directory は、Microsoft 以外のソリューションを必要とすることなくハイブリッド実装とクラウド専用の実装を実現するために、Office 365 などの Microsoft オンライン サービスに対してシングル サインオンとアプリケーション アクセスのセキュリティ強化を提供します。 Office 365 は、Microsoft のオンライン サービスのほとんどと同様に、ディレクトリ サービス、認証、承認のために Azure Active Directory と統合されます。 また、Azure Active Directory では、多数の SaaS アプリケーションやオンプレミスの Web アプリケーションにもシングル サインオンを提供しています。 サポートされている SaaS アプリケーションについては、Azure Active Directory アプリケーション ギャラリーを参照してください。

Microsoft 以外のフェデレーション ソリューションに投資している組織のために、このトピックでは、次の "Azure Active Directory フェデレーション互換性リスト" に記載されている Microsoft 以外の ID プロバイダーを使用して、Microsoft オンライン サービスを使用する Windows Server Active Directory ユーザーのシングル サインオンを構成する場合のガイダンスを示しています。 

![](./media/active-directory-aadconnect-federation-compatibility/oxford2.jpg)   
サードパーティの [Oxford Computer Group](http://oxfordcomputergroup.com/) は Microsoft に代わり、Microsoft 以外の ID プロバイダーを使用して、Azure Active Directory でよく見られる一連のユース ケースに対して以下のシングル サインオン エクスペリエンスをテストしました。

以下のリストにあるサードパーティ ID プロバイダーの入手方法については、 [idp@oxfordcomputergroup.com](mailto:idp@oxfordcomputergroup.com)から Oxford Computer Group に問い合わせてください。

> [!IMPORTANT]
> Oxford Computer Group がテストしたのは、これらのシングル サインオン シナリオのフェデレーション機能のみです。 Oxford Computer Group は、これらのシングル サインオン シナリオで、同期、2 要素認証などのコンポーネントのテストは実施していません。
> 
> このプログラムでは、UPN への代替 ID によるサインインの使用もテストしません。
> 
> 

* [Azure Active Directory](#azure-active-directory)
* [Optimal IDM Virtual Identity Server Federation Services](#optimal-idm-virtual-identity-server-federation-services) 
* [PingFederate 6.11](#pingfederate-611) 
* [PingFederate 7.2](#pingfederate-72) 
* [PingFederate 8.x](#pingfederate-8x)
* [Centrify](#centrify) 
* [IBM Tivoli Federated Identity Manager 6.2.2](#ibm-tivoli-federated-identity-manager-622) 
* [SecureAuth IdP 7.2.0](#secureauth-idp-720) 
* [CA SiteMinder 12.52](#ca-siteminder-1252-sp1-cumulative-release-4) 
* [RadiantOne CFS 3.0](#radiantone-cfs-30) 
* [Okta](#okta) 
* [OneLogin](#onelogin) 
* [NetIQ Access Manager 4.0.1](#netiq-access-manager-401) 
* [BIG-IP と BIG-IP Access Policy Manager Version 11.3x ～ 11.6x](#big-ip-with-access-policy-manager-big-ip-ver-113x--116x) 
* [VMware  Workspace Portal version 2.1](#vmware--workspace-portal-version-21) 
* [Sign&go 5.3](#signgo-53) 
* [IceWall Federation Version 3.0](#icewall-federation-version-30) 
* [CA Secure Cloud](#ca-secure-cloud) 
* [Dell One Identity Cloud Access Manager v7.1](#dell-one-identity-cloud-access-manager-v71) 
* [AuthAnvil Single Sign On 4.5](#authanvil-single-sign-on-45)
* [Sailpoint IdentityNow](#sailpoint-identitynow)
* [NetIQ Access Manager 4.x](#netiq-access-manager-4x) 

> [!IMPORTANT]
> これらはサード パーティ製品であるため、Microsoft では、これらの ID プロバイダーに関するデプロイ、構成、トラブルシューティング、ベスト プラクティスなどの問題や質問をサポートしていません。 これらの ID プロバイダーに関するサポートと質問については、サポートしているサード パーティに直接お問い合わせください。
> 
> これらのサード パーティの ID プロバイダーについては、WS-Federation プロトコルと WS-Trust プロトコルのみを使用して Microsoft クラウド サービスとの相互運用性のテストが済んでいます。 SAML プロトコルを使用したテストは実施されていません。
> 
> 

## <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory は、オンプレミスの Active Directory とのフェデレーションを介してユーザーを認証するか、オンプレミスのフェデレーション サーバーとのフェデレーションを介さずにパスワード同期を使用してユーザーを認証することができます。 

このサインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。 

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |なし |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |
| Office 2016 などの ADAL を使用した最新のアプリケーション |サポートされています |なし |

AD FS による Azure Active Directory の使用方法の詳細については、[Active Directory フェデレーション サービス (ADFS)](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) に関するページを参照してください。

パスワード同期による Azure Active Directory の使用方法の詳細については、 [Azure AD Connect](active-directory-aadconnect.md)に関するページを参照してください。

## <a name="optimal-idm-virtual-identity-server-federation-services"></a>Optimal IDM Virtual Identity Server Federation Services
Optimal IDM Virtual Identity Server Federation Services では、お客様のオンプレミスの Active Directory に属するユーザーを認証できます。

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |統合 Windows 認証 |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |クライアント アクセス ポリシーの詳細については、「 [Limiting Access to Office 365 Services Based on the Location of the Client (クライアントの場所に基づいた Office 365 サービスへのアクセスの制限)](https://technet.microsoft.com/library/hh526961.aspx) |

## <a name="pingfederate-611"></a>PingFederate 6.11
PingFederate 6.11 は、広く使用されている WS-Federation ID 標準を実装して、シングル サインオンと属性交換フレームワークを提供しています。

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |なし (これより前のバージョンは 6.11 にアップグレードする必要があります) |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

Active Directory ユーザーにシングル サインオン エクスペリエンスを提供するように PingFederate でこの STS を構成する手順については、 [こちら](http://go.microsoft.com/fwlink/?LinkID=266321)

## <a name="pingfederate-72"></a>PingFederate 7.2
PingFederate 7.2 は、広く使用されている WS-Federation/WS-Trust ID 標準を実装して、シングル サインオンと属性交換フレームワークを提供しています。

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |なし |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

Active Directory ユーザーにシングル サインオン エクスペリエンスを提供するように PingFederate でこの STS を構成する手順については、 [こちら](http://documentation.pingidentity.com/display/PF72/PingFederate+7.2)

## <a name="pingfederate-8x"></a>PingFederate 8.x
PingFederate 8.x は、広く使用されている WS-Federation/WS-Trust ID 標準を実装して、シングル サインオンと属性交換フレームワークを提供しています。

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |なし |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

Active Directory ユーザーにシングル サインオン エクスペリエンスを提供するように PingFederate でこの STS を構成する手順については、 [こちら](http://documentation.pingidentity.com/display/PFS/SSO+to+Office+365+Introduction)

## <a name="centrify"></a>Centrify
Centrify では、オンプレミスのフェデレーション サーバーをホストする必要なく Office 365 のフェデレーション シングル サインオン エクスペリエンスを提供します。

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |なし |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |クライアント アクセス制御はサポートされていません |

Centrify の詳細については、 [こちら](http://www.centrify.com/cloud/apps/single-sign-on-for-office-365.asp)|

## <a name="ibm-tivoli-federated-identity-manager-622"></a>IBM Tivoli Federated Identity Manager 6.2.2
IBM Security Access Manager for Microsoft Applications 1.4 を使用する IBM Tivoli Federated Identity Manager 6.2.2 は、広く使用されている WS-Federation/WS-Trust ID 標準を実装して、シングル サインオンと属性交換フレームワークを提供しています。

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。 

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |なし |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

IBM Tivoli Federated Identity Manager の詳細については、「 [IBM Security Access Manager for Microsoft Applications](http://www-01.ibm.com/support/docview.wss?uid=swg24029517)

## <a name="secureauth-idp-720"></a>SecureAuth IdP 7.2.0
SecureAuth IdP 7.2.0 は、広く使用されている WS-Federation/WS-Trust ID 標準を実装して、シングル サインオン エクスペリエンスと属性交換フレームワークを提供しています。

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。 

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |なし |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

SecureAuth の詳細については、 [SecureAuth IdP](http://go.microsoft.com/?linkid=9845293)に関するページを参照してください。

## <a name="ca-siteminder-1252-sp1-cumulative-release-4"></a>CA SiteMinder 12.52 SP1 Cumulative Release 4
CA SiteMinder Federation 12.52 は、広く使用されている WS-Federation/WS-Trust ID 標準を実装して、シングル サインオンと属性交換フレームワークを提供しています。

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。 

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |なし |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

CA SiteMinder の詳細については、 [CA SiteMinder Federation](http://www.ca.com/us/products/ca-single-sign-on.html) 

## <a name="radiantone-cfs-30"></a>RadiantOne CFS 3.0
RadiantOne Cloud Federation Service (CFS) 3.0 は、広く使用されている WS-Federation/WS-Trust ID 標準を実装して、シングル サインオンと属性交換フレームワークを提供しています。

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。 

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |統合 Windows 認証 |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

RadiantOne CFS の詳細については、 [RadiantOne CFS](http://www.radiantlogic.com/products/radiantone-cfs/)

## <a name="okta"></a>Okta
Okta は、広く使用されている WS-Federation/WS-Trust ID 標準を実装して、シングル サインオンと属性交換フレームワークを提供しています。

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。 

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |統合 Windows 認証では、追加の Web サーバーと Okta アプリケーションのセットアップが必要です。 |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |統合 Windows 認証 |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

Okta の詳細については、 [Okta](https://www.okta.com/)

## <a name="onelogin"></a>OneLogin
2014 年 5 月にテストされた OneLogin は、広く使用されている WS-Federation/WS-Trust ID 標準を実装して、シングル サインオンと属性交換フレームワークを提供しています。

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。 

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |統合 Windows 認証 |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |統合 Windows 認証 |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

OneLogin の詳細については、 [OneLogin](https://www.onelogin.com/)

## <a name="netiq-access-manager-401"></a>NetIQ Access Manager 4.0.1
NetIQ Access Manager 4.0.1 は、広く使用されている WS-Federation/WS-Trust ID 標準を実装して、シングル サインオンと属性交換フレームワークを提供しています。

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |*Kerberos Contract がサポートされています |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |統合 Windows 認証はサポートされていません |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

*NetIQ は、Kerberos Contract の構成を介した Kerberos 認証をサポートしています。  この構成に関するサポートについては、NetIQ 社に問い合わせるか、セットアップ ガイドを参照してください。 NetIQ Access Manager の詳細については、 [NetIQ Access Manager](https://www.netiq.com/documentation/netiqaccessmanager4/identityserverhelp/data/b12iqp0m.html)

## <a name="big-ip-with-access-policy-manager-big-ip-ver-113x--116x"></a>BIG-IP と BIG-IP Access Policy Manager Version  11.3x ～ 11.6x
BIG-IP with Access Policy Manager、(APM) BIG-IP Version  11.3x ～ 11.6x は、広く使用されている SAML ID 標準を実装して、シングル サインオン エクスペリエンスと属性交換フレームワークを提供しています。

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。 

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされていません |サポートされていません |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

BIG-IP Access Policy Manager の詳細については、 [BIG-IP Access Policy Manager](https://f5.com/products/modules/access-policy-manager) 

Active Directory ユーザーにシングル サインオン エクスペリエンスを提供するように BIG-IP Access Policy Manager でこの STS を構成する手順については、 [こちら](http://www.f5.com/pdf/deployment-guides/microsoft-office-365-idp-dg.pdf)

## <a name="vmware--workspace-portal-version-21"></a>VMware  Workspace Portal version 2.1
VMware Workspace Portal Version 2.1 は、広く使用されている WS-Federation/WS-Trust ID 標準を実装して、シングル サインオンと属性交換フレームワークを提供しています。

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |統合 Windows 認証はサポートされていません |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |統合 Windows 認証はサポートされていません |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

VMware Workspace Portal Version 2.1 の詳細については、[こちら](http://pubs.vmware.com/workspace-portal-21/topic/com.vmware.ICbase/PDF/workspace-portal-21-resource.pdf)から PDF をダウンロードしてください。

## <a name="signgo-53"></a>Sign&go 5.3
Sign&go 5.3 は、広く使用されている WS-Federation/WS-Trust ID 標準を実装して、シングル サインオンと属性交換フレームワークを提供しています。

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |Kerberos Contract がサポートされています |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |なし |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

Sign&go 5.3 は、Kerberos Contract の構成を介した Kerberos 認証をサポートしています。  この構成に関するサポートについては、Ilex に問い合わせるか、 [こちら](http://www.ilex-international.com/docs/sign&go_wsfederation_en.pdf)

## <a name="icewall-federation-version-30"></a>IceWall Federation Version 3.0
IceWall Federation Version 3.0 は、広く使用されている WS-Federation/WS-Trust ID 標準を実装して、シングル サインオンと属性交換フレームワークを提供しています。

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |統合 Windows 認証はサポートされていません |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |統合 Windows 認証はサポートされていません |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

IceWall Federation の詳細については、[こちら](http://h50146.www5.hp.com/products/software/security/icewall/eng/federation/)と[こちら](http://h50146.www5.hp.com/products/software/security/icewall/federation/office365.html)を参照してください。

## <a name="ca-secure-cloud"></a>CA Secure Cloud
CA Secure Cloud は、広く使用されている WS-Federation/WS-Trust ID 標準を実装して、シングル サインオンと属性交換フレームワークを提供しています。

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |統合 Windows 認証はサポートされていません |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |統合 Windows 認証はサポートされていません |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

CA Secure Cloud の詳細については、 [CA Secure Cloud](http://www.ca.com/us/products/security-as-a-service.aspx)

## <a name="dell-one-identity-cloud-access-manager-v71"></a>Dell One Identity Cloud Access Manager v7.1
Dell One Identity Cloud Access Manager は、広く使用されている WS-Federation/WS-Trust ID 標準を実装して、シングル サインオンと属性交換フレームワークを提供しています。

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |なし |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

Dell One Identity Cloud Access Manager の詳細については、 [Dell One Identity の Cloud Access Manager](http://software.dell.com/products/cloud-access-manager)

 Office 365 ユーザーにシングル サインオン エクスペリエンスを提供するようにこの STS を構成する手順については、 [Office 365 ユーザーの構成](http://documents.software.dell.com/dell-one-identity-cloud-access-manager/7.1/how-to-configure-microsoft-office-365) 

## <a name="authanvil-single-sign-on-45"></a>AuthAnvil Single Sign On 4.5
AuthAnvil Single Sign On 4.5 は、広く使用されている WS-Federation/WS-Trust ID 標準を実装して、シングル サインオンと属性交換フレームワークを提供しています。

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |統合 Windows 認証はサポートされていません |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |統合 Windows 認証はサポートされていません |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

詳細については、 [AuthAnvil のシングル サインオン](https://help.scorpionsoft.com/entries/26538603-How-can-I-Configure-Single-Sign-On-for-Office-365-)

## <a name="sailpoint-identitynow"></a>Sailpoint IdentityNow
Sailpoint IdentityNow は、広く使用されている WS-Federation/WS-Trust ID 標準を実装して、シングル サインオンと属性交換フレームワークを提供しています。

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |統合 Windows 認証はサポートされていません |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |統合 Windows 認証はサポートされていません |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

詳細については、「[Sailpoint IdentityNow](https://www.sailpoint.com/idaas-identity-as-a-service-identitynow/)」を参照してください。

## <a name="netiq-access-manager-4x"></a>NetIQ Access Manager 4.x
NetIQ Access Manager は、広く使用されている WS-Federation/WS-Trust ID 標準を実装して、シングル サインオンと属性交換フレームワークを提供しています。

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし|
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |なし|
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

詳しくは、「[NetIQ Access Manager](https://www.netiq.com/documentation/access-manager-43/admin/data/b65ogn0.html#b12iqp0m)」をご覧ください


