---
title: Azure Active Directory でデバイスの状態を使用して回復性を強化する
description: アーキテクトと IT 管理者を対象とした、デバイスの状態を使用して回復性を強化するためのガイド
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d476be7a417cfc31cca76d3409074aaaa281a56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724612"
---
# <a name="build-resilience-with-device-states"></a>デバイスの状態を使用して回復性を強化する

管理者は、Azure AD で[デバイスの状態](../devices/overview.md)を有効にすることで、デバイスの状態に基づいてアプリケーションへのアクセスを制御する[条件付きアクセス ポリシー](../conditional-access/overview.md)を作成できます。 デバイスはリソースにアクセスするための強力な認証要件を満たしているために、追加の MFA 認証要求が削減され、回復性が向上するというメリットが付加されます。 

次のフロー チャートは、デバイスの状態を有効にするように Azure AD でデバイスをオンボードするさまざまな方法を示しています。 組織ではこのうちの複数を使用できます。

![デバイスの状態を選択するためのフロー チャート](./media/resilience-with-device-states/admin-resilience-devices.png)

[デバイスの状態](../devices/overview.md)を使用する場合は、ほとんどのケースでユーザーが[プライマリ更新トークン](../devices/concept-primary-refresh-token.md) (PRT) を通じてリソースにシングル サインオンすることになります。 PRT は、ユーザーとデバイスに関する要求を含んでおり、デバイスからアプリケーションにアクセスするための認証トークンを取得するために使用されます。 PRT は 14 日間有効であり、ユーザーがデバイスをアクティブに使用している限り継続的に更新されることで、回復性があるユーザーエクスペリエンスを提供します。 また、PRT は複数の方法で多要素認証 (MFA) 要求を受けることもできます。 詳細については、「[PRT が MFA 要求を受けるのはいつですか?](../devices/concept-primary-refresh-token.md)」を参照してください。

## <a name="how-do-device-states-help"></a>デバイスの状態がもたらすメリット

アプリケーションへのアクセスを要求するために PRT が使用されると、デバイス、セッション、MFA に関するその要求は Azure AD によって信頼されます。 管理者がデバイスベースのコントロールまたは多要素認証コントロールを必要とするポリシーを作成すると、多要素認証を試行することなくデバイスの状態によってそのポリシー要件を満たすことができます。 同じデバイスで多要素認証プロンプトが追加で表示されることはありません。 これにより、Azure MFA サービスや、その依存関係 (ローカル通信プロバイダーなど) の中断に対する回復性が向上します。

## <a name="how-do-i-implement-device-states"></a>デバイスの状態を実装する方法

* 会社が所有する Windows デバイスで[ハイブリッド Azure AD 参加](../devices/hybrid-azuread-join-plan.md)および [Azure AD 参加](../devices/azureadjoin-plan.md)を有効にし、可能であれば参加するように要求します。 可能でない場合は登録を要求します。

  組織に古いバージョンの Windows がある場合は、Windows 10 を使用するようにそれらのデバイスをアップグレードします。

* [Microsoft Edge](/deployedge/microsoft-edge-security-identity) または Google Chrome のいずれかで PRT を使用した Web アプリケーションへのシームレスな SSO を可能にする[サポート対象の](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)[拡張機能](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb)を使用するようにユーザーのブラウザー アクセスを標準化します。

* 個人または会社が所有する iOS デバイスと Android デバイスでは、[Microsoft Authenticator アプリ](../user-help/user-help-auth-app-overview.md)をデプロイします。 Microsoft Authenticator アプリは、多要素認証機能とパスワードレス サインイン機能に加えて、エンドユーザーに対する認証プロンプトが少ない[ブローカー認証](../develop/msal-android-single-sign-on.md)を使用して、ネイティブ アプリケーション全体でシングル サインオンを有効にします。

* 個人または会社が所有する iOS デバイスと Android デバイスでは、[モバイル アプリケーション管理](/mem/intune/apps/app-management)を使用して、少ない認証要求で会社のリソースに安全にアクセスできるようにします。 

* [Apple デバイス用の Microsoft Enterprise SSO プラグイン (プレビュー) を使用します](../develop/apple-sso-plugin.md)。 これは、デバイスを登録するとともに、ブラウザーとネイティブ Azure AD アプリケーション全体で SSO を実現します。 

## <a name="next-steps"></a>次のステップ
管理者とアーキテクト向けの回復性に関するリソース
 
* [資格情報管理を使用して回復性を強化する](resilience-in-credentials.md)

* [継続的アクセス評価 (CAE) を使用して回復性を強化する](resilience-with-continuous-access-evaluation.md)

* [外部ユーザー認証の回復性を強化する](resilience-b2b-authentication.md)

* [ハイブリッド認証で回復性を強化する](resilience-in-hybrid.md)

* [アプリケーション プロキシを使用したアプリケーション アクセスで回復性を強化する](resilience-on-premises-access.md)


開発者向けの回復性に関するリソース

* [アプリケーションで IAM の回復性を強化する](resilience-app-development-overview.md)

* [CIAM システムで回復性を強化する](resilience-b2c.md)