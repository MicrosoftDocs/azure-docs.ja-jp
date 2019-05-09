---
title: Azure AD 参加済みデバイス上でオンプレミス リソースへの SSO が機能するしくみ | Microsoft Docs
description: ハイブリッド Azure Active Directory 参加済みデバイスの構成方法について説明します。
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2018
ms.author: joflore
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45941de6a90a5824ebc1e5d31b18b68f5fd9d493
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520549"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>Azure AD 参加済みデバイス上でオンプレミス リソースへの SSO が機能するしくみ

Azure Active Directory (Azure AD) に参加しているデバイスによって、ご利用のテナントのクラウド アプリへのシングル サインオン (SSO) エクスペリエンスが提供されることは、おそらく驚くことではありません。 ご利用の環境にオンプレミスの Active Directory (AD) がある場合、それらのデバイスでの SSO エクスペリエンスをその AD に拡張できます。

この記事では、この動作のしくみについて説明します。

## <a name="how-it-works"></a>動作のしくみ 

1 つのユーザー名とパスワードを覚えておくだけでよいため、SSO によってリソースへのアクセスが簡略化され、ご利用の環境のセキュリティが向上します。 ユーザーは Azure AD 参加済みデバイスを使用して、ご利用の環境内のクラウド アプリへの SSO エクスペリエンスを既に手に入れています。 ご利用の環境内に Azure AD とオンプレミス AD がある場合は、SSO エクスペリエンスの範囲をオンプレミスの業種 (LOB) アプリ、ファイル共有、およびプリンターにまで拡張することをおそらく希望するでしょう。  


Azure AD 参加済みデバイスには、オンプレミス AD 環境についての情報はありません (その環境に参加していないため)。 ただし、Azure AD Connect を使用して、ご利用のオンプレミス AD に関する追加情報をこれらのデバイスに提供することができます。
Azure AD とオンプレミス AD の両方を使用している環境は、ハイブリッド環境とも呼ばれます。 ハイブリッド環境を使用している場合は、オンプレミスの ID 情報をクラウドに同期するために、Azure AD Connect を既にデプロイ済みである可能性があります。 同期プロセスの一部として、Azure AD Connect はオンプレミスのドメイン情報を Azure AD に同期します。 ハイブリッド環境においてユーザーが Azure AD 参加済みデバイスにサインインしたとき:

1. Azure AD からデバイスに、ユーザーがメンバーになっているオンプレミス ドメインの名前が送信されます。 

2. ローカル セキュリティ機関 (LSA) サービスによって、デバイス上の Kerberos 認証が有効になります。

ユーザーのオンプレミス ドメイン内にあるリソースへのアクセス試行時、デバイスでは次のことが行われます。

1. ドメイン情報を使用して、ドメイン コント ローラー (DC) を見つけます。 

2. ユーザーを認証するために、見つかった DC にオンプレミス ドメインの情報とユーザーの資格情報を送信します。

3. AD 参加済みリソースへのアクセスに使用する Kerberos の[チケット保証チケット (TGT)](https://docs.microsoft.com/windows/desktop/secauthn/ticket-granting-tickets) を受け取ります。

**Windows 統合認証**の対象として構成されているすべてのアプリでは、ユーザーからのアクセスが試みられたときに、SSO がシームレスに適用されます。  

Windows Hello for Business では、Azure AD 参加済みデバイスからのオンプレミスの SSO を有効にするために、追加の構成が必要です。 詳細については、「[Configure Azure AD joined devices for On-premises Single-Sign On using Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base)」 (Windows Hello for Business を使用してオンプレミス シングル サインオン用に Azure AD 参加済みデバイスを構成する) を参照してください。 

## <a name="what-you-get"></a>取得内容

SSO を使用すると、Azure AD 参加済みデバイスで次のことができます。 

- AD のメンバー サーバー上の UNC パスへのアクセス

- Windows 統合セキュリティ用に構成された AD メンバーである Web サーバーへのアクセス 



Windows デバイスからオンプレミス AD を管理する場合は、[Windows 10 用リモート サーバー管理ツール](https://www.microsoft.com/en-us/download/details.aspx?id=45520)をインストールします。

使用できるもの:

- すべての AD オブジェクトを管理するための、Active Directory ユーザーとコンピューター (ADUC) スナップイン。 ただし、手動で接続するドメインを指定する必要があります。

- AD 参加済み DHCP サーバーを管理するための、DHCP スナップイン。 ただし、DHCP サーバーの名前またはアドレスを指定する必要があります。

 
## <a name="what-you-should-know"></a>知っておくべきこと

必要なドメインについてのデータが確実に同期されるように、Azure AD Connect において[ドメインベースのフィルター処理](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering)を調整することが必要になる場合があります。

Azure AD 参加済みデバイス上に AD 内のコンピューター オブジェクトがないため、Active Directory のコンピューター認証に依存するアプリとリソースは機能しません。 

Azure AD 参加済みデバイス上でファイルを他のユーザーと共有することはできません。

## <a name="next-steps"></a>次の手順

詳細については、「[Azure Active Directory のデバイス管理とは](overview.md)」を参照してください。 
