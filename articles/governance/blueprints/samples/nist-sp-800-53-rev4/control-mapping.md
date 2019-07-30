---
title: サンプル - NIST SP 800-53 R4 ブループリント - コントロール マッピング
description: NIST SP 800-53 R4 ブループリント サンプルの Azure Policy に対するコントロール マッピング。
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 251dbc396aea5694c4bdec45c194439c9476238b
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226050"
---
# <a name="control-mapping-of-the-nist-sp-800-53-r4-blueprint-sample"></a>NIST SP 800-53 R4 のブループリント サンプルのコントロール マッピング

以下の記事は、Azure Blueprints NIST SP 800-53 R4 のブループリント サンプルが NIST SP 800-53 R4 のコントロールにどのようにマップされているかを説明したものです。 そのコントロールについて詳しくは、[NIST SP 800-53](https://nvd.nist.gov/800-53) をご覧ください。

以下のマッピングは、**NIST SP 800-53 (Rev. 4)** コントロールに対するものです。 右側のナビゲーションを使用すると、特定のコントロール マッピングに直接ジャンプできます。 マップ コントロールの多くは、[Azure Policy](../../../policy/overview.md) イニシアチブを使用して実装されますす。 イニシアチブの詳細を確認するには、Azure portal で **[ポリシー]** を開き、 **[定義]** ページを選択します。 続いて、次を探して選択します: **[\[Preview\]:Audit NIST SP 800-53 R4 controls and deploy specific VM Extensions to support audit requirements]\([プレビュー]: NIST SP 800-53 R4 コントロールの監査と監査要件をサポートするための特定の VM 拡張機能のデプロイ\)** ビルトイン ポリシー イニシアチブ。

## <a name="ac-2-account-management"></a>AC-2 アカウント管理

このブループリントは、組織のアカウント管理の要件に準拠していない可能性のあるアカウントの確認を支援するものです。 このブループリントでは、サブスクリプションに対して読み取り、書き込み、所有者のアクセス許可を持つ外部アカウントと、非推奨アカウントを監査するための Azure Policy 定義が 5 件割り当てられます。 これらのポリシーによって監査されるアカウントを確認することによって、適切な措置を講じてアカウント管理の要件を満たすことが可能になります。

- \[プレビュー\]:サブスクリプション上にある非推奨アカウントの監査
- \[プレビュー\]:サブスクリプション上で所有者アクセス許可を持つ非推奨のアカウントの監査
- \[プレビュー\]:サブスクリプション上にある所有者アクセス許可を持つ外部アカウントの監査
- \[プレビュー\]:サブスクリプション上にある読み取りアクセス許可を持つ外部アカウントの監査
- \[プレビュー\]:サブスクリプション上にある書き込みアクセス許可を持つ外部アカウントの監査

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) アカウント管理 | ロールベースのスキーム

Azure では、Azure のリソースにアクセスするユーザーを効果的に管理できるように、[ロールベースのアクセス制御](../../../../role-based-access-control/overview.md) (RBAC) が実装されています。 Azure リソースにできるユーザーとそのアクセス許可は、Azure portal を使用して確認できます。 また、このブループリントでは、SQL Server と Service Fabric に対する Azure Active Directory 認証の使用状況を監査するための [Azure Policy](../../../policy/overview.md) 定義が 2 件割り当てられます。 Azure Active Directory 認証を使用すると、アクセス許可の管理を簡単にし、データベース ユーザーとその他の Microsoft サービスの ID を一元管理できます。

- SQL サーバー向けの Azure Active Directory 管理者のプロビジョニングの監査
- Service Fabric でクライアント認証に Azure Active Directory を使用していることの監査

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) アカウント管理 | アカウントの監視および特殊な利用

Just-In-Time (JIT) の仮想マシン アクセスでは、Azure 仮想マシンへのインバウンド トラフィックがロックダウンされるので、必要な場合に VM に簡単に接続できる状態を保ちつつ、攻撃に対する露出を減らすことができます。 仮想マシンにアクセスするための JIT 要求はいずれもアクティビティ ログに記録されるので、特殊な利用を監視できます。 このブループリントは、Just-In-Time のアクセスをサポートできるものの、その構成がまだ済んでいない仮想マシンを監視するうえで役立つ [Azure Policy](../../../policy/overview.md) 定義を 1 件割り当てるものです。

- \[プレビュー\]:可能なネットワーク Just In Time (JIT) アクセスの Azure Security Center での監視

## <a name="ac-4-information-flow-enforcement"></a>AC-4 情報フローの適用

クロス オリジン リソース共有 (CORS) を使うと、外部ドメインから App Services リソースを要求できるようになります。 Microsoft では、API、関数、Web アプリケーションの操作に必要なドメインのみを許可することをお勧めしています。 このブループリントは、Azure Security Center 内で CORS リソースに関するアクセス制限を監視するうえで役立つ [Azure Policy](../../../policy/overview.md) 定義を 1 件割り当てるものです。
CORS の実装を理解することは、情報フローのコントロールが実装できていることを確認するのに役立ちます。

- \[プレビュー\]:Web アプリケーションの CORS リソース アクセス制限の監査

## <a name="ac-5-separation-of-duties"></a>AC-5 職務の分離

Azure サブスクリプションの所有者を 1 人しか設定しなかった場合、管理の冗長性は確保されません。 逆に、Azure サブスクリプションの所有者が多すぎると、所有者アカウントが侵害されてセキュリティ侵害が発生するリスクが高まります。 このブループリントでは、Azure サブスクリプションの所有者数を監査する 2 つの [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、Azure サブスクリプションの所有者を適切な数に維持することができます。 また、このブループリントでは、Windows 仮想マシンの Administrators グループのメンバーシップを管理するうえで役立つ Azure Policy 定義が 4 件割り当てられます。 サブスクリプション所有者と仮想マシン管理者のアクセス許可を管理することで、職務を適切に分離することができます。

- \[プレビュー\]:サブスクリプションの所有者の最大数の監査
- \[プレビュー\]:サブスクリプションの所有者の最小数の監査
- Windows VM 内の Administrators グループから特定のメンバーが除外されているかどうかの監査
- Windows VM 内の Administrators グループに特定のメンバーが含まれているかどうかの監査
- Windows VM 内の Administrators グループから特定のメンバーが除外されているかどうかを監査するための VM 拡張機能のデプロイ
- Windows VM 内の Administrators グループに特定のメンバーが含まれているかどうかを監査するための VM 拡張機能のデプロイ

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>AC-6 (7) 最小限の特権 | ユーザー特権の確認

Azure では、Azure のリソースにアクセスするユーザーを効果的に管理できるように、[ロールベースのアクセス制御](../../../../role-based-access-control/overview.md) (RBAC) が実装されています。 Azure リソースにできるユーザーとそのアクセス許可は、Azure portal を使用して確認できます。 このブループリントは、優先的に確認する必要があるアカウントを監査するための [Azure Policy](../../../policy/overview.md) 定義を 6 件割り当てるものです。 これらのアカウント インジケーターを確認すれば、最小限の特権コントロールが実装されているかどうかを確かめることができます。

- \[プレビュー\]:サブスクリプションの所有者の最大数の監査
- \[プレビュー\]:サブスクリプションの所有者の最小数の監査
- Windows VM 内の Administrators グループから特定のメンバーが除外されているかどうかの監査
- Windows VM 内の Administrators グループに特定のメンバーが含まれているかどうかの監査
- Windows VM 内の Administrators グループから特定のメンバーが除外されているかどうかを監査するための VM 拡張機能のデプロイ
- Windows VM 内の Administrators グループに特定のメンバーが含まれているかどうかを監査するための VM 拡張機能のデプロイ

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) リモート アクセス | 自動監視および制御

このブループリントは、Azure App Service アプリケーションのリモート デバッグがオフになっているかどうかを監視するための [Azure Policy](../../../policy/overview.md) 定義 3 件と、パスワードなしでアカウントからのリモート接続を許可する Linux 仮想マシンを監査するポリシー定義 2 件を割り当てることによって、リモート アクセスの監視と制御を支援するものです。 また、このブループリントでは、ストレージ アカウントに対する無制限のアクセスの監視に役立つ Azure Policy 定義も 1 件割り当てられます。 これらのインジケーターを監視すれば、リモート アクセスの方式がセキュリティ ポリシーに従っているかどうかを確かめることができます。

- \[プレビュー\]:関数アプリのリモート デバッグ状態の監査
- \[プレビュー\]:Web アプリケーションのリモート デバッグ状態の監査
- \[プレビュー\]:API アプリのリモート デバッグ状態の監査
- \[プレビュー\]:Linux VM でパスワードなしのアカウントからのリモート接続が許可されないことの監査
- \[プレビュー\]:VM 拡張機能をデプロイして、Linux VM でパスワードなしのアカウントからのリモート接続が許可されないことを監査
- ストレージ アカウントに対する制限のないネットワーク アクセスの監査

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>AU-3 (2) 監査レコードの内容 | 計画的な監査レコードの内容の集中管理

Azure Monitor で収集されたログ データは、Log Analytics ワークスペースに保存されるので、集中的に構成と管理が可能です。 このブループリントは、Azure 仮想マシンにおける Log Analytics エージェントのデプロイを監査および実施するための [Azure Policy](../../../policy/overview.md) 定義を 7 件割り当てることによって、イベントのログ記録の徹底を支援するものです。

- \[プレビュー\]:Audit Log Analytics エージェントのデプロイ - 一覧にない VM イメージ (OS)
- \[プレビュー\]:VMSS の Log Analytics エージェントのデプロイの監査 - VM イメージ (OS) が一覧にない
- \[プレビュー\]:VM 用 Audit Log Analytics ワークスペース - 不一致の報告
- \[プレビュー\]:Linux VM スケール セット (VMSS) 用の Log Analytics エージェントのデプロイ
- \[プレビュー\]:Linux VM への Log Analytics エージェントのデプロイ
- \[プレビュー\]:Windows VM Scale Sets (VMSS) 用の Log Analytics エージェントのデプロイ
- \[プレビュー\]:Windows VM への Log Analytics エージェントのデプロイ

## <a name="au-5-response-to-audit-processing-failures"></a>AU-5 監査処理エラーへの対応

このブループリントは、監査とイベントのログ記録の構成を監視するための [Azure Policy](../../../policy/overview.md) 定義を 5 件割り当てるものです。 これらの構成を監視することは、監査システムの障害や構成ミスを発見したり、是正措置を講じたりするうえで役立ちます。

- \[プレビュー\]:未監査の SQL サーバーの Azure Security Center での監視
- 診断設定の監査
- Advanced Data Security を使用していない SQL マネージド インスタンスの監査
- SQL サーバー レベルの監査設定の監査
- Advanced Data Security を使用していない SQL サーバーの監査

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) 監査の確認、分析、およびレポート | 集中的な確認と分析

Azure Monitor で収集されたログ データは、Log Analytics ワークスペースに保存されるので、集中的にレポートと分析が可能です。 このブループリントは、Azure 仮想マシンにおける Log Analytics エージェントのデプロイを監査および実施するための [Azure Policy](../../../policy/overview.md) 定義を 7 件割り当てることによって、イベントのログ記録の徹底を支援するものです。

- \[プレビュー\]:Audit Log Analytics エージェントのデプロイ - 一覧にない VM イメージ (OS)
- \[プレビュー\]:VMSS の Log Analytics エージェントのデプロイの監査 - VM イメージ (OS) が一覧にない
- \[プレビュー\]:VM 用 Audit Log Analytics ワークスペース - 不一致の報告
- \[プレビュー\]:Linux VM スケール セット (VMSS) 用の Log Analytics エージェントのデプロイ
- \[プレビュー\]:Linux VM への Log Analytics エージェントのデプロイ
- \[プレビュー\]:Windows VM Scale Sets (VMSS) 用の Log Analytics エージェントのデプロイ
- \[プレビュー\]:Windows VM への Log Analytics エージェントのデプロイ

## <a name="au-12-audit-generation"></a>AU-12 監査の生成

このブループリントは、Azure リソースのログ設定を監査する [Azure Policy](../../../policy/overview.md) 定義を 15 件割り当てることによって、システム イベントのログ記録の徹底を支援するものです。 これらのポリシー定義では、Azure 仮想マシンにおける Log Analytics エージェントのデプロイのほか、他の Azure リソースの監査設定の構成が監査および実施されます。 また、これらのポリシー定義では、診断ログの構成も監査され、Azure リソース内で実行された処理に関する分析情報が提供されます。 さらに、SQL サーバーには監査と Advanced Data Security が構成されます。

- \[プレビュー\]:Audit Log Analytics エージェントのデプロイ - 一覧にない VM イメージ (OS)
- \[プレビュー\]:VMSS の Log Analytics エージェントのデプロイの監査 - VM イメージ (OS) が一覧にない
- \[プレビュー\]:VM 用 Audit Log Analytics ワークスペース - 不一致の報告
- \[プレビュー\]:Linux VM スケール セット (VMSS) 用の Log Analytics エージェントのデプロイ
- \[プレビュー\]:Linux VM への Log Analytics エージェントのデプロイ
- \[プレビュー\]:Windows VM Scale Sets (VMSS) 用の Log Analytics エージェントのデプロイ
- \[プレビュー\]:Windows VM への Log Analytics エージェントのデプロイ
- \[プレビュー\]:未監査の SQL サーバーの Azure Security Center での監視
- ネットワーク セキュリティ グループの診断設定の適用
- 診断設定の監査
- Advanced Data Security を使用していない SQL マネージド インスタンスの監査
- SQL サーバー レベルの監査設定の監査
- Advanced Data Security を使用していない SQL サーバーの監査
- SQL サーバーに対する Advanced Data Security のデプロイ
- SQL サーバーに対する監査のデプロイ

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM-7 (2) 最小限の機能 | プログラムの実行の防止

Azure Security Center で提供される適応型アプリケーション制御は、仮想マシン上で特定のソフトウェアの実行をブロックまたは防止できる、自動化されたインテリジェントなエンドツーエンドのアプリケーション ホワイトリスト登録ソリューションです。 アプリケーション制御は、未承認のアプリケーションの実行を禁止する強制モードで実行できます。 このブループリントは、アプリケーションのホワイトリストが推奨されるものの、その構成がまだ済んでいない仮想マシンを監視するうえで役立つ Azure Policy 定義を 1 件割り当てるものです。

- \[プレビュー\]:可能なアプリのホワイトリスト登録の Azure Security Center での監視

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) 最小限の機能 | 承認されたソフトウェアまたはホワイトリスト登録

Azure Security Center で提供される適応型アプリケーション制御は、仮想マシン上で特定のソフトウェアの実行をブロックまたは防止できる、自動化されたインテリジェントなエンドツーエンドのアプリケーション ホワイトリスト登録ソリューションです。 アプリケーション制御は、仮想マシンについて承認済みのアプリケーションの一覧を作成するうえで役立ちます。 このブループリントは、アプリケーションのホワイトリストが推奨されるものの、その構成がまだ済んでいない仮想マシンを監視するうえで役立つ [Azure Policy](../../../policy/overview.md) 定義を 1 件割り当てるものです。

- \[プレビュー\]:可能なアプリのホワイトリスト登録の Azure Security Center での監視

## <a name="cm-11-user-installed-software"></a>CM-11 ユーザーがインストールするソフトウェア

Azure Security Center で提供される適応型アプリケーション制御は、仮想マシン上で特定のソフトウェアの実行をブロックまたは防止できる、自動化されたインテリジェントなエンドツーエンドのアプリケーション ホワイトリスト登録ソリューションです。 アプリケーション制御は、ソフトウェア制限ポリシーに対するコンプライアンスを強制および監視するうえで役立ちます。 このブループリントは、アプリケーションのホワイトリストが推奨されるものの、その構成がまだ済んでいない仮想マシンを監視するうえで役立つ [Azure Policy](../../../policy/overview.md) 定義を 1 件割り当てるものです。

- \[プレビュー\]:可能なアプリのホワイトリスト登録の Azure Security Center での監視

## <a name="cp-7-alternate-processing-site"></a>CP-7 代替処理サイト

Azure Site Recovery では、仮想マシンで実行中のワークロードがプライマリ ロケーションからセカンダリ ロケーションにレプリケートされます。 プライマリ サイトで障害が発生した場合は、ワークロードがセカンダリ ロケーションにフェールオーバーされます。 このブループリントは、ディザスター リカバリーが構成されていない仮想マシンを監査する [Azure Policy](../../../policy/overview.md) 定義を 1 件割り当てるものです。 このインジケーターを監視することは、必要なコンティンジェンシー コントロールが備わっているかどうかを確認するのに役立ちます。

- ディザスター リカバリーが構成されていない仮想マシンの監査

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) 識別と認証 (組織のユーザー) | 特権アカウントへのネットワーク アクセス

このブループリントは、所有者アクセス許可や書き込みアクセス許可を持ち、かつ多要素認証が有効になっていないアカウントを監査するための [Azure Policy](../../../policy/overview.md) 定義を 2 件割り当てることによって、特権アクセスの制限と制御を支援するものです。 多要素認証は、1 つの認証情報が侵害された場合でも、アカウントのセキュリティが維持されるようにするために役立ちます。 多要素認証が有効になっていないアカウントを監視することで、侵害される可能性が高いアカウントを特定することができます。

- \[プレビュー\]:サブスクリプション上にある所有者アクセス許可を持つアカウントで MFA が有効でないものの監査
- \[プレビュー\]:サブスクリプション上にある書き込みアクセス許可を持つアカウントで MFA が有効でないものの監査

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) 識別と認証 (組織のユーザー) | 特権のないアカウントへのネットワーク アクセス

このブループリントは、読み取りアクセス許可を持ち、かつ多要素認証が有効になっていないアカウントを監査するための [Azure Policy](../../../policy/overview.md) 定義を 1 件割り当てることによって、アクセスの制限と制御を支援するものです。 多要素認証は、1 つの認証情報が侵害された場合でも、アカウントのセキュリティが維持されるようにするために役立ちます。 多要素認証が有効になっていないアカウントを監視することで、侵害される可能性が高いアカウントを特定することができます。

- \[プレビュー\]:サブスクリプション上にある読み取りアクセス許可を持つアカウントで MFA が有効でないものの監査

## <a name="ia-5-authenticator-management"></a>IA-5 認証子の管理

このブループリントは、パスワードなしでアカウントからのリモート接続を許可する Linux 仮想マシンや、passwd ファイルに誤ったアクセス許可が設定されている Linux 仮想マシンを監査する [Azure Policy](../../../policy/overview.md) 定義を 5 件割り当てるものです。 また、このブループリントでは、Windows 仮想マシンのパスワード暗号化の種類が正しいかどうかを監査するポリシー定義も 1 件割り当てられます。 これらのインジケーターを監視することは、システム認証子が ID と認証に関する組織のポリシーに準拠しているかどうかの確認に役立ちます。

- \[プレビュー\]:Linux VM にパスワードなしのアカウントがないことの監査
- \[プレビュー\]:VM 拡張機能をデプロイして、Linux VM にパスワードなしのアカウントがないことを監査
- \[プレビュー\]:Linux VM で passwd ファイルのアクセス許可が 0644 に設定されていることの監査
- \[プレビュー\]:Windows VM で暗号化を元に戻せる状態でパスワードが保存されることの監査
- \[プレビュー\]:VM 拡張機能をデプロイして、Linux VM で passwd ファイルへのアクセス許可が 0644 に設定されていることを監査

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) 認証子の管理 | パスワードベースの認証

このブループリントは、最小強度やその他のパスワード要件が適用されていない Windows 仮想マシンを監査する [Azure Policy](../../../policy/overview.md) 定義を 12 件割り当てることにより、強力なパスワード ポリシーの実施を支援するものです。 パスワード強度のポリシーに違反している仮想マシンを把握できるようになるので、適切な是正措置を実施し、すべての仮想マシンのユーザー アカウントに対して、組織のパスワード ポリシーへの準拠を徹底させることができます。

- \[プレビュー\]:Windows VM で過去 24 個分のパスワードを再利用できないことの監査
- \[プレビュー\]:Windows VM のパスワードの最大有効期間が 70 日であることの監査
- \[プレビュー\]:Windows VM のパスワードの最小有効期間が 1 日であることの監査
- \[プレビュー\]:Windows VM でパスワードの複雑さ設定が有効になっていることの監査
- \[プレビュー\]:Windows VM でパスワードの最小長が 14 文字に制限されていることの監査
- \[プレビュー\]:Windows VM で暗号化を元に戻せる状態でパスワードが保存されることの監査
- \[プレビュー\]:VM 拡張機能をデプロイして、Windows VM で過去 24 個分のパスワードを再利用できないことを監査
- \[プレビュー\]:VM 拡張機能をデプロイして、Windows VM のパスワードの最大有効期間が 70 日であることを監査
- \[プレビュー\]:VM 拡張機能をデプロイして、Windows VM のパスワードの最小有効期間が 1 日であることを監査
- \[プレビュー\]:VM 拡張機能をデプロイして、Windows VM でパスワードの複雑さ設定が有効になっていることを監査
- \[プレビュー\]:VM 拡張機能をデプロイして、Windows VM でパスワードの最小長が 14 文字に制限されていることを監査
- \[プレビュー\]:VM 拡張機能をデプロイして、Windows VM で暗号化を元に戻せる状態でパスワードが保存されないことを監査

## <a name="ra-5-vulnerability-scanning"></a>RA-5 脆弱性のスキャン

このブループリントは、オペレーティング システムの脆弱性、SQL の脆弱性、仮想マシンの脆弱性を Azure Security Center で監視する [Azure Policy](../../../policy/overview.md) 定義を 4 件割り当てることによって、情報システムの脆弱性管理を支援するものです。 Azure Security Center では、デプロイされた Azure リソースのセキュリティ状態をリアルタイムに分析するためのレポート機能が提供されます。 このブループリントではほかにも、SQL サーバー上で Advanced Data Security を使用しているかどうかを監査し、SQL サーバーに Advanced Data Security を適用するポリシー定義が 3 件割り当てられます。 Advanced Data Security には、脆弱性の評価機能と高度な脅威に対する保護機能が含まれており、デプロイ済みのリソースに存在する脆弱性について理解を深めるうえで役立ちます。

- Advanced Data Security を使用していない SQL マネージド インスタンスの監査
- Advanced Data Security を使用していない SQL サーバーの監査
- SQL サーバーに対する Advanced Data Security のデプロイ
- \[プレビュー\]:Azure Security Center で仮想マシン スケール セットでの OS 脆弱性を監査
- \[プレビュー\]:OS の脆弱性の Azure Security Center での監視
- \[プレビュー\]:SQL の脆弱性評価の結果の Azure Security Center での監視
- \[プレビュー\]:VM の脆弱性の Azure Security Center での監視

## <a name="sc-5-denial-of-service-protection"></a>SC-5 サービス拒否の防止

Azure の分散型サービス拒否 (DDoS) Standard レベルでは、Basic サービス レベルに加えて追加の機能と緩和機能が提供されます。 追加の機能には、Azure Monitor の統合や攻撃後の緩和レポートの閲覧機能が含まれます。 このブループリントは、DDoS Standard レベルが有効になっているかどうかを監査する [Azure Policy](../../../policy/overview.md) 定義を 1 件割り当てるものです。 サービス レベル間の機能の違いを理解しておくと、Azure 環境をサービス拒否から守るうえで最適なソリューションを選択する際に役立ちます。

- \[プレビュー\]:仮想ネットワークで Standard レベルの DDoS 保護が有効なことの監査

## <a name="sc-7-boundary-protection"></a>SC-7 境界保護

このブループリントは、寛容なルールが適用されているネットワーク セキュリティ グループを監視する [Azure Policy](../../../policy/overview.md) 定義を割り当てることによって、システム境界の管理と統制を支援するものです。 ルールが寛容すぎると、意図しないネットワーク アクセスが許可される恐れがあるため、そのようなルールがないか確認する必要があります。 このブループリントではほかにも、Azure Security Center 内でネットワーク セキュリティ グループを強化する推奨事項があるかどうかを監視するポリシー定義が 1 件割り当てられます。 Azure Security Center では、インターネットに接続している仮想マシンのトラフィック パターンが分析され、ネットワーク セキュリティ グループのルールに関連して攻撃を受ける危険性の抑制に役立つ推奨事項が提示されます。
また、このブループリントでは、保護されていないエンドポイント、アプリケーション、ストレージ アカウントを監視する Azure Policy 定義も 3 件割り当てられます。 ファイアウォールで保護されていないエンドポイントやアプリケーションがあったり、アクセス制限のないストレージ アカウントがあると、情報システム内の情報に対する意図しないアクセスが許可される恐れがあります。

- \[プレビュー\]:ネットワーク セキュリティ グループのトラフィック強化のレコメンデーションを確認するため、インターネットに接続する仮想マシンを監視
- \[プレビュー\]:制限が少なすぎるネットワーク アクセスの Azure Security Center での監視
- \[プレビュー\]:保護されていないネットワーク エンドポイントの Azure Security Center での監視
- \[プレビュー\]:保護されていない Web アプリケーションの Azure Security Center での監視
- ストレージ アカウントに対する制限のないネットワーク アクセスの監査

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) 境界保護 | アクセス ポイント

Just-In-Time (JIT) の仮想マシン アクセスでは、Azure 仮想マシンへのインバウンド トラフィックがロックダウンされるので、必要な場合に VM に簡単に接続できる状態を保ちつつ、攻撃に対する露出を減らすことができます。 JIT の仮想マシン アクセスを使うと、Azure 内のリソースに対する外部接続の数を制限できます。 このブループリントは、Just-In-Time のアクセスをサポートできるものの、その構成がまだ済んでいない仮想マシンを監視するうえで役立つ [Azure Policy](../../../policy/overview.md) 定義を 1 件割り当てるものです。

- \[プレビュー\]:可能なネットワーク Just In Time (JIT) アクセスの Azure Security Center での監視

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) 境界保護 | 外部通信サービス

Just-In-Time (JIT) の仮想マシン アクセスでは、Azure 仮想マシンへのインバウンド トラフィックがロックダウンされるので、必要な場合に VM に簡単に接続できる状態を保ちつつ、攻撃に対する露出を減らすことができます。 JIT の仮想マシン アクセスを使うと、アクセスの要求と承認のプロセスの労力が少なくなるので、トラフィック フロー ポリシーの例外を管理しやすくなります。 このブループリントは、Just-In-Time のアクセスをサポートできるものの、その構成がまだ済んでいない仮想マシンを監視するうえで役立つ [Azure Policy](../../../policy/overview.md) 定義を 1 件割り当てるものです。

- \[プレビュー\]:可能なネットワーク Just In Time (JIT) アクセスの Azure Security Center での監視

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) 保存情報の保護 | 暗号化による保護

このブループリントは、特定の暗号化コントロールを適用し、脆弱な暗号化設定の使用を監査する [Azure Policy](../../../policy/overview.md) 定義を 9 件割り当てることで、保存情報の保護のための暗号化コントロールの使用に関するポリシーの実施を支援するものです。 最適でない暗号化構成が Azure リソースのどこに存在しているかを把握することにより、適切な是正措置を実施し、リソースの構成を情報セキュリティ ポリシーに準拠させることができます。 具体的には、このブループリントにより割り当てられるポリシー定義では、Data Lake Storage アカウントの暗号化と SQL データベースでの Transparent Data Encryption が必須になるほか、SQL データベース、仮想マシン ディスク、Automation アカウント変数の暗号化に漏れがないかどうかが監査されます。

- \[プレビュー\]:暗号化されていない SQL データベースの Azure Security Center での監視
- \[プレビュー\]:暗号化されていない VM ディスクの Azure Security Center での監視
- ストレージ アカウントへの安全な転送の監査
- Advanced Data Security を使用していない SQL マネージド インスタンスの監査
- Advanced Data Security を使用していない SQL サーバーの監査
- Transparent Data Encryption の状態の監査
- SQL サーバーに対する Advanced Data Security のデプロイ
- SQL DB Transparent Data Encryption のデプロイ
- Data Lake Store アカウントに対する暗号化の適用

## <a name="si-2-flaw-remediation"></a>SI-2 欠陥の修復

このブループリントは、未実行のシステム更新、オペレーティング システムの脆弱性、SQL の脆弱性、仮想マシンの脆弱性を Azure Security Center で監視する [Azure Policy](../../../policy/overview.md) 定義を 6 件割り当てることによって、情報システムの欠陥管理を支援するものです。 Azure Security Center では、デプロイされた Azure リソースのセキュリティ状態をリアルタイムに分析するためのレポート機能が提供されます。 また、このブループリントでは、仮想マシン スケール セットのオペレーティング システムの自動アップグレードを確実に実施するためのポリシー定義が 1 件割り当てられます。

- \[プレビュー\]:仮想マシン スケール セット上で不足しているシステム更新プログラムの Azure Security Center での監査
- \[プレビュー\]:Azure Security Center で仮想マシン スケール セットでの OS 脆弱性を監査
- \[プレビュー\]:システムの更新プログラムの欠落の Azure Security Center での監視
- \[プレビュー\]:OS の脆弱性の Azure Security Center での監視
- \[プレビュー\]:SQL の脆弱性評価の結果の Azure Security Center での監視
- \[プレビュー\]:VM の脆弱性の Azure Security Center での監視
- VMSS に対する自動 OS アップグレードとアプリ正常性チェックの適用

## <a name="si-3-malicious-code-protection"></a>SI-3 悪意のあるコードからの保護

このブループリントは、Azure Security Center 内で仮想マシン上にエンドポイント保護が不足していないかどうかを監視し、Windows 仮想マシンに Microsoft のマルウェア対策ソリューションを適用する [Azure Policy](../../../policy/overview.md) 定義を 3 件割り当てることによって、悪意のあるコードからの保護を含めたエンドポイント保護の管理を支援するものです。

- \[プレビュー\]:仮想マシン スケール セット上の Endpoint Protection ソリューションの Azure Security Center での監査
- \[プレビュー\]:Endpoint Protection の欠落の Azure Security Center での監視
- 既定の Windows Server 用 Microsoft IaaS マルウェア対策拡張機能のデプロイ

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) 悪意のあるコードからの保護 | 一元的な管理

このブループリントは、Azure Security Center 内で仮想マシン上にエンドポイント保護が不足していないかどうかを監視する [Azure Policy](../../../policy/overview.md) 定義を 2 件割り当てることによって、悪意のあるコードからの保護を含めたエンドポイント保護の管理を支援するものです。 Azure Security Center では、デプロイ済みの Azure リソースのセキュリティ状態をリアルタイムに分析するための一元管理とレポートの機能が提供されます。

- \[プレビュー\]:仮想マシン スケール セット上の Endpoint Protection ソリューションの Azure Security Center での監査
- \[プレビュー\]:Endpoint Protection の欠落の Azure Security Center での監視

## <a name="si-4-information-system-monitoring"></a>SI-4 情報システムの監視

このブループリントは、さまざまな Azure リソースを対象にログ記録とデータ セキュリティを監査および適用することによって、システムの監視を支援するものです。 具体的には、割り当てられるポリシーによって、Log Analytics エージェントのデプロイが監査および実施されるほか、SQL データベース、ストレージ アカウント、ネットワーク リソースの高度なセキュリティ設定が監査および適用されます。 これらの機能は、異常な動作や攻撃の兆候の検出に役立つので、適切な措置を講じることができるようになります。

- \[プレビュー\]:Audit Log Analytics エージェントのデプロイ - 一覧にない VM イメージ (OS)
- \[プレビュー\]:VMSS の Log Analytics エージェントのデプロイの監査 - VM イメージ (OS) が一覧にない
- \[プレビュー\]:VM 用 Audit Log Analytics ワークスペース - 不一致の報告
- \[プレビュー\]:Linux VM スケール セット (VMSS) 用の Log Analytics エージェントのデプロイ
- \[プレビュー\]:Linux VM への Log Analytics エージェントのデプロイ
- \[プレビュー\]:Windows VM Scale Sets (VMSS) 用の Log Analytics エージェントのデプロイ
- \[プレビュー\]:Windows VM への Log Analytics エージェントのデプロイ
- Advanced Data Security を使用していない SQL マネージド インスタンスの監査
- Advanced Data Security を使用していない SQL サーバーの監査
- SQL サーバーに対する Advanced Data Security のデプロイ
- ストレージ アカウントに対する Advanced Threat Protection のデプロイ
- SQL サーバーに対する監査のデプロイ
- 仮想ネットワーク作成時の Network Watcher のデプロイ
- SQL サーバーに対する脅威検出のデプロイ

## <a name="si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>SI-4 (18) 情報システムの監視 | トラフィックまたは隠れたデータ流出の分析

Advanced Threat Protection for Azure Storage では、ストレージ アカウントにアクセスしたり、ストレージ アカウントを利用したりする試みに通常と異なるところがあり、有害な性質が疑われる場合に、そのような試みを検出できます。 保護アラートには、異常なアクセス パターン、異常な抽出またはアップロードのほか、疑わしいストレージ アクティビティが表示されます。 これらのインジケーターは、情報が密かに流出する事態を検出するのに役立ちます。

- ストレージ アカウントに対する Advanced Threat Protection のデプロイ

## <a name="next-steps"></a>次の手順

NIST SP 800-53 R4 のブループリントのコントロール マッピングを確認しました。次に、以下の記事でブループリントおよびこのサンプルをデプロイする方法を確認します。

> [!div class="nextstepaction"]
> [NIST SP 800-53 R4 ブループリント - 概要](./index.md)
> [NIST SP 800-53 R4 ブループリント - デプロイ手順](./deploy.md)

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../../how-to/update-existing-assignments.md)方法を参照する。
