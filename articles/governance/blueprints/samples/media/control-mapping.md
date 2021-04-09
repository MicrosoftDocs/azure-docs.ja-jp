---
title: メディア ブループリント サンプル コントロール
description: メディア ブループリント サンプルのコントロール マッピング。 それぞれのコントロールは、評価を支援する 1 つまたは複数の Azure Policy 定義に対応します。
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: e1356acfd7b6b9693322000077b221781b58681f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98035038"
---
# <a name="control-mapping-of-the-media-blueprint-sample"></a>メディア ブループリント サンプルのコントロール マッピング

以下の記事は、Azure Blueprints メディア ブループリント サンプルがメディア コントロールにどのようにマップされているかを説明したものです。 コントロールの詳細については、[メディア](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)に関するページを参照してください。

以下のマッピングは、**メディア** コントロールに対するものです。 右側のナビゲーションを使用すると、特定のコントロール マッピングに直接ジャンプできます。 マップ コントロールの多くは、[Azure Policy](../../../policy/overview.md) イニシアチブを使用して実装されますす。 イニシアチブの詳細を確認するには、Azure portal で **[ポリシー]** を開き、 **[定義]** ページを選択します。 続いて、次を探して選択します: **[\[Preview\]:Audit Media controls** built-in policy initiative]\(プレビュー: メディア コントロール ビルトイン ポリシー イニシアチブの監査\)。

> [!IMPORTANT]
> 以下の各コントロールは、1 つ以上の [Azure Policy](../../../policy/overview.md) 定義に関連します。 これらのポリシーは、コントロールに対する[コンプライアンスの評価](../../../policy/how-to/get-compliance-data.md)に役立つ場合があります。ただし、多くの場合、コントロールと 1 つ以上のポリシーとの間には、一対一での一致、または完全な一致はありません。 そのため、Azure Policy での **準拠** は、ポリシー自体のみを指しています。これによって、コントロールのすべての要件に完全に準拠していることが保証されるわけではありません。 また、コンプライアンス標準には、現時点でどの Azure Policy 定義でも対応されていないコントロールが含まれています。 したがって、Azure Policy でのコンプライアンスは、全体のコンプライアンス状態の部分的ビューでしかありません。 このコンプライアンス ブループリント サンプルのコントロールと Azure Policy 定義の間の関連付けは、時間の経過と共に変わる可能性があります。 変更履歴を表示するには、[GitHub のコミット履歴](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/media/control-mapping.md)に関するページを参照してください。

## <a name="access-control"></a>アクセス制御

### <a name="ac-11--ensure-no-root-access-key-exists"></a>AC-1.1- ルート アクセス キーが存在しないようにする

- \[プレビュー\]:指定された証明書が信頼されたルートに含まれていない Windows VM を監査するための前提条件をデプロイする

### <a name="ac-12---passwords-pins-and-tokens-must-be-protected"></a>AC-1.2 - パスワード、PIN、トークンを保護する

- \[プレビュー\]:パスワードの最小文字数が 14 文字に制限されていない Windows VM を監査する前提条件をデプロイする

### <a name="ac-18---shared-account-access-is-prohibited"></a>AC-1.8 - 共有アカウント アクセスを禁止する

- Service Bus の名前空間から RootManageSharedAccessKey 以外のすべての承認規則を削除する必要がある

### <a name="ac-19--system-must-restrict-access-to-authorized-users"></a>AC-1.9 - 許可されているユーザーのみアクセスできるようシステムで制限する

- ストレージ アカウントに対する制限のないネットワーク アクセスの監査

### <a name="ac--114--system-must-enforce-access-rights"></a>AC- 1.14 - システムでアクセス権を強制する

- \[プレビュー\]:"ユーザー権利の割り当て" の Windows VM 構成を監査するための前提条件をデプロイする

### <a name="ac--115--prevent-unauthorized-access-to-security-relevant-information-or-functions"></a>AC- 1.15 - セキュリティ関連の情報や機能への未承認のアクセスを防止する

- \[プレビュー\]:"セキュリティ オプション - システム設定" の Windows VM 構成の監査結果を表示する

### <a name="ac-1-21---separation-of-duties-must-be-enforced-through-appropriate-assignment-of-role"></a>AC-1-21 - 適切な役割の割り当てを通じて職務権限の分離を徹底する

- [プレビュー\]: Kubernetes サービスでロールベースのアクセス制御 (RBAC) を使用する必要がある

### <a name="ac-140--ensure-that-systems-are-not-connecting-trusted-network-and-untrusted-networks-at-the-same-time"></a>AC-1.40- 信頼されたネットワークと信頼されていないネットワークにシステムが同時に接続しないようにする

- \[プレビュー\]:"セキュリティ オプション - ネットワーク アクセス" の Windows VM 構成を監査するための前提条件をデプロイする

### <a name="ac-142--ac--143---remote-access-for-non-employees-must-be-restricted-to-allow-access-only-to-specifically-approved-information-systems"></a>AC-1.42 および AC- 1.43 - 従業員以外のリモート アクセスを制限し、個別に承認された情報システムにのみアクセスを許可する

- \[プレビュー\]:パスワードなしのアカウントからのリモート接続が許可されている Linux VM の監査結果を表示する

### <a name="ac-150--log-security-related-events-for-all-information-system-components"></a>AC-1.50 - すべての情報システム コンポーネントを対象にセキュリティ関連のイベントをログする

- Logic Apps で診断ログを有効にする必要がある

### <a name="ac-154--ensure-multi-factor-authentication-mfa-is-enabled-for-all-cloud-console-users"></a>AC-1.54 - すべてのクラウド コンソール ユーザーに対して多要素認証 (MFA) を有効にする

- サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある
- アカウントまたはリソースに対する侵害を防止するために、書き込み権限を持つすべてのサブスクリプション アカウントに対して 多要素認証 (MFA) を有効にする必要があります。

## <a name="auditing--logging"></a>監査とログ

### <a name="al-21--successful-and-unsuccessful-events-must-be-logged"></a>AL-2.1 - 成功したイベントと失敗したイベントを記録する

- Search サービスにおける診断ログを有効にする必要がある

### <a name="al--216---network-devicesinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-network-deviceinstance-elbs-web-application-firewalls-etc"></a>AL -2.16 - ネットワーク デバイスまたはインスタンス (ELB、Web アプリケーション ファイアウォールなど) によって重大なセキュリティ イベントとして分類されたすべてのイベントを、そのネットワーク デバイスまたはインスタンスでログする

- \[プレビュー\]:"セキュリティ オプション - アカウント" の Windows VM 構成の監査結果を表示する

### <a name="al-217--serversinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-serverinstance"></a>AL-2.17 - サーバーまたはインスタンスによって重大なセキュリティ イベントとして分類されたすべてのイベントを、そのサーバーまたはインスタンスでログする

- \[プレビュー\]:"セキュリティ オプション - アカウント" の Windows VM 構成の監査結果を表示する

### <a name="al-219---domain-events-must-log-any-event-classified-as-a-critical-or-high-security-event-by-the-domain-management-software"></a>AL-2.19 - ドメイン管理ソフトウェアによって重大または高レベルのセキュリティ イベントとして分類されたすべてのイベントを、そのドメイン イベントでログする

- \[プレビュー\]:"セキュリティ オプション - アカウント" の Windows VM 構成の監査結果を表示する
- \[プレビュー\]:"セキュリティ オプション - Microsoft ネットワーク クライアント" の Windows VM 構成を監査するための前提条件をデプロイする

### <a name="al-220--domain-events-must-log-any-event-classified-as-a-critical-security-event-by-domain-security-controls"></a>AL-2.20 - ドメイン セキュリティ コントロールによって重大なセキュリティ イベントとして分類されたすべてのイベントを、そのドメイン イベントでログする

- \[プレビュー\]:"セキュリティ オプション - アカウント" の Windows VM 構成の監査結果を表示する

### <a name="al-221--domain-events-must-log-any-access-or-changes-to-the-domain-log"></a>AL-2.21- ドメイン ログへのすべてのアクセスまたは変更をドメイン イベントでログする

- \[プレビュー\]:"セキュリティ オプション - 回復コンソール" の Windows VM 構成の監査結果を表示する

## <a name="cryptographic-controls"></a>暗号化制御

### <a name="cc-42--applications-and-systems-must-use-current-cryptographic-solutions-for-protecting-data"></a>CC-4.2 - アプリケーションおよびシステムで、最新の暗号ソリューションを使用してデータを保護する

- SQL データベースで Transparent Data Encryption を有効にする必要がある
- 保存データを保護し、コンプライアンス要件を満たすには、Transparent Data Encryption を有効にする必要があります

### <a name="cc-45--digital-certificates-must-be-signed-by-an-approved-certificate-authority"></a>CC-4.5 - デジタル証明書には承認済みの証明機関が署名する

- \[プレビュー\]:指定した日数以内に期限切れになる証明書を含む Windows VM からの監査結果を表示する

### <a name="cc-46--digital-certificates-must-be-uniquely-assigned-to-a-user-or-device"></a>CC-4.6 - ユーザーまたはデバイスにはデジタル証明書を一意的に割り当てる

- \[プレビュー\]:指定した日数以内に期限切れになる証明書を含む Windows VM を監査するための前提条件をデプロイする

### <a name="cc-47--cryptographic-material-must-be-stored-to-enable-decryption-of-the-records-for-the-length-of-time-the-records-are-retained"></a>CC-4.7 - レコードの保持期間にわたってレコードの解読ができるように暗号化マテリアルを格納する

- 仮想マシンでディスク暗号化を適用する必要がある
- ディスク暗号化が有効になっていない VM が、推奨設定として Azure Security Center によって監視されます

### <a name="cc-48--secret-and-private-keys-must-be-stored-securely"></a>CC-4.8- シークレットと秘密キーは安全に保管する

- SQL データベースで Transparent Data Encryption を有効にする必要がある
- 保存データを保護し、コンプライアンス要件を満たすには、Transparent Data Encryption を有効にする必要があります

## <a name="change--config-management"></a>変更と構成の管理

### <a name="cm-52--only-authorized-users-may-implement-approved-changes-on-the-system"></a>CM-5.2 - 許可されているユーザーだけが、システムに対する承認済みの変更を実装できる

- システム更新プログラムをマシンにインストールする必要がある
- 使用中のサーバーにおけるセキュリティ関連のシステム更新プログラムの不足が、推奨設定として Azure Security Center で監視されます

### <a name="cm-512--maintain-an-up-to-date-complete-accurate-and-readily-available-baseline-configuration-of-the-information-system"></a>CM-5.12 - 情報システムの最新かつ完全で、すぐに使用できる正確なベースライン構成を維持する

- システム更新プログラムをマシンにインストールする必要がある
- 使用中のサーバーにおけるセキュリティ関連のシステム更新プログラムの不足が、推奨設定として Azure Security Center で監視されます

### <a name="cm-513--employ-automated-tools-to-maintain-a-baseline-configuration-of-the-information-system"></a>CM-5.13 - 情報システムのベースライン構成を維持するための自動化されたツールを採用する

- システム更新プログラムをマシンにインストールする必要がある
- 使用中のサーバーにおけるセキュリティ関連のシステム更新プログラムの不足が、推奨設定として Azure Security Center で監視されます

### <a name="cm-514--identify-and-disable-unnecessary-andor-non-secure-functions-ports-protocols-and-services"></a>CM-5.14 - 不要であるか、安全でない機能やポート、プロトコル、サービスを特定して無効にする

- ネットワーク インターフェイスで IP 転送を無効にする
- \[プレビュー\]:仮想マシンでの IP 転送を無効にする必要がある

### <a name="cm-519--monitor-changes-to-the-security-configuration-settings"></a>CM-5.19 - セキュリティ構成設定に対する変更を監視する

- ネットワーク セキュリティ グループの診断設定のデプロイ

### <a name="cm-522--ensure-that-only-authorized-software-and-updates-are-installed-on-company-systems"></a>CM-5.22 - 承認されたソフトウェアと更新プログラムだけが会社のシステムにインストールされるようにする

- システム更新プログラムをマシンにインストールする必要がある
- 使用中のサーバーにおけるセキュリティ関連のシステム更新プログラムの不足が、推奨設定として Azure Security Center で監視されます

## <a name="identity--authentication"></a>ID と認証

### <a name="ia-71--user-accounts-must-be-uniquely-assigned-to-individuals-for-access-to-information-that-is-not-classified-as-public-account-ids-must-be-constructed-using-a-standardized-logical-format"></a>IA-7.1 - パブリックとして分類されない情報にアクセスする個人には、ユーザー アカウントを一意的に割り当てる。 アカウント ID を、標準化された論理的な形式を使用して構築する。

- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある
- 監視されていないアクセスを防止するために、所有者アクセス許可を持つ外部アカウントは、サブスクリプションから削除する必要があります。

## <a name="network-security"></a>ネットワークのセキュリティ

### <a name="ns-92--access-to-network-device-management-functionality-is-restricted-to-authorized-users"></a>NS-9.2 - ネットワーク デバイスの管理機能へのアクセスを、許可されているユーザーに制限する

- \[プレビュー\]:"セキュリティ オプション - ネットワーク アクセス" の Windows VM 構成を監査するための前提条件をデプロイする

### <a name="ns-93--all-network-devices-must-be-configured-using-their-most-secure-configurations"></a>NS-9.3 - すべてのネットワーク デバイスをその最も安全な構成を使用して設定する

- \[プレビュー\]:"セキュリティ オプション - ネットワーク アクセス" の Windows VM 構成を監査するための前提条件をデプロイする

### <a name="ns-95--all-network-connections-to-a-system-through-a-firewall-must-be-approved-and-audited-on-a-regular-basis"></a>NS-9.5 - システムへのファイアウォールを介したすべてのネットワーク接続を定期的に承認、監査する

- \[プレビュー\]:"Windows ファイアウォール プロパティ" の Windows VM 構成の監査結果を表示する

### <a name="ns-97--appropriate-controls-must-be-present-at-any-boundary-between-a-trusted-network-and-any-untrusted-or-public-network"></a>NS-9.7 - 信頼されたネットワークと信頼されていないネットワーク (公衆ネットワーク) との境界にはすべて適切な制御を存在させる

- \[プレビュー\]:"Windows ファイアウォール プロパティ" の Windows VM 構成を監査するための前提条件をデプロイする

## <a name="security-planning"></a>セキュリティ計画

### <a name="sp-113--threats-must-be-identified-that-could-negatively-impact-the-confidentiality-integrity-or-availability-of-company-information-and-content-along-with-the-likelihood-of-their-occurrence"></a>SP-11.3 - 会社の情報とコンテンツの機密性、整合性、可用性に悪影響を及ぼす脅威とその発生の可能性を特定する

- SQL マネージド インスタンスの Advanced Data Security 設定で、[Advanced Threat Protection の種類] を [すべて] に設定する必要がある

### <a name="security-continuity"></a>セキュリティ継続性

## <a name="sc-125--data-in-long-term-storage-must-be-accessible-throughout-the-retention-period-and-protected-against-media-degradation-and-technology-changes"></a>SC-12.5 - 長期ストレージのデータに保持期間の最初から最後までアクセスでき、メディアの劣化やテクノロジの変化から保護された状態にする

- SQL Server では、90 日を超える監査保持期間を構成する必要がある。
- 監査期間が 90 日未満に構成されている SQL サーバーを監査します。

## <a name="system-integrity"></a>システムの整合性

### <a name="si-143--only-authorized-personnel-may-monitor-network-and-user-activities"></a>SI-14.3 - ネットワークとユーザーのアクティビティを、許可されている担当者だけが監視できる

- SQL データベースの脆弱性を修復する必要がある
- 脆弱性評価スキャン結果と、データベースの脆弱性を修正する方法についての推奨事項を監視します。

### <a name="si-144--internet-facing-systems-must-have-intrusion-detection"></a>SI-14.4 - インターネットに接続するシステムには侵入検出対策が必要

- SQL サーバーでの脅威検出のデプロイ

### <a name="si-1413--standardized-centrally-managed-anti-malware-software-should-be-implemented-across-the-company"></a>SI-14.13 - 標準化された一元管理型のマルウェア対策ソフトウェアを全社的に導入する

- Windows Server 用の既定の Microsoft IaaSAntimalware 拡張機能のデプロイ

### <a name="si-1414--anti-malware-software-must-scan-computers-and-media-weekly-at-a-minimum"></a>SI-14.14 - コンピューターとメディアを最低でも週に一度はマルウェア対策ソフトウェアでスキャンする

- Windows Server 用の既定の Microsoft IaaSAntimalware 拡張機能のデプロイ

## <a name="vulnerability-management"></a>脆弱性の管理

### <a name="vm-154--ensure-that-applications-are-scanned-for-vulnerabilities-on-a-monthly-basis"></a>VM-15.4 - アプリケーションを毎月スキャンして脆弱性がないか調べる

- 仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある
- 攻撃から保護するため、お使いの仮想マシン スケール セットの OS 脆弱性を監査します。

### <a name="vm-155--ensure-that-vulnerabilities-are-identified-paired-to-threats-and-evaluated-for-risk"></a>VM-15.5 - 脆弱性を特定し、脅威と結び付け、リスクを評価する

- 仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある
- 攻撃から保護するため、お使いの仮想マシン スケール セットの OS 脆弱性を監査します。

### <a name="vm-156--ensure-that-identified-vulnerabilities-have-been-remediated-within-a-mutually-agreed-upon-timeline"></a>VM-15.6 - 特定された脆弱性を、相互に合意したタイムライン内で修正する

- 仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある
- 攻撃から保護するため、お使いの仮想マシン スケール セットの OS 脆弱性を監査します。

### <a name="vm-157--access-to-and-use-of-vulnerability-management-systems-must-be-restricted-to-authorized-personnel"></a>VM-15.7 - 脆弱性管理システムへのアクセスとその使用を、許可されている担当者に制限する

- 仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある
- 攻撃から保護するため、お使いの仮想マシン スケール セットの OS 脆弱性を監査します。

> [!NOTE]
> 特定の Azure Policy 定義を利用できるかどうかは、Azure Government とその他の National Clouds で異なる場合があります。 

## <a name="next-steps"></a>次のステップ

ここではメディア ブループリント サンプルのコントロール マッピングを確認しました。 次に、概要と、このサンプルをデプロイする方法を確認するには、次の記事を参照してください。

> [!div class="next step action"]
> [メディア ブループリント - コントロール マッピング](./control-mapping.md)
> [メディア ブループリント - デプロイ手順](./deploy.md)

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../../how-to/update-existing-assignments.md)方法を参照する。
