---
title: サンプル - CIS Microsoft Azure Foundations Benchmark ブループリント - 推奨事項のマッピング
description: CIS Microsoft Azure Foundations Benchmark ブループリント サンプルと Azure Policy の推奨事項のマッピング。
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/09/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: f048262003a3567175c40ebf4ee744c41e11b5f9
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918708"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>CIS Microsoft Azure Foundations Benchmark ブループリント サンプルの推奨事項のマッピング

次の記事では、Azure Blueprints CIS Microsoft Azure Foundations Benchmark ブループリント サンプルが CIS Microsoft Azure Foundations Benchmark の推奨事項にどのようにマッピングされるかを詳しく説明しています。 推奨事項の詳細については、[CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/) に関するページを参照してください。

次のマッピングは、**CIS Microsoft Azure Foundations Benchmark v1.1.0** の推奨事項へのマッピングです。 右側のナビゲーションを使用すると、特定の推奨事項のマッピングに直接移動できます。
マッピングされた推奨事項の多くは、[Azure Policy](../../../policy/overview.md) イニシアティブを使用して実装されます。 イニシアチブの詳細を確認するには、Azure portal で **[ポリシー]** を開き、 **[定義]** ページを選択します。 その後、 **[\[Preview\] Audit CIS Microsoft Azure Foundations Benchmark v1.1.0 recommendations and deploy specific VM Extensions to support audit requirements]\([プレビュー] CIS Microsoft Azure Foundations Benchmark v1.1.0 の推奨事項を監査し、特定の VM 拡張機能をデプロイして監査要件をサポートする\)** 組み込みポリシー イニシアティブを見つけて選択します。

> [!IMPORTANT]
> 以下の各コントロールは、1 つまたは複数の [Azure Policy](../../../policy/overview.md) 定義に関連付けられています。 これらのポリシーは、コントロールに対する[コンプライアンスを評価](../../../policy/how-to/get-compliance-data.md)するのに役立つ場合があります。ただし、多くの場合、コントロールと 1 つまたは複数のポリシーとの間に 1:1 または完全な一致はありません。 そのため、Azure Policy での**準拠**は、ポリシー自体のみを指しています。これによって、コントロールのすべての要件に完全に準拠していることが保証されるわけではありません。 また、コンプライアンス標準には、現時点でどの Azure Policy 定義にもアドレス指定されていないコントロールが含まれます。 したがって、Azure Policy でのコンプライアンスは、全体のコンプライアンス状態の部分的ビューでしかありません。 このコンプライアンス ブループリント サンプルのコントロールと Azure Policy 定義の間の関連付けは、時間の経過と共に変わる可能性があります。 変更履歴を表示するには、[GitHub のコミット履歴](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md)を参照してください。

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1.1 特権のあるすべてのユーザーに対して多要素認証が有効になっていることを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある
- サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1.2 特権のないすべてのユーザーに対して多要素認証が有効になっていることを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある

## <a name="13-ensure-that-there-are-no-guest-users"></a>1.3 ゲスト ユーザーがいないことを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある
- 読み取りアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある
- 書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2.3 ASC の既定のポリシー設定 [システムの更新プログラムの監視] が [無効] になっていないことを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- システム更新プログラムをマシンにインストールする必要がある

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2.4 ASC の既定のポリシー設定 [OS 脆弱性の監視] が [無効] になっていないことを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- 使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2.5 ASC の既定のポリシー設定 [Endpoint Protection の監視] が [無効] になっていないことを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- Endpoint Protection の欠落の Azure Security Center での監視

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2.6 ASC の既定のポリシー設定 [ディスク暗号化の監視] が [無効] になっていないことを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- 仮想マシンでディスク暗号化を適用する必要がある

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2.8 ASC の既定のポリシー設定 [Web アプリケーション ファイアウォールの監視] が [無効] になっていないことを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- IaaS 上の Web アプリケーションに対する NSG 規則を強化する必要がある

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2.10 ASC の既定のポリシー設定 [脆弱性評価を監視する] が [無効] になっていないことを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- 脆弱性評価ソリューションによって脆弱性を修復する必要がある

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2.12 ASC の既定のポリシー設定 [JIT ネットワーク アクセスの監視] が [無効] になっていないことを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- 仮想マシンで Just-In-Time ネットワーク アクセス制御を適用する必要がある

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2.15 ASC の既定のポリシー設定 [SQL 暗号化の監視] が [無効] になっていないことを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- SQL データベースで Transparent Data Encryption を有効にする必要がある

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3.1 [安全な転送が必要] が [有効] に設定されていることを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- ストレージ アカウントへの安全な転送を有効にする必要がある

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3.7 ストレージ アカウントの既定のネットワーク アクセス ルールが拒否に設定されていることを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- ストレージ アカウントに対する制限のないネットワーク アクセスの監査

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4.1 [監査] が [オン] に設定されていることを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- SQL Server の高度なデータ セキュリティ設定で監査を有効にする必要がある

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4.2 SQL Server の "監査" ポリシーの "AuditActionGroups" が適切に設定されていることを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- SQL 監査設定では、重要なアクティビティをキャプチャするように Action-Groups を構成しておく必要がある

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4.3 "監査" 保持期間が "90 日を超える" ことを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- SQL Server では、90 日を超える監査保持期間を構成する必要がある。

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4.4 SQL Server の "Advanced Data Security" が [オン] に設定されていることを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- Advanced Data Security を、SQL サーバー上で有効にする必要がある

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4.5 [脅威検出の種類] が [すべて] に設定されていることを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- SQL Server の Advanced Data Security 設定で、[Advanced Threat Protection の種類] を [すべて] に設定する必要がある
- SQL マネージド インスタンスの Advanced Data Security 設定で、[Advanced Threat Protection の種類] を [すべて] に設定する必要がある

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4.6 [アラートの送信先] が設定されていることを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- SQL Server の高度なデータ セキュリティ設定に、セキュリティ アラートを受信するためのメール アドレスが含まれている必要がある

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4.7 [サービスの管理者/共同管理者に電子メールを送信] が [有効] になっていることを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- SQL マネージド インスタンスの高度なデータ セキュリティ設定に、セキュリティ アラートを受信するためのメール アドレスが含まれている必要がある

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4.8 Azure Active Directory 管理者が構成されていることを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4.9 SQL Database の [データ暗号化] が [オン] に設定されていることを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- SQL データベースで Transparent Data Encryption を有効にする必要がある

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4.10 SQL Server の TDE 保護機能が BYOK (自分のキーの使用) で暗号化されていることを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- SQL Server の TDE 保護機能を自分のキーを使用して暗号化する必要がある
- SQL マネージド インスタンスの TDE 保護機能を自分のキーを使用して暗号化する必要がある

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 Azure KeyVault のログ記録が [有効] になっていることを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- Key Vault で診断ログを有効にする必要がある

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7.1 "OS ディスク" が暗号化されていることを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- 仮想マシンでディスク暗号化を適用する必要がある

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7.2 "データ ディスク" が暗号化されていることを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- 仮想マシンでディスク暗号化を適用する必要がある

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7.5 すべての仮想マシンの最新の OS 修正プログラムが適用されていることを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- システム更新プログラムをマシンにインストールする必要がある

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7.6 すべての仮想マシンの Endpoint Protection がインストールされていることを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- Endpoint Protection の欠落の Azure Security Center での監視

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8.5 Azure Kubernetes サービス内でロールベースのアクセス制御 (RBAC) を有効にする

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

- \[プレビュー\]:Kubernetes サービスでロールベースのアクセス制御 (RBAC) を使用する必要がある

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9.2 Web アプリがすべての HTTP トラフィックを Azure App Service の HTTPS にリダイレクトすることを確認する

このブループリントでは、この CIS の推奨事項に沿った [Azure Policy](../../../policy/overview.md) 定義を割り当てます。

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