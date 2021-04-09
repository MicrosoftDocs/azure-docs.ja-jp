---
title: Azure AD 参加済みデバイス上でオンプレミス リソースへの SSO が機能するしくみ | Microsoft Docs
description: ハイブリッド Azure Active Directory 参加済みデバイスを構成し、SSO 体験を拡張する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: da22a4e5e9ab13ec18347e58bea6cfc5f45333de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98630702"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>Azure AD 参加済みデバイス上でオンプレミス リソースへの SSO が機能するしくみ

Azure Active Directory (Azure AD) に参加しているデバイスによって、ご利用のテナントのクラウド アプリへのシングル サインオン (SSO) エクスペリエンスが提供されることは、おそらく驚くことではありません。 ご利用の環境にオンプレミスの Active Directory (AD) がある場合は、Azure AD 参加済みデバイス上の SSO エクスペリエンスをオンプレミスの AD に依存するリソースとアプリケーションにも取得できます。 

この記事では、この動作のしくみについて説明します。

## <a name="prerequisites"></a>前提条件

オンプレミスの SSO には、オンプレミスの AD DS ドメイン コントローラーとの見通し内通信が必要です。 Azure AD 参加済みデバイスが組織のネットワークに接続されていない場合は、VPN または他のネットワーク インフラストラクチャが必要です。 

## <a name="how-it-works"></a>しくみ 

ユーザーは Azure AD 参加済みデバイスを使用して、ご利用の環境内のクラウド アプリへの SSO エクスペリエンスを既に手に入れています。 ご利用の環境に Azure AD とオンプレミス AD がある場合は、SSO エクスペリエンスの範囲をオンプレミスの業種 (LOB) アプリ、ファイル共有、およびプリンターにまで拡張することができます。

Azure AD 参加済みデバイスには、オンプレミス AD 環境についての情報はありません (その環境に参加していないため)。 ただし、Azure AD Connect を使用して、ご利用のオンプレミス AD に関する追加情報をこれらのデバイスに提供することができます。

Azure AD とオンプレミス AD の両方を使用するハイブリッド環境がある場合は、オンプレミスの ID 情報をクラウドに同期するために、Azure AD Connect を既にデプロイ済みである可能性があります。 同期プロセスの一部として、Azure AD Connect はオンプレミス ユーザーとドメイン情報を Azure AD に同期します。 ハイブリッド環境においてユーザーが Azure AD 参加済みデバイスにサインインしたとき:

1. Azure AD は、ユーザーのオンプレミス ドメインの詳細を[プライマリ更新トークン](concept-primary-refresh-token.md)と共にデバイスに返送します。
1. ローカル セキュリティ機関 (LSA) サービスによって、デバイス上の Kerberos および NTLM 認証が有効になります。

>[!NOTE]
> Windows Hello for Business では、Azure AD 参加済みデバイスからのオンプレミスの SSO を有効にするために、追加の構成が必要です。 詳細については、「[Configure Azure AD joined devices for On-premises Single-Sign On using Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base)」 (Windows Hello for Business を使用してオンプレミス シングル サインオン用に Azure AD 参加済みデバイスを構成する) を参照してください。 

ユーザーのオンプレミス環境で Kerberos または NTLM を要求しているリソースへのアクセスが試行されると、デバイスは次のようになります。

1. ユーザーを認証するために、見つかった DC にオンプレミス ドメインの情報とユーザーの資格情報を送信します。
1. オンプレミスのリソースまたはアプリケーションがサポートするプロトコルに基づいて、Kerberos の[チケット発行許諾チケット (TGT)](/windows/desktop/secauthn/ticket-granting-tickets) または NTLM トークンを受信します。 ドメインの Kerberos TGT または NTLM トークンの取得の試行が失敗すると (関連する DCLocator タイムアウトにより遅延が発生する可能性があります)、資格情報マネージャー エントリが試行されるか、ターゲット リソースの資格情報を要求する認証ポップアップをユーザーが受信する場合があります。

**Windows 統合認証** の対象として構成されているすべてのアプリでは、ユーザーからのアクセスが試みられたときに、SSO がシームレスに適用されます。

## <a name="what-you-get"></a>取得内容

SSO を使用すると、Azure AD 参加済みデバイスで次のことができます。 

- AD のメンバー サーバー上の UNC パスへのアクセス
- Windows 統合セキュリティ用に構成された AD メンバーである Web サーバーへのアクセス 

Windows デバイスからオンプレミス AD を管理する場合は、[Windows 10 用リモート サーバー管理ツール](https://www.microsoft.com/download/details.aspx?id=45520)をインストールします。

使用できるもの:

- すべての AD オブジェクトを管理するための、Active Directory ユーザーとコンピューター (ADUC) スナップイン。 ただし、手動で接続するドメインを指定する必要があります。
- AD 参加済み DHCP サーバーを管理するための、DHCP スナップイン。 ただし、DHCP サーバーの名前またはアドレスを指定する必要があります。
 
## <a name="what-you-should-know"></a>知っておくべきこと

必要なドメインについてのデータが確実に同期されるように、Azure AD Connect において[ドメインベースのフィルター処理](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering)を調整することが必要になる場合があります。

Azure AD 参加済みデバイス上に AD 内のコンピューター オブジェクトがないため、Active Directory のコンピューター認証に依存するアプリとリソースは機能しません。 

Azure AD 参加済みデバイス上でファイルを他のユーザーと共有することはできません。

## <a name="next-steps"></a>次のステップ

詳細については、「[Azure Active Directory のデバイス管理とは](overview.md)」を参照してください。 
