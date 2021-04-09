---
title: Azure Active Directory を使用してセキュリティで保護された ID に迅速に対応する
description: Azure AD クラウドベースの ID を使用して脅威に迅速に対応する
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: davidspo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c4301a61a79ab2351c18af0c76cccc3d07dd202
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94836684"
---
# <a name="rapidly-respond-to-secure-identities-with-azure-ad"></a>Azure AD を使用してセキュリティで保護された ID に迅速に対応する

今日の世界では、worker をセキュリティで保護しようとするのが困難に見える場合があります。迅速に対応し、多数のサービスへのアクセスを迅速に提供する必要がある場合は特にそうです。 この記事は、所有するライセンスの種類に基づいて、Azure AD 機能をデプロイする順序を特定し、優先順位を付けることに役立てるため、実行するすべてのアクションの簡潔なリストを提供することを目的としています。 Azure AD には多数の機能が用意されており、ID に対して多くのセキュリティ層が用意されているため、関連性の高い機能に移動するのが困難な場合があります。 多くの組織が既にクラウドを使用しているか、クラウドへの移行を迅速に進めています。このドキュメントは、お客様の ID を保護することを第一に考えて、サービスを迅速にデプロイできるようにすることを目的としています。 

各表には、ユーザーへの影響を最小限に抑え、ユーザー エクスペリエンスを向上させると同時に、主要なセキュリティ攻撃 (侵害のリプレイ、フィッシング、パスワード スプレー) から管理者とユーザーの両方の ID を保護するため、セキュリティに関する一貫した推奨事項が説明されています。 

また、このガイダンスによって管理者は、セキュリティで保護された方法で SaaS とオンプレミス アプリケーションへのアクセスを構成することもでき、これをクラウドまたはハイブリッド (同期済み) ID に適用して、リモートまたはオフィスで作業しているユーザーに適用できます。

このチェックリストは、重要な推奨アクションを迅速に展開して、組織を直ちに保護するうえで役に立ちます。ここでは、以下の方法について説明されています。

- 資格情報を強化する。
- 攻撃の対象となる領域を減らす。
- 脅威への対応を自動化する。
- クラウド インテリジェンスを利用します。
- エンドユーザー セルフサービスを有効にします。

## <a name="prerequisites"></a>前提条件

このガイドでは、クラウドのみの ID またはハイブリッド ID が Azure AD で既に確立されていることを前提としています。 ID の種類を選択する方法については、「[Azure Active Directory ハイブリッド ID ソリューションの適切な認証方法を選択する](../hybrid/choose-ad-authn.md)」の記事を参照してください。 

## <a name="summary"></a>まとめ

セキュリティで保護された ID インフラストラクチャには多数の側面がありますが、このチェックリストでは、ユーザーがリモートで作業できるようにセキュリティで保護された安全な ID インフラストラクチャに焦点を当てています。 ID をセキュリティで保護することは、セキュリティ対策の一環にすぎず、データ、アプリケーション、デバイスの保護も考慮する必要があります。

### <a name="guidance-for-azure-ad-free-office-365-or-microsoft-365-customers"></a>Azure AD Free、Office 365、または Microsoft 365 のお客様向けのガイダンス

Azure AD Free、Office 365、または Microsoft 365 の各アプリのお客様が自分のユーザー ID を保護するために行うべき、いくつかの推奨事項があります。次の表は、次のライセンス サブスクリプション向けの主要なアクションを強調することを目的としています。

- Office 365 (Office 365 E1、E3、E5、F1、A1、A3、A5)
- Microsoft 365 (Business Basic、ビジネス向けアプリ、Business Standard、Business Premium、A1)
- Azure AD Free (Azure、Dynamics 365、Intune、および Power Platform に含まれています)

| 推奨アクション | Detail |
| --- | --- |
| [セキュリティの既定値群を有効にする](concept-fundamentals-security-defaults.md) | MFA を有効にし、レガシ認証をブロックすることで、すべてのユーザー ID とアプリケーションを保護します |
| [パスワード ハッシュ同期を有効にする](../hybrid/how-to-connect-password-hash-synchronization.md) (ハイブリッド ID を使用している場合) | 認証に冗長性を提供し、セキュリティを強化します (スマート ロックアウト、IP ロックアウト、漏洩した資格情報を検出する機能など) |
| [ADFS スマート ロックアウトを有効にする](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (該当する場合) | 悪意のあるアクティビティによるエクストラネット アカウント ロックアウトの発生からユーザーを保護します。 |
| [Azure Active Directory スマート ロックアウトを有効にする](../authentication/howto-password-smart-lockout.md) (マネージド ID を使用している場合) | スマート ロックアウトは、組織のユーザーのパスワードを推測したり、ブルート フォース方法を使用して侵入しようとする悪意のあるユーザーのロックアウトを支援します。 |
| [アプリケーションに対するエンドユーザーの同意を無効にする](../manage-apps/configure-user-consent.md) | 管理者の同意ワークフローによって、管理者は、エンド ユーザーが会社のデータを漏洩することがないように、管理者の承認を必要とするアプリケーションへのアクセス権を安全に付与することができます。 Microsoft では、将来のユーザーの同意動作をすべて無効にし、攻撃の対象となる領域を減らし、このリスクを軽減することをお勧めしています。 |
| [サポートされている SaaS アプリケーションをギャラリーから Azure AD に統合し、シングル サインオンを有効にする](../manage-apps/add-application-portal.md) | Azure AD には、あらかじめ統合された何千ものアプリケーションが含まれるギャラリーがあります。 組織で使用しているアプリケーションの一部は、おそらく、Azure portal から直接アクセスできるギャラリーにあります。 ユーザー エクスペリエンスの向上 (SSO) により、企業の SaaS アプリケーションへのアクセスをリモートで安全に提供します |
| [SaaS アプリケーションからのユーザー プロビジョニングとプロビジョニング解除を自動化する](../app-provisioning/user-provisioning.md) (該当する場合) | ユーザーがアクセスする必要のあるクラウド (SaaS) アプリケーションのユーザー ID とロールを自動的に作成します。 自動プロビジョニングには、ユーザー ID の作成に加えて、状態または役割が変化したときのユーザー ID のメンテナンスおよび削除が含まれ、組織のセキュリティが向上します。 |
| [安全なハイブリッド アクセスを有効にする:既存のアプリ デリバリー コントローラーとネットワークを使用してレガシ アプリをセキュリティで保護する](../manage-apps/secure-hybrid-access.md) (該当する場合) | オンプレミスやクラウドのレガシ認証アプリケーションを、既存のアプリケーション デリバリー コントローラーまたはネットワークを使用して Azure AD に接続することで、それらの認証アプリケーションを発行して保護します。 |
| [セルフサービス パスワード リセットを有効にする](../authentication/tutorial-enable-sspr.md) (クラウドのみのアカウントに適用可能) | この機能により、ユーザーが自分のデバイスやアプリケーションにサインインできなくなった場合のヘルプ デスクの問い合わせが減り、生産性の喪失も軽減されます。 |
| [可能な場合は非グローバル管理者ロールを使用する](../roles/permissions-reference.md) | 管理者には、アクセスする必要がある領域だけに必要なアクセスのみを付与します。 すべての管理者がグローバル管理者である必要はありません。 |
| [Microsoft のパスワードのガイダンスを有効にする](https://www.microsoft.com/research/publication/password-guidance/) | 設定したスケジュールでのパスワード変更をユーザーに要求することをやめ、複雑さに関する要件を無効にします。ユーザーは自分のパスワードを、より覚えやすく安全な何かのままにしておきやすくなります。 |


### <a name="guidance-for-azure-ad-premium-plan-1-customers"></a>Azure AD Premium プラン 1 のお客様向けのガイダンス

次の表は、次のライセンス サブスクリプション向けの主要なアクションを強調することを目的としています。

- Azure Active Directory Premium P1 (Azure AD P1)
- Enterprise Mobility + Security (EMS E3)
- Microsoft 365 (M365 E3、A3、F1、F3)

| 推奨アクション | Detail |
| --- | --- |
| [Azure AD MFA と SSPR の統合された登録エクスペリエンスを有効にして、ユーザー登録エクスペリエンスを簡略化する](../authentication/howto-registration-mfa-sspr-combined.md) | Azure AD Multi-Factor Authentication とセルフサービス パスワード リセットの両方について、ユーザーが 1 つの共通操作で登録できるようにします。 |
| [組織の MFA 設定を構成する](../authentication/howto-mfa-getstarted.md) | 多要素認証を使用した侵害からアカウントを確実に保護する |
| [セルフサービス パスワード リセットを有効にする](../authentication/tutorial-enable-sspr.md) | この機能により、ユーザーが自分のデバイスやアプリケーションにサインインできなくなった場合のヘルプ デスクの問い合わせが減り、生産性の喪失も軽減されます |
| [パスワード ライトバックを実装する](../authentication/tutorial-enable-sspr-writeback.md)(ハイブリッド ID を使用している場合) | クラウドでのパスワードの変更を、オンプレミスの Windows Server Active Directory 環境に書き戻すことを許可します。 |
| 条件付きアクセス ポリシーを作成して有効にする | [管理者が管理者権限を割り当てられているアカウントを保護するための MFA。](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [レガシ認証プロトコルに関連するリスクが増加したため、レガシ認証プロトコルをブロックします。](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [ユーザーとアプリケーションをセキュリティで保護して、ご使用の環境にバランスの取れた MFA ポリシーを作成するためのすべてのユーザーとアプリケーション向けの MFA。](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) <br><br> [Azure リソースにアクセスするすべてのユーザーに対して多要素認証を要求することで、特権リソースを保護するには、Azure 管理のために MFA を必須にします。](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [パスワード ハッシュ同期を有効にする](../hybrid/how-to-connect-password-hash-synchronization.md) (ハイブリッド ID を使用している場合) | 認証に冗長性を提供し、セキュリティを強化します (スマート ロックアウト、IP ロックアウト、漏洩した資格情報を検出する機能など) |
| [ADFS スマート ロックアウトを有効にする](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (該当する場合) | 悪意のあるアクティビティによるエクストラネット アカウント ロックアウトの発生からユーザーを保護します。 |
| [Azure Active Directory スマート ロックアウトを有効にする](../authentication/howto-password-smart-lockout.md) (マネージド ID を使用している場合) | スマート ロックアウトは、組織のユーザーのパスワードを推測したり、ブルート フォース方法を使用して侵入しようとする悪意のあるユーザーのロックアウトを支援します。 |
| [アプリケーションに対するエンドユーザーの同意を無効にする](../manage-apps/configure-user-consent.md) | 管理者の同意ワークフローによって、管理者は、エンド ユーザーが会社のデータを漏洩することがないように、管理者の承認を必要とするアプリケーションへのアクセス権を安全に付与することができます。 Microsoft では、将来のユーザーの同意動作をすべて無効にし、攻撃の対象となる領域を減らし、このリスクを軽減することをお勧めしています。 |
| [ アプリケーション プロキシを使用してオンプレミスのレガシ アプリケーションへのリモート アクセスを有効にする](../manage-apps/application-proxy-add-on-premises-application.md) | Azure AD アプリケーション プロキシを有効にし、レガシ アプリと統合して、ユーザーが自分の Azure AD アカウントでサインインすることで、オンプレミスのアプリケーションに安全にアクセスできるようにします。 |
| [安全なハイブリッド アクセスを有効にする:既存のアプリ デリバリー コントローラーとネットワークを使用してレガシ アプリをセキュリティで保護する](../manage-apps/secure-hybrid-access.md) (該当する場合)。 | オンプレミスやクラウドのレガシ認証アプリケーションを、既存のアプリケーション デリバリー コントローラーまたはネットワークを使用して Azure AD に接続することで、それらの認証アプリケーションを発行して保護します。 |
| [サポートされている SaaS アプリケーションをギャラリーから Azure AD に統合し、シングル サインオンを有効にする](../manage-apps/add-application-portal.md) | Azure AD には、あらかじめ統合された何千ものアプリケーションが含まれるギャラリーがあります。 組織で使用しているアプリケーションの一部は、おそらく、Azure portal から直接アクセスできるギャラリーにあります。 ユーザー エクスペリエンスの向上 (SSO) により、企業の SaaS アプリケーションへのアクセスをリモートで安全に提供します。 |
| [SaaS アプリケーションからのユーザー プロビジョニングとプロビジョニング解除を自動化する](../app-provisioning/user-provisioning.md) (該当する場合) | ユーザーがアクセスする必要のあるクラウド (SaaS) アプリケーションのユーザー ID とロールを自動的に作成します。 自動プロビジョニングには、ユーザー ID の作成に加えて、状態または役割が変化したときのユーザー ID のメンテナンスおよび削除が含まれ、組織のセキュリティが向上します。 |
| [条件付きアクセスを有効にする – デバイス ベース](../conditional-access/require-managed-devices.md) | デバイスベースの条件付きアクセスを使用して、セキュリティとユーザー エクスペリエンスを向上させます。 この手順によって、ユーザーは、セキュリティとコンプライアンスの基準を満たすデバイスからのみアクセスできるようになります。 これらのデバイスはマネージド デバイスとも呼ばれます。 マネージド デバイスには、Intune に準拠しているデバイスまたは Hybrid Azure AD 参加済みデバイスが可能です。 |
| [パスワード保護を有効にする](../authentication/howto-password-ban-bad-on-premises-deploy.md) | ユーザーが脆弱で簡単に推測できるパスワードを使用できないようにします。 |
| [複数のグローバル管理者を指定する](../roles/security-emergency-access.md) | 緊急事態があった場合に使用するため、クラウド専用の永続的なグローバル管理者アカウントを少なくとも 2 つ割り当てます。 これらのアカウントは日常は使用されないもので、長く複雑なパスワードにする必要があります。 緊急用アカウントを使用すると、緊急時にサービスにアクセスできるようになります。 |
| [可能な場合は非グローバル管理者ロールを使用する](../roles/permissions-reference.md) | 管理者には、アクセスする必要がある領域だけに必要なアクセスのみを付与します。 すべての管理者がグローバル管理者である必要はありません。 |
| [Microsoft のパスワードのガイダンスを有効にする](https://www.microsoft.com/research/publication/password-guidance/) | 設定したスケジュールでのパスワード変更をユーザーに要求することをやめ、複雑さに関する要件を無効にします。ユーザーは自分のパスワードを、より覚えやすく安全な何かのままにしておきやすくなります。 |
| [ゲスト ユーザー アクセスの計画を作成する](../external-identities/what-is-b2b.md) | ゲスト ユーザーが各自の職場 ID、学校 ID、またはソーシャル ID を使用してアプリやサービスにサインインできるようにして、共同作業を行います。 |

### <a name="guidance-for-azure-ad-premium-plan-2-customers"></a>Azure AD Premium プラン 2 のお客様向けのガイダンス

次の表は、次のライセンス サブスクリプション向けの主要なアクションを強調することを目的としています。

- Azure Active Directory Premium P2 (Azure AD P2)
- Enterprise Mobility + Security (EMS E5)
- Microsoft 365 (M365 E5、A5)

| 推奨アクション | Detail |
| --- | --- |
| [Azure AD MFA と SSPR の統合された登録エクスペリエンスを有効にして、ユーザー登録エクスペリエンスを簡略化する](../authentication/howto-registration-mfa-sspr-combined.md) | Azure AD Multi-Factor Authentication とセルフサービス パスワード リセットの両方について、ユーザーが 1 つの共通操作で登録できるようにします。 |
| [組織の MFA 設定を構成する](../authentication/howto-mfa-getstarted.md) | 多要素認証を使用した侵害からアカウントを確実に保護する |
| [セルフサービス パスワード リセットを有効にする](../authentication/tutorial-enable-sspr.md) | この機能により、ユーザーが自分のデバイスやアプリケーションにサインインできなくなった場合のヘルプ デスクの問い合わせが減り、生産性の喪失も軽減されます |
| [パスワード ライトバックを実装する](../authentication/tutorial-enable-sspr-writeback.md)(ハイブリッド ID を使用している場合) | クラウドでのパスワードの変更を、オンプレミスの Windows Server Active Directory 環境に書き戻すことを許可します。 |
| [Identity Protection ポリシーを有効にして MFA 登録を強制する](../identity-protection/howto-identity-protection-configure-mfa-policy.md) | Azure AD Multi-Factor Authentication (MFA) のロールアウトを管理します。 |
| [Identity Protection のユーザーとサインインのリスク ポリシーを有効にする](../identity-protection/howto-identity-protection-configure-risk-policies.md) | Identity Protection のユーザーとサインインのポリシーを有効にします。 推奨されるサインイン ポリシーは、中程度のリスクのサインインをターゲットとし、MFA を要求します。 ユーザー ポリシーの場合は、パスワードの変更操作を必要とする危険度の高いユーザーをターゲットにする必要があります。 |
| 条件付きアクセス ポリシーを作成して有効にする | [管理者が管理者権限を割り当てられているアカウントを保護するための MFA。](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [レガシ認証プロトコルに関連するリスクが増加したため、レガシ認証プロトコルをブロックします。](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [Azure リソースにアクセスするすべてのユーザーに対して多要素認証を要求することで、特権リソースを保護するには、Azure 管理のために MFA を必須にします。](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [パスワード ハッシュ同期を有効にする](../hybrid/how-to-connect-password-hash-synchronization.md) (ハイブリッド ID を使用している場合) | 認証に冗長性を提供し、セキュリティを強化します (スマート ロックアウト、IP ロックアウト、漏洩した資格情報を検出する機能など) |
| [ADFS スマート ロックアウトを有効にする](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (該当する場合) | 悪意のあるアクティビティによるエクストラネット アカウント ロックアウトの発生からユーザーを保護します。 |
| [Azure Active Directory スマート ロックアウトを有効にする](../authentication/howto-password-smart-lockout.md) (マネージド ID を使用している場合) | スマート ロックアウトは、組織のユーザーのパスワードを推測したり、ブルート フォース方法を使用して侵入しようとする悪意のあるユーザーのロックアウトを支援します。 |
| [アプリケーションに対するエンドユーザーの同意を無効にする](../manage-apps/configure-user-consent.md) | 管理者の同意ワークフローによって、管理者は、エンド ユーザーが会社のデータを漏洩することがないように、管理者の承認を必要とするアプリケーションへのアクセス権を安全に付与することができます。 Microsoft では、将来のユーザーの同意動作をすべて無効にし、攻撃の対象となる領域を減らし、このリスクを軽減することをお勧めしています。 |
| [ アプリケーション プロキシを使用してオンプレミスのレガシ アプリケーションへのリモート アクセスを有効にする](../manage-apps/application-proxy-add-on-premises-application.md) | Azure AD アプリケーション プロキシを有効にし、レガシ アプリと統合して、ユーザーが自分の Azure AD アカウントでサインインすることで、オンプレミスのアプリケーションに安全にアクセスできるようにします。 |
| [安全なハイブリッド アクセスを有効にする:既存のアプリ デリバリー コントローラーとネットワークを使用してレガシ アプリをセキュリティで保護する](../manage-apps/secure-hybrid-access.md) (該当する場合)。 | オンプレミスやクラウドのレガシ認証アプリケーションを、既存のアプリケーション デリバリー コントローラーまたはネットワークを使用して Azure AD に接続することで、それらの認証アプリケーションを発行して保護します。 |
| [サポートされている SaaS アプリケーションをギャラリーから Azure AD に統合し、シングル サインオンを有効にする](../manage-apps/add-application-portal.md) | Azure AD には、あらかじめ統合された何千ものアプリケーションが含まれるギャラリーがあります。 組織で使用しているアプリケーションの一部は、おそらく、Azure portal から直接アクセスできるギャラリーにあります。 ユーザー エクスペリエンスの向上 (SSO) により、企業の SaaS アプリケーションへのアクセスをリモートで安全に提供します。 |
| [SaaS アプリケーションからのユーザー プロビジョニングとプロビジョニング解除を自動化する](../app-provisioning/user-provisioning.md) (該当する場合) | ユーザーがアクセスする必要のあるクラウド (SaaS) アプリケーションのユーザー ID とロールを自動的に作成します。 自動プロビジョニングには、ユーザー ID の作成に加えて、状態または役割が変化したときのユーザー ID のメンテナンスおよび削除が含まれ、組織のセキュリティが向上します。 |
| [条件付きアクセスを有効にする – デバイス ベース](../conditional-access/require-managed-devices.md) | デバイスベースの条件付きアクセスを使用して、セキュリティとユーザー エクスペリエンスを向上させます。 この手順によって、ユーザーは、セキュリティとコンプライアンスの基準を満たすデバイスからのみアクセスできるようになります。 これらのデバイスはマネージド デバイスとも呼ばれます。 マネージド デバイスには、Intune に準拠しているデバイスまたは Hybrid Azure AD 参加済みデバイスが可能です。 |
| [パスワード保護を有効にする](../authentication/howto-password-ban-bad-on-premises-deploy.md) | ユーザーが脆弱で簡単に推測できるパスワードを使用できないようにします。 |
| [複数のグローバル管理者を指定する](../roles/security-emergency-access.md) | 緊急事態があった場合に使用するため、クラウド専用の永続的なグローバル管理者アカウントを少なくとも 2 つ割り当てます。 これらのアカウントは日常は使用されないもので、長く複雑なパスワードにする必要があります。 緊急用アカウントを使用すると、緊急時にサービスにアクセスできるようになります。 |
| [可能な場合は非グローバル管理者ロールを使用する](../roles/permissions-reference.md) | 管理者には、アクセスする必要がある領域だけに必要なアクセスのみを付与します。 すべての管理者がグローバル管理者である必要はありません。 |
| [Microsoft のパスワードのガイダンスを有効にする](https://www.microsoft.com/research/publication/password-guidance/) | 設定したスケジュールでのパスワード変更をユーザーに要求することをやめ、複雑さに関する要件を無効にします。ユーザーは自分のパスワードを、より覚えやすく安全な何かのままにしておきやすくなります。 |
| [ゲスト ユーザー アクセスの計画を作成する](../external-identities/what-is-b2b.md) | ゲスト ユーザーが各自の職場 ID、学校 ID、またはソーシャル ID を使用してアプリやサービスにサインインできるようにして、共同作業を行います。 |
| [Privileged Identity Management を有効にする](../privileged-identity-management/pim-configure.md) | 管理者が必要なときに承認を受けた場合にのみアクセス権が得られるように、組織内の重要なリソースへのアクセスの管理、制御、監視を可能にします。 |

## <a name="next-steps"></a>次の手順

- Azure AD の個々の機能に関する詳細なデプロイ ガイダンスについては、[Azure AD プロジェクトのデプロイ計画](active-directory-deployment-plans.md)に関する記事を参照してください。

- エンドツーエンドの Azure AD デプロイのチェックリストについては、「[Azure Active Directory 機能のデプロイ ガイド](active-directory-deployment-checklist-p2.md)」の記事を参照してください。