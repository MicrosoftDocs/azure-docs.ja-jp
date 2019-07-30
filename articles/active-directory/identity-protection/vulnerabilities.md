---
title: Azure Active Directory Identity Protection で検出される脆弱性
description: Azure Active Directory Identity Protection で検出される脆弱性の概要
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 446e2ef33b714afe06f24f3dbfc46bc6a42790b8
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335156"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection で検出される脆弱性

脆弱性は、攻撃者によって悪用される可能性のある環境内の弱点です。 管理者の方には、これらの脆弱性に対処して組織のセキュリティ対策を強化することをお勧めします。

![Identity Protection で報告される脆弱性](./media/vulnerabilities/identity-protection-vulnerabilities.png)

次のセクションでは、Identity Protection で報告される脆弱性の概要を示します。

## <a name="multi-factor-authentication-registration-not-configured"></a>未構成の多要素認証登録

この脆弱性は、組織内の Azure Multi-Factor Authentication のデプロイを評価するのに役立ちます。

Azure Multi-Factor Authentication は、ユーザー認証に対して第 2 のセキュリティ層を提供します。 シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護することが可能です。 Azure Multi-Factor Authentication には、次のような使いやすい検証オプションが用意されています。

* 音声通話
* テキスト メッセージ
* モバイル アプリの通知
* OTP 確認コード

ユーザーのサインインに対して Azure Multi-Factor Authentication を要求することをお勧めします。多要素認証は、Identity Protection で使用可能なリスクに基づく条件付きアクセス ポリシーにおいて重要な役割を果たします。

詳細については、「[Azure Multi-Factor Authentication とは](../authentication/multi-factor-authentication.md)」を参照してください

## <a name="unmanaged-cloud-apps"></a>管理されていないクラウド アプリ

この脆弱性は、組織内にある管理されていないクラウド アプリの識別に役立ちます。

IT スタッフは、多くの場合、組織内のすべてのクラウド アプリケーションを認識していません。 管理者が企業データへの不正アクセスを心配している理由は容易に理解できます。データの漏洩やその他のセキュリティ リスクが発生するおそれがあるからです。

Cloud Discovery をデプロイしてアンマネージド クラウド アプリケーションを検出し、Azure Active Directory を使用してこれらのアプリケーションを管理することをお勧めします。

詳細については、「[Cloud Discovery](/cloud-app-security/set-up-cloud-discovery)」を参照してください。

## <a name="security-alerts-from-privileged-identity-management"></a>Privileged Identity Management からのセキュリティ アラート

この脆弱性は、組織内の特権 ID に関するアラートを検出して解決するのに役立ちます。  

ユーザーが特権操作を実行できるよう、組織はユーザーに Azure AD、Azure や Office 365 のリソース、他の SaaS アプリへの一時的または永続的な特権アクセスを付与することが必要になる場合があります。 このような特権ユーザーにより組織の攻撃対象領域が増えます。 この脆弱性は、不要な特権アクセスを持つユーザーを特定し、それらがもたらすリスクを軽減または除去するための適切なアクションを実施するのに役立ちます。

組織で Azure AD Privileged Identity Management を使用して、Azure AD および他の Microsoft オンライン サービス (Office 365、Microsoft Intune など) の特権 ID を管理、制御、および監視することをお勧めします。

詳しくは、[Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) に関するページを参照してください。

## <a name="see-also"></a>関連項目

[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
