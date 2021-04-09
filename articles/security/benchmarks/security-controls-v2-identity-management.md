---
title: Azure セキュリティ ベンチマーク V2 - ID 管理
description: Azure セキュリティ ベンチマーク V2 ID 管理
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 4a36bd69ff5ddbc79e358d6f8a2c5b4d640c6d5c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051448"
---
# <a name="security-control-v2-identity-management"></a>セキュリティ コントロール V2:ID 管理

ID 管理は、Azure Active Directory を使用して、セキュリティで保護された ID とアクセスの制御を確立するためのコントロールを対象とします。 これには、シングル サインオン、強力な認証、アプリケーションのマネージド ID (およびサービスの原則)、条件付きアクセス、およびアカウントの異常監視の使用が含まれます。

該当する組み込み Azure Policy を確認するには、「[Azure セキュリティ ベンチマーク規制コンプライアンスの組み込みイニシアチブの詳細: ID 管理](../../governance/policy/samples/azure-security-benchmark.md#identity-management)」を参照してください。

## <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1:Azure Active Directory を中央 ID および認証システムとして標準化する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| IM-1 | 16.1、16.2、16.4、16.5 | IA-2、IA-8、AC-2、AC-3 |

Azure Active Directory (Azure AD) は、Azure の既定の ID およびアクセス管理サービスです。 以下での組織の ID とアクセス管理のガバナンスを、Azure AD で標準化する必要があります。
- Azure portal、Azure Storage、Azure Virtual Machines (Linux と Windows)、Azure Key Vault、PaaS、SaaS アプリケーションなどの Microsoft クラウド リソース。

- Azure 上のアプリケーションや企業ネットワーク リソースなどの組織のリソース。

Azure AD を保護することは、組織のクラウド セキュリティ プラクティスの中で高い優先順位を持つ必要があります。 Azure AD により、Microsoft のベスト プラクティスの推奨事項と比較して、ID セキュリティ体制を評価するのに役立つ ID セキュリティ スコアが提供されます。 スコアを使用して、構成がベスト プラクティスの推奨事項とどの程度一致しているかを測定し、セキュリティ体制を強化します。

注:Azure AD では、外部 ID プロバイダーがサポートされます。これにより、Microsoft アカウントを持たないユーザーが、外部 ID を使用してアプリケーションやリソースにサインインできるようになります。

- [Azure AD のテナント](../../active-directory/develop/single-and-multi-tenant-apps.md)

- [Azure AD インスタンスを作成して構成する方法](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure AD テナントを定義する](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)

- [アプリケーションに外部 ID プロバイダーを使用する](../../active-directory/external-identities/identity-providers.md)

- [Azure AD の ID セキュリティ スコアとは](../../active-directory/fundamentals/identity-secure-score.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [ID およびキー管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys) 

- [セキュリティのアーキテクチャ](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [アプリケーションのセキュリティと DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [体制管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2:アプリケーション ID を安全かつ自動的に管理する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| IM-2 | N/A | AC-2、AC-3、IA-2、IA-4、IA-9 |

サービスや自動化などの人間以外のアカウントでは、リソースにアクセスしたりコードを実行したりするために、より強力な人間のアカウントを作成するのではなく、Azure のマネージド ID を使用します。 Azure マネージド ID によって、Azure AD 認証をサポートする Azure のサービスとリソースに対して認証を行うことができます。 認証は事前に定義されたアクセス許可規則によって有効になり、ソース コードまたは構成ファイル内でハードコーディングされた資格情報を使用せずに済みます。 

マネージド ID をサポートしていないサービスに対しては、Azure AD を使用して、リソース レベルでアクセス許可が制限されたサービス プリンシパルを代わりに作成します。 証明書の資格情報を使用してサービス プリンシパルを構成し、クライアント シークレットにフォールバックすることが推奨されます。 どちらの場合も、Azure Key Vault を Azure マネージド ID と組み合わせて使用し、ランタイム環境 (Azure 関数など) でキー コンテナーから資格情報を取得できるようにすることができます。

- [Azure マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md)

- [Azure リソースのマネージド ID をサポートするサービス](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Azure サービス プリンシパル](/powershell/azure/create-azure-service-principal-azureps)

- [証明書を使用してサービス プリンシパルを作成する](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

セキュリティ プリンシパルの登録に Azure Key Vault を使用する: authentication#authorize-a-security-principal-to-access-key-vault

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [ID およびキー管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [アプリケーションのセキュリティと DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3:アプリケーションのアクセスに Azure AD シングル サインオン (SSO) を使用する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| IM-3 | 4.4. | IA-2、IA-4 |

Azure AD により、Azure リソース、クラウド アプリケーション、オンプレミス アプリケーションに対する ID とアクセスの管理が提供されます。 ID およびアクセスの管理は、従業員などの企業 ID だけでなく、パートナー、ベンダー、サプライヤーなどの外部 ID にも適用されます。

Azure AD シングル サインオン (SSO) を使用して、オンプレミスとクラウドの組織のデータとリソースへのアクセスを管理し、セキュリティで保護します。 すべてのユーザー、アプリケーション、デバイスを、シームレスで安全なアクセスおよび可視性と制御の向上のために、Azure AD に接続します。 

- [Azure AD でのアプリケーションの SSO を理解する](../../active-directory/manage-apps/what-is-single-sign-on.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティのアーキテクチャ](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [ID およびキー管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [アプリケーションのセキュリティと DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4:すべての Azure Active Directory ベースのアクセスに強力な認証制御を使用する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| IM-4 | 4.2、4.4、4.5、11.5、12.11、16.3 | AC-2、AC-3、IA-2、IA-4 |

Azure AD では、多要素認証 (MFA) と強力なパスワードレスの方法によって、強力な認証制御がサポートされています。

- 多要素認証:Azure AD MFA を有効にして、MFA のセットアップに対する Azure Security Center ID とアクセス管理の推奨事項に従います。 MFA は、サインインの条件とリスク要因に基づいて、すべてのユーザー、選択されたユーザー、またはユーザー単位のレベルで適用できます。

- パスワードレス認証: Windows Hello for Business、Microsoft Authenticator アプリ、オンプレミスの認証方法 (スマート カードなど) という 3 つのパスワードレス認証オプションを使用できます。

管理者と特権ユーザーについて、高いレベルの強力な認証方法が使用されていることを確認し、その後、適切な強力な認証ポリシーを他のユーザーにロールアウトします。

Azure AD 認証に従来のパスワード ベースの認証がまだ使用されている場合は、クラウド専用アカウント (Azure で直接作成されたユーザー アカウント) では既定のベースライン パスワード ポリシーが使用されていることに注意してください。 また、ハイブリッド アカウント (オンプレミスの Active Directory から取得したユーザー アカウント) は、オンプレミスのパスワード ポリシーに従います。 パスワード ベースの認証を使用すると、Azure AD により、ユーザーが推測されやすいパスワードを設定できないようにするパスワード保護機能が提供されます。 Microsoft では、テレメトリに基づいて更新される禁止パスワードのグローバル リストを提供しており、顧客は各自のニーズ (ブランドや文化的リファレンスなど) に基づいてこのリストに追加できます。 このパスワード保護は、クラウド専用アカウントとハイブリッド アカウントに使用できます。

注:パスワード資格情報だけに基づく認証は、一般的な攻撃方法の影響を受けやすいものです。 セキュリティを強化するには、MFA や強力なパスワード ポリシーなどの強力な認証を使用します。 既定のパスワードが使用されている可能性のあるサードパーティ製のアプリケーションや Marketplace サービスの場合は、サービスの初期セットアップ時にそれを変更する必要があります。

- [Azure で MFA を有効にする方法](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Active Directory のパスワードレス認証オプションの概要](../../active-directory/authentication/concept-authentication-passwordless.md)

- [Azure AD の規定のパスワード ポリシー](../../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Azure AD のパスワード保護を使用して不適切なパスワードを排除する](../../active-directory/authentication/concept-password-ban-bad.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティのアーキテクチャ](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [ID およびキー管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [アプリケーションのセキュリティと DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5:アカウントの異常を監視してアラートを出す

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| IM-5 | 4.8、4.9、16.12、16.13 | AC-2、AC-3、AC-7、AU-6 |

Azure AD には、次のデータ ソースが用意されています。 
-   サインイン - サインイン レポートは、マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報を提供します。

-   監査ログ - Azure AD 内のさまざまな機能によって行われたすべての変更についてログによる追跡可能性を提供します。 ログに記録される変更の監査ログの例としては、ユーザー、アプリ、グループ、ロール、ポリシーの追加や削除などがあります。

-   リスクの高いサインイン - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。

-   リスクのフラグ付きユーザー - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。

これらのデータ ソースは、Azure Monitor、Azure Sentinel、またはサードパーティの SIEM システムと統合できます。

Azure Security Center で、認証試行の失敗回数が多すぎるなど、サブスクリプションにおける特定の不審なアクティビティや、使用すべきでないアカウントに対してアラートを生成することもできます。 

セキュリティ ソリューションである Azure Advanced Threat Protection (ATP) では、オンプレミスの Active Directory シグナル信号を使用することで、高度な脅威、セキュリティ侵害を受けた ID、および悪意のある内部関係者のアクションを特定、検出、および調査できます。

- [Azure AD でアクティビティ レポートを監査する](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure AD の危険なサインインを表示する方法](../../active-directory/identity-protection/overview-identity-protection.md)

- [危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法](../../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法](../../security-center/security-center-identity-access.md)

- [Azure Security Center の脅威インテリジェンス保護モジュールでのアラート](../../security-center/alerts-reference.md)

- [Azure アクティビティ ログを Azure Monitor に統合する方法](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure AD Identity Protection からデータを接続する](../../sentinel/connect-azure-ad-identity-protection.md)

- [Microsoft Defender for Identity](/azure-advanced-threat-protection/what-is-atp)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [アプリケーションのセキュリティと DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [体制管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6:条件に基づいて Azure リソースへのアクセスを制限する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| IM-6 | N/A | AC-2、AC-3 |

特定の IP 範囲からのユーザー ログインに対する MFA の使用の要求など、ユーザー定義の条件に基づく、より詳細なアクセス制御のために、Azure AD の条件付きアクセスを使用します。 詳細な認証セッションの管理は、さまざまなユース ケースに対する Azure AD 条件付きアクセス ポリシーによって使用することもできます。 

- [Azure での条件付きアクセスの概要](../../active-directory/conditional-access/overview.md)

- [一般的な条件付きアクセス ポリシー](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [条件付きアクセスを使用して認証セッション管理を構成する](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [ID およびキー管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [アプリケーションのセキュリティと DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [体制管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [脅威インテリジェンス](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7:意図しない資格情報の公開を排除する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| IM-7 | 18.1、18.7 | IA-5 |

コード内で資格情報を特定する Azure DevOps 資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

GitHub の場合、ネイティブ シークレット スキャン機能を使用して、コード内で資格情報やその他の形式のシークレットを識別できます。

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [GitHub シークレット スキャン](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [アプリケーションのセキュリティと DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [体制管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-8-secure-user-access-to-legacy-applications"></a>IM-8:レガシ アプリケーションへのユーザー アクセスをセキュリティで保護する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| IM-8 | 14.6 | AC-2、AC-3、SC-11 |

レガシ アプリケーションとそれらによって格納および処理されるデータに対し、最新のアクセス制御とセッション監視が使用されていることを確認します。 レガシ アプリケーションへのアクセスには VPN がよく使用されますが、多くの場合、基本的なアクセス制御と限られたセッション監視しか行われません。

Azure AD アプリケーション プロキシを使用すると、Azure AD 条件付きアクセスを使用してリモート ユーザーとデバイスの両方の信頼性を明示的に検証しながら、シングル サインオン (SSO) を使用してリモート ユーザーにレガシ オンプレミス アプリケーションを発行することができます。

また、Microsoft Cloud App Security はクラウド アクセス セキュリティ ブローカー (CASB) サービスであり、それを使用すると、ユーザーのアプリケーション セッションを監視し、アクションをブロックするコントロールを提供できます (レガシ オンプレミス アプリケーションと、クラウドのサービスとしてのソフトウェア (SaaS) アプリケーションの両方)。

- [Azure AD アプリケーション プロキシ](../../active-directory/manage-apps/application-proxy.md#what-is-application-proxy)

- [Microsoft Cloud App Security のベスト プラクティス](/cloud-app-security/best-practices)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティのアーキテクチャ](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [インフラストラクチャとエンドポイント セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [アプリケーションのセキュリティと DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)