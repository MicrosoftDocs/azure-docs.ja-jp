---
title: Australian Government ISM PROTECTED ブループリント サンプルのコントロール
description: Australian Government ISM PROTECTED ブループリント サンプルのコントロール マッピング。 それぞれのコントロールは、評価を支援する 1 つまたは複数の Azure Policy 定義に対応します。
ms.date: 01/21/2021
ms.topic: sample
ms.openlocfilehash: df143a871db3d871fe6354eab23b7ca6dc012b78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98679999"
---
# <a name="control-mapping-of-the-australian-government-ism-protected-blueprint-sample"></a>Australian Government ISM PROTECTED ブループリント サンプルのコントロール マッピング

以下の記事は、Azure Blueprints Australian Government ISM PROTECTED ブループリント サンプルが ISM PROTECTED コントロールにどのようにマップされているかを詳しく説明したものです。 コントロールについて詳しくは、[ISM PROTECTED](https://www.cyber.gov.au/ism) に関するページを参照してください。

以下のマッピングは、**ISM PROTECTED** コントロールに対するものです。 右側のナビゲーションを使用すると、特定のコントロール マッピングに直接ジャンプできます。 マップ コントロールの多くは、[Azure Policy](../../../policy/overview.md) イニシアチブを使用して実装されますす。 イニシアチブの詳細を確認するには、Azure portal で **[ポリシー]** を開き、 **[定義]** ページを選択します。 続いて、次を探して選択します: **[\[Preview\]:Audit Australian Government ISM PROTECTED controls and deploy specific VM Extensions to support audit requirements]\([プレビュー]: Australian Government ISM PROTECTED コントロールの監査と監査要件をサポートするための特定の VM 拡張機能のデプロイ\)** ビルトイン ポリシー イニシアチブ。

> [!IMPORTANT]
> 以下の各コントロールは、1 つ以上の [Azure Policy](../../../policy/overview.md) 定義に関連します。 これらのポリシーは、コントロールに対する[コンプライアンスの評価](../../../policy/how-to/get-compliance-data.md)に役立つ場合があります。ただし、多くの場合、コントロールと 1 つ以上のポリシーとの間には、一対一での一致、または完全な一致はありません。 そのため、Azure Policy での **準拠** は、ポリシー自体のみを指しています。これによって、コントロールのすべての要件に完全に準拠していることが保証されるわけではありません。 また、コンプライアンス標準には、現時点でどの Azure Policy 定義でも対応されていないコントロールが含まれています。 したがって、Azure Policy でのコンプライアンスは、全体のコンプライアンス状態の部分的ビューでしかありません。 このコンプライアンス ブループリント サンプルのコントロールと Azure Policy 定義の間の関連付けは、時間の経過と共に変わる可能性があります。 変更履歴を表示するには、[GitHub のコミット履歴](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ism-protected/control-mapping.md)に関するページを参照してください。


## <a name="location-constraints"></a>場所の制約

このブループリントは、以下の Azure Policy 定義を割り当てることによって、すべてのリソースとリソース グループをデプロイする場所を "オーストラリア中部"、"オーストラリア中部 2"、"オーストラリア東部"、"オーストラリア南東部" に制限するのに役立ちます。

- 許可されている場所 ("オーストラリア中部"、"オーストラリア中部 2"、"オーストラリア東部"、"オーストラリア南東部" にハードコーディングされている)
- リソース グループに許可された場所 ("オーストラリア中部"、"オーストラリア中部 2"、"オーストラリア東部"、"オーストラリア南東部" にハードコーディングされている)

## <a name="guidelines-for-personnel-security---access-to-systems-and-their-resources"></a>人的セキュリティのガイドライン - システムとそのリソースへのアクセス

### <a name="0414-personnel-granted-access-to-a-system-and-its-resources-are-uniquely-identifiable"></a>0414 システムとそのリソースへのアクセスが許可されている担当者が一意に識別できる

- サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある
- サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある
- サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある

### <a name="1503-standard-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1503 システム、アプリケーション、データ リポジトリへの標準アクセスが、担当者がその職務を遂行するうえで必要なものに制限されている

- 最大 3 人の所有者をサブスクリプションに対して指定する必要がある
- 複数の所有者がサブスクリプションに割り当てられている必要がある
- 指定されたメンバーのいずれかが Administrators グループに含まれている Windows VM からの監査結果を表示する
- 指定されたメンバーのいずれかが Administrators グループに含まれている Windows VM を監査する前提条件をデプロイする

### <a name="1507-privileged-access-to-systems-applications-and-data-repositories-is-validated-when-first-requested-and-revalidated-on-an-annual-or-more-frequent-basis"></a>1507 システム、アプリケーション、データ リポジトリへの特権アクセスが初回要求時に検証され、なおかつ年 1 回またはそれ以上の頻度で再検証される

- 指定されたメンバーのいずれかが Administrators グループに含まれている Windows VM からの監査結果を表示する
- 指定されたメンバーのいずれかが Administrators グループに含まれている Windows VM を監査する前提条件をデプロイする

### <a name="1508-privileged-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1508 システム、アプリケーション、データ リポジトリへの特権アクセスが、担当者がその職務を遂行するうえで必要なものに制限されている

- 最大 3 人の所有者をサブスクリプションに対して指定する必要がある
- 複数の所有者がサブスクリプションに割り当てられている必要がある
- 指定されたメンバーのいずれかが Administrators グループに含まれている Windows VM からの監査結果を表示する
- 指定されたメンバーのいずれかが Administrators グループに含まれている Windows VM を監査する前提条件をデプロイする
- 仮想マシンで Just-In-Time ネットワーク アクセス制御を適用する必要がある

### <a name="0415-the-use-of-shared-user-accounts-is-strictly-controlled-and-personnel-using-such-accounts-are-uniquely-identifiable"></a>0415 共有ユーザー アカウントの使用が厳しく管理され、そのようなアカウントを使用する担当者を一意に識別できる

- 指定されたメンバーのいずれかが Administrators グループに含まれている Windows VM からの監査結果を表示する
- 指定されたメンバーのいずれかが Administrators グループに含まれている Windows VM を監査する前提条件をデプロイする

### <a name="0445-privileged-users-are-assigned-a-dedicated-privileged-account-to-be-used-solely-for-tasks-requiring-privileged-access"></a>0445 特権アクセスを必要とするタスクにのみ使用される専用の特権アカウントが特権ユーザーに割り当てられている

- 指定されたメンバーのいずれかが Administrators グループに含まれている Windows VM からの監査結果を表示する
- 指定されたメンバーのいずれかが Administrators グループに含まれている Windows VM を監査する前提条件をデプロイする

### <a name="0430-access-to-systems-applications-and-data-repositories-is-removed-or-suspended-on-the-same-day-personnel-no-longer-have-a-legitimate-requirement-for-access"></a>0430 アクセスへの正当な要件を担当者が失ったその日に、システム、アプリケーション、データ リポジトリへのアクセスが削除または一時停止される

- 非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある

### <a name="0441-when-personnel-are-granted-temporary-access-to-a-system-effective-security-controls-are-put-in-place-to-restrict-their-access-to-only-information-required-for-them-to-undertake-their-duties"></a>0441 システムへの一時的なアクセス権が担当者に付与される際、その職務を遂行するうえで必要な情報のみにアクセスを制限するための効果的なセキュリティ コントロールが導入される

- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある
- 書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある
- 非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある

## <a name="guidelines-for-system-hardening---operating-system-hardening"></a>システムのセキュリティ強化のためのガイドライン - オペレーティング システムのセキュリティ強化

### <a name="1407-the-latest-version-n-or-n-1-version-of-an-operating-system-is-used-for-standard-operating-environments-soes"></a>1407 標準オペレーティング環境 (SOE) にオペレーティング システムの最新バージョン (N) または N-1 バージョンが使用されている

- システム更新プログラムをマシンにインストールする必要がある
- 仮想マシン スケール セットにシステムの更新プログラムをインストールする必要がある

### <a name="0380-unneeded-operating-system-accounts-software-components-services-and-functionality-are-removed-or-disabled"></a>0380 不要なオペレーティング システム アカウント、ソフトウェア、コンポーネント、サービス、機能が削除されるか無効にされている

- 非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある

### <a name="1490-an-application-whitelisting-solution-is-implemented-on-all-servers-to-restrict-the-execution-of-executables-software-libraries-scripts-and-installers-to-an-approved-set"></a>1490 実行可能ファイル、ソフトウェア ライブラリ、スクリプト、インストーラーの実行を承認済みのセットに制限するために、アプリケーション ホワイトリスト登録ソリューションがすべてのサーバーに実装されている

- 適応型アプリケーション制御を仮想マシンで有効にする必要がある

### <a name="1417-antivirus-software-is-implemented-on-workstations-and-servers-and-configured-with-signature-based-detection-enabled-and-set-to-a-high-level-heuristic-based-detection-enabled-and-set-to-a-high-level-detection-signatures-checked-for-currency-and-updated-on-at-least-a-daily-basis-automatic-and-regular-scanning-configured-for-all-fixed-disks-and-removable-media"></a>1417 ワークステーションおよびサーバーにウイルス対策ソフトウェアが実装され、必要な構成が済んでいる (シグネチャベースの検出が有効で高レベルに設定されていること、ヒューリスティックベースの検出が有効で高レベルに設定されていること、検出シグネチャが最新であるかどうかがチェックされ、少なくとも 1 日に 1 回は更新されていること、すべての固定ディスクとリムーバブル メディアに対して定期的な自動スキャンが構成されていること)

- Microsoft IaaSAntimalware 拡張機能は Windows Server 上にデプロイする必要がある
- エンドポイント保護ソリューションを仮想マシン スケール セットにインストールする必要がある
- Endpoint Protection の欠落の Azure Security Center での監視

## <a name="guidelines-for-system-hardening---authentication-hardening"></a>システムのセキュリティ強化のためのガイドライン - 認証のセキュリティ強化

### <a name="1546-users-are-authenticated-before-they-are-granted-access-to-a-system-and-its-resources"></a>1546 システムとそのリソースにアクセスする前にユーザーが認証される

- ストレージ アカウントに対する制限のないネットワーク アクセスの監査
- Service Fabric クラスターは、クライアント認証に Azure Active Directory だけを使用する必要がある
- パスワードなしのアカウントからのリモート接続が許可されている Linux VM の監査結果を表示する
- パスワードなしのアカウントからのリモート接続が許可されている Linux VM を監査する前提条件をデプロイする
- パスワードなしのアカウントが存在する Linux VM の監査結果を表示する
- パスワードなしのアカウントがある Linux VM を監査するための前提条件をデプロイする

### <a name="0974-multi-factor-authentication-is-used-to-authenticate-standard-users"></a>0974 標準ユーザーの認証には多要素認証が使用される

- サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある

### <a name="1173-multi-factor-authentication-is-used-to-authenticate-all-privileged-users-and-any-other-positions-of-trust"></a>1173 すべての特権ユーザーおよびその他責任のある地位のユーザーの認証には多要素認証が使用される

- サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある
- サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある

### <a name="0421-passphrases-used-for-single-factor-authentication-are-a-minimum-of-14-characters-with-complexity-ideally-as-4-random-words"></a>0421 単一要素認証に使用されるパスフレーズは長さが 14 文字以上で、複雑さを備えている (4 つのランダムな単語を含むのが理想)

- "セキュリティ設定 - アカウント ポリシー" の Windows VM 構成の監査結果を表示する
- "セキュリティ設定 - アカウント ポリシー" の Windows VM 構成を監査するための前提条件をデプロイする

## <a name="guidelines-for-system-management---system-administration"></a>システム管理のためのガイドライン - システム管理

### <a name="1384-multi-factor-authentication-is-used-to-authenticate-users-each-time-they-perform-privileged-actions"></a>1384 特権を必要とする操作がユーザーによって実行されるたびに、多要素認証を使用してそのユーザーを認証する

- サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある
- サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある
- サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある

### <a name="1386-management-traffic-is-only-allowed-to-originate-from-network-zones-that-are-used-to-administer-systems-and-applications"></a>1386 管理トラフィックは、システムとアプリケーションの管理に使用されるネットワーク ゾーンからのみ送信が許可されている

- 仮想マシンで Just-In-Time ネットワーク アクセス制御を適用する必要がある
- API アプリでリモート デバッグを無効にする必要がある
- 関数アプリでリモート デバッグを無効にする必要がある
- Web アプリケーションのリモート デバッグを無効にする

## <a name="guidelines-for-system-management---system-patching"></a>システム管理のためのガイドライン - システムのパッチ適用

### <a name="1144-security-vulnerabilities-in-applications-and-drivers-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1144 アプリケーションおよびドライバーについて、きわめて高リスクと評価されたセキュリティ脆弱性には、ベンダー、独立したサード パーティ、システム管理者、またはユーザーがその脆弱性を特定してから 48 時間以内にパッチの適用、更新、軽減策が実施される

- SQL データベースの脆弱性を修復する必要がある
- 脆弱性評価を SQL サーバー上で有効にする必要がある
- 脆弱性評価を SQL マネージド インスタンス上で有効にする必要がある
- マシンで脆弱性評価を有効にする必要がある
- 仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある
- 脆弱性評価ソリューションによって脆弱性を修復する必要がある
- 使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある
- コンテナーのセキュリティ構成の脆弱性を修復する必要がある
- SQL Server の脆弱性評価の設定には、スキャン レポートを受信するためのメール アドレスが含まれている必要がある

### <a name="0940-security-vulnerabilities-in-applications-and-drivers-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>0940 アプリケーションおよびドライバーについて、高リスクと評価されたセキュリティ脆弱性には、ベンダー、独立したサード パーティ、システム管理者、またはユーザーがその脆弱性を特定してから 2 週間以内にパッチの適用、更新、軽減策が実施される

- SQL データベースの脆弱性を修復する必要がある
- 脆弱性評価を SQL サーバー上で有効にする必要がある
- 脆弱性評価を SQL マネージド インスタンス上で有効にする必要がある
- Virtual Machines で脆弱性評価を有効にする必要がある
- 仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある
- 脆弱性評価ソリューションによって脆弱性を修復する必要がある
- 使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある
- コンテナーのセキュリティ構成の脆弱性を修復する必要がある
- SQL Server の脆弱性評価の設定には、スキャン レポートを受信するためのメール アドレスが含まれている必要がある

### <a name="1472-security-vulnerabilities-in-applications-and-drivers-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1472 アプリケーションおよびドライバーについて、リスクが中または低と評価されたセキュリティ脆弱性には、ベンダー、独立したサード パーティ、システム管理者、またはユーザーがその脆弱性を特定してから 1 か月以内にパッチの適用、更新、軽減策が実施される

- SQL データベースの脆弱性を修復する必要がある
- 脆弱性評価を SQL サーバー上で有効にする必要がある
- 脆弱性評価を SQL マネージド インスタンス上で有効にする必要がある
- Virtual Machines で脆弱性評価を有効にする必要がある
- 仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある
- 脆弱性評価ソリューションによって脆弱性を修復する必要がある
- 使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある
- コンテナーのセキュリティ構成の脆弱性を修復する必要がある
- SQL Server の脆弱性評価の設定には、スキャン レポートを受信するためのメール アドレスが含まれている必要がある

### <a name="1494-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1494 オペレーティング システムおよびファームウェアについて、きわめて高リスクと評価されたセキュリティ脆弱性には、ベンダー、独立したサード パーティ、システム管理者、またはユーザーがその脆弱性を特定してから 48 時間以内にパッチの適用、更新、軽減策が実施される

- SQL データベースの脆弱性を修復する必要がある
- 脆弱性評価を SQL サーバー上で有効にする必要がある
- 脆弱性評価を SQL マネージド インスタンス上で有効にする必要がある
- Virtual Machines で脆弱性評価を有効にする必要がある
- 仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある
- 脆弱性評価ソリューションによって脆弱性を修復する必要がある
- 使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある
- コンテナーのセキュリティ構成の脆弱性を修復する必要がある
- SQL Server の脆弱性評価の設定には、スキャン レポートを受信するためのメール アドレスが含まれている必要がある

### <a name="1495-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1495 オペレーティング システムおよびファームウェアについて、高リスクと評価されたセキュリティ脆弱性には、ベンダー、独立したサード パーティ、システム管理者、またはユーザーがその脆弱性を特定してから 2 週間以内にパッチの適用、更新、軽減策が実施される

- SQL データベースの脆弱性を修復する必要がある
- 脆弱性評価を SQL サーバー上で有効にする必要がある
- 脆弱性評価を SQL マネージド インスタンス上で有効にする必要がある
- Virtual Machines で脆弱性評価を有効にする必要がある
- 仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある
- 脆弱性評価ソリューションによって脆弱性を修復する必要がある
- 使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある
- コンテナーのセキュリティ構成の脆弱性を修復する必要がある
- SQL Server の脆弱性評価の設定には、スキャン レポートを受信するためのメール アドレスが含まれている必要がある

### <a name="1496-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1496 オペレーティング システムおよびファームウェアについて、リスクが中または低と評価されたセキュリティ脆弱性には、ベンダー、独立したサード パーティ、システム管理者、またはユーザーがその脆弱性を特定してから 1 か月以内にパッチの適用、更新、軽減策が実施される

- SQL データベースの脆弱性を修復する必要がある
- 脆弱性評価を SQL サーバー上で有効にする必要がある
- 脆弱性評価を SQL マネージド インスタンス上で有効にする必要がある
- Virtual Machines で脆弱性評価を有効にする必要がある
- 仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある
- 脆弱性評価ソリューションによって脆弱性を修復する必要がある
- 使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある
- コンテナーのセキュリティ構成の脆弱性を修復する必要がある
- SQL Server の脆弱性評価の設定には、スキャン レポートを受信するためのメール アドレスが含まれている必要がある

## <a name="guidelines-for-system-management---data-backup-and-restoration"></a>システム管理のためのガイドライン - データ バックアップと復元

### <a name="1511-backups-of-important-information-software-and-configuration-settings-are-performed-at-least-daily"></a>1511 重要な情報、ソフトウェア、構成設定のバックアップが少なくとも 1 日に 1 回実行される

- ディザスター リカバリーが構成されていない仮想マシンの監査

## <a name="guidelines-for-system-monitoring---event-logging-and-auditing"></a>システム監視のためのガイドライン - イベント ログと監査

### <a name="1405-a-centralised-logging-facility-is-implemented-and-systems-are-configured-to-save-event-logs-to-the-centralised-logging-facility-as-soon-as-possible-after-each-event-occurs"></a>1405 一元化されたログ記録機構が実装され、イベントが発生したらできるだけ早く、そのログ記録機構にイベント ログを保存するようにシステムが構成されている

- Azure サブスクリプションにはアクティビティ ログのログ プロファイルが必要

### <a name="0582-the-following-events-are-logged-for-operating-systems-access-to-important-data-and-processes-application-crashes-and-any-error-messages-attempts-to-use-special-privileges-changes-to-accounts-changes-to-security-policy-changes-to-system-configurations-domain-name-system-dns-and-hypertext-transfer-protocol-http-requests-failed-attempts-to-access-data-and-system-resources-service-failures-and-restarts-system-startup-and-shutdown-transfer-of-data-to-external-media-user-or-group-management-use-of-special-privileges"></a>0582 オペレーティング システムに関して、次のイベントがログに記録される (重要なデータやプロセスへのアクセス、アプリケーションのクラッシュとエラー メッセージ、特別な権限を使用する試み、アカウントに対する変更、セキュリティ ポリシーに対する変更、システム構成に対する変更、ドメイン ネーム システム (DNS) とハイパーテキスト転送プロトコル (HTTP) の要求、データとシステム リソースへのアクセスの試行の失敗、サービスの障害と再開、システムの起動とシャットダウン、外部メディアへのデータの転送、ユーザーまたはグループの管理、特別な権限の使用)

- \[プレビュー\]:Audit Log Analytics エージェントのデプロイ - 一覧にない VM イメージ (OS)
- VMSS の Log Analytics エージェントのデプロイの監査 - VM イメージ (OS) が一覧にない
- VM 用 Audit Log Analytics ワークスペース - 不一致の報告
- 診断設定の監査

### <a name="1537-the-following-events-are-logged-for-databases-access-to-particularly-important-information-addition-of-new-users-especially-privileged-users-any-query-containing-comments-any-query-containing-multiple-embedded-queries-any-query-or-database-alerts-or-failures-attempts-to-elevate-privileges-attempted-access-that-is-successful-or-unsuccessful-changes-to-the-database-structure-changes-to-user-roles-or-database-permissions-database-administrator-actions-database-logons-and-logoffs-modifications-to-data-use-of-executable-commands"></a>1537 データベースに関して、次のイベントがログに記録される (特に重要な情報へのアクセス、新しいユーザー (特に特権ユーザー) の追加、コメントを含むクエリ、複数の埋め込みクエリを含んだクエリ、クエリまたはデータベースのアラートまたはエラー、権限昇格の試み、成功または失敗したアクセスの試行、データベース構造に対する変更、ユーザー ロールまたはデータベース権限に対する変更、データベース管理者アクション、データベースのログオンとログオフ、データの変更、実行可能コマンドの使用)

- Advanced Data Security を、SQL サーバー上で有効にする必要がある
- 診断設定の監査
- Advanced Data Security を SQL Managed Instance 上で有効にする必要がある

## <a name="guidelines-for-system-monitoring---vulnerability-management"></a>システム監視のガイドライン - 脆弱性の管理

### <a name="0911-vulnerability-assessments-and-penetration-tests-are-conducted-by-suitably-skilled-personnel-before-a-system-is-deployed-after-a-significant-change-to-a-system-and-at-least-annually-or-as-specified-by-the-system-owner"></a>0911 システムのデプロイ前、システムに対する大きな変更の後、年 1 回以上、またはシステム所有者から指示されたときに、適切なスキルを備えた担当者が脆弱性の評価と侵入テストを実施する

- SQL データベースの脆弱性を修復する必要がある
- 脆弱性評価を SQL サーバー上で有効にする必要がある
- 脆弱性評価を SQL マネージド インスタンス上で有効にする必要がある
- Virtual Machines で脆弱性評価を有効にする必要がある
- 仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある
- 脆弱性評価ソリューションによって脆弱性を修復する必要がある
- 使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある
- コンテナーのセキュリティ構成の脆弱性を修復する必要がある

## <a name="guidelines-for-database-systems-management---database-servers"></a>データベース システム管理のガイドライン - データベース サーバー

### <a name="1425-hard-disks-of-database-servers-are-encrypted-using-full-disk-encryption"></a>1425 フル ディスク暗号化を使用してデータベース サーバーのハード ディスクが暗号化されている

- 仮想マシンでディスク暗号化を適用する必要がある
- SQL データベースで Transparent Data Encryption を有効にする必要がある

### <a name="1277-information-communicated-between-database-servers-and-web-applications-is-encrypted"></a>1277 データベース サーバーと Web アプリケーションとの間でやり取りされる情報が暗号化されている

- Redis Cache に対してセキュリティで保護された接続のみを有効にする必要がある
- ストレージ アカウントへの安全な転送を有効にする必要がある
- セキュリティで保護された通信プロトコルを使用していない Windows Web サーバーの監査結果を表示する
- セキュリティで保護された通信プロトコルを使用していない Windows Web サーバーを監査する前提条件をデプロイする

## <a name="guidelines-for-database-systems-management---database-management-system-software"></a>データベース システム管理のガイドライン - データベース管理システム ソフトウェア

### <a name="1260-default-database-administrator-accounts-are-disabled-renamed-or-have-their-passphrases-changed"></a>1260 既定のデータベース管理者アカウントが無効にされているか、名前が変更されている、またはパスフレーズが変更されている

- SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある

### <a name="1262-database-administrators-have-unique-and-identifiable-accounts"></a>1262 データベース管理者に一意かつ識別可能なアカウントがある

- SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある

### <a name="1261-database-administrator-accounts-are-not-shared-across-different-databases"></a>1261 データベース管理者アカウントが複数のデータベース間で共有されていない

- SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある

### <a name="1263-database-administrator-accounts-are-used-exclusively-for-administrative-tasks-with-standard-database-accounts-used-for-general-purpose-interactions-with-database"></a>1263 データベース管理者アカウントが管理タスクにのみ使用され、一般的なデータベース操作の用途には標準データベース アカウントが使用されている

- SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある

### <a name="1264-database-administrator-access-is-restricted-to-defined-roles-rather-than-accounts-with-default-administrative-permissions-or-all-permissions"></a>1264 データベース管理者アクセスが、既定の管理者権限 (すべての権限) を持つアカウントではなく、規定されたロールに制限されている

- SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある

## <a name="guidelines-for-using-cryptography---cryptographic-fundamentals"></a>暗号を使用するためのガイドライン - 暗号に関する基礎的条件

### <a name="0459-encryption-software-used-for-data-at-rest-implements-full-disk-encryption-or-partial-encryption-where-access-controls-will-only-allow-writing-to-the-encrypted-partition"></a>0459 保存データ用の暗号化ソフトウェアにフル ディスク暗号化または (アクセスの制御により、暗号化されたパーティションにのみ書き込みが許可される) 部分暗号化が実装されている

- 仮想マシンでディスク暗号化を適用する必要がある

## <a name="guidelines-for-using-cryptography---transport-layer-security"></a>暗号を使用するためのガイドライン - トランスポート層セキュリティ

### <a name="1139-only-the-latest-version-of-tls-is-used"></a>1139 最新バージョンの TLS のみが使用されている

- API アプリでは最新の TLS バージョンを使用する必要がある
- Web アプリでは最新の TLS バージョンを使用する必要がある
- 関数アプリでは最新の TLS バージョンを使用する必要がある
- セキュリティで保護された通信プロトコルを使用していない Windows Web サーバーを監査する前提条件をデプロイする
- セキュリティで保護された通信プロトコルを使用していない Windows Web サーバーの監査結果を表示する

## <a name="guidelines-for-data-transfers-and-content-filtering---content-filtering"></a>データ転送とコンテンツ フィルタリングのガイドライン - コンテンツ フィルタリング

### <a name="1288-antivirus-scanning-using-multiple-different-scanning-engines-is-performed-on-all-content"></a>1288 複数の異なるスキャン エンジンを使用したウイルス対策スキャンがすべてのコンテンツに実行される

- Microsoft IaaSAntimalware 拡張機能は Windows Server 上にデプロイする必要がある
- エンドポイント保護ソリューションを仮想マシン スケール セットにインストールする必要がある
- Endpoint Protection の欠落の Azure Security Center での監視

## <a name="guidelines-for-data-transfers-and-content-filtering---web-application-development"></a>データ転送とコンテンツ フィルタリングのガイドライン - Web アプリケーション開発

### <a name="1552-all-web-application-content-is-offered-exclusively-using-https"></a>1552 すべての Web アプリケーション コンテンツが HTTPS のみを使用して提供される

- Function App には HTTPS 経由でのみアクセスできるようにする
- API アプリには HTTPS を介してのみアクセスできるようにする
- Web アプリケーションには HTTPS を介してのみアクセスできるようにする
- Redis Cache に対してセキュリティで保護された接続のみを有効にする必要がある

### <a name="1424-web-browser-based-security-controls-are-implemented-for-web-applications-in-order-to-help-protect-both-web-applications-and-their-users"></a>1424 Web アプリケーションとそのユーザーの両方の保護を促進するために、Web アプリケーションに Web ブラウザーベースのセキュリティ コントロールが実装されている

- CORS で、Web アプリケーションへのアクセスをすべてのリソースには許可しない

## <a name="guidelines-for-network-management---network-design-and-configuration"></a>ネットワーク管理のためのガイドライン - ネットワーク設計と構成

### <a name="0520-network-access-controls-are-implemented-on-networks-to-prevent-the-connection-of-unauthorised-network-devices"></a>0520 承認されていないネットワーク デバイスの接続を防ぐネットワーク アクセスの制御がネットワークに実装されている

- ストレージ アカウントに対する制限のないネットワーク アクセスの監査

### <a name="1182-network-access-controls-are-implemented-to-limit-traffic-within-and-between-network-segments-to-only-those-that-are-required-for-business-purposes"></a>1182 ネットワーク セグメント内またはネットワーク セグメント間のトラフィックを業務上必要なトラフィックに限定するために、ネットワーク アクセスの制御が実装されている

- インターネットに接続する仮想マシンは、ネットワーク セキュリティ グループを使用して保護する必要がある
- ストレージ アカウントに対する制限のないネットワーク アクセスの監査
- アダプティブ ネットワーク強化の推奨事項をインターネット接続仮想マシンに適用する必要がある

## <a name="guidelines-for-network-management---service-continuity-for-online-services"></a>ネットワーク管理のガイドライン - オンライン サービスのサービス継続性

### <a name="1431-denial-of-service-attack-prevention-and-mitigation-strategies-are-discussed-with-service-providers-specifically-their-capacity-to-withstand-denial-of-service-attacks-any-costs-likely-to-be-incurred-by-customers-resulting-from-denial-of-service-attacks-thresholds-for-notifying-customers-or-turning-off-their-online-services-during-denial-of-service-attacks-pre-approved-actions-that-can-be-undertaken-during-denial-of-service-attacks-denial-of-service-attack-prevention-arrangements-with-upstream-providers-to-block-malicious-traffic-as-far-upstream-as-possible"></a>1431 サービス拒否攻撃の阻止と軽減策がサービス プロバイダーとの間で協議されている (具体的には、サービス拒否攻撃に耐える能力、サービス拒否攻撃によって顧客が被る可能性のあるコスト、サービス拒否攻撃の発生中にオンライン サービスを無効にしたり顧客に通知したりする際のしきい値、サービス拒否攻撃の発生時に講じることができる事前承認されたアクション、サービス拒否攻撃を阻止するために、悪質なトラフィックを可能な限り上流でブロックするためにアップストリームのプロバイダーとの間で決められた段取りなど)

- DDoS Protection Standard を有効にする必要がある


> [!NOTE]
> 特定の Azure Policy 定義を利用できるかどうかは、Azure Government とその他の National Clouds で異なる場合があります。 

## <a name="next-steps"></a>次のステップ

ブループリントとその使用方法に関するその他の記事:

> [!div class="nextstepaction"]
> [ISM PROTECTED ブループリント - 概要](./index.md)
> [ISM PROTECTED ブループリント - デプロイ手順](./deploy.md)

- [ブループリントのライフサイクル](../../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../../how-to/update-existing-assignments.md)方法を参照する。
