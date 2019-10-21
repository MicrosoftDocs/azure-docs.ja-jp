---
title: サンプル - CIS Microsoft Azure Foundations Benchmark ブループリント - 推奨事項のマッピング
description: CIS Microsoft Azure Foundations Benchmark ブループリント サンプルと Azure Policy の推奨事項のマッピング。
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/01/2019
ms.topic: sample
ms.service: blueprints
ms.openlocfilehash: 9368ad220f1ded1a11360dbdf0af86c27277a207
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001281"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>CIS Microsoft Azure Foundations Benchmark ブループリント サンプルの推奨事項のマッピング

次の記事では、Azure Blueprints CIS Microsoft Azure Foundations Benchmark ブループリント サンプルが CIS Microsoft Azure Foundations Benchmark の推奨事項にどのようにマッピングされるかを詳しく説明しています。 推奨事項の詳細については、[CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/) に関するページを参照してください。

次のマッピングは、**CIS Microsoft Azure Foundations Benchmark v1.1.0** の推奨事項へのマッピングです。 右側のナビゲーションを使用すると、特定の推奨事項のマッピングに直接移動できます。
マッピングされた推奨事項の多くは、[Azure Policy](../../../policy/overview.md) イニシアティブを使用して実装されます。 イニシアチブの詳細を確認するには、Azure portal で **[ポリシー]** を開き、 **[定義]** ページを選択します。 その後、 **[\[Preview\] Audit CIS Microsoft Azure Foundations Benchmark v1.1.0 recommendations and deploy specific VM Extensions to support audit requirements]\([プレビュー] CIS Microsoft Azure Foundations Benchmark v1.1.0 の推奨事項を監査し、特定の VM 拡張機能をデプロイして監査要件をサポートする\)** 組み込みポリシー イニシアティブを見つけて選択します。

> [!IMPORTANT]
> 以下の各コントロールは、1 つまたは複数の [Azure Policy](../../../policy/overview.md) 定義に関連付けられています。 これらのポリシーは、コントロールに対する[コンプライアンスを評価](../../../policy/how-to/get-compliance-data.md)するのに役立つ場合があります。ただし、多くの場合、コントロールと 1 つまたは複数のポリシーとの間に 1:1 または完全な一致はありません。 そのため、Azure Policy での**準拠**は、ポリシー自体のみを指しています。これによって、コントロールのすべての要件に完全に準拠していることが保証されるわけではありません。 また、コンプライアンス標準には、現時点でどの Azure Policy 定義でも対応されていないコントロールが含まれています。 したがって、Azure Policy でのコンプライアンスは、全体のコンプライアンス状態の部分的ビューでしかありません。 このコンプライアンス ブループリント サンプルのコントロールと Azure Policy 定義の間の関連付けは、時間の経過と共に変わる可能性があります。 変更履歴を表示するには、[GitHub のコミット履歴](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md)を参照してください。

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1.1 特権のあるすべてのユーザーに対して多要素認証が有効になっていることを確認する

このブループリントでは、特権 Azure Active Directory アカウントで多要素認証が有効になっていない場合の監視に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある
- サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1.2 特権のないすべてのユーザーに対して多要素認証が有効になっていることを確認する

このブループリントでは、非特権 Azure Active Directory アカウントで多要素認証が有効になっていない場合の監視に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある

## <a name="13-ensure-that-there-are-no-guest-users"></a>1.3 ゲスト ユーザーがいないことを確認する

このブループリントでは、削除が必要になる可能性のあるゲスト アカウントの監視に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある
- 読み取りアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある
- 書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある

## <a name="21-ensure-that-standard-pricing-tier-is-selected"></a>2.1 Standard 価格レベルが選択されていることを確認する

このブループリントでは、Security Center Standard レベルが有効になっていないネットワークと仮想マシンの監視に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

 - Security Center の Standard 価格レベルを選択する必要がある

## <a name="22-ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>2.2 [監視エージェントの自動プロビジョニング] が [オン] に設定されていることを確認する

このブループリントでは、Log Analytics エージェントの自動プロビジョニングを有効にするのに役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- 自分のサブスクリプションで Log Analytics 監視エージェントの自動プロビジョニングを有効にする必要がある

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2.3 ASC の既定のポリシー設定 [システムの更新プログラムの監視] が [無効] になっていないことを確認する

このブループリントでは、システムの更新プログラムが仮想マシンにインストールされていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- システム更新プログラムをマシンにインストールする必要がある

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2.4 ASC の既定のポリシー設定 [OS 脆弱性の監視] が [無効] になっていないことを確認する

このブループリントでは、未修復の仮想マシンの脆弱性を監視するのに役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- 使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2.5 ASC の既定のポリシー設定 [Endpoint Protection の監視] が [無効] になっていないことを確認する

このブループリントでは、仮想マシンでエンドポイント保護が有効になっていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- Endpoint Protection の欠落の Azure Security Center での監視

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2.6 ASC の既定のポリシー設定 [ディスク暗号化の監視] が [無効] になっていないことを確認する

このブループリントでは、仮想マシン ディスクが暗号化されていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- 仮想マシンでディスク暗号化を適用する必要がある

## <a name="27-ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>2.7 ASC の既定のポリシー設定 [ネットワーク セキュリティ グループの監視] が [無効] になっていないことを確認する

このブループリントでは、インターネットに接続する仮想マシンの保護に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- インターネットに接続している仮想マシン用のネットワーク セキュリティ グループ ルールは、強化する必要がある

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2.8 ASC の既定のポリシー設定 [Web アプリケーション ファイアウォールの監視] が [無効] になっていないことを確認する

このブループリントでは、Web アプリケーションを実行している仮想マシンの保護に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- IaaS 上の Web アプリケーションに対する NSG 規則を強化する必要がある

## <a name="29-ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>2.9 ASC の既定のポリシー設定 [次世代のファイアウォール (NGFW) 監視を有効にする] が [無効] になっていないことを確認する

このブループリントでは、アクセスを制限してサブネットと仮想マシンを脅威から保護するのに役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。 この CIS Microsoft Azure Foundations Benchmark の推奨事項で参照されている Security Center ポリシーは、2 つの新しい推奨事項に置き換えられています。 次に示すポリシーは、新しい推奨事項に対応しています。

- サブネットはネットワーク セキュリティ グループに関連付けられている必要がある
- 仮想マシンはネットワーク セキュリティ グループに関連付けられている必要がある

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2.10 ASC の既定のポリシー設定 [脆弱性評価を監視する] が [無効] になっていないことを確認する

このブループリントでは、脆弱性が検出され修復されていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- 脆弱性評価ソリューションによって脆弱性を修復する必要がある

## <a name="211-ensure-asc-default-policy-setting-monitor-storage-blob-encryption-is-not-disabled"></a>2.11 ASC の既定のポリシー設定 [Storage BLOB 暗号化の監視] が [無効] になっていないことを確認する

Azure Storage 暗号化は、新規と既存のすべてのストレージ アカウントに対して有効にされ、無効にすることはできません。 (これは既定の Azure の機能です。ポリシーの割り当てはありません)

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2.12 ASC の既定のポリシー設定 [JIT ネットワーク アクセスの監視] が [無効] になっていないことを確認する

このブループリントでは、仮想マシンに対するアクセスの制御に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- 仮想マシンで Just-In-Time ネットワーク アクセス制御を適用する必要がある

## <a name="213-ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>2.13 ASC の既定のポリシー設定 [Monitor Adaptive Application Whitelisting]\(適応型アプリケーションのホワイトリスト登録の監視\) が [無効] になっていないことを確認する

このブループリントでは、仮想マシンで適応型アプリケーション制御が有効になっていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- 適応型アプリケーション制御を仮想マシンで有効にする必要がある

## <a name="214-ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>2.14 ASC の既定のポリシー設定 [SQL 監査の監視] が [無効] になっていないことを確認する

このブループリントでは、SQL サーバー監査が有効になっていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- SQL Server の高度なデータ セキュリティ設定で監査を有効にする必要がある

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2.15 ASC の既定のポリシー設定 [SQL 暗号化の監視] が [無効] になっていないことを確認する

このブループリントでは、SQL データベースで Transparent Data Encryption が有効になっていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- SQL データベースで Transparent Data Encryption を有効にする必要がある

## <a name="216-ensure-that-security-contact-emails-is-set"></a>2.16 [セキュリティ連絡先の電子メール] が設定されていることを確認する

このブループリントでは、セキュリティ通知が適切に有効になっていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- 自分のサブスクリプションに対し、セキュリティ連絡先のメール アドレスを指定する必要がある

## <a name="217-ensure-that-security-contact-phone-number-is-set"></a>2.17 セキュリティ連絡先の [電話番号] が設定されていることを確認する

このブループリントでは、セキュリティ通知が適切に有効になっていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- 自分のサブスクリプションに対し、セキュリティ連絡先の電話番号を指定する必要がある

## <a name="218-ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>2.18 [重要度 - 高についてアラートの電子メール通知を送信する] が [オン] に設定されていることを確認する

このブループリントでは、セキュリティ通知が適切に有効になっていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- 重要度 - 高のアラートのメール通知を有効にする必要がある

## <a name="219-ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>2.19 [サブスクリプションの所有者にもメールを送信する] が [オン] に設定されていることを確認する

このブループリントでは、セキュリティ通知が適切に有効になっていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- サブスクリプション所有者に対する重要度 - 高のアラートのメール通知を有効にする必要がある

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3.1 [安全な転送が必要] が [有効] に設定されていることを確認する

このブループリントでは、安全でない接続を許可しているストレージ アカウントの監視に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- ストレージ アカウントへの安全な転送を有効にする必要がある

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3.7 ストレージ アカウントの既定のネットワーク アクセス ルールが拒否に設定されていることを確認する

このブループリントでは、無制限のアクセスを許可しているストレージ アカウントの監視に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- ストレージ アカウントに対する制限のないネットワーク アクセスの監査

## <a name="38-ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>3.8 ストレージ アカウント アクセスに対して [信頼された Microsoft サービス] が有効になっていることを確認する

このブループリントでは、信頼された Microsoft サービスからのアクセスを許可していないストレージ アカウントの監視に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- ストレージ アカウントは、信頼された Microsoft サービスからのアクセスを許可する必要がある

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4.1 [監査] が [オン] に設定されていることを確認する

このブループリントでは、SQL サーバー監査が有効になっていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。 

- SQL Server の高度なデータ セキュリティ設定で監査を有効にする必要がある

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4.2 SQL Server の "監査" ポリシーの "AuditActionGroups" が適切に設定されていることを確認する

このブループリントでは、SQL サーバー監査が適切に構成されていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- SQL 監査設定では、重要なアクティビティをキャプチャするように Action-Groups を構成しておく必要がある

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4.3 "監査" 保持期間が "90 日を超える" ことを確認する

このブループリントでは、SQL Server ログが少なくとも 90 日間保持されることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- SQL Server では、90 日を超える監査保持期間を構成する必要がある。

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4.4 SQL Server の "Advanced Data Security" が [オン] に設定されていることを確認する

このブループリントでは、SQL サーバーと SQL マネージド インスタンスで Advanced Data Security が有効になっていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- Advanced Data Security を SQL マネージド インスタンス上で有効にする必要がある
- Advanced Data Security を、SQL サーバー上で有効にする必要がある

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4.5 [脅威検出の種類] が [すべて] に設定されていることを確認する

このブループリントでは、SQL サーバーと SQL マネージド インスタンスで Advanced Threat Protection が適切に構成されていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- SQL Server の Advanced Data Security 設定で、[Advanced Threat Protection の種類] を [すべて] に設定する必要がある
- SQL マネージド インスタンスの Advanced Data Security 設定で、[Advanced Threat Protection の種類] を [すべて] に設定する必要がある

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4.6 [アラートの送信先] が設定されていることを確認する

このブループリントでは、Advanced Data Security 通知が適切に有効になっていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- SQL マネージド インスタンスの高度なデータ セキュリティ設定に、セキュリティ アラートを受信するためのメール アドレスが含まれている必要がある
- SQL Server の高度なデータ セキュリティ設定に、セキュリティ アラートを受信するためのメール アドレスが含まれている必要がある

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4.7 [サービスの管理者/共同管理者に電子メールを送信] が [有効] になっていることを確認する

このブループリントでは、Advanced Data Security 通知が適切に有効になっていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- SQL マネージド インスタンスの Advanced Data Security 設定で管理者とサブスクリプションの所有者に対するメール通知を有効にする必要がある
- SQL Server の Advanced Data Security 設定で、管理者とサブスクリプションの所有者に対するメール通知を有効にする必要がある

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4.8 Azure Active Directory 管理者が構成されていることを確認する

このブループリントでは、Azure Active Directory 管理者が SQL サーバーに対してプロビジョニングされていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4.9 SQL Database の [データ暗号化] が [オン] に設定されていることを確認する

このブループリントでは、SQL データベースで Transparent Data Encryption が有効になっていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- SQL データベースで Transparent Data Encryption を有効にする必要がある

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4.10 SQL Server の TDE 保護機能が BYOK (自分のキーの使用) で暗号化されていることを確認する

このブループリントでは、SQL サーバーと SQL マネージド インスタンス用の透過的なデータ暗号化保護機能が自分のキーを使用して暗号化されていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- SQL マネージド インスタンスの TDE 保護機能を自分のキーを使用して暗号化する必要がある
- SQL Server の TDE 保護機能を自分のキーを使用して暗号化する必要がある

## <a name="411-ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>4.11 MySQL データベース サーバーで [SSL 接続を強制する] が [有効] に設定されていることを確認する

このブループリントでは、MySQL データベース サーバーで SSL 接続が強制的に適用されることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- MySQL データベース サーバーで [SSL 接続を強制する] が有効でなければならない

## <a name="413-ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>4.13 PostgreSQL データベース サーバーで [SSL 接続を強制する] が [有効] に設定されていることを確認する

このブループリントでは、PostgreSQL データベース サーバーで SSL 接続が強制的に適用されることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- PostgreSQL データベース サーバーで [SSL 接続を強制する] が有効でなければならない

## <a name="417-ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>4.17 サーバー パラメーターである "connection_throttling" が PostgreSQL データベース サーバー向けに [オン] に設定されていることを確認する

このブループリントでは、PostgreSQL データベース サーバーに対するブルート フォース攻撃の緩和に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- PostgreSQL データベース サーバーで接続の調整が有効でなければならない

## <a name="419-ensure-that-azure-active-directory-admin-is-configured"></a>4.19 Azure Active Directory 管理者が構成されていることを確認する

このブループリントでは、Azure Active Directory 管理者が SQL サーバーに対してプロビジョニングされていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。 CIS Microsoft Azure Foundations Benchmark には、この推奨事項が含まれています。ただし、これは[推奨事項 4.8](#48-ensure-that-azure-active-directory-admin-is-configured) と重複しています。

- SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある

## <a name="511-ensure-that-a-log-profile-exists"></a>5.1.1 ログ プロファイルが存在することを確認する

このブループリントでは、すべての Azure サブスクリプションにログ プロファイルが存在していることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。 

- Azure サブスクリプションにはアクティビティ ログのログ プロファイルが必要

## <a name="512-ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>5.1.2 アクティビティ ログのリテンション期間が 365 日以上に設定されていることを確認する

このブループリントでは、アクティビティ ログが少なくとも 1 年間保持されることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- アクティビティ ログを少なくとも 1 年間保持する必要がある

## <a name="513-ensure-audit-profile-captures-all-the-activities"></a>5.1.3 監査プロファイルによってすべてのアクティビティがキャプチャされることを確認する

このブループリントでは、ログ プロファイルが適切に構成されていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- Azure Monitor ログ プロファイルで、"書き込み"、"削除"、"アクション" の各カテゴリのログを収集する必要がある

## <a name="514-ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>5.1.4 ログ プロファイルによってグローバルを含むすべてのリージョンのアクティビティ ログがキャプチャされることを確認する

このブループリントでは、ログ プロファイルが適切に構成されていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- Azure Monitor ですべてのリージョンからアクティビティ ログを収集する必要がある

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 Azure KeyVault のログ記録が [有効] になっていることを確認する

このブループリントでは、キー コンテナーに対して診断ログが有効になっていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- Key Vault で診断ログを有効にする必要がある

## <a name="65-ensure-that-network-watcher-is-enabled"></a>6.5 Network Watcher が [有効] になっていることを確認する

このブループリントでは、リソースがデプロイされているすべてのリージョンで Network Watcher が有効になっていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。 このポリシーでは、適用可能なすべてのリージョンを指定するパラメーター配列が必須です。 このポリシーのイニシアティブ定義の既定値は "eastus" です。

- Network Watcher を有効にする必要がある

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7.1 "OS ディスク" が暗号化されていることを確認する

このブループリントでは、仮想マシンでディスク暗号化が有効になっていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- 仮想マシンでディスク暗号化を適用する必要がある

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7.2 "データ ディスク" が暗号化されていることを確認する

このブループリントでは、仮想マシンでディスク暗号化が有効になっていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- 仮想マシンでディスク暗号化を適用する必要がある

## <a name="73-ensure-that-unattached-disks-are-encrypted"></a>7.3 "アタッチされていないディスク" が暗号化されていることを確認する

このブループリントでは、アタッチされていないディスクが暗号化されていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- アタッチされていないディスクを暗号化する必要がある

## <a name="74-ensure-that-only-approved-extensions-are-installed"></a>7.4 承認済みの拡張機能のみがインストールされていることを確認する

このブループリントでは、承認済みの仮想マシン拡張機能のみがインストールされていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。 このポリシーでは、承認済みのすべての仮想マシン拡張機能を指定するパラメーター配列が必須です。 このポリシーのイニシアティブ定義には、顧客が検証する必要のある、推奨される既定値が含まれています。 

 - 承認済みの VM 拡張機能のみがインストールされている必要がある

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7.5 すべての仮想マシンの最新の OS 修正プログラムが適用されていることを確認する

このブループリントでは、システムの更新プログラムが仮想マシンにインストールされていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- システム更新プログラムをマシンにインストールする必要がある

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7.6 すべての仮想マシンの Endpoint Protection がインストールされていることを確認する

このブループリントでは、仮想マシンでエンドポイント保護が有効になっていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- Endpoint Protection の欠落の Azure Security Center での監視

## <a name="84-ensure-the-key-vault-is-recoverable"></a>8.4 キー コンテナーが回復可能であることを確認する

このブループリントでは、誤削除があった場合でもキー コンテナー オブジェクトが回復可能であることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- キー コンテナー オブジェクトが回復可能でなければならない

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8.5 Azure Kubernetes サービス内でロールベースのアクセス制御 (RBAC) を有効にする

このブループリントでは、Kubernetes サービス クラスター内でのアクセス許可を管理するためにロールベースのアクセス制御が使用されていることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- \[プレビュー\]:Kubernetes サービスでロールベースのアクセス制御 (RBAC) を使用する必要がある

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9.2 Web アプリがすべての HTTP トラフィックを Azure App Service の HTTPS にリダイレクトすることを確認する

このブループリントでは、セキュリティで保護された接続を介してのみ Web アプリケーションにアクセスできることの確認に役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- Web アプリケーションには HTTPS を介してのみアクセスできるようにする

## <a name="next-steps"></a>次の手順

CIS Microsoft Azure Foundations Benchmark ブループリントのコントロール マッピングを確認したので、次の記事にアクセスしてこのブループリントについて学習するか、Azure portal の Azure Policy にアクセスしてイニシアティブを割り当ててください。

> [!div class="nextstepaction"]
> [CIS Microsoft Azure Foundations Benchmark ブループリント - 概要](./index.md)
> [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../../how-to/update-existing-assignments.md)方法を参照する。