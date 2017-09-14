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
ms.date: 07/13/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: 6b0fd60e79308b75c3a2797ff25065b8a500a3b4
ms.contentlocale: ja-jp
ms.lasthandoff: 06/10/2017

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
* [AuthAnvil Single Sign On 4.5](#authanvil-single-sign-on-45)
* [BIG-IP と BIG-IP Access Policy Manager Version 11.3x ～ 11.6x](#big-ip-with-access-policy-manager-big-ip-ver-113x--116x) 
* [BitGlass](#bitglass)
* [CA Secure Cloud](#ca-secure-cloud) 
* [CA SiteMinder 12.52](#ca-siteminder-1252-sp1-cumulative-release-4) 
* [Centrify](#centrify) 
* [Dell One Identity Cloud Access Manager v7.1](#dell-one-identity-cloud-access-manager-v71) 
* [DigitalPersona Composite Authentication](#digitalpersona-composite-authentication)
* [IBM Tivoli Federated Identity Manager 6.2.2](#ibm-tivoli-federated-identity-manager-622) 
* [IceWall Federation Version 3.0](#icewall-federation-version-30) 
* [Memority](#memority)
* [NetIQ Access Manager 4.x](#netiq-access-manager-4x) 
* [Okta](#okta) 
* [OneLogin](#onelogin) 
* [Optimal IDM Virtual Identity Server Federation Services](#optimal-idm-virtual-identity-server-federation-services) 
* [PingFederate 6.11、7.2、8.x](#pingfederate-611-72-8x)
* [RadiantOne CFS 3.0](#radiantone-cfs-30) 
* [Sailpoint IdentityNow](#sailpoint-identitynow)
* [SecureAuth IdP 7.2.0](#secureauth-idp-720) 
* [Sign&go 5.3](#signgo-53) 
* [SoftBank Technology Online Service Gate](#softbank)
* [VMware Workspace One](#vmware-workspace-one)



> [!IMPORTANT]
> これらはサード パーティ製品であるため、Microsoft では、これらの ID プロバイダーに関するデプロイ、構成、トラブルシューティング、ベスト プラクティスなどの問題や質問をサポートしていません。 これらの ID プロバイダーに関するサポートと質問については、サポートしているサード パーティに直接お問い合わせください。
> 
> これらのサード パーティの ID プロバイダーについては、WS-Federation プロトコルと WS-Trust プロトコルのみを使用して Microsoft クラウド サービスとの相互運用性のテストが済んでいます。 SAML プロトコルを使用したテストは実施されていません。
> 


## <a name="azure-active-directory"></a>Azure Active Directory

このサインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。 

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |なし |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |
| Office 2016 などの ADAL を使用した最新のアプリケーション |サポートされています |なし |

AD FS と共に Azure Active Directory を使用する方法の詳細については、[Active Directory フェデレーション サービス (AD FS)](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) に関するページを参照してください。

パスワード同期による Azure Active Directory の使用方法の詳細については、 [Azure AD Connect](active-directory-aadconnect.md)に関するページを参照してください。

## <a name="authanvil-single-sign-on-45"></a>AuthAnvil Single Sign On 4.5

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |統合 Windows 認証はサポートされていません |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |統合 Windows 認証はサポートされていません |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

詳細については、[AuthAnvil のシングル サインオン](https://help.scorpionsoft.com/entries/26538603-How-can-I-Configure-Single-Sign-On-for-Office-365-)に関する説明を参照してください。


## <a name="big-ip-with-access-policy-manager-big-ip-ver-113x--116x"></a>BIG-IP と BIG-IP Access Policy Manager Version  11.3x ～ 11.6x

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。 

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされていません |サポートされていません |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

BIG-IP Access Policy Manager の詳細については、 [BIG-IP Access Policy Manager](https://f5.com/products/modules/access-policy-manager) 

Active Directory ユーザーにシングル サインオン エクスペリエンスを提供するように BIG-IP Access Policy Manager でこの STS を構成する手順については、[BIG-IP](http://www.f5.com/pdf/deployment-guides/microsoft-office-365-idp-dg.pdf) に関する PDF をダウンロードしてください。

## <a name="bitglass"></a>BitGlass

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |統合 Windows 認証はサポートされていません |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |統合 Windows 認証はサポートされていません |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

BitGlass の詳細については、[BitGlass](http://www.bitglass.com) のページを参照してください。

## <a name="ca-secure-cloud"></a>CA Secure Cloud

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |統合 Windows 認証はサポートされていません |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |統合 Windows 認証はサポートされていません |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

CA Secure Cloud の詳細については、[CA Secure Cloud](http://www.ca.com/us/products/security-as-a-service.aspx) のページを参照してください。

## <a name="ca-siteminder-1252-sp1-cumulative-release-4"></a>CA SiteMinder 12.52 SP1 Cumulative Release 4

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。 

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |なし |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

CA SiteMinder の詳細については、「[CA SiteMinder Federation](http://www.ca.com/us/products/ca-single-sign-on.html)」 (CA SiteMinder フェデレーション) を参照してください。 

## <a name="centrify"></a>Centrify

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |なし |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |クライアント アクセス制御はサポートされていません |

Centrify の詳細については、[Centrify](http://www.centrify.com/cloud/apps/single-sign-on-for-office-365.asp) のページを参照してください。

## <a name="dell-one-identity-cloud-access-manager-v71"></a>Dell One Identity Cloud Access Manager v7.1

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |なし |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

Dell One Identity Cloud Access Manager の詳細については、「[Dell One Identity Cloud Access Manager](http://software.dell.com/products/cloud-access-manager)」 (Dell One Identity の Cloud Access Manager) を参照してください。

 Office 365 ユーザーにシングル サインオン エクスペリエンスを提供するようにこの STS を構成する手順については、「[Configure Office 365 Users](http://documents.software.dell.com/dell-one-identity-cloud-access-manager/7.1/how-to-configure-microsoft-office-365)」 (Office 365 ユーザーの構成) を参照してください。 

## <a name="digitalpersona-composite-authentication"></a>DigitalPersona Composite Authentication  

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |統合 Windows 認証はサポートされていません|
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |統合 Windows 認証はサポートされていません|
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

詳細については、[DigitalPersona Composite Authentication](http://www.crossmatch.com/uploadedFiles/Support/Reference_Material/DigitalPersona-Office-365-Deployment-Guide.pdf) に関する説明を参照してください。


## <a name="ibm-tivoli-federated-identity-manager-622"></a>IBM Tivoli Federated Identity Manager 6.2.2

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。 

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |なし |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

IBM Tivoli Federated Identity Manager の詳細については、「[IBM Security Access Manager for Microsoft Applications](http://www-01.ibm.com/support/docview.wss?uid=swg24029517)」を参照してください。

## <a name="icewall-federation-version-30"></a>IceWall Federation Version 3.0

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |統合 Windows 認証はサポートされていません |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |統合 Windows 認証はサポートされていません |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

IceWall Federation の詳細については、「[IceWall Federation Version 3.0](http://h50146.www5.hp.com/products/software/security/icewall/eng/federation/)」と「[IceWall Federation - Office 365 とのフェデレーション](http://h50146.www5.hp.com/products/software/security/icewall/federation/office365.html)」を参照してください。

## <a name="memority"></a>Memority

このサインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。 

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |なし |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

Memority の使用方法の詳細については、[Memority](http://www.memority.com) のページを参照してください。


## <a name="netiq-access-manager-4x"></a>NetIQ Access Manager 4.x

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし|
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |なし|
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

詳細については、「[NetIQ Access Manager](https://www.netiq.com/documentation/access-manager-43/admin/data/b65ogn0.html#b12iqp0m)」を参照してください。

## <a name="okta"></a>Okta

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。 

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |統合 Windows 認証では、追加の Web サーバーと Okta アプリケーションのセットアップが必要です。 |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |統合 Windows 認証 |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

Okta の詳細については、[Okta](https://www.okta.com/) のページを参照してください。

## <a name="onelogin"></a>OneLogin

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。 

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |統合 Windows 認証 |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |統合 Windows 認証 |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

OneLogin の詳細については、[OneLogin](https://www.onelogin.com/) のページを参照してください。

## <a name="optimal-idm-virtual-identity-server-federation-services"></a>Optimal IDM Virtual Identity Server Federation Services

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |統合 Windows 認証 |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |

クライアント アクセス ポリシーの詳細については、「[Limiting Access to Office 365 Services Based on the Location of the Client](https://technet.microsoft.com/library/hh526961.aspx)」 (クライアントの場所に基づいた Office 365 サービスへのアクセスの制限) を参照してください。





## <a name="pingfederate-611-72-8x"></a>PingFederate 6.11、7.2、8.x

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |なし |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

Active Directory ユーザーにシングル サインオン エクスペリエンスを提供するように PingFederate でこの STS を構成する手順については、次のいずれかをご覧ください。 

- [PingFederate 6.11](http://go.microsoft.com/fwlink/?LinkID=266321)
- [PingFederate 7.2](http://documentation.pingidentity.com/display/PF72/PingFederate+7.2)
- [PingFederate 8.x](http://documentation.pingidentity.com/display/PFS/SSO+to+Office+365+Introduction)

## <a name="radiantone-cfs-30"></a>RadiantOne CFS 3.0

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。 

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |統合 Windows 認証 |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

RadiantOne CFS の詳細については、[RadiantOne CFS](http://www.radiantlogic.com/products/radiantone-cfs/) のページを参照してください。

## <a name="sailpoint-identitynow"></a>Sailpoint IdentityNow

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |統合 Windows 認証はサポートされていません |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |統合 Windows 認証はサポートされていません |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

詳細については、[Sailpoint IdentityNow](https://www.sailpoint.com/idaas-identity-as-a-service-identitynow/) のページを参照してください。

## <a name="secureauth-idp-720"></a>SecureAuth IdP 7.2.0

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。 

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |なし |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |なし |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

SecureAuth の詳細については、 [SecureAuth IdP](http://go.microsoft.com/?linkid=9845293)に関するページを参照してください。














## <a name="signgo-53"></a>Sign&go 5.3

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |Kerberos Contract がサポートされています |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |なし |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

Sign&go 5.3 は、Kerberos Contract の構成を介した Kerberos 認証をサポートしています。  この構成に関するサポートについては、Ilex に問い合わせるか、「[Sign&go](http://www.ilex-international.com/docs/sign&go_wsfederation_en.pdf)」セットアップ ガイドを参照してください。

## <a name="softbank-technology-online-service-gate"></a>SoftBank Technology Online Service Gate

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |統合 Windows 認証はサポートされていません |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |統合 Windows 認証はサポートされていません |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

SoftBank Technology Online Service Gate の詳細については、[Softbank](https://www.softbanktech.jp/service/list/osg-pro-ent/) のページを参照してください。

## <a name="vmware-workspace-one"></a>VMware Workspace One

このシングル サインオン エクスペリエンスのシナリオにおけるサポート状況を次に示します。

| クライアント | サポート | 例外 |
| --- | --- | --- |
| Exchange Web Access や SharePoint Online などの Web ベースのクライアント |サポートされています |統合 Windows 認証はサポートされていません |
| Lync、Office サブスクリプション、CRM などのリッチ クライアント アプリケーション |サポートされています |統合 Windows 認証はサポートされていません |
| Outlook や ActiveSync などの電子メール リッチ クライアント |サポートされています |なし |

詳細については、[VMware の Workspace One](http://www.vmware.com/pdf/vidm-office365-saml.pdf) のページを参照してください。


