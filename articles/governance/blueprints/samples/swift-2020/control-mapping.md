---
title: SWIFT CSP-CSCF v2020 ブループリント サンプルのコントロール
description: SWIFT CSP-CSCF v2020 ブループリント サンプルのコントロール マッピング。 それぞれのコントロールは、評価を支援する 1 つまたは複数の Azure Policy 定義に対応します。
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: 466b37b287d79a891fba39b133ab5fa79dbd147e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98048795"
---
# <a name="control-mapping-of-the-swift-csp-cscf-v2020-blueprint-sample"></a>SWIFT CSP-CSCF v2020 ブループリント サンプルのコントロール マッピング

以下の記事は、Azure Blueprints SWIFT CSP-CSCF v2020 のブループリント サンプルが SWIFT CSP-CSCF v2020 のコントロールにどのようにマップされているかを説明したものです。 コントロールの詳細については、[SWIFT CSP-CSCF v2020](https://www.swift.com/myswift/customer-security-programme-csp) に関するページを参照してください。

以下のマッピングは、**SWIFT CSP-CSCF v2020** コントロールに対するものです。 右側のナビゲーションを使用すると、特定のコントロール マッピングに直接ジャンプできます。 マップ コントロールの多くは、[Azure Policy](../../../policy/overview.md) イニシアチブを使用して実装されますす。 イニシアチブの詳細を確認するには、Azure portal で **[ポリシー]** を開き、 **[定義]** ページを選択します。 続いて、次を探して選択します: **[\[Preview\]:Audit SWIFT CSP-CSCF v2020 controls and deploy specific VM Extensions to support audit requirements]\([プレビュー]: SWIFT CSP-CSCF v2020 コントロールの監査と監査要件をサポートするための特定の VM 拡張機能のデプロイ\)** ビルトイン ポリシー イニシアチブ。

> [!IMPORTANT]
> 以下の各コントロールは、1 つ以上の [Azure Policy](../../../policy/overview.md) 定義に関連します。 これらのポリシーは、コントロールに対する[コンプライアンスの評価](../../../policy/how-to/get-compliance-data.md)に役立つ場合があります。ただし、多くの場合、コントロールと 1 つ以上のポリシーとの間には、一対一での一致、または完全な一致はありません。 そのため、Azure Policy での **準拠** は、ポリシー自体のみを指しています。これによって、コントロールのすべての要件に完全に準拠していることが保証されるわけではありません。 また、コンプライアンス標準には、現時点でどの Azure Policy 定義でも対応されていないコントロールが含まれています。 したがって、Azure Policy でのコンプライアンスは、全体のコンプライアンス状態の部分的ビューでしかありません。 このコンプライアンス ブループリント サンプルのコントロールと Azure Policy 定義の間の関連付けは、時間の経過と共に変わる可能性があります。 変更履歴を表示するには、[GitHub のコミット履歴](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/swift-2020/control-mapping.md)に関するページを参照してください。

## <a name="12-and-51-account-management"></a>1.2 および 5.1 アカウント管理

このブループリントは、組織のアカウント管理の要件に準拠していない可能性のあるアカウントの確認を支援するものです。 このブループリントでは、サブスクリプションに対して読み取り、書き込み、所有者のアクセス許可を持つ外部アカウントと、非推奨アカウントを監査するための [Azure Policy](../../../policy/overview.md) 定義が割り当てられます。 これらのポリシーによって監査されるアカウントを確認することによって、適切な措置を講じてアカウント管理の要件を満たすことが可能になります。

- 非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある
- 読み取りアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある
- 書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある

## <a name="26-51-64-and-65a-account-management--role-based-schemes"></a>2.6、5.1、6.4、および 6.5A アカウント管理 | ロールベースのスキーム

[Azure ロールベースのアクセス制御 (Azure RBAC)](../../../../role-based-access-control/overview.md) を使用して、Azure のリソースにアクセスするユーザーを管理できます。 Azure リソースにできるユーザーとそのアクセス許可は、Azure portal を使用して確認できます。 また、このブループリントでは、SQL Server と Service Fabric に対する Azure Active Directory 認証の使用状況を監査するための [Azure Policy](../../../policy/overview.md) 定義も割り当てられます。 Azure Active Directory 認証を使用すると、アクセス許可の管理を簡単にし、データベース ユーザーとその他の Microsoft サービスの ID を一元管理できます。 さらに、このブループリントでは、カスタム Azure RBAC 規則の使用状況を監査するための Azure Policy 定義が割り当てられます。 カスタム Azure RBAC 規則ではエラーが発生しやすいため、カスタム Azure RBAC 規則の実装状況を把握しておくと、実装の必要性や適切性の確認に役立ちます。

- SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある
- マネージド ディスクを使用していない VM の監査
- Service Fabric クラスターは、クライアント認証に Azure Active Directory だけを使用する必要がある

## <a name="29a--account-management--account-monitoring--atypical-usage"></a>2.9A アカウント管理 | アカウントの監視および特殊な使用方法

Just-In-Time (JIT) の仮想マシン アクセスでは、Azure 仮想マシンへのインバウンド トラフィックがロックダウンされるので、必要な場合に VM に簡単に接続できる状態を保ちつつ、攻撃に対する露出を減らすことができます。 仮想マシンにアクセスするための JIT 要求はいずれもアクティビティ ログに記録されるので、特殊な利用を監視できます。 このブループリントは、Just-In-Time のアクセスをサポートできるものの、その構成がまだ済んでいない仮想マシンを監視するうえで役立つ [Azure Policy](../../../policy/overview.md) 定義を 1 件割り当てるものです。

- 仮想マシンの管理ポートは、Just-In-Time のネットワーク アクセス制御で保護する必要がある

## <a name="13-51-and-64-separation-of-duties"></a>1.3、5.1、および 6.4 職務の分離

Azure サブスクリプションの所有者を 1 人しか設定しなかった場合、管理の冗長性は確保されません。 逆に、Azure サブスクリプションの所有者が多すぎると、所有者アカウントが侵害されてセキュリティ侵害が発生するリスクが高まります。 このブループリントでは、Azure サブスクリプションの所有者数を監査する [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、Azure サブスクリプションの所有者を適切な数に維持することができます。 また、このブループリントでは、Windows 仮想マシン上の Administrators グループのメンバーシップを管理するうえで役立つ Azure Policy 定義が割り当てられます。 サブスクリプション所有者と仮想マシン管理者のアクセス許可を管理することで、職務を適切に分離することができます。

- 最大 3 人の所有者をサブスクリプションに対して指定する必要がある
- 指定されたメンバーの一部が Administrators グループに含まれていない Windows VM の監査結果を表示する
- 指定されたメンバーの一部が Administrators グループに含まれていない Windows VM を監査する前提条件をデプロイする
- 複数の所有者がサブスクリプションに割り当てられている必要がある

## <a name="13-51-and-64-least-privilege--review-of-user-privileges"></a>1.3、5.1、および 6.4 最小限の特権 | ユーザー特権の確認

[Azure ロールベースのアクセス制御 (Azure RBAC)](../../../../role-based-access-control/overview.md) を使用して、Azure のリソースにアクセスするユーザーを管理できます。 Azure リソースにできるユーザーとそのアクセス許可は、Azure portal を使用して確認できます。 このブループリントでは、優先的に確認する必要があるアカウントを監査するための [Azure Policy](../../../policy/overview.md) 定義が割り当てられます。 これらのアカウント インジケーターを確認すれば、最小限の特権コントロールが実装されているかどうかを確かめることができます。

- 最大 3 人の所有者をサブスクリプションに対して指定する必要がある
- 指定されたドメインに参加していない Windows VM からの監査結果を表示する
- 指定されたドメインに参加していない Windows VM を監査する前提条件をデプロイする
- 複数の所有者がサブスクリプションに割り当てられている必要がある

## <a name="22-and-27-security-attributes"></a>2.2 および 2.7 セキュリティ属性

Azure SQL Database 用の高度なデータ セキュリティであるデータ検出および分類機能を使用すると、データベース内の機密データを検出、分類、ラベル付け、および保護することができます。 データベースの分類の状態を把握し、データベース内やその境界を越えて機密データへのアクセスを追跡するために使用できます。 Advanced Data Security は、情報が組織の適切なセキュリティ属性に関連付けられていることを確認するために役立ちます。 このブループリントでは、SQL サーバーに対する Advanced Data Security の使用を監視および強制する [Azure Policy](../../../policy/overview.md) 定義が割り当てられます。

- Advanced Data Security を、SQL サーバー上で有効にする必要がある
- SQL サーバーに対する Advanced Data Security のデプロイ

## <a name="22-27-41-and-61-remote-access--automated-monitoring--control"></a>2.2、2.7、4.1、および 6.1 リモート アクセス | 自動監視および制御

このブループリントは、Azure App Service アプリケーションのリモート デバッグがオフになっているかどうかを監視するための [Azure Policy](../../../policy/overview.md) 定義と、パスワードなしでアカウントからのリモート接続を許可する Linux 仮想マシンを監査するポリシー定義を割り当てることによって、リモート アクセスの監視と制御を支援するものです。 また、このブループリントでは、ストレージ アカウントに対する無制限のアクセスの監視に役立つ Azure Policy 定義も 1 件割り当てられます。 これらのインジケーターを監視すれば、リモート アクセスの方式がセキュリティ ポリシーに従っているかどうかを確かめることができます。

- パスワードなしのアカウントからのリモート接続が許可されている Linux VM の監査結果を表示する
- パスワードなしのアカウントからのリモート接続が許可されている Linux VM を監査する前提条件をデプロイする
- ストレージ アカウントではネットワーク アクセスを制限する必要がある
- API アプリでリモート デバッグを無効にする
- Function App でリモート デバッグを無効にする
- Web アプリケーションのリモート デバッグを無効にする

## <a name="13-and-64-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>1.3 および 6.4 監査レコードの内容 | 計画的な監査レコードの内容の集中管理

Azure Monitor で収集されたログ データは、Log Analytics ワークスペースに保存されるので、集中的に構成と管理が可能です。 このブループリントは、Azure 仮想マシンに対する Log Analytics エージェントのデプロイを監査および強制するための [Azure Policy](../../../policy/overview.md) 定義を割り当てることによって、イベントのログ記録の徹底を支援するものです。

- \[プレビュー\]:Audit Log Analytics エージェントのデプロイ - 一覧にない VM イメージ (OS)
- Linux VM への Log Analytics エージェントのデプロイ
- Windows VM への Log Analytics エージェントのデプロイ

## <a name="22-27-and-64-response-to-audit-processing-failures"></a>2.2、2.7、および 6.4 監査処理エラーへの対応

このブループリントは、監査とイベントのログ記録の構成を監視するための [Azure Policy](../../../policy/overview.md) 定義を割り当てるものです。 これらの構成を監視することは、監査システムの障害や構成ミスを発見したり、是正措置を講じたりするうえで役立ちます。

- Advanced Data Security を、SQL サーバー上で有効にする必要がある
- 診断設定の監査
- SQL Server の監査を有効にする必要があります

## <a name="13-and-64-audit-review-analysis-and-reporting--central-review-and-analysis"></a>1.3 および 6.4 監査の確認、分析、およびレポート | 集中的な確認と分析

Azure Monitor で収集されたログ データは、Log Analytics ワークスペースに保存されるので、集中的にレポートと分析が可能です。 このブループリントは、Azure 仮想マシンに対する Log Analytics エージェントのデプロイを監査および強制するための [Azure Policy](../../../policy/overview.md) 定義を割り当てることによって、イベントのログ記録の徹底を支援するものです。

- \[プレビュー\]:Audit Log Analytics エージェントのデプロイ - 一覧にない VM イメージ (OS)
- Linux VM への Log Analytics エージェントのデプロイ
- Windows VM への Log Analytics エージェントのデプロイ

## <a name="13-22-27-64-and-65a-audit-generation"></a>1.3、2.2、2.7、6.4、および 6.5A 監査の生成

このブループリントでは、Azure リソースのログ設定を監査する [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、システム イベントのログ記録の徹底を支援します。
これらのポリシー定義では、Azure 仮想マシンにおける Log Analytics エージェントのデプロイのほか、他の Azure リソースの監査設定の構成が監査および実施されます。 また、これらのポリシー定義では、診断ログの構成も監査され、Azure リソース内で実行された処理に関する分析情報が提供されます。 さらに、SQL サーバーには監査と Advanced Data Security が構成されます。

- Audit Log Analytics エージェントのデプロイ - 一覧にない VM イメージ (OS)
- Linux VM スケール セット (VMSS) 用の Log Analytics エージェントのデプロイ
- Linux VM への Log Analytics エージェントのデプロイ
- Windows VM Scale Sets (VMSS) 用の Log Analytics エージェントのデプロイ
- Windows VM への Log Analytics エージェントのデプロイ
- 診断設定の監査
- SQL サーバー レベルの監査設定の監査
- Advanced Data Security を、SQL サーバー上で有効にする必要がある
- SQL サーバーに対する Advanced Data Security のデプロイ
- SQL Server の監査を有効にする必要があります
- ネットワーク セキュリティ グループの診断設定のデプロイ

## <a name="11-least-functionality--prevent-program-execution"></a>1.1 最小限の機能 | プログラムの実行の防止

Azure Security Center の適応型アプリケーション制御は、仮想マシン上で特定のソフトウェアの実行をブロックまたは防止できる、自動化されたインテリジェントなエンドツーエンドのアプリケーション フィルタリング ソリューションです。 アプリケーション制御は、未承認のアプリケーションの実行を禁止する強制モードで実行できます。 このブループリントは、アプリケーションの許可リストが推奨されるものの、その構成がまだ済んでいない仮想マシンを監視するうえで役立つ Azure Policy 定義を 1 件割り当てるものです。

- 安全なアプリケーションの定義のために適応型アプリケーション制御をマシンで有効にする必要がある

## <a name="11-least-functionality--authorized-software--whitelisting"></a>1.1 最小限の機能 | 承認されたソフトウェアまたはホワイトリスト登録

Azure Security Center の適応型アプリケーション制御は、仮想マシン上で特定のソフトウェアの実行をブロックまたは防止できる、自動化されたインテリジェントなエンドツーエンドのアプリケーション フィルタリング ソリューションです。 アプリケーション制御は、仮想マシンについて承認済みのアプリケーションの一覧を作成するうえで役立ちます。 このブループリントは、アプリケーションの許可リストが推奨されるものの、その構成がまだ済んでいない仮想マシンを監視するうえで役立つ [Azure Policy](../../../policy/overview.md) 定義を 1 件割り当てるものです。

- 安全なアプリケーションの定義のために適応型アプリケーション制御をマシンで有効にする必要がある

## <a name="11-user-installed-software"></a>1.1 ユーザーがインストールするソフトウェア

Azure Security Center の適応型アプリケーション制御は、仮想マシン上で特定のソフトウェアの実行をブロックまたは防止できる、自動化されたインテリジェントなエンドツーエンドのアプリケーション フィルタリング ソリューションです。 アプリケーション制御は、ソフトウェア制限ポリシーに対するコンプライアンスを強制および監視するうえで役立ちます。 このブループリントは、アプリケーションの許可リストが推奨されるものの、その構成がまだ済んでいない仮想マシンを監視するうえで役立つ [Azure Policy](../../../policy/overview.md) 定義を 1 件割り当てるものです。

- 安全なアプリケーションの定義のために適応型アプリケーション制御をマシンで有効にする必要がある
- 仮想マシンを新しい Azure Resource Manager リソースに移行する必要がある

## <a name="42-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>4.2 識別と認証 (組織のユーザー) | 特権のあるアカウントへのネットワーク アクセス

このブループリントは、所有者アクセス許可や書き込みアクセス許可を持ち、かつ多要素認証が有効になっていないアカウントを監査するための [Azure Policy](../../../policy/overview.md) 定義を割り当てることによって、特権アクセスの制限と制御を支援するものです。 多要素認証は、1 つの認証情報が侵害された場合でも、アカウントのセキュリティが維持されるようにするために役立ちます。 多要素認証が有効になっていないアカウントを監視することで、侵害される可能性が高いアカウントを特定することができます。

- サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある
- サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある

## <a name="42-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>4.2 識別と認証 (組織のユーザー) | 特権のないアカウントへのネットワーク アクセス

このブループリントは、読み取りアクセス許可を持ち、かつ多要素認証が有効になっていないアカウントを監査するための [Azure Policy](../../../policy/overview.md) 定義を 1 件割り当てることによって、アクセスの制限と制御を支援するものです。 多要素認証は、1 つの認証情報が侵害された場合でも、アカウントのセキュリティが維持されるようにするために役立ちます。 多要素認証が有効になっていないアカウントを監視することで、侵害される可能性が高いアカウントを特定することができます。

- サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある

## <a name="23-and-41-authenticator-management"></a>2.3 および 4.1 認証子の管理

このブループリントは、パスワードなしでアカウントからのリモート接続を許可する Linux 仮想マシンや、passwd ファイルに誤ったアクセス許可が設定されている Linux 仮想マシンを監査する [Azure Policy](../../../policy/overview.md) 定義を割り当てるものです。 また、このブループリントでは、Windows 仮想マシンのパスワード暗号化の種類の構成を監査するポリシー定義も割り当てられます。 これらのインジケーターを監視することは、システム認証子が ID と認証に関する組織のポリシーに準拠しているかどうかの確認に役立ちます。

- passwd ファイルのアクセス許可が 0644 に設定されていない Linux VM の監査結果を表示する
- passwd ファイルのアクセス許可が 0644 に設定されていない Linux VM を監査する要件をデプロイする
- パスワードなしのアカウントが存在する Linux VM の監査結果を表示する
- パスワードなしのアカウントが存在する Linux VM を監査する要件をデプロイする
- 元に戻せる暗号化を使用してパスワードを格納しない Windows VM の監査結果を表示する
- 元に戻せる暗号化を使用してパスワードを格納しない Windows VM を監査する要件をデプロイする

## <a name="23-and-41-authenticator-management--password-based-authentication"></a>2.3 および 4.1 認証子の管理 | パスワードベースの認証

このブループリントは、最小強度やその他のパスワード要件が適用されていない Windows 仮想マシンを監査する [Azure Policy](../../../policy/overview.md) 定義を割り当てることにより、強力なパスワードの強制を支援するものです。 パスワード強度のポリシーに違反している仮想マシンを把握できるようになるので、適切な是正措置を実施し、すべての仮想マシンのユーザー アカウントに対して、組織のパスワード ポリシーへの準拠を徹底させることができます。

- 以前の 24 個のパスワードの再利用が許可されている Windows VM の監査結果を表示する
- パスワードの最長有効期間が 70 日になっていない Windows VM の監査結果を表示する
- パスワードの最短有効期間が 1 日になっていない Windows VM の監査結果を表示する
- パスワードの複雑さの設定が有効になっていない Windows VM の監査結果を表示する
- パスワードの最小文字数が 14 文字に制限されていない Windows VM の監査結果を表示する
- 元に戻せる暗号化を使用してパスワードを格納しない Windows VM の監査結果を表示する
- 以前の 24 個のパスワードの再利用が許可されている Windows VM を監査する前提条件をデプロイする
- パスワードの有効期間が 70 日になっていない Windows VM を監査する前提条件をデプロイする
- パスワードの最短有効期間が 1 日になっていない Windows VM を監査する前提条件をデプロイする
- パスワードの複雑さの設定が有効になっていない Windows VM を監査する前提条件をデプロイする
- パスワードの最小文字数が 14 文字に制限されていない Windows VM を監査する前提条件をデプロイする
- 元に戻せる暗号化を使用してパスワードを格納しない Windows VM を監査する前提条件をデプロイする

## <a name="22-and-27-vulnerability-scanning"></a>2.2 および 2.7 脆弱性のスキャン

このブループリントは、オペレーティング システムの脆弱性、SQL の脆弱性、仮想マシンの脆弱性を Azure Security Center で監視する [Azure Policy](../../../policy/overview.md) 定義を割り当てることによって、情報システムの脆弱性管理を支援するものです。
Azure Security Center では、デプロイされた Azure リソースのセキュリティ状態をリアルタイムに分析するためのレポート機能が提供されます。 このブループリントでは、他にも、SQL サーバー上で Advanced Data Security を監査および強制するポリシー定義が割り当てられます。 Advanced Data Security には、脆弱性の評価機能と高度な脅威に対する保護機能が含まれており、デプロイ済みのリソースに存在する脆弱性について理解を深めるうえで役立ちます。

- Advanced Data Security を、SQL サーバー上で有効にする必要がある
- SQL Server の監査を有効にする必要があります
- 仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある
- SQL データベースの脆弱性を修復する必要がある 
- 使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある

## <a name="13-denial-of-service-protection"></a>1.3 サービス拒否の防止

Azure の分散型サービス拒否 (DDoS) Standard レベルでは、Basic サービス レベルに加えて追加の機能と緩和機能が提供されます。 追加の機能には、Azure Monitor の統合や攻撃後の緩和レポートの閲覧機能が含まれます。 このブループリントは、DDoS Standard レベルが有効になっているかどうかを監査する [Azure Policy](../../../policy/overview.md) 定義を 1 件割り当てるものです。 サービス レベル間の機能の違いを理解しておくと、Azure 環境をサービス拒否から守るうえで最適なソリューションを選択する際に役立ちます。

- Azure DDoS Protection Standard を有効にする必要がある

## <a name="11-and-61-boundary-protection"></a>1.1 および 6.1 境界保護

このブループリントは、Azure Security Center でネットワーク セキュリティ グループの強化された推奨事項を監視する [Azure Policy](../../../policy/overview.md) 定義を割り当てることによって、システム境界の管理と統制を支援するものです。 Azure Security Center では、インターネットに接続している仮想マシンのトラフィック パターンが分析され、ネットワーク セキュリティ グループのルールに関連して攻撃を受ける危険性の抑制に役立つ推奨事項が提示されます。 また、このブルー プリントでは、保護されていないエンドポイント、アプリケーション、ストレージ アカウントを監視するポリシー定義も割り当てられます。 ファイアウォールで保護されていないエンドポイントやアプリケーションがあったり、アクセス制限のないストレージ アカウントがあると、情報システム内の情報に対する意図しないアクセスが許可される恐れがあります。

- アダプティブ ネットワーク強化の推奨事項をインターネット接続仮想マシンに適用する必要がある
- インターネットに接続するエンドポイント経由のアクセスを制限する必要がある
- ストレージ アカウントに対する制限のないネットワーク アクセスの監査

## <a name="29a-boundary-protection--access-points"></a>2.9A 境界保護 | アクセス ポイント

Just-In-Time (JIT) の仮想マシン アクセスでは、Azure 仮想マシンへのインバウンド トラフィックがロックダウンされるので、必要な場合に VM に簡単に接続できる状態を保ちつつ、攻撃に対する露出を減らすことができます。 JIT の仮想マシン アクセスを使うと、Azure 内のリソースに対する外部接続の数を制限できます。 このブループリントは、Just-In-Time のアクセスをサポートできるものの、その構成がまだ済んでいない仮想マシンを監視するうえで役立つ [Azure Policy](../../../policy/overview.md) 定義を 1 件割り当てるものです。

- 仮想マシンの管理ポートは、Just-In-Time のネットワーク アクセス制御で保護する必要がある

## <a name="29a-boundary-protection--external-telecommunications-services"></a>2.9A 境界保護 | 外部通信サービス

Just-In-Time (JIT) の仮想マシン アクセスでは、Azure 仮想マシンへのインバウンド トラフィックがロックダウンされるので、必要な場合に VM に簡単に接続できる状態を保ちつつ、攻撃に対する露出を減らすことができます。 JIT の仮想マシン アクセスを使うと、アクセスの要求と承認のプロセスの労力が少なくなるので、トラフィック フロー ポリシーの例外を管理しやすくなります。 このブループリントは、Just-In-Time のアクセスをサポートできるものの、その構成がまだ済んでいない仮想マシンを監視するうえで役立つ [Azure Policy](../../../policy/overview.md) 定義を 1 件割り当てるものです。

- 仮想マシンの管理ポートは、Just-In-Time のネットワーク アクセス制御で保護する必要がある

## <a name="21-24-24a-25a-and-26-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>2.1、2.4、2.4A、2.5A、および 2.6 送信の機密性と整合性 | 暗号化または代替の物理的保護

このブループリントは、通信プロトコル用に実装された暗号化メカニズムを監視するのに役立つ [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、送信された情報の機密性と整合性を保護するのに役立ちます。 通信が適切に暗号化されていることを確認することで、組織の要件を満たしたり、承認されていない開示や変更から情報を保護したりできます。

- API アプリには HTTPS を介してのみアクセスできるようにする
- セキュリティで保護された通信プロトコルを使用していない Windows Web サーバーの監査結果を表示する
- セキュリティで保護された通信プロトコルを使用していない Windows Web サーバーを監査する前提条件をデプロイする
- Function App には HTTPS 経由でのみアクセスできるようにする
- Redis Cache に対してセキュリティで保護された接続のみを有効にする必要がある
- ストレージ アカウントへの安全な転送を有効にする必要がある
- Web アプリケーションには HTTPS を介してのみアクセスできるようにする

## <a name="22-23-25-41-and-27-protection-of-information-at-rest--cryptographic-protection"></a>2.2、2.3、2.5、4.1、および 2.7 保存情報の保護 | 暗号化による保護

このブループリントは、特定の暗号化コントロールを適用し、脆弱な暗号化設定の使用を監査する [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、保存情報の保護のための暗号化コントロールの使用に関するポリシーの実施を支援するものです。 最適でない暗号化構成が Azure リソースのどこに存在しているかを把握することにより、適切な是正措置を実施し、リソースの構成を情報セキュリティ ポリシーに準拠させることができます。 具体的には、このブループリントにより割り当てられるポリシー定義では、Data Lake Storage アカウントの暗号化と SQL データベースでの Transparent Data Encryption が必須になるほか、SQL データベース、仮想マシン ディスク、Automation アカウント変数の暗号化に漏れがないかどうかが監査されます。

- Advanced Data Security を、SQL サーバー上で有効にする必要がある
- SQL Server に対する Advanced Data Security のデプロイ
- SQL DB Transparent Data Encryption のデプロイ
- SQL データベースで Transparent Data Encryption を有効にする必要がある

## <a name="13-22-and-27-flaw-remediation"></a>1.3、2.2、および 2.7 欠陥の修復

このブループリントは、未実行のシステム更新、オペレーティング システムの脆弱性、SQL の脆弱性、仮想マシンの脆弱性を Azure Security Center で監視する [Azure Policy](../../../policy/overview.md) 定義を割り当てることによって、情報システムの欠陥管理を支援するものです。 Azure Security Center では、デプロイされた Azure リソースのセキュリティ状態をリアルタイムに分析するためのレポート機能が提供されます。 また、このブループリントでは、仮想マシン スケール セットのオペレーティング システムでの修正プログラムを確実に適用するためのポリシー定義が割り当てられます。

- Virtual Machine Scale Sets 上で OS イメージの修正プログラムの自動適用を必要とする
- 仮想マシン スケール セットにシステムの更新プログラムをインストールする必要がある
- システム更新プログラムを仮想マシンにインストールする必要がある
- Virtual Machine Scale Sets における Dependency Agent のデプロイの監査 - VM イメージ (OS) が一覧にない
- Automation アカウント変数は、暗号化する必要がある
- 仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある
- 使用している仮想マシン上のセキュリティ構成の脆弱性を修復する必要がある
- SQL データベースの脆弱性を修復する必要がある

## <a name="61-malicious-code-protection"></a>6.1 悪意のあるコードからの保護

このブループリントは、Azure Security Center 内で仮想マシン上にエンドポイント保護が不足していないかどうかを監視し、Windows 仮想マシンに Microsoft のマルウェア対策ソリューションを適用する [Azure Policy](../../../policy/overview.md) 定義を割り当てることによって、悪意のあるコードからの保護を含めたエンドポイント保護の管理を支援するものです。

- Windows Server 用の既定の Microsoft IaaSAntimalware 拡張機能のデプロイ
- エンドポイント保護ソリューションを仮想マシン スケール セットにインストールする必要がある
- Endpoint Protection の欠落の Azure Security Center での監視
- ストレージ アカウントを新しい Azure Resource Manager リソースに移行する必要がある

## <a name="61-malicious-code-protection--central-management"></a>6.1 悪意のあるコードからの保護 | 一元的な管理

このブループリントは、Azure Security Center 内で仮想マシン上にエンドポイント保護が不足していないかどうかを監視する [Azure Policy](../../../policy/overview.md) 定義を割り当てることによって、悪意のあるコードからの保護を含めたエンドポイント保護の管理を支援するものです。 Azure Security Center では、デプロイ済みの Azure リソースのセキュリティ状態をリアルタイムに分析するための一元管理とレポートの機能が提供されます。

- エンドポイント保護ソリューションを仮想マシン スケール セットにインストールする必要がある
- Endpoint Protection の欠落の Azure Security Center での監視

## <a name="11-13-22-27-28-and-64-information-system-monitoring"></a>1.1、1.3、2.2、2.7、2.8、および 6.4 情報システムの監視

このブループリントは、さまざまな Azure リソースを対象にログ記録とデータ セキュリティを監査および適用することによって、システムの監視を支援するものです。 具体的には、割り当てられるポリシーによって、Log Analytics エージェントのデプロイが監査および実施されるほか、SQL データベース、ストレージ アカウント、ネットワーク リソースの高度なセキュリティ設定が監査および適用されます。 これらの機能は、異常な動作や攻撃の兆候の検出に役立つので、適切な措置を講じることができるようになります。

- Log Analytics エージェントが適切に接続されていない Windows VM からの監査結果を表示する
- Linux VM スケール セット (VMSS) 用の Log Analytics エージェントのデプロイ
- Linux VM への Log Analytics エージェントのデプロイ
- Windows VM Scale Sets (VMSS) 用の Log Analytics エージェントのデプロイ
- Windows VM への Log Analytics エージェントのデプロイ
- Advanced Data Security を、SQL サーバー上で有効にする必要がある
- SQL Server の高度なデータ セキュリティ設定に、セキュリティ アラートを受信するためのメール アドレスが含まれている必要がある
- Azure Stream Analytics で診断ログを有効にする必要がある
- SQL サーバーに対する Advanced Data Security のデプロイ
- SQL Server での監査のデプロイ
- 仮想ネットワーク作成時の Network Watcher のデプロイ
- SQL サーバーでの脅威検出のデプロイ

## <a name="22-and-28-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>2.2 および 2.8 情報システムの監視 | トラフィックまたは隠れた流出の分析

Advanced Threat Protection for Azure Storage では、ストレージ アカウントにアクセスしたり、ストレージ アカウントを利用したりする試みに通常と異なるところがあり、有害な性質が疑われる場合に、そのような試みを検出できます。 保護アラートには、異常なアクセス パターン、異常な抽出またはアップロードのほか、疑わしいストレージ アクティビティが表示されます。 これらのインジケーターは、情報が密かに流出する事態を検出するのに役立ちます。

- SQL サーバーでの脅威検出のデプロイ

> [!NOTE]
> 特定の Azure Policy 定義を利用できるかどうかは、Azure Government とその他の National Clouds で異なる場合があります。

## <a name="next-steps"></a>次のステップ

SWIFT CSP-CSCF v2020 のブループリントのコントロール マッピングを確認しました。次に、以下の記事でブループリントおよびこのサンプルをデプロイする方法を確認します。

> [!div class="nextstepaction"]
> [SWIFT CSP-CSCF v2020 ブループリント - 概要](./index.md)
> [SWIFT CSP-CSCF v2020 ブループリント - デプロイ手順](./deploy.md)

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../../how-to/update-existing-assignments.md)方法を参照する。
