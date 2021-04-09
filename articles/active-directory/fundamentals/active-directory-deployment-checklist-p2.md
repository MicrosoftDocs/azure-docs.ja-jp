---
title: Azure AD デプロイ チェックリスト
description: Azure Active Directory 機能のデプロイ チェックリスト
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92d1e5b8ac6492b0b1d819431e4616d32a092cc8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836922"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Azure Active Directory 機能のデプロイ ガイド

組織の Azure Active Directory (Azure AD) をデプロイして、継続的にセキュリティで保護するのは、大変に感じる場合があります。 この記事では、お客様が 30 日間、60 日間、90 日間、またはそれ以上にわたって複数のフェーズでセキュリティ体制の強化を完了するうえで役立つ一般的なタスクを明らかにします。 既に Azure AD をデプロイした組織でも、投資から確実に最大限のメリットを引き出すために、このガイドを利用することができます。

綿密に計画されて作り上げられる ID インフラストラクチャは、既知のユーザーとデバイスのみが、生産性向上のワークロードとデータにセキュリティで保護されたアクセスを行うための道を開きます。

さらに、お客様は自社の [ID セキュリティ スコア](identity-secure-score.md)をチェックし、マイクロソフトのベスト プラクティスにどれだけ整合しているかを確認することができます。 これらの推奨事項を実装する前後にセキュリティ スコアをチェックして、同じ業界の他の企業や、同じ規模の他の組織と比べてどの程度うまくやっているかを確認します。

## <a name="prerequisites"></a>前提条件

このガイドの推奨事項の多くは、Azure AD Free で、またはまったくライセンスがなくても、実装することができます。 ライセンスが必要な場合は、タスクを遂行するために最小限どのライセンスが必要であるかを示します。

ライセンスに関する追加の情報については、以下のページをご覧ください。

* [Azure AD のライセンス](https://azure.microsoft.com/pricing/details/active-directory/)
* [Microsoft 365 Enterprise](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Azure AD External Identities の価格](../external-identities/external-identities-pricing.md)

## <a name="phase-1-build-a-foundation-of-security"></a>フェーズ 1:セキュリティ基盤の構築

このフェーズでは、通常のユーザー アカウントをインポートまたは作成する前に、管理者がベースラインのセキュリティ機能を有効にして、より安全で使いやすい基盤を Azure AD 内に作成します。 この基盤フェーズにより、ユーザーが最初からより安全な状態にあること、およびエンドユーザーに紹介される新しい概念は一度に 1 つだけにする必要があることを確実にします。

| タスク | Detail | 必要とされるライセンス |
| ---- | ------ | ---------------- |
| [複数のグローバル管理者を指定する](../roles/security-emergency-access.md) | 緊急事態があった場合に使用するため、クラウド専用の永続的なグローバル管理者アカウントを少なくとも 2 つ割り当てます。 これらのアカウントは日常は使用されないもので、長く複雑なパスワードにする必要があります。 | Azure AD Free |
| [可能な場合は非グローバル管理者ロールを使用する](../roles/permissions-reference.md) | 管理者には、アクセスする必要がある領域だけに必要なアクセスのみを付与します。 すべての管理者がグローバル管理者である必要はありません。 | Azure AD Free |
| [管理者ロールの使用の追跡について Privileged Identity Management を有効にする](../privileged-identity-management/pim-getting-started.md) | Privileged Identity Management を有効にして、管理者ロールの使用状況の追跡を開始します。 | Azure AD Premium P2 |
| [セルフサービス パスワード リセット をロール アウトする](../authentication/howto-sspr-deployment.md) | ユーザーに管理者権限を付与するポリシーを使用して、スタッフによる各自のパスワードのリセットを許可することで、パスワードのリセットに関するヘルプデスクの呼び出しを削減します。 | |
| [組織固有の禁止パスワードのカスタム リストを作成する](../authentication/tutorial-configure-custom-password-protection.md) | ユーザーが、お客様の組織や分野において一般的な単語や語句を含むパスワードを作成しないようにします。 | |
| [Azure AD のパスワード保護とのオンプレミス統合を有効にする](../authentication/concept-password-ban-bad-on-premises.md) | 禁止パスワード リストをオンプレミスのディレクトリまで拡張して、オンプレミスで設定されるパスワードが、グローバルでテナント固有の禁止パスワード リストにも確実に準拠するようにします。 | Azure AD Premium P1 |
| [Microsoft のパスワードのガイダンスを有効にする](https://www.microsoft.com/research/publication/password-guidance/) | 設定したスケジュールでのパスワード変更をユーザーに要求することをやめ、複雑さに関する要件を無効にします。ユーザーは自分のパスワードを、より覚えやすく安全な何かのままにしておきやすくなります。 | Azure AD Free |
| [クラウド ベースのユーザー アカウントの定期的なパスワード リセットを無効にする](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | 定期的なパスワード リセットでは、ユーザーに、既存のパスワードを増やすことが推奨されています。 Microsoft のパスワードのガイダンス ドキュメントに記載されたガイドラインを使用し、オンプレミスのポリシーをクラウドのみのユーザーにミラー化します。 | Azure AD Free |
| [Azure Active Directory のスマート ロックアウトをカスタマイズする](../authentication/howto-password-smart-lockout.md) | オンプレミスの Active Directory ユーザーにレプリケートされないように、クラウド ベースのユーザーからのロックアウトを停止します | |
| [AD FS のエクストラネット スマート ロックアウトを有効にする](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | AD FS のエクストラネット ロックアウトは、ブルート フォース パスワード推測攻撃からの保護を行います。同時に、有効な AD FS ユーザーには引き続き自分のアカウントを使用させます。 | |
| [条件付きアクセスを使用して Azure AD へのレガシ認証をブロックする](../conditional-access/block-legacy-authentication.md) | Multi-Factor Authentication を適用できないため、敵対者に好まれるエントリ ポイントになる POP、SMTP、IMAP、MAPI などのレガシ認証プロトコルをブロックします。 | Azure AD Premium P1 |
| [条件付きアクセス ポリシーを使用して Azure AD Multi-Factor Authentication をデプロイする](../authentication/howto-mfa-getstarted.md) | 条件付きアクセス ポリシーを使用して、機密性の高いアプリケーションにアクセスするときには 2 段階認証を実行するようユーザーに要求します。 | Azure AD Premium P1 |
| [Azure Active Directory Identity Protection を有効にする](../identity-protection/overview-identity-protection.md) | 組織内のユーザーについて、リスクのあるサインインや侵害された資格情報の追跡を有効にします。 | Azure AD Premium P2 |
| [リスク検出を使用して多要素認証とパスワード変更をトリガーする](../authentication/tutorial-risk-based-sspr-mfa.md) | 多要素認証、パスワードのリセット、リスクに基づいたサインインのブロックなどのイベントをトリガーできるオートメーションを有効にします。 | Azure AD Premium P2 |
| [セルフサービス パスワード リセットと Azure AD Multi-Factor Authentication の統合された登録を有効にする](../authentication/concept-registration-mfa-sspr-combined.md) | Azure AD Multi-Factor Authentication とセルフサービス パスワード リセットの両方について、ユーザーが 1 つの共通操作で登録できるようにします。 | Azure AD Premium P1 |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>フェーズ 2:ユーザーのインポート、同期の有効化、デバイスの管理

次に、フェーズ 1 で構築した基盤への追加を行います。ユーザーのインポート、同期の有効化、ゲスト アクセスの計画、追加機能のサポートのための準備を行います。

| タスク | Detail | 必要とされるライセンス |
| ---- | ------ | ---------------- |
| [Azure AD Connect のインストール](../hybrid/how-to-connect-install-select-installation.md) | 既存のオンプレミス ディレクトリのユーザーをクラウドに同期する準備をします。 | Azure AD Free |
| [パスワード ハッシュ同期の実装](../hybrid/how-to-connect-password-hash-synchronization.md) | パスワード ハッシュを同期し、パスワード変更のレプリケート、不正なパスワードの検出と修正、漏洩した資格情報の報告を行えるようにします。 | Azure AD Free |
| [パスワード ライトバックの実装](../authentication/tutorial-enable-sspr-writeback.md) | クラウドでのパスワードの変更を、オンプレミスの Windows Server Active Directory 環境に書き戻すことを許可します。 | Azure AD Premium P1 |
| [Azure AD Connect Health の実装](../hybrid/whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) | Azure AD Connect サーバー、AD FS サーバー、およびドメイン コント ローラーについて、正常性に関する重要な統計情報の監視を有効にします。 | Azure AD Premium P1 |
| [Azure Active Directory のグループ メンバーシップでユーザーにライセンスを割り当てる](../enterprise-users/licensing-groups-assign.md) | ユーザーごとの設定ではなく、グループ別に機能を有効または無効にするライセンス グループを作成することで、時間と労力を節約します。 | |
| [ゲスト ユーザー アクセスの計画を作成する](../external-identities/what-is-b2b.md) | ゲスト ユーザーが各自の職場 ID、学校 ID、またはソーシャル ID を使用してアプリやサービスにサインインできるようにして、共同作業を行います。 | [Azure AD External Identities の価格](../external-identities/external-identities-pricing.md) |
| [デバイス管理戦略を決定する](../devices/overview.md) | デバイスに関して組織が何を許可するかを決定します。 登録か参加か、Bring Your Own Device か会社支給かなどです。 | |
| [組織に Windows Hello for Business を配置する](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | Windows Hello を使用するパスワードなしの認証のための準備を行います | |
| [ユーザー用にパスワードなしの認証方法をデプロイする](../authentication/concept-authentication-passwordless.md) | ユーザーに便利なパスワードなしの認証方法を提供します | Azure AD Premium P1 |

## <a name="phase-3-manage-applications"></a>フェーズ 3: アプリケーションの管理

以前のフェーズを基にした構築を続行するので、移行と Azure AD との統合の候補となるアプリケーションを特定し、それらのアプリケーションのセットアップを完了します。

| タスク | Detail | 必要とされるライセンス |
| ---- | ------ | ---------------- |
| アプリケーションを特定する | オンプレミス アプリケーション、クラウドの SaaS アプリケーション、その他の基幹業務アプリケーションなど、組織で使用中のアプリケーションを特定します。 これらのアプリケーションの Azure AD での管理が、可能であるか、する必要があるかを判断します。 | ライセンス不要 |
| [ギャラリーのサポートされている SaaS アプリケーションを統合する](../manage-apps/add-application-portal.md) | Azure AD には、あらかじめ統合された何千ものアプリケーションが含まれるギャラリーがあります。 組織で使用しているアプリケーションの一部は、おそらく、Azure portal から直接アクセスできるギャラリーにあります。 | Azure AD Free |
| [アプリケーション プロキシを使用してオンプレミス アプリケーションを統合する](../manage-apps/application-proxy-add-on-premises-application.md) | アプリケーション プロキシを使用すると、ユーザーは Azure AD アカウントでサインインして、オンプレミスのアプリケーションにアクセスできます。 | |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>フェーズ 4: 特権 ID の監査、アクセス レビューの完了、ユーザー ライフ サイクルの管理

フェーズ 4 では、管理者が、管理用の最低限の特権を適用し、最初のアクセス レビューを完了し、一般的なユーザー ライフ サイクルのタスクの自動化を有効にします。

| タスク | Detail | 必要とされるライセンス |
| ---- | ------ | ---------------- |
| [Privileged Identity Management の使用を強制する](../privileged-identity-management/pim-security-wizard.md) | 通常の日常ユーザー アカウントから管理者ロールを削除します。 管理ユーザーにそのロールの使用を認めるのは、多要素認証チェックの成功後、業務上妥当である理由の提示後、指定された承認者からの要求後とします。 | Azure AD Premium P2 |
| [PIM で Azure AD ディレクトリ ロールのアクセス レビューを完了する](../privileged-identity-management/pim-how-to-start-security-review.md) | セキュリティおよびリーダーシップ チームと協力して、組織のポリシーに基づいて管理アクセスをレビューするアクセス レビュー ポリシーを作成します。 | Azure AD Premium P2 |
| [動的グループ メンバーシップのポリシーを実装する](../enterprise-users/groups-dynamic-membership.md) | 動的グループを使用して、部門、役職、リージョン、その他の属性など、人事 (または、信頼できるソース) からの属性に基づいて、グループにユーザーを自動的に割り当てます。 |  |
| [グループ ベースのアプリケーション プロビジョニングを実装する](../manage-apps/what-is-access-management.md) | グループベース アクセス管理のプロビジョニングを使用して、SaaS アプリケーションのユーザーを自動的にプロビジョニングします。 |  |
| [ユーザー プロビジョニングとプロビジョニング解除を自動化する](../app-provisioning/user-provisioning.md) | 未承認のアクセスを防ぐために、従業員のアカウント ライフサイクルから手動の手順をなくします。 適切なソース (人事システム) からの ID を Azure AD に同期する。 |  |

## <a name="next-steps"></a>次のステップ

[Azure AD のライセンスと価格の詳細](https://azure.microsoft.com/pricing/details/active-directory/)

[ID とデバイスのアクセスの構成](/microsoft-365/enterprise/microsoft-365-policies-configurations)

[推奨される一般的な ID とデバイスのアクセス ポリシー](/microsoft-365/enterprise/identity-access-policies)