---
title: カナダ連邦の PBMM ブループリント サンプルのコントロール
description: カナダ連邦の PBMM ブループリント サンプルのコントロール マッピング。 それぞれのコントロールは、評価を支援する 1 つまたは複数の Azure Policy 定義に対応します。
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: 440b07db514d8af0cf7f4177a6bac497eead2b37
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627216"
---
# <a name="control-mapping-of-the-canada-federal-pbmm-blueprint-sample"></a>カナダ連邦の PBMM ブループリント サンプルのコントロール マッピング

次の記事では、Azure Blueprints のカナダ連邦 Protected B, Medium Integrity, Medium Availability (PBMM) のブループリント サンプルを、カナダ連邦の PBMM コントロールにマップする方法について詳しく説明します。 コントロールの詳細については、[カナダ連邦 PBMM](https://www.canada.ca/en/government/system/digital-government/digital-government-innovations/cloud-services/government-canada-security-control-profile-cloud-based-it-services.html) を参照してください。

以下のマッピングは、**カナダ連邦 PBMM** コントロールに対するものです。 右側のナビゲーションを使用すると、特定のコントロール マッピングに直接ジャンプできます。 マップ コントロールの多くは、[Azure Policy](../../../policy/overview.md) イニシアチブを使用して実装されますす。 イニシアチブの詳細を確認するには、Azure portal で **[ポリシー]** を開き、 **[定義]** ページを選択します。 続いて、次を探して選択します: **[\[Preview\]:Audit Canada Federal PBMM controls]\(プレビュー: カナダ連邦 PBMM コントロールの監査\)** 組み込みポリシー イニシアティブ。

> [!IMPORTANT]
> 以下の各コントロールは、1 つ以上の [Azure Policy](../../../policy/overview.md) 定義に関連します。 これらのポリシーは、コントロールに対する[コンプライアンスの評価](../../../policy/how-to/get-compliance-data.md)に役立つ場合があります。ただし、多くの場合、コントロールと 1 つ以上のポリシーとの間には、一対一での一致、または完全な一致はありません。 そのため、Azure Policy での **準拠** は、ポリシー自体のみを指しています。これによって、コントロールのすべての要件に完全に準拠していることが保証されるわけではありません。 また、コンプライアンス標準には、現時点でどの Azure Policy 定義でも対応されていないコントロールが含まれています。 したがって、Azure Policy でのコンプライアンスは、全体のコンプライアンス状態の部分的ビューでしかありません。 このコンプライアンス ブループリント サンプルのコントロールと Azure Policy 定義の間の関連付けは、時間の経過と共に変わる可能性があります。 変更履歴を表示するには、[GitHub のコミット履歴](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/canada-federal-pbmm/control-mapping.md)に関するページを参照してください。

## <a name="location-constraints"></a>場所の制約

このブループリントは、以下の Azure Policy 定義を割り当てることによって、すべてのリソースとリソース グループをデプロイする場所を "カナダ中部" と "カナダ東部" に制限するのに役立ちます。

- 許可された場所 ("カナダ中部" および "カナダ東部" にハードコーディングされている)
- リソース グループに許可された場所 ("カナダ中部" および "カナダ東部" にハードコーディングされている)

## <a name="ac-2-account-management"></a>AC-2 アカウント管理

このブループリントは、組織のアカウント管理の要件に準拠していない可能性のあるアカウントの確認を支援するものです。 このブループリントでは、サブスクリプションに対して読み取り、書き込み、所有者のアクセス許可を持つ外部アカウントと、非推奨アカウントを監査するための [Azure Policy](../../../policy/overview.md) 定義が割り当てられます。 これらのポリシーによって監査されるアカウントを確認することによって、適切な措置を講じてアカウント管理の要件を満たすことが可能になります。

- 非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある
- 読み取りアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある
- 書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある


## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) アカウント管理 | ロールベースのスキーム

Azure では、Azure のリソースにアクセスするユーザーを効果的に管理できるように、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../../../role-based-access-control/overview.md) が実装されています。 Azure リソースにできるユーザーとそのアクセス許可は、Azure portal を使用して確認できます。 また、このブループリントでは、SQL Server と Service Fabric に対する Azure Active Directory 認証の使用状況を監査するための [Azure Policy](../../../policy/overview.md) 定義も割り当てられます。 Azure Active Directory 認証を使用すると、アクセス許可の管理を簡単にし、データベース ユーザーとその他の Microsoft サービスの ID を一元管理できます。 さらに、このブループリントでは、カスタム Azure RBAC 規則の使用状況を監査するための Azure Policy 定義が割り当てられます。 カスタム Azure RBAC 規則ではエラーが発生しやすいため、カスタム Azure RBAC 規則の実装状況を把握しておくと、実装の必要性や適切性の確認に役立ちます。

- SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある
- Service Fabric クラスターは、クライアント認証に Azure Active Directory だけを使用する必要がある

## <a name="ac-4-information-flow-enforcement"></a>AC-4 情報フローの適用

クロス オリジン リソース共有 (CORS) を使うと、外部ドメインから App Services リソースを要求できるようになります。 Microsoft では、API、関数、Web アプリケーションの操作に必要なドメインのみを許可することをお勧めしています。 このブループリントは、Azure Security Center 内で CORS リソースに関するアクセス制限を監視するうえで役立つ [Azure Policy](../../../policy/overview.md) 定義を 1 件割り当てるものです。 CORS の実装を理解することは、情報フローのコントロールが実装できていることを確認するのに役立ちます。

- CORS で、Web アプリケーションへのアクセスをすべてのリソースには許可しない

## <a name="ac-5-separation-of-duties"></a>AC-5 職務の分離

Azure サブスクリプションの所有者を 1 人しか設定しなかった場合、管理の冗長性は確保されません。 逆に、Azure サブスクリプションの所有者が多すぎると、所有者アカウントが侵害されてセキュリティ侵害が発生するリスクが高まります。 このブループリントでは、Azure サブスクリプションの所有者数を監査する [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、Azure サブスクリプションの所有者を適切な数に維持することができます。 また、このブループリントでは、Windows 仮想マシン上の Administrators グループのメンバーシップを管理するうえで役立つ Azure Policy 定義が割り当てられます。 サブスクリプション所有者と仮想マシン管理者のアクセス許可を管理することで、職務を適切に分離することができます。

- 最大 3 人の所有者をサブスクリプションに対して指定する必要がある
- 複数の所有者がサブスクリプションに割り当てられている必要がある
- Administrators グループ内に指定されたメンバーが存在する Windows マシンを監査する
- Administrators グループ内の指定されたメンバーが欠けている Windows マシンを監査する

## <a name="ac-6-least-privilege"></a>AC-6 最小限の特権

Azure では、Azure のリソースにアクセスするユーザーを効果的に管理できるように、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../../../role-based-access-control/overview.md) が実装されています。 Azure リソースにできるユーザーとそのアクセス許可は、Azure portal を使用して確認できます。 このブループリントでは、優先的に確認する必要があるアカウントを監査するための [Azure Policy](../../../policy/overview.md) 定義が割り当てられます。 これらのアカウント インジケーターを確認すれば、最小限の特権コントロールが実装されているかどうかを確かめることができます。

- 最大 3 人の所有者をサブスクリプションに対して指定する必要がある
- 複数の所有者がサブスクリプションに割り当てられている必要がある
- Administrators グループ内に指定されたメンバーが存在する Windows マシンを監査する
- Administrators グループ内の指定されたメンバーが欠けている Windows マシンを監査する

## <a name="ac-7-security-attributes"></a>AC-7 セキュリティ属性

Azure SQL Database 用の高度なデータ セキュリティであるデータ検出および分類機能を使用すると、データベース内の機密データを検出、分類、ラベル付け、および保護することができます。 データベースの分類の状態を把握し、データベース内やその境界を越えて機密データへのアクセスを追跡するために使用できます。 Advanced Data Security は、情報が組織の適切なセキュリティ属性に関連付けられていることを確認するために役立ちます。 このブループリントでは、SQL サーバーに対する Advanced Data Security の使用を監視および強制する [Azure Policy](../../../policy/overview.md) 定義が割り当てられます。

- SQL マネージド インスタンスで Advanced Data Security を有効にする必要がある
- Advanced Data Security を、SQL サーバー上で有効にする必要がある
- SQL Server に対する Advanced Data Security のデプロイ

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) リモート アクセス | 自動監視および制御

このブループリントは、Azure App Service アプリケーションのリモート デバッグがオフになっていることを監視するための [Azure Policy](../../../policy/overview.md) 定義を割り当てることによってリモート アクセスの監視と制御を支援するものです。 また、パスワードなしでアカウントからのリモート接続を許可する Linux 仮想マシンを監査するポリシー定義も、このブループリントによって割り当てられます。 さらに、このブループリントでは、ストレージ アカウントに対する無制限のアクセスの監視に役立つ Azure Policy 定義も 1 件割り当てられます。 これらのインジケーターを監視すれば、リモート アクセスの方式がセキュリティ ポリシーに従っているかどうかを確かめることができます。

- パスワードなしのアカウントからのリモート接続が許可されている Linux VM の監査結果を表示する
- ストレージ アカウントではネットワーク アクセスを制限する必要がある
- API アプリでリモート デバッグを無効にする必要がある
- 関数アプリでリモート デバッグを無効にする必要がある
- Web アプリケーションのリモート デバッグを無効にする

## <a name="au-3-2-content-of-audit-records"></a>AU-3 (2) 監査レコードの内容

Azure Monitor で収集されたログ データは、Log Analytics ワークスペースに保存されるので、集中的に構成と管理が可能です。 このブループリントは、Azure 仮想マシンに対する Log Analytics エージェントのデプロイを監査および強制するための [Azure Policy](../../../policy/overview.md) 定義を割り当てることによって、イベントのログ記録の徹底を支援するものです。

- \[プレビュー\]:Audit Log Analytics エージェントのデプロイ - 一覧にない VM イメージ (OS)
- Virtual Machine Scale Sets における Log Analytics エージェントのデプロイの監査 - 一覧にない VM イメージ (OS)
- VM 用 Audit Log Analytics ワークスペース - 不一致の報告
- \[プレビュー\]:Linux VM への Log Analytics エージェントのデプロイ
- \[プレビュー\]:Windows VM への Log Analytics エージェントのデプロイ

## <a name="au-5-response-to-audit-processing-failures"></a>AU-5 監査処理エラーへの対応

このブループリントは、監査とイベントのログ記録の構成を監視するための [Azure Policy](../../../policy/overview.md) 定義を割り当てるものです。 これらの構成を監視することは、監査システムの障害や構成ミスを発見したり、是正措置を講じたりするうえで役立ちます。

- 診断設定の監査
- SQL Server の監査を有効にする必要があります
- SQL マネージド インスタンスで Advanced Data Security を有効にする必要がある
- Advanced Data Security を、SQL サーバー上で有効にする必要がある

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) 監査の確認、分析、およびレポート | 集中的な確認と分析

Azure Monitor で収集されたログ データは、Log Analytics ワークスペースに保存されるので、集中的にレポートと分析が可能です。 このブループリントは、Azure 仮想マシンに対する Log Analytics エージェントのデプロイを監査および強制するための [Azure Policy](../../../policy/overview.md) 定義を割り当てることによって、イベントのログ記録の徹底を支援するものです。

- \[プレビュー\]:Audit Log Analytics エージェントのデプロイ - 一覧にない VM イメージ (OS)
- Virtual Machine Scale Sets における Log Analytics エージェントのデプロイの監査 - 一覧にない VM イメージ (OS)
- VM 用 Audit Log Analytics ワークスペース - 不一致の報告
- \[プレビュー\]:Linux VM への Log Analytics エージェントのデプロイ
- \[プレビュー\]:Windows VM への Log Analytics エージェントのデプロイ

## <a name="au-12-audit-generation"></a>AU-12 監査の生成

このブループリントでは、Azure リソースのログ設定を監査する [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、システム イベントのログ記録の徹底を支援します。
これらのポリシー定義では、Azure 仮想マシンにおける Log Analytics エージェントのデプロイのほか、他の Azure リソースの監査設定の構成が監査および実施されます。 また、これらのポリシー定義では、診断ログの構成も監査され、Azure リソース内で実行された処理に関する分析情報が提供されます。 さらに、SQL サーバーには監査と Advanced Data Security が構成されます。

- \[プレビュー\]:Audit Log Analytics エージェントのデプロイ - 一覧にない VM イメージ (OS)
- Virtual Machine Scale Sets における Log Analytics エージェントのデプロイの監査 - 一覧にない VM イメージ (OS)
- VM 用 Audit Log Analytics ワークスペース - 不一致の報告

- \[プレビュー\]:Linux VM への Log Analytics エージェントのデプロイ
- \[プレビュー\]:Windows VM への Log Analytics エージェントのデプロイ
- 診断設定の監査
- SQL Server の監査を有効にする必要があります
- SQL マネージド インスタンスで Advanced Data Security を有効にする必要がある
- Advanced Data Security を、SQL サーバー上で有効にする必要がある
- SQL Server に対する Advanced Data Security のデプロイ
- SQL Server での監査のデプロイ
- ネットワーク セキュリティ グループの診断設定のデプロイ

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) 最小限の機能 | 承認されたソフトウェアまたはホワイトリスト登録

Azure Security Center で提供される適応型アプリケーション制御は、仮想マシン上で特定のソフトウェアの実行をブロックまたは防止できる、自動化されたインテリジェントなエンドツーエンドのアプリケーション許可リスト ソリューションです。 アプリケーション制御は、仮想マシンについて承認済みのアプリケーションの一覧を作成するうえで役立ちます。 このブループリントは、アプリケーションの許可リストが推奨されるものの、その構成がまだ済んでいない仮想マシンを監視するうえで役立つ [Azure Policy](../../../policy/overview.md) 定義を 1 件割り当てるものです。

- 安全なアプリケーションの定義のために適応型アプリケーション制御をマシンで有効にする必要がある

## <a name="cm-11-user-installed-software"></a>CM-11 ユーザーがインストールするソフトウェア

Azure Security Center で提供される適応型アプリケーション制御は、仮想マシン上で特定のソフトウェアの実行をブロックまたは防止できる、自動化されたインテリジェントなエンドツーエンドのアプリケーション許可リスト ソリューションです。 アプリケーション制御は、ソフトウェア制限ポリシーに対するコンプライアンスを強制および監視するうえで役立ちます。 このブループリントは、アプリケーションの許可リストが推奨されるものの、その構成がまだ済んでいない仮想マシンを監視するうえで役立つ [Azure Policy](../../../policy/overview.md) 定義を 1 件割り当てるものです。

- 安全なアプリケーションの定義のために適応型アプリケーション制御をマシンで有効にする必要がある

## <a name="cp-7-alternate-processing-site"></a>CP-7 代替処理サイト

Azure Site Recovery では、仮想マシンで実行中のワークロードがプライマリ ロケーションからセカンダリ ロケーションにレプリケートされます。 プライマリ サイトで障害が発生した場合は、ワークロードがセカンダリ ロケーションにフェールオーバーされます。 このブループリントは、ディザスター リカバリーが構成されていない仮想マシンを監査する [Azure Policy](../../../policy/overview.md) 定義を 1 件割り当てるものです。 このインジケーターを監視することは、必要なコンティンジェンシー コントロールが備わっているかどうかを確認するのに役立ちます。

- ディザスター リカバリーが構成されていない仮想マシンの監査

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) 識別と認証 (組織のユーザー) | 特権アカウントへのネットワーク アクセス

このブループリントは、所有者アクセス許可や書き込みアクセス許可を持ち、かつ多要素認証が有効になっていないアカウントを監査するための [Azure Policy](../../../policy/overview.md) 定義を割り当てることによって、特権アクセスの制限と制御を支援するものです。 多要素認証は、1 つの認証情報が侵害された場合でも、アカウントのセキュリティが維持されるようにするために役立ちます。 多要素認証が有効になっていないアカウントを監視することで、侵害される可能性が高いアカウントを特定することができます。

- サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある
- サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある

## <a name="ia-5-authenticator-management"></a>IA-5 認証子の管理

このブループリントは、パスワードなしでアカウントからのリモート接続を許可する Linux 仮想マシンや、passwd ファイルに誤ったアクセス許可が設定されている Linux 仮想マシンを監査する [Azure Policy](../../../policy/overview.md) 定義を割り当てるものです。 また、このブループリントでは、Windows 仮想マシンのパスワード暗号化の種類の構成を監査するポリシー定義も割り当てられます。 これらのインジケーターを監視することは、システム認証子が ID と認証に関する組織のポリシーに準拠しているかどうかの確認に役立ちます。

- passwd ファイルのアクセス許可が 0644 に設定されていない Linux VM の監査結果を表示する
- パスワードなしのアカウントが存在する Linux VM の監査結果を表示する

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) 認証子の管理 | パスワードベースの認証

このブループリントは、最小強度やその他のパスワード要件が適用されていない Windows 仮想マシンを監査する [Azure Policy](../../../policy/overview.md) 定義を割り当てることにより、強力なパスワードの強制を支援するものです。 パスワード強度のポリシーに違反している仮想マシンを把握できるようになるので、適切な是正措置を実施し、すべての仮想マシンのユーザー アカウントに対して、組織のパスワード ポリシーへの準拠を徹底させることができます。

- 以前の 24 個のパスワードの再利用が許可されている Windows VM の監査結果を表示する
- パスワードの最長有効期間が 70 日になっていない Windows VM の監査結果を表示する
- パスワードの最短有効期間が 1 日になっていない Windows VM の監査結果を表示する
- パスワードの複雑さの設定が有効になっていない Windows VM の監査結果を表示する
- パスワードの最小文字数が 14 文字に制限されていない Windows VM の監査結果を表示する

## <a name="ia-8-100-identification-and-authentication-non-organizational-users--identity-and-credential-assurance-levels"></a>IA-8 (100) 識別と認証 (組織外のユーザー) | ID および資格情報保証のレベル

このブループリントは、所有者アクセス許可や書き込みアクセス許可を持ち、かつ多要素認証が有効になっていないアカウントを監査するための [Azure Policy](../../../policy/overview.md) 定義を割り当てることによって、特権アクセスの制限と制御を支援するものです。 多要素認証は、1 つの認証情報が侵害された場合でも、アカウントのセキュリティが維持されるようにするために役立ちます。 多要素認証が有効になっていないアカウントを監視することで、侵害される可能性が高いアカウントを特定することができます。

- サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある
- サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある

## <a name="ra-5-vulnerability-scanning"></a>RA-5 脆弱性のスキャン

このブループリントは、オペレーティング システムの脆弱性、SQL の脆弱性、仮想マシンの脆弱性を Azure Security Center で監視する [Azure Policy](../../../policy/overview.md) 定義を割り当てることによって、情報システムの脆弱性管理を支援するものです。
Azure Security Center では、デプロイされた Azure リソースのセキュリティ状態をリアルタイムに分析するためのレポート機能が提供されます。 このブループリントでは、他にも、SQL サーバー上で Advanced Data Security を監査および強制するポリシー定義が割り当てられます。 Advanced Data Security には、脆弱性の評価機能と高度な脅威に対する保護機能が含まれており、デプロイ済みのリソースに存在する脆弱性について理解を深めるうえで役立ちます。

- SQL マネージド インスタンスで Advanced Data Security を有効にする必要がある
- Advanced Data Security を、SQL サーバー上で有効にする必要がある
- SQL Server に対する Advanced Data Security のデプロイ
- 仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある
- 使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある
- SQL データベースの脆弱性を修復する必要がある
- 脆弱性評価ソリューションによって脆弱性を修復する必要がある

## <a name="sc-5-denial-of-service-protection"></a>SC-5 サービス拒否の防止

Azure の分散型サービス拒否 (DDoS) Standard レベルでは、Basic サービス レベルに加えて追加の機能と緩和機能が提供されます。 追加の機能には、Azure Monitor の統合や攻撃後の緩和レポートの閲覧機能が含まれます。 このブループリントは、DDoS Standard レベルが有効になっているかどうかを監査する [Azure Policy](../../../policy/overview.md) 定義を 1 件割り当てるものです。 サービス レベル間の機能の違いを理解しておくと、Azure 環境をサービス拒否から守るうえで最適なソリューションを選択する際に役立ちます。

- DDoS Protection Standard を有効にする必要がある

## <a name="sc-7-boundary-protection"></a>SC-7 境界保護

このブループリントは、Azure Security Center でネットワーク セキュリティ グループの強化された推奨事項を監視する [Azure Policy](../../../policy/overview.md) 定義を割り当てることによって、システム境界の管理と統制を支援するものです。 Azure Security Center では、インターネットに接続している仮想マシンのトラフィック パターンが分析され、ネットワーク セキュリティ グループのルールに関連して攻撃を受ける危険性の抑制に役立つ推奨事項が提示されます。 また、このブルー プリントでは、保護されていないエンドポイント、アプリケーション、ストレージ アカウントを監視するポリシー定義も割り当てられます。 ファイアウォールで保護されていないエンドポイントやアプリケーションがあったり、アクセス制限のないストレージ アカウントがあると、情報システム内の情報に対する意図しないアクセスが許可される恐れがあります。

- アダプティブ ネットワーク強化の推奨事項をインターネット接続仮想マシンに適用する必要がある
- インターネットに接続するエンドポイント経由のアクセスを制限する必要がある
- ストレージ アカウントではネットワーク アクセスを制限する必要がある

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) 境界保護 | アクセス ポイント

Just-In-Time (JIT) の仮想マシン アクセスでは、Azure 仮想マシンへのインバウンド トラフィックがロックダウンされるので、必要な場合に VM に簡単に接続できる状態を保ちつつ、攻撃に対する露出を減らすことができます。 JIT の仮想マシン アクセスを使うと、Azure 内のリソースに対する外部接続の数を制限できます。 このブループリントは、Just-In-Time のアクセスをサポートできるものの、その構成がまだ済んでいない仮想マシンを監視するうえで役立つ [Azure Policy](../../../policy/overview.md) 定義を 1 件割り当てるものです。

- Azure DDoS Protection Standard を有効にする必要がある

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) 境界保護 | 外部通信サービス

Just-In-Time (JIT) の仮想マシン アクセスでは、Azure 仮想マシンへのインバウンド トラフィックがロックダウンされるので、必要な場合に VM に簡単に接続できる状態を保ちつつ、攻撃に対する露出を減らすことができます。 JIT の仮想マシン アクセスを使うと、アクセスの要求と承認のプロセスの労力が少なくなるので、トラフィック フロー ポリシーの例外を管理しやすくなります。 このブループリントは、Just-In-Time のアクセスをサポートできるものの、その構成がまだ済んでいない仮想マシンを監視するうえで役立つ [Azure Policy](../../../policy/overview.md) 定義を 1 件割り当てるものです。

- Azure DDoS Protection Standard を有効にする必要がある

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>SC-8 (1) 送信の機密性と整合性 | 暗号化または代替の物理的保護

このブループリントは、通信プロトコル用に実装された暗号化メカニズムを監視するのに役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、送信された情報の機密性と整合性を保護するのに役立ちます。 通信が適切に暗号化されていることを確認することで、組織の要件を満たしたり、承認されていない開示や変更から情報を保護したりできます。

- API アプリには HTTPS を介してのみアクセスできるようにする
- セキュリティで保護された通信プロトコルを使用していない Windows Web サーバーからの監査結果を表示する
- Function App には HTTPS 経由でのみアクセスできるようにする
- Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある
- Web アプリケーションには HTTPS を介してのみアクセスできるようにする
- ストレージ アカウントへの安全な転送を有効にする必要がある

## <a name="sc-28-1-protection-of-information-at-rest"></a>SC-28 (1) 保存情報の保護

このブループリントは、特定の暗号化コントロールを適用し、脆弱な暗号化設定の使用を監査する [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、保存情報の保護のための暗号化コントロールの使用に関するポリシーの実施を支援するものです。 最適でない暗号化構成が Azure リソースのどこに存在しているかを把握することにより、適切な是正措置を実施し、リソースの構成を情報セキュリティ ポリシーに準拠させることができます。 具体的には、このブループリントにより割り当てられるポリシー定義では、Data Lake Storage アカウントの暗号化と SQL データベースでの Transparent Data Encryption が必須になるほか、SQL データベース、仮想マシン ディスク、Automation アカウント変数の暗号化に漏れがないかどうかが監査されます。

- SQL マネージド インスタンスで Advanced Data Security を有効にする必要がある
- Advanced Data Security を、SQL サーバー上で有効にする必要がある
- SQL Server に対する Advanced Data Security のデプロイ
- SQL DB Transparent Data Encryption のデプロイ
- 仮想マシンでディスク暗号化を適用する必要がある
- Data Lake Store アカウントの暗号化を要求する
- SQL データベースで Transparent Data Encryption を有効にする必要がある

## <a name="si-2-flaw-remediation"></a>SI-2 欠陥の修復

このブループリントは、未実行のシステム更新、オペレーティング システムの脆弱性、SQL の脆弱性、仮想マシンの脆弱性を Azure Security Center で監視する [Azure Policy](../../../policy/overview.md) 定義を割り当てることによって、情報システムの欠陥管理を支援するものです。 Azure Security Center では、デプロイされた Azure リソースのセキュリティ状態をリアルタイムに分析するためのレポート機能が提供されます。 また、このブループリントでは、仮想マシン スケール セットのオペレーティング システムでの修正プログラムを確実に適用するためのポリシー定義が割り当てられます。

- Virtual Machine Scale Sets 上で OS イメージの修正プログラムの自動適用を必要とする
- 仮想マシン スケール セットにシステムの更新プログラムをインストールする必要がある
- システム更新プログラムをマシンにインストールする必要がある
- 仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある
- 使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある
- SQL データベースの脆弱性を修復する必要がある
- 脆弱性評価ソリューションによって脆弱性を修復する必要がある

## <a name="si-3-malicious-code-protection"></a>SI-3 悪意のあるコードからの保護

このブループリントは、Azure Security Center 内で仮想マシン上にエンドポイント保護が不足していないかどうかを監視し、Windows 仮想マシンに Microsoft のマルウェア対策ソリューションを適用する [Azure Policy](../../../policy/overview.md) 定義を割り当てることによって、悪意のあるコードからの保護を含めたエンドポイント保護の管理を支援するものです。

- Windows Server 用の既定の Microsoft IaaSAntimalware 拡張機能のデプロイ
- エンドポイント保護ソリューションを仮想マシン スケール セットにインストールする必要がある
- Endpoint Protection の欠落の Azure Security Center での監視

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) 悪意のあるコードからの保護 | 一元的な管理

このブループリントは、Azure Security Center 内で仮想マシン上にエンドポイント保護が不足していないかどうかを監視する [Azure Policy](../../../policy/overview.md) 定義を割り当てることによって、悪意のあるコードからの保護を含めたエンドポイント保護の管理を支援するものです。 Azure Security Center では、デプロイ済みの Azure リソースのセキュリティ状態をリアルタイムに分析するための一元管理とレポートの機能が提供されます。

- エンドポイント保護ソリューションを仮想マシン スケール セットにインストールする必要がある
- Endpoint Protection の欠落の Azure Security Center での監視

## <a name="si-4-information-system-monitoring"></a>SI-4 情報システムの監視

このブループリントは、さまざまな Azure リソースを対象にログ記録とデータ セキュリティを監査および適用することによって、システムの監視を支援するものです。 具体的には、割り当てられるポリシーによって、Log Analytics エージェントのデプロイが監査および実施されるほか、SQL データベース、ストレージ アカウント、ネットワーク リソースの高度なセキュリティ設定が監査および適用されます。 これらの機能は、異常な動作や攻撃の兆候の検出に役立つので、適切な措置を講じることができるようになります。

- \[プレビュー\]:Audit Log Analytics エージェントのデプロイ - 一覧にない VM イメージ (OS)
- Virtual Machine Scale Sets における Log Analytics エージェントのデプロイの監査 - 一覧にない VM イメージ (OS)
- VM 用 Audit Log Analytics ワークスペース - 不一致の報告
- \[プレビュー\]:Linux VM への Log Analytics エージェントのデプロイ
- \[プレビュー\]:Windows VM への Log Analytics エージェントのデプロイ
- SQL マネージド インスタンスで Advanced Data Security を有効にする必要がある
- Advanced Data Security を、SQL サーバー上で有効にする必要がある
- SQL Server に対する Advanced Data Security のデプロイ
- ストレージ アカウントに対する Advanced Threat Protection のデプロイ
- SQL Server での監査のデプロイ
- 仮想ネットワーク作成時の Network Watcher のデプロイ
- SQL Server での脅威検出のデプロイ

> [!NOTE]
> 特定の Azure Policy 定義を利用できるかどうかは、Azure Government とその他の National Clouds で異なる場合があります。 

## <a name="next-steps"></a>次のステップ

カナダ連邦の PBMM ブループリント サンプルのコントロール マッピングを確認しました。 次に、概要と、このサンプルをデプロイする方法を確認するには、次の記事を参照してください。

> [!div class="nextstepaction"]
> [カナダ連邦の PBMM ブループリント - 概要](./control-mapping.md)
> [カナダ連邦の PBMM ブループリント - デプロイ手順](./deploy.md)

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../../how-to/update-existing-assignments.md)方法を参照する。
