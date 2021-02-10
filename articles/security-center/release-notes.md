---
title: Azure Security Center のリリース ノート
description: Azure Security Center の新機能と変更点の説明
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2021
ms.author: memildin
ms.openlocfilehash: 5dd58dd5f43481184b17ca4bdd694a1df76697db
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916467"
---
# <a name="whats-new-in-azure-security-center"></a>Azure Security Center の最新情報

Security Center のセキュリティは精力的な開発の下、継続的に改善されています。 常に最新の開発情報を把握していただけるよう、このページでは新しい機能、バグの修正、非推奨になった機能に関する情報を提供します。

このページは頻繁に更新されるため、定期的にアクセスしてご確認ください。 

Security Center で近日中に公開を "*予定されている*" 変更については、「[Azure Security Center への今後予定されている重要な変更](upcoming-changes.md)」を参照してください。 

> [!TIP]
> 6 か月以上前の項目を探す場合は、「[Azure Security Center の最新情報のアーカイブ](release-notes-archive.md)」をご覧ください。


## <a name="january-2021"></a>2021 年 1 月

1 月の更新プログラムには次のものが含まれます。

- [Azure セキュリティ ベンチマークが Azure Security Center の既定のポリシー イニシアティブに](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [オンプレミスおよびマルチクラウド マシンの脆弱性評価が一般提供 (GA) リリース](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [管理グループのセキュア スコア (プレビュー) が利用可能に](#secure-score-for-management-groups-is-now-available-in-preview)
- [Secure Scores API が一般提供 (GA) リリース](#secure-score-api-is-released-for-general-availability-ga)
- [Azure Defender for App Service に未解決の DNS 保護を追加](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [マルチクラウド コネクタが一般提供 (GA) リリース](#multi-cloud-connectors-are-released-for-general-availability-ga)
- [サブスクリプションと管理グループのセキュア スコアから推奨事項全体を除外する](#exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups)
- [テナント全体の可視性をユーザーがグローバル管理者にリクエスト可能に](#users-can-now-request-tenant-wide-visibility-from-their-global-administrator)
- [Azure セキュリティ ベンチマークのカバレッジを広げるために追加される、35 個のプレビュー推奨事項](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [フィルター処理された推奨事項一覧の CSV エクスポート](#csv-export-of-filtered-list-of-recommendations)
- [Azure Policy の評価で "適用除外" であったリソースが "準拠" とレポートされるように](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)
- [セキュア スコアと規制コンプライアンス データの週単位のスナップショットを連続エクスポートでエクスポート (プレビュー)](#export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview)


### <a name="azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center"></a>Azure セキュリティ ベンチマークが Azure Security Center の既定のポリシー イニシアティブに

Azure セキュリティ ベンチマークは Microsoft が作成したもので、一般的なコンプライアンス フレームワークに基づくセキュリティとコンプライアンスのベスト プラクティスに関する Azure 固有のガイドラインのセットです。 この広く遵守されているベンチマークは、クラウド中心のセキュリティに重点を置いた [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) と [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) の統制に基づいています。

ここ数か月で、Security Center の組み込みのセキュリティ推奨事項が大幅に拡充され、このベンチマークのカバー範囲が広がってきています。

このリリースからは、そのベンチマークが Security Center の推奨事項の基盤となり、既定のポリシー イニシアティブとして完全に統合されます。 

すべての Azure サービスには、対応するドキュメントにセキュリティ ベースライン ページが用意されています。 たとえば、[こちらは Security Center のベースライン](security-baseline.md)です。 それらのベースラインは、Azure セキュリティ ベンチマークに基づいて作成されています。

Security Center の規制コンプライアンス ダッシュボードを使用している場合、切り替え期間中は、ベンチマークのインスタンスが 2 つ表示されます。

:::image type="content" source="media/release-notes/regulatory-compliance-with-azure-security-benchmark.png" alt-text="Azure セキュリティ ベンチマークが表示されている Azure Security Center の規制コンプライアンス ダッシュボード":::

既存の推奨事項は影響を受けず、ベンチマークが増えるにつれて、Security Center 内に変更が自動的に反映されます。 

詳細については、次のページを参照してください。

- [Azure セキュリティ ベンチマークについての詳細情報](../security/benchmarks/introduction.md)
- [規制コンプライアンス ダッシュボードでの標準セットのカスタイマイズ](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>オンプレミスおよびマルチクラウド マシンの脆弱性評価が一般提供 (GA) リリース

Microsoft では 10 月に、[Azure Defender for servers](defender-for-servers-introduction.md) の統合脆弱性評価スキャナー (Qualys を使用) による、Azure Arc 対応サーバーのスキャンのプレビューを発表していました。

このほど、それが一般提供 (GA) リリースとなりました。

Azure 以外のマシンで Azure Arc を有効にした場合、Security Center では、統合された脆弱性スキャナーをそれらに手動で大規模にデプロイできるようにします。

この更新では、**Azure Defender for servers** の能力を最大限に活用して、すべての Azure および Azure 以外の資産にわたって脆弱性管理プログラムを統合できます。

主な機能は、次のとおりです。

- Azure Arc マシンでの VA (脆弱性評価) スキャナーのプロビジョニング状態を監視する
- 保護されていない Windows および Linux Azure Arc マシンに、統合された VA エージェントをプロビジョニングする (手動で大規模に)
- デプロイされたエージェントで検出された脆弱性を受け取って分析する (手動で大規模に)
- Azure VM と Azure Arc マシンでエクスペリエンスを統一する

[ハイブリッド マシンへの統合された脆弱性スキャナーのデプロイについての詳細を参照してください](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)。

[Azure Arc 対応のサーバーについての詳細を参照してください](../azure-arc/servers/index.yml)。


### <a name="secure-score-for-management-groups-is-now-available-in-preview"></a>管理グループのセキュア スコア (プレビュー) が利用可能に

セキュア スコア ページに、サブスクリプション レベルに加え、管理グループの集計セキュア スコアが表示されるようになりました。 そのため、組織内の管理グループと各管理グループのスコアを一覧表示することが可能になりました。

:::image type="content" source="media/secure-score-security-controls/secure-score-management-groups.png" alt-text="管理グループのセキュア スコアを表示する。":::

詳細については、[Azure Security Center のセキュリティ スコアとセキュリティ コントロール](secure-score-security-controls.md)に関するページを参照してください。

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>Secure Scores API が一般提供 (GA) リリース

[Secure Scores API](/rest/api/securitycenter/securescores/) を使用してスコアにアクセスできるようになりました。 この API メソッドを使用すると、データに対してクエリを実行したり、一定期間のセキュリティ スコアをレポートする独自のメカニズムを構築したりするための柔軟性が得られます。 たとえば、**Secure Scores** API を使用して、特定のサブスクリプションのスコアを取得できます。 また、**Secure Score Controls** API を使用して、サブスクリプションのセキュリティ コントロールと現在のスコアを一覧表示できます。

セキュリティ スコア API で実現可能な外部ツールの例については、「[GitHub コミュニティのセキュリティ スコア エリア](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)」を参照してください。

詳細については、[Azure Security Center のセキュリティ スコアとセキュリティ コントロール](secure-score-security-controls.md)に関するページを参照してください。


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>Azure Defender for App Service に未解決の DNS 保護を追加

サブドメインの乗っ取りは、組織にとって重大度の高い一般的な脅威です。 サブドメインの乗っ取りは、プロビジョニング解除された Web サイトを参照する DNS レコードがある場合に発生する可能性があります。 このような DNS レコードは、"未解決の DNS" エントリとも呼ばれます。 CNAME レコードは、この脅威に対して特に脆弱です。 

サブドメインの乗っ取りが発生すると、脅威アクターが悪意のあるアクティビティを実行しているサイトに、組織のドメイン向けのトラフィックをリダイレクトできるようになります。

今後は、App Service Web サイトの使用を停止する際、Azure Defender for App Service によって未解決の DNS エントリが検出されます。 DNS エントリの参照先リソースが存在せず、Web サイトがサブドメインの乗っ取りに対して無防備になる瞬間です。 これらの保護は、ドメインの管理に Azure DNS を使用しているか、外部のドメイン レジストラーを使用しているかに関係なく利用でき、Windows 上の App Service と Linux 上の App Service の両方に適用されます。

詳細情報:

- [App Service のアラート参照テーブル](alerts-reference.md#alerts-azureappserv) - 未解決の DNS エントリが検出されたときにトリガーされる新しい Azure Defender アラートが 2 つ追加されています
- [未解決の DNS エントリを防ぎ、サブドメインの乗っ取りを回避する](../security/fundamentals/subdomain-takeover.md) - サブドメインの乗っ取りの脅威と未解決の DNS に関する事柄について説明しています
- [Azure Defender for App Service の概要](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>マルチクラウド コネクタが一般提供 (GA) リリース

通常、クラウド ワークロードは複数のクラウド プラットフォームにまたがるため、クラウド セキュリティサービスもそうである必要があります。

Azure Security Center は、Azure、アマゾン ウェブ サービス (AWS)、および Google Cloud Platform (GCP) のワークロードを保護します。

AWS アカウントまたは GCP アカウントを接続すると、そのネイティブ セキュリティ ツール (AWS Security Hub、GCP Security Command Center など) が Azure Security Center に統合されます。

この機能は、あらゆる大手クラウド環境が、Security Center によってもたらされる可視性と保護の対象になることを意味します。 この統合のメリットをいくつか紹介します。

- エージェントの自動プロビジョニング - Security Center は Azure Arc を使用して、Log Analytics エージェントをお使いの AWS インスタンスにデプロイします
- ポリシー管理
- 脆弱性の管理
- 埋め込まれたエンドポイント検出と応答 (EDR)
- セキュリティ構成ミスの検出
- すべてのクラウド プロバイダーからのセキュリティに関する推奨事項を一元的に表示
- Security Center のセキュリティ スコアの計算へのすべてのリソースの組み込み
- AWS リソースと GCP リソースの規制コンプライアンスの評価

Security Center のメニューから、 **[マルチ クラウド コネクタ]** を選択すると、新しいコネクタを作成するためのオプションが表示されます。

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Security Center のマルチ クラウド コネクタ ページの [Add AWS account]\(AWS アカウントの追加\) ボタン":::

詳細情報:
- [Azure Security Center への AWS アカウントの接続](quickstart-onboard-aws.md)
- [Azure Security Center への GCP アカウントの接続](quickstart-onboard-gcp.md)


### <a name="exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups"></a>サブスクリプションと管理グループのセキュア スコアから推奨事項全体を除外する

現在、Microsoft では、推奨事項全体を除外できるように機能を拡張しています。 サブスクリプション、管理グループ、またはリソースに対して Security Center が行うセキュリティの推奨事項を微調整できるよう、さまざまなオプションを追加しているところです。

まれに、サードパーティ ツールによって問題が解決済みであるにもかかわらず、Security Center で検出されず、リソースが異常として表示されてしまうことがあります。 または、推奨事項が属していないと思われるスコープに表示されます。 この推奨事項は、特定のサブスクリプションに適していない可能性があります。 または、ご自分の組織が、単に特定のリソースまたは推奨事項に関連するリスクを受け入れることにしたのかもしれません。

このプレビュー機能では、推奨事項の除外を作成できるようになりました。

- **リソースを除外** して、将来、問題のあるリソースと共に一覧に表示されないようにします。セキュリティ スコアには影響しません。 リソースは適用外として表示され、その理由は "除外" として、選択した特定の正当な理由と共に示されます。

- **サブスクリプションまたは管理グループを除外** して、推奨事項がセキュリティ スコアに影響を与えないようにします。サブスクリプションまたは管理グループには今後表示されません。 これは、既存のリソースと、今後作成するすべてのリソースに関係してきます。 推奨事項は、選択したスコープに対して選択した特定の理由でマークされます。

詳細については、「[セキュリティ スコアからのリソースと推奨事項の除外](exempt-resource.md)」を参照してください。



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>テナント全体の可視性をユーザーがグローバル管理者にリクエスト可能に

Security Center のデータを表示するアクセス許可がないユーザーには、その組織のグローバル管理者にアクセス許可をリクエストするためのリンクが表示されるようになりました。 リクエストには、希望するロールとそれが必要な理由を添えることになります。

:::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="テナント全体のアクセス許可を要求できることをユーザーに通知するバナー。":::

詳細については、「[テナント全体のアクセス許可が不十分な場合に要求する](security-center-management-groups.md#request-tenant-wide-permissions-when-yours-are-insufficient)」を参照してください。


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Azure セキュリティ ベンチマークのカバレッジを広げるために追加される、35 個のプレビュー推奨事項

Azure セキュリティ ベンチマークが Azure Security Center の既定のポリシー イニシアティブに 

このベンチマークのカバー範囲を拡大するために、次の 35 個のプレビュー推奨事項が Security Center に追加されています。

> [!TIP]
> プレビューの推奨事項によってリソースが異常な状態になることはありません。これらの推奨事項は、セキュリティ スコアの計算には含まれません。 これらの推奨事項はプレビュー期間が終了した時点でスコアに反映されるため、可能な限り修復してください。 これらの推奨事項に対応する方法については、「[Azure Security Center の修復レコメンデーション](security-center-remediate-recommendations.md)」を参照してください。

| セキュリティ コントロール                     | 新しい推奨事項                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 保存時の暗号化を有効にする            | - Azure Cosmos DB アカウントでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある<br>- Azure Machine Learning ワークスペースは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある<br>- MySQL サーバーに対して Bring Your Own Key データ保護を有効にする必要がある<br>- PostgreSQL サーバーに対して Bring Your Own Key データ保護を有効にする必要がある<br>- Cognitive Services アカウントでカスタマー マネージド キー (CMK) によるデータ暗号化を有効にする必要がある<br>- コンテナー レジストリは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある<br>- SQL マネージド インスタンスでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある<br>- SQL サーバーでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある<br>- ストレージ アカウントでは暗号化にカスタマー マネージド キー (CMK) を使用する必要がある                                                                                                                                                              |
| セキュリティのベストプラクティスを実装する    | - サブスクリプションには、セキュリティの問題に備えて連絡先メール アドレスが用意されている必要がある<br> - 自分のサブスクリプションで Log Analytics エージェントの自動プロビジョニングを有効にする必要がある<br> - 重要度が高いアラートの電子メール通知を有効にする必要がある<br> - 重要度が高いアラートについて、サブスクリプション所有者に対する電子メール通知を有効にする必要がある<br> - キー コンテナーで消去保護が有効になっている必要がある<br> - キー コンテナーで論理的な削除が有効になっている必要がある |
| アクセスおよびアクセス許可の管理        | - 関数アプリで "クライアント証明書 (着信クライアント証明書)" を有効にする必要がある |
| DDoS 攻撃からアプリケーションを保護する | - Application Gateway に対して Web アプリケーション ファイアウォール (WAF) を有効にする必要がある<br> - Azure Front Door Service サービスに対して Web アプリケーション ファイアウォール (WAF) を有効にする必要がある |
| 承認されていないネットワーク アクセスの制限 | - キー コンテナーでファイアウォールを有効にする必要がある<br> - キー コンテナー用にプライベート エンドポイントを構成する必要がある<br> - App Configuration ではプライベート リンクを使用する必要がある<br> - Azure Cache for Redis は仮想ネットワーク内に存在しなければならない<br> - Azure Event Grid ドメインではプライベート リンクを使用する必要がある<br> - Azure Event Grid トピックではプライベート リンクを使用する必要がある<br> - Azure Machine Learning ワークスペースではプライベート リンクを使用する必要がある<br> - Azure SignalR Service ではプライベート リンクを使用する必要がある<br> - Azure Spring Cloud ではネットワークの挿入を使用する必要がある<br> - コンテナー レジストリでは無制限のネットワーク アクセスを許可しない<br> - コンテナー レジストリではプライベート リンクを使用する必要がある<br> - MariaDB サーバーでは、公衆ネットワーク アクセスを無効にする必要がある<br> - MySQL サーバーでは、公衆ネットワーク アクセスを無効にする必要がある<br> - PostgreSQL サーバーでは、公衆ネットワーク アクセスを無効にする必要がある<br> - ストレージ アカウントではプライベート リンク接続を使用する必要がある<br> - ストレージ アカウントでは、仮想ネットワーク ルールを使用してネットワーク アクセスを制限する必要がある<br> - VM Image Builder テンプレートでは、プライベート リンクを使用する必要がある|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

関連リンク:

- [Azure セキュリティ ベンチマークについての詳細情報](../security/benchmarks/introduction.md)
- [Azure Database for MariaDB についての詳細情報](../mariadb/overview.md)
- [Azure Database for MySQL についての詳細情報](../mysql/overview.md)
- [Azure Database for PostgreSQL についての詳細情報](../postgresql/overview.md)




### <a name="csv-export-of-filtered-list-of-recommendations"></a>フィルター処理された推奨事項一覧の CSV エクスポート 

2020 年 11 月には、推奨事項のページにフィルターを追加しました (「[推奨事項の一覧にフィルターを追加](#recommendations-list-now-includes-filters)」を参照してください)。 12 月には、それらのフィルターを拡張しました (「[環境、重大度、利用可能な応答用の新しいフィルターが推奨事項のページに追加](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)」を参照してください)。 

今回は、フィルター処理された一覧に表示されている推奨事項のみが CSV エクスポートの対象となるよう、 **[Download to CSV]\(CSV にダウンロード\)** ボタンの動作を変更しています。 

たとえば、以下の画像を見ると、一覧がフィルター処理されて 2 つの推奨事項が表示されていることがわかります。 生成された CSV ファイルには、それらの 2 つの推奨事項に関係する各リソースの状態情報が含まれています。   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="フィルター処理された推奨事項を CSV ファイルにエクスポートする":::

詳細については、[Azure Security Center でのセキュリティに関する推奨事項](security-center-recommendations.md)を参照してください。


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>Azure Policy の評価で "適用除外" であったリソースが "準拠" とレポートされるように

以前は、Azure Policy の推奨事項で **適用除外** と評価されたリソースが "非準拠" と表示されていました。 ユーザー アクションによって状態を "準拠" に変更することはできませんでした。 今回の変更以降、わかりやすくするためにこれらは "準拠" としてレポートされます。

Azure Policy への影響は 1 つだけで、準拠しているリソースの数が増えます。 Azure Security Center のセキュア スコアには影響はありません。


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>セキュア スコアと規制コンプライアンス データの週単位のスナップショットを連続エクスポートでエクスポート (プレビュー)

セキュア スコアと規制コンプライアンス データの週単位のスナップショットをエクスポートするための新しいプレビュー機能を[連続エクスポート](continuous-export.md) ツールに追加しました。

エクスポートの頻度は、連続エクスポートを定義するときに設定します。

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="連続エクスポートの頻度を選択する":::

- **ストリーミング** - リソースの正常性状態が更新されたときに評価がリアルタイムで送信されます (更新がなければデータは送信されません)。
- **スナップショット** - 規制コンプライアンスの全評価を対象に現在の状態のスナップショットが毎週送信されます (セキュア スコアと規制コンプライアンス データの週単位のスナップショットに関するプレビュー機能)。

この機能の全容については、「[Security Center のデータを連続的にエクスポートする](continuous-export.md)」を参照してください。

## <a name="december-2020"></a>2020 年 12 月

12 月の更新プログラムには次のものが含まれます。

- [マシン上の SQL サーバー向け Azure Defender の一般提供開始](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [Azure Synapse Analytics の専用 SQL プールに対する Azure Defender for SQL のサポートの一般提供開始](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [グローバル管理者が自分にテナントレベルのアクセス許可を付与できるようになりました](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [2 つの新しい Azure Defender プラン: Azure Defender for DNS と Azure Defender for Resource Manager (プレビュー段階)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Azure portal の新しいセキュリティ アラート ページ (プレビュー)](#new-security-alerts-page-in-the-azure-portal-preview)
- [Azure SQL Database と SQL Managed Instance における Security Center エクスペリエンスの刷新](#revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance)
- [資産インベントリ ツールとフィルターの更新](#asset-inventory-tools-and-filters-updated)
- [SSL 証明書を要求する Web アプリに関する推奨事項がセキュリティ スコアの対象から除外](#recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score)
- [環境、重大度、利用可能な応答用の新しいフィルターが推奨事項のページに追加](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)
- [連続エクスポートで新しいデータ型が利用可能になり、deployifnotexist ポリシーが改善](#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)


### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>マシン上の SQL サーバー向け Azure Defender の一般提供開始

Azure Security Center には、2 つの SQL Server 用 Azure Defender プランが用意されています。

- **Azure SQL データベース サーバー向け Azure Defender** - Azure ネイティブの SQL Server を保護します 
- **マシン上の SQL サーバー向け Azure Defender** - ハイブリッド、マルチクラウド、およびオンプレミスの環境で SQL サーバーに対して同様の保護を拡張します

本発表以降は、**Azure Defender for SQL** によって、データベースとそのデータが場所にかかわらず保護されます。

Azure Defender for SQL には、脆弱性評価機能が用意されています。 脆弱性評価ツールには、次の高度な機能が含まれています。

- **ベースライン構成** (新機能) を使用すると、脆弱性スキャンの結果を、実際のセキュリティ問題を示す可能性があるものにインテリジェントに絞り込むことができます。 ベースラインのセキュリティ状態を確立した後は、脆弱性評価ツールによって、そのベースラインからの逸脱のみがレポートされます。 ベースラインに一致する結果は、それ以降のスキャンで合格と見なされます。 これにより、ユーザーとアナリストは、重要な問題に注意を集中できます。
- **詳細なベンチマーク情報** は、検出された結果や、それらとリソースの関連性を "*把握する*" のに役立ちます。
- **修復スクリプト** は、特定されたリスクを軽減するのに役立ちます。

[Azure Defender for SQL](defender-for-sql-introduction.md) についてさらに詳しく学習します。


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>Azure Synapse Analytics の専用 SQL プールに対する Azure Defender for SQL のサポートの一般提供開始

Azure Synapse Analytics (以前の SQL DW) は、エンタープライズ データ ウェアハウスとビッグ データの分析を兼ね備えた分析サービスです。 専用 SQL プールは、Azure Synapse のエンタープライズ データ ウェアハウス機能です。 詳細については、[Azure Synapse Analytics (以前の SQL DW) の概要](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)に関するページを参照してください。

Azure Defender for SQL では、次のものを使用して専用 SQL プールを保護します。

- **高度な脅威保護** - 脅威と攻撃を検出します 
- **脆弱性評価機能** - セキュリティに関する構成ミスを特定して修復します

Azure Defender for SQL での Azure Synapse Analytics SQL プールのサポートは、Azure Security Center 内の Azure SQL データベース バンドルに自動的に追加されます。 Azure portal の [Synapse ワークスペース] ページに、新しい [Azure Defender for SQL] タブが表示されます。

[Azure Defender for SQL](defender-for-sql-introduction.md) についてさらに詳しく学習します。


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>グローバル管理者が自分にテナントレベルのアクセス許可を付与できるようになりました

Azure Active Directory ロールが **グローバル管理者** であるユーザーは、テナント全体の責任を持つ場合がありますが、Azure Security Center でその組織全体の情報を表示するための Azure アクセス許可がありません。 

テナントレベルのアクセス許可を自分に割り当てるには、「[テナント全体のアクセス許可を自分に付与する](security-center-management-groups.md#grant-tenant-wide-permissions-to-yourself)」セクションの手順に従います。


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>2 つの新しい Azure Defender プラン: Azure Defender for DNS と Azure Defender for Resource Manager (プレビュー段階)

Azure 環境向けに、2 つの新しいクラウドネイティブ範囲の脅威防止機能が追加されました。

これらの新しい保護により、脅威アクターからの攻撃に対する回復性が強化され、Azure Defender によって保護される Azure リソースの数が大幅に増加します。

- **Azure Defender for Resource Manager** - 組織で実行されるすべてのリソース管理操作を自動的に監視します。 詳細については、次を参照してください。
    - [Azure Defender for Resource Manager の概要](defender-for-resource-manager-introduction.md)
    - [Azure Defender for Resource Manager アラートに対応する](defender-for-resource-manager-usage.md)
    - [Azure Defender for Resource Manager で提供されるアラートの一覧](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender for DNS** - Azure リソースからのすべての DNS クエリを継続的に監視します。 詳細については、次を参照してください。
    - [Azure Defender for DNS の概要](defender-for-dns-introduction.md)
    - [Azure Defender for DNS アラートに対応する](defender-for-dns-usage.md)
    - [Azure Defender for DNS で提供されるアラートの一覧](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Azure portal の新しいセキュリティ アラート ページ (プレビュー)

Azure Security Center のセキュリティ アラート ページは、次の機能を提供できるように再設計されました。

- **アラートのトリアージ エクスペリエンスの向上** - アラートに対応する労力を減らし、最も関連性の高い脅威に焦点を絞りやすくするために、一覧にはカスタマイズ可能なフィルターとグループ化のオプションが追加されました
- **アラート一覧への情報の追加** - MITRE ATT & ACK の戦術など
- **サンプル アラートを作成するためのボタン** - Azure Defender の機能を評価し、アラートの構成 (SIEM 統合、メール通知、ワークフローの自動化) をテストします。すべての Azure Defender プランでサンプル アラートを作成できます
- **Azure Sentinel のインシデント エクスペリエンスとの連携** - 両方の製品を使用するお客様は、より簡単に 2 つを切り替えることによって、どちらの製品からも、もう一方の製品が提供する情報を取得しやすくなりました
- 長大なアラート一覧の **パフォーマンス向上**
- アラート一覧の **キーボードによるナビゲーション**
- **Azure Resource Graph のアラート** - Azure Resource Graph (すべてのリソースを対象とする、Kusto に似た API) のアラートにクエリを実行できます。 これは、独自のアラート ダッシュボードを作成している場合にも役立ちます。 [Azure Resource Graph の詳細についてさらに学習します](../governance/resource-graph/index.yml)。

新しいエクスペリエンスにアクセスするには、セキュリティ アラート ページの上部にあるバナーの "今すぐ試す" リンクを使用します。

:::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="新しいアラート エクスペリエンス (プレビュー) へのリンクが表示されたバナー":::

新しいアラート エクスペリエンスでサンプル アラートを作成するには、「[Azure Defender のサンプル アラートを生成する](security-center-alert-validation.md#generate-sample-azure-defender-alerts)」を参照してください。


### <a name="revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance"></a>Azure SQL Database と SQL Managed Instance における Security Center エクスペリエンスの刷新 

SQL 内の Security Center エクスペリエンスでは、Security Center と Azure Defender for SQL の次の機能にアクセスできます。

- **セキュリティに関する推奨事項** - Security Center では、接続されているすべての Azure リソースのセキュリティの状態を定期的に分析して、潜在的なセキュリティの構成ミスを特定します。 そのうえで、それらの脆弱性を修復し、組織のセキュリティ体制を改善する方法に関する推奨事項を提供します。
- **セキュリティ アラート** - SQL インジェクション、ブルートフォース攻撃、特権の悪用などの脅威がないか、Azure SQL アクティビティを継続的に監視する検出サービス。 このサービスは、Security Center で詳細なアクション指向のセキュリティ アラートをトリガーし、Microsoft の Azure ネイティブ SIEM ソリューションである Azure Sentinel を使用して調査を継続するためのオプションを提供します。
- **調査結果** – Azure SQL 構成を継続的に監視し、脆弱性を修復するのに役立つ脆弱性評価サービスです。 評価スキャンでは、Azure SQL のセキュリティ状態の概要が詳細なセキュリティ調査結果と一緒に示されます。     

:::image type="content" source="media/release-notes/azure-security-center-experience-in-sql.png" alt-text="Azure Security Center の SQL 用セキュリティ機能が Azure SQL 内から利用可能":::


### <a name="asset-inventory-tools-and-filters-updated"></a>資産インベントリ ツールとフィルターの更新

Azure Security Center のインベントリ ページが更新され、次の変更が加えられました。

- **[Guides and feedback]\(ガイドとフィードバック\)** がツール バーに追加されました。 これにより、関連する情報やツールへのリンクがあるペインが開きます。 
- **サブスクリプション フィルター** が、リソースで使用可能な既定のフィルターに追加されました。
- 現在のフィルター オプションを Azure Resource Graph クエリとして開くための **[クエリを開く]** リンク (以前は "Resource Graph エクスプローラーで表示" という名前でした)。
- 各フィルターの **演算子オプション**。 "=" に加えて追加の論理演算子を選択できるようになりました。 たとえば、タイトルに "encrypt" という文字列が含まれているアクティブな推奨事項があるすべてのリソースを検索することができます。 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="資産インベントリのフィルターでの演算子オプションのコントロール":::

インベントリの詳細については、「[資産インベントリを使用してリソースの調査と管理を行う](asset-inventory.md)」を参照してください。


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>SSL 証明書を要求する Web アプリに関する推奨事項がセキュリティ スコアの対象から除外

推奨事項 "Web apps should request an SSL certificate for all incoming requests (Web アプリではすべての受信要求に対して SSL 証明書を要求する必要がある)" が、 **[Manage access and permissions]\(アクセスおよびアクセス許可の管理\)** (最大 4 ポイントに相当) セキュリティ コントロールから **[Implement security best practices]\(セキュリティのベスト プラクティスを実装する\)** (ポイントなしに相当) に移動されました。 

Web アプリが証明書を要求するようにすることで、確実にセキュリティが向上します。 ただし、公開されている Web アプリの場合は関係ありません。 HTTPS ではなく HTTP 経由でサイトにアクセスする場合は、クライアント証明書を受信しません。 したがって、アプリケーションにクライアント証明書が必要な場合は、HTTP 経由でのアプリケーションへの要求を許可しないでください。 詳細については、「[Azure App Service に対する TLS 相互認証の構成](../app-service/app-service-web-configure-tls-mutual-auth.md)」を参照してください。

この変更により、この推奨事項は、スコアに影響を与えない、推奨のベスト プラクティスになりました。 

各セキュリティ コントロールに含まれる推奨事項については、「[セキュリティ コントロールとその推奨事項](secure-score-security-controls.md#security-controls-and-their-recommendations)」を参照してください。


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>環境、重大度、利用可能な応答用の新しいフィルターが推奨事項のページに追加

Azure Security Center では、接続されているすべてのリソースを監視し、セキュリティの推奨事項を生成します。 これらの推奨事項を使用して、ハイブリッド クラウドの体制を強化し、組織、業界、および国に関連するポリシーと標準へのコンプライアンス状況を追跡します。

Security Center のカバレッジと機能が拡張されるにつれて、セキュリティに関する推奨事項の一覧が毎月増えていきます。 たとえば、「[Azure セキュリティ ベンチマークのカバレッジを広げるために追加された、29 個のプレビュー推奨事項](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)」を参照してください。

一覧が増加するのに伴い、フィルターを使用して最も関心のある推奨事項を絞り込む機能が求められるようになりました。 11 月に、推奨事項のページにフィルターを追加しました (「[推奨事項の一覧にフィルターを追加](#recommendations-list-now-includes-filters)」を参照してください)。

この月に追加されたフィルターには、次の条件で推奨事項の一覧を絞り込むためのオプションがあります。

- **環境** - AWS、GCP、または Azure リソース (または任意の組み合わせ) の推奨事項を表示します
- **重大度** - Security Center によって設定された重大度分類に従って推奨事項を表示します
- **応答アクション** - Security Center の応答オプションの可用性に応じて推奨事項を表示します (クイック修正、拒否、強制)

    > [!TIP]
    > 応答アクション フィルターは、 **[Quick fix available (Yes/No)]\(クイック修正を使用できます (はい/いいえ)\)** フィルターに置き換わるものです。 
    > 
    > これらの応答オプションの詳細については、次を参照してください。
    > - [クイック修正による修復](security-center-remediate-recommendations.md#quick-fix-remediation)
    > - [適用/拒否の推奨事項を使用した構成ミスの防止](prevent-misconfigurations.md)

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="セキュリティ コントロールにグループ化された推奨事項" lightbox="./media/release-notes/added-recommendations-filters.png":::

### <a name="continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies"></a>連続エクスポートで新しいデータ型が利用可能になり、deployifnotexist ポリシーが改善

Azure Security Center の連続エクスポート ツールを使用すると、環境内の他の監視ツールで使用するために Security Center の推奨事項とアラートをエクスポートできます。

連続エクスポートを使用して、エクスポートする "内容" とエクスポート先の "場所" を完全にカスタマイズできます。 詳細については、「[Security Center のデータを連続的にエクスポートする](continuous-export.md)」を参照してください。

これらのツールは、次の点で強化および拡張されました。

- **連続エクスポートの deployifnotexist ポリシーの強化**。 これらのポリシーで、以下のことが可能になりました。

    - **構成が有効になっているかどうかの確認。** 有効になっていない場合、ポリシーによって非準拠として表示され、準拠しているリソースが作成されます。 提供されている Azure Policy テンプレートの詳細については、「[連続エクスポートを設定する](continuous-export.md#set-up-a-continuous-export)」の [Azure ポリシーを使用して大規模にデプロイする] タブを参照してください。

    - **セキュリティ調査結果のエクスポートのサポート。** Azure Policy テンプレートを使用する場合、調査結果が含まれるように連続エクスポートを構成できます。 これは、"サブ" 推奨事項 (たとえば、脆弱性評価スキャナーからの調査結果) や、"親" 推奨事項 "システム更新プログラムをマシンにインストールする必要がある" に対する特定のシステム更新プログラムを含む推奨事項をエクスポートする場合に関連します。
    
    - **セキュリティ スコア データのエクスポートのサポート。**

- **規制コンプライアンス評価データの追加 (プレビュー)。** 任意のカスタム イニシアティブを含め、規制コンプライアンス評価の更新を Log Analytics ワークスペースまたはイベント ハブに連続的にエクスポートできるようになりました。 この機能は、各国のクラウドまたはソブリン クラウドでは使用できません。

    :::image type="content" source="media/release-notes/continuous-export-regulatory-compliance-option.png" alt-text="連続エクスポート データに規制コンプライアンス評価情報を含めるためのオプション。":::


## <a name="november-2020"></a>2020 年 11 月

11 月の更新プログラムには次のものが含まれます。

- [Azure セキュリティ ベンチマークのカバレッジを広げるために追加された、29 個のプレビュー推奨事項](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [Security Center の法令遵守ダッシュボードに追加された NIST SP 800 171 R2](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [推奨事項の一覧にフィルターを追加](#recommendations-list-now-includes-filters)
- [自動プロビジョニング エクスペリエンスの向上と拡張](#auto-provisioning-experience-improved-and-expanded)
- [連続エクスポートでセキュア スコアが利用可能に (プレビュー)](#secure-score-is-now-available-in-continuous-export-preview)
- ["システム更新プログラムをマシンにインストールする必要がある" 推奨事項にサブ推奨事項を追加](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations)
- [Azure portal のポリシー管理ページに、既定のポリシー割り当ての状態を表示](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Azure セキュリティ ベンチマークのカバレッジを広げるために追加された、29 個のプレビュー推奨事項

Azure セキュリティ ベンチマークは Microsoft が作成したもので、一般的なコンプライアンス フレームワークに基づくセキュリティとコンプライアンスのベスト プラクティスに関する Azure 固有のガイドラインのセットです。 [Azure セキュリティ ベンチマークの詳細を確認してください](../security/benchmarks/introduction.md)。

ベンチマークのカバレッジを拡大するために、次の 29 個のプレビュー推奨事項が Security Center に追加されています。

プレビューの推奨事項によってリソースが異常な状態になることはありません。これらの推奨事項は、セキュリティ スコアの計算には含まれません。 これらの推奨事項はプレビュー期間が終了した時点でスコアに反映されるため、可能な限り修復してください。 これらの推奨事項に対応する方法については、「[Azure Security Center の修復レコメンデーション](security-center-remediate-recommendations.md)」を参照してください。

| セキュリティ コントロール                     | 新しい推奨事項                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 転送中のデータを暗号化する              | - PostgreSQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない<br>- MySQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない<br>- API アプリに対して TLS を最新バージョンに更新する必要がある<br>- 関数アプリに対して TLS を最新バージョンに更新する必要がある<br>- Web アプリに対して TLS を最新バージョンに更新する必要がある<br>- API アプリでは FTPS を必須とする<br>- 関数アプリでは FTPS を必須とする<br>- Web アプリでは FTPS を必須とする                                                                                                                                                                                                                                                                                                                                                                                                                           |
| アクセスおよびアクセス許可の管理        | - Web アプリではすべての受信要求に対して SSL 証明書を要求する必要がある<br>- API アプリではマネージド ID を使用する必要がある<br>- 関数アプリではマネージド ID を使用する必要がある<br>- Web アプリではマネージド ID を使用する必要がある                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 承認されていないネットワーク アクセスの制限 | - PostgreSQL サーバーに対してプライベート エンドポイントを有効にする必要がある<br>- MariaDB サーバーに対してプライベート エンドポイントを有効にする必要がある<br>- MySQL サーバーに対してプライベート エンドポイントを有効にする必要がある                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| 監査とログ記録を有効にする          | - App Services の診断ログを有効にする必要があります                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| セキュリティのベストプラクティスを実装する    | - 仮想マシンに対して Azure Backup を有効にする必要がある<br>- Azure Database for MariaDB の geo 冗長バックアップを有効にする必要がある<br>- Azure Database for MySQL の geo 冗長バックアップを有効にする必要がある<br>- Azure Database for PostgreSQL の geo 冗長バックアップを有効にする必要がある<br>- API アプリに対して PHP を最新バージョンに更新する必要がある<br>- Web アプリに対して PHP を最新バージョンに更新する必要がある<br>- API アプリに対して Java を最新バージョンに更新する必要がある<br>- 関数アプリに対して Java を最新バージョンに更新する必要がある<br>- Web アプリに対して Java を最新バージョンに更新する必要がある<br>- API アプリに対して Python を最新バージョンに更新する必要がある<br>- 関数アプリに対して Python を最新バージョンに更新する必要がある<br>- Web アプリに対して Python を最新バージョンに更新する必要がある<br>- SQL サーバーの監査のリテンション期間は少なくとも 90 日に設定する必要がある |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

関連リンク:

- [Azure セキュリティ ベンチマークについての詳細情報](../security/benchmarks/introduction.md)
- [Azure API アプリについての詳細情報](../app-service/app-service-web-tutorial-rest-api.md)
- [Azure 関数アプリについての詳細情報](../azure-functions/functions-overview.md)
- [Azure Web アプリについての詳細情報](../app-service/overview.md)
- [Azure Database for MariaDB についての詳細情報](../mariadb/overview.md)
- [Azure Database for MySQL についての詳細情報](../mysql/overview.md)
- [Azure Database for PostgreSQL についての詳細情報](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>Security Center の法令遵守ダッシュボードに追加された NIST SP 800 171 R2

NIST SP 800-171 R2 標準が、Azure Security Center の法令遵守ダッシュボードで使用するための組み込みイニシアティブとして使用できるようになりました。 コントロールのマッピングの詳細については、「[NIST SP 800-171 R2 規制コンプライアンスの組み込みイニシアチブの詳細](../governance/policy/samples/nist-sp-800-171-r2.md)」を参照してください。 

標準をサブスクリプションに適用し、対応状態を継続的に監視するには、「[規制コンプライアンス ダッシュボードでの標準セットのカスタイマイズ](update-regulatory-compliance-packages.md)」の手順を使用します。

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="Security Center の法令遵守ダッシュボードでの NIST SP 800 171 R2 標準":::

このコンプライアンス標準の詳細については、[NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final) に関するページを参照してください。


### <a name="recommendations-list-now-includes-filters"></a>推奨事項の一覧にフィルターを追加

さまざまな条件に従って、セキュリティに関する推奨事項の一覧をフィルター処理できるようになりました。 次の例では、以下の条件を満たす推奨事項を表示するように、推奨事項の一覧がフィルター処理されています。

- **一般提供** されている (つまり、プレビューではない)
- **ストレージ アカウント** を対象とする
- **クイック修正** による修復をサポートしている

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="推奨事項の一覧のフィルター":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>自動プロビジョニング エクスペリエンスの向上と拡張

自動プロビジョニング機能は、Security Center の保護によるメリットが得られるように、必要な拡張機能を新規および既存の Azure VM にインストールすることで管理オーバーヘッドの削減を支援します。 

Azure Security Center の拡大に伴って、より多くの拡張機能が開発されています。Security Center では、リソースの種類の大規模な一覧を監視できます。 自動プロビジョニング ツールが拡張され、Azure Policy の機能を活用して、追加の拡張機能とリソースの種類がサポートされるようになりました。

次の自動プロビジョニングを構成できるようになりました。

- Log Analytics エージェント
- (新) Kubernetes 用の Azure Policy アドオン
- (新) Microsoft Dependency Agent

詳細については、「[Azure Security Center からのエージェントと拡張機能の自動プロビジョニング](security-center-enable-data-collection.md)」をご覧ください。


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>連続エクスポートでセキュア スコアが利用可能に (プレビュー)

セキュア スコアの連続エクスポートを使用すると、スコアの変更を Azure Event Hubs または Log Analytics ワークスペースにリアルタイムでストリーム配信することができます。 この機能を使用すると、次のことができます。

- 動的レポートを使用し、一定期間にわたってセキュア スコアを追跡する
- Azure Sentinel (または他の SIEM) にセキュア スコア データをエクスポートする
- そのデータを、組織内でセキュア スコアを監視する目的で既に使用しているプロセスに統合する

[Security Center のデータを連続的にエクスポートする](continuous-export.md)方法についての詳しい情報をご覧ください。


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations"></a>"システム更新プログラムをマシンにインストールする必要がある" 推奨事項にサブ推奨事項を追加

**システム更新プログラムをマシンにインストールする必要がある** 推奨事項が強化されています。 新しいバージョンには、不足している更新プログラムごとのサブ推奨事項が含まれているほか、次の点が改善されています。

- Azure portal の Azure Security Center ページでの再設計されたエクスペリエンス。 "**システム更新プログラムをマシンにインストールする必要がある**" に関する推奨事項の詳細ページには、次に示すような結果の一覧が表示されます。 1 つの結果を選択すると、詳細ウィンドウが開き、修復情報および影響を受けるリソースの一覧へのリンクが表示されます。

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="更新された推奨事項のポータル エクスペリエンスで、サブ推奨事項のいずれかを開く":::

- Azure Resource Graph (ARG) からの推奨事項用に強化されたデータ。 ARG は、効率的なリソース探索を実現するように設計されている Azure のサービスです。 ARG を使用すると、特定のサブスクリプション セットの大規模なクエリを実行し、環境を効果的に管理できます。 

    Azure Security Center では、ARG および [Kusto クエリ言語 (KQL)](/azure/data-explorer/kusto/query/) を使用して、幅広いセキュリティ態勢データに対してクエリを実行できます。

    以前は、この推奨事項を ARG で照会した場合、取得できるのは、マシン上でその推奨事項を修復する必要があるという情報のみでした。 次に示す強化されたバージョンのクエリを実行すると、不足している各システム更新プログラムがマシン別にグループ化されて返されます。

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>Azure portal のポリシー管理ページに、既定のポリシー割り当ての状態を表示

サブスクリプションに既定の Security Center ポリシーが割り当てられているかどうかを確認できます。これは、Azure portal の Security Center の **[セキュリティ ポリシー]** ページに表示されます。

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="既定のポリシー割り当てを示す Azure Security Center の [ポリシー管理] ページ":::

## <a name="october-2020"></a>2020 年 10 月

10 月の更新プログラムには次のものが含まれます。
- [オンプレミスおよびマルチクラウド マシンの脆弱性評価 (プレビュー)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Azure Firewall の推奨事項の追加 (プレビュー)](#azure-firewall-recommendation-added-preview)
- ["Kubernetes Services では承認された IP 範囲を定義する必要があります" という推奨事項のクイック修正による更新](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [規制コンプライアンス ダッシュボードへの、標準を削除するオプションの追加](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [Azure Resource Graph (ARG) からの Microsoft.Security/securityStatuses テーブルの削除](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>オンプレミスおよびマルチクラウド マシンの脆弱性評価 (プレビュー)

[Azure Defender for servers](defender-for-servers-introduction.md) の統合された脆弱性評価スキャナー (Qualys を使用) により、Azure Arc 対応サーバーがスキャンされるようになりました。

Azure 以外のマシンで Azure Arc を有効にした場合、Security Center では、統合された脆弱性スキャナーをそれらに手動で大規模にデプロイできるようにします。

この更新では、**Azure Defender for servers** の能力を最大限に活用して、すべての Azure および Azure 以外の資産にわたって脆弱性管理プログラムを統合できます。

主な機能は、次のとおりです。

- Azure Arc マシンでの VA (脆弱性評価) スキャナーのプロビジョニング状態を監視する
- 保護されていない Windows および Linux Azure Arc マシンに、統合された VA エージェントをプロビジョニングする (手動で大規模に)
- デプロイされたエージェントで検出された脆弱性を受け取って分析する (手動で大規模に)
- Azure VM と Azure Arc マシンでエクスペリエンスを統一する

[ハイブリッド マシンへの統合された脆弱性スキャナーのデプロイについての詳細を参照してください](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)。

[Azure Arc 対応のサーバーについての詳細を参照してください](../azure-arc/servers/index.yml)。


### <a name="azure-firewall-recommendation-added-preview"></a>Azure Firewall の推奨事項の追加 (プレビュー)

Azure Firewall を使用してすべての仮想ネットワークを保護するための新しい推奨事項が追加されました。

**仮想ネットワークは Azure Firewall によって保護する必要がある** という推奨事項では、仮想ネットワークへのアクセスを制限し、Azure Firewall を使用して潜在的な脅威を防止することを勧めています。

[Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) の詳細を参照します。


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>"Kubernetes Services では承認された IP 範囲を定義する必要があります" という推奨事項のクイック修正による更新

**Kubernetes Services では承認された IP 範囲を定義する必要があります** という推奨事項に、クイック修正オプションが追加されました。

この推奨事項の詳細と、Security Center の他のすべての推奨事項については、「[セキュリティの推奨事項 - リファレンス ガイド](recommendations-reference.md)」を参照してください。

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="クイック修正オプションがある &quot;Kubernetes Services では承認された IP 範囲を定義する必要があります&quot; という推奨事項":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>規制コンプライアンス ダッシュボードへの、標準を削除するオプションの追加

Security Center の規制コンプライアンス ダッシュボードでは、特定のコンプライアンスのコントロールと要件をどのように満たしているかに基づいて、コンプライアンス体制に関する分析情報が提供されます。

ダッシュボードには、規制標準の既定のセットが含まれています。 提供されている標準に、組織に関連していないものが含まれている場合、簡単なプロセスでサブスクリプションの UI から削除できるようになりました。 標準は、"*サブスクリプション*" レベルだけで削除できます。管理グループのスコープでは、削除できません。

詳細については、「[ダッシュボードから標準を削除する](update-regulatory-compliance-packages.md#removing-a-standard-from-your-dashboard)」セクションを参照してください。


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Azure Resource Graph (ARG) からの Microsoft.Security/securityStatuses テーブルの削除

Azure Resource Graph は、効率的なリソース探索を提供するように設計されている、Azure のサービスです。環境を効果的に管理できるように、特定のサブスクリプションのセットにわたって大規模なクエリを実行する機能を備えています。 

Azure Security Center では、ARG および [Kusto クエリ言語 (KQL)](/azure/data-explorer/kusto/query/) を使用して、幅広いセキュリティ態勢データに対してクエリを実行できます。 次に例を示します。

- 資産インベントリが使用します (ARG)
- [多要素認証 (MFA) が有効になっていないアカウントを識別する](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)方法を示すために、サンプル ARG クエリのドキュメントを作成しました

ARG 内には、クエリで使用できるデータのテーブルがあります。

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Azure Resource Graph エクスプローラーと利用可能なテーブル":::

> [!TIP]
> ARG ドキュメントの「[Azure Resource Graph のテーブルとリソースの種類のリファレンス](../governance/resource-graph/reference/supported-tables-resources.md)」には、使用可能なすべてのテーブルの一覧があります。

この更新から、**Microsoft.Security/securityStatuses** テーブルが削除されました。 securityStatuses API は引き続き使用できます。

データ置換は、Microsoft.Security/Assessments テーブルで使用できます。

Microsoft.Security/securityStatuses と Microsoft.Security/Assessments の大きな違いは、前者が評価の集計を示すのに対して、後者はそれぞれの単一のレコードを保持することです。

たとえば、Microsoft.Security/securityStatuses は、次のように 2 つの policyAssessments の配列で結果を返します。

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
一方、Microsoft.Security/Assessments では、次のような各ポリシー評価のレコードが保持されます。

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**securityStatuses を使用する既存の ARG クエリを、評価テーブルを使用するように変換する例:**

SecurityStatuses を参照するクエリ:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Assessments テーブル用の置換クエリ:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

詳細については、以下のリンクを参照してください。
- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)
- [Kusto クエリ言語 (KQL)](/azure/data-explorer/kusto/query/)


## <a name="september-2020"></a>2020 年 9 月

9 月の更新プログラムには次のものが含まれます。
- [Security Center の外観の変更](#security-center-gets-a-new-look)
- [Azure Defender のリリース](#azure-defender-released)
- [Azure Defender for Key Vault の一般提供開始](#azure-defender-for-key-vault-is-generally-available)
- [Files と ADLS Gen2 を対象とした Azure Defender for Storage 保護の一般提供開始](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [資産インベントリ ツールの一般提供開始](#asset-inventory-tools-are-now-generally-available)
- [コンテナー レジストリと仮想マシンのスキャンに対する特定の脆弱性の検出結果の無効化](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [推奨事項からリソースを除外する](#exempt-a-resource-from-a-recommendation)
- [Security Center の AWS および GCP コネクタによるマルチクラウド エクスペリエンスの実現](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Kubernetes ワークロード保護の推奨事項バンドル](#kubernetes-workload-protection-recommendation-bundle)
- [連続エクスポートで脆弱性評価の結果が利用可能](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [新しいリソースを作成するときに推奨事項を適用してセキュリティ構成ミスを防止](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [ネットワーク セキュリティ グループ推奨事項の改善](#network-security-group-recommendations-improved)
- [プレビューの AKS 推奨事項 "Kubernetes Services でポッドのセキュリティ ポリシーを定義する必要がある" の非推奨化](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [Azure Security Center からの電子メールの改善](#email-notifications-from-azure-security-center-improved)
- [セキュリティ スコアからのプレビューの推奨事項の除外](#secure-score-doesnt-include-preview-recommendations)
- [推奨事項への重大度インジケーターと更新間隔の追加](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>Security Center の外観の変更

Security Center のポータル ページの UI が更新されました。 新しいページには、新しい概要ページと、セキュリティ スコア、資産インベントリ、および Azure Defender のダッシュボードが含まれています。

新しいデザインの概要ページには、セキュリティ スコア、資産インベントリ、Azure Defender の各ダッシュボードにアクセスするためのタイルが追加されました。 規制コンプライアンス ダッシュボードにリンクしているタイルもあります。

詳細については、[概要ページ](overview-page.md)に関するページをご覧ください。


### <a name="azure-defender-released"></a>Azure Defender のリリース

**Azure Defender** は、Azure とハイブリッド ワークロードを高度かつインテリジェントに保護するために Security Center 内に統合された、クラウド ワークロード保護プラットフォーム (CWPP) です。 これは Azure Security Center の Standard 価格レベル オプションに代わるものです。 

Azure Security Center の **価格と設定** の領域から Azure Defender を有効にすると、次の Defender プランが同時にすべて有効になり、環境のコンピューティング、データ、およびサービス レイヤーに対する包括的な防御が提供されます。

- [Azure Defender for servers](defender-for-servers-introduction.md)
- [Azure Defender for App Service](defender-for-app-service-introduction.md)
- [Azure Defender for Storage](defender-for-storage-introduction.md)
- [Azure Defender for SQL](defender-for-sql-introduction.md)
- [Azure Defender for Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender for Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender for container registries](defender-for-container-registries-introduction.md)

これらのプランについてはそれぞれ、Security Center のドキュメントで個別に説明されています。

Azure Defender では、専用ダッシュボードによって、仮想マシン、SQL データベース、コンテナー、Web アプリケーション、ネットワークなどに対して、セキュリティ アラートと高度な脅威防止が提供されます。

[Azure Defender に関する詳細情報](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Azure Defender for Key Vault の一般提供開始

Azure Key Vault は、暗号化キーとシークレット (証明書、接続文字列、パスワードなど) を保護するクラウド サービスです。 

**Azure Defender for Key Vault** には、Azure Key Vault 用に Azure ネイティブの高度な脅威防止機能が用意されており、セキュリティ インテリジェンスのレイヤーが追加されます。 結果的に、Azure Defender for Key Vault は、拡張機能によって Key Vault アカウントに依存する多くのリソースを保護します。

このオプション プランの一般提供が開始されました。 この機能は、プレビューでは "Azure Key Vault 向けの高度な脅威防止機能" として利用できました。

また、Azure portal の Key Vault ページには、**Security Center** の推奨事項とアラートの専用 **セキュリティ** ページが追加されています。

詳細については、「[Azure Defender for Key Vault](defender-for-key-vault-introduction.md)」を参照してください。


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Files と ADLS Gen2 を対象とした Azure Defender for Storage 保護の一般提供開始 

**Azure Defender for Storage** では、Azure Storage アカウント上の潜在的に有害なアクティビティが検出されます。 BLOB コンテナー、ファイル共有、またはデータ レイクのいずれに格納されているデータでも保護できます。

[Azure Files](../storage/files/storage-files-introduction.md) および [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) サポートの一般提供が開始されました。

2020 年 10 月 1 日から、これらのサービスのリソースの保護に対して課金が開始されます。

詳細については、「[Azure Defender for Storage](defender-for-storage-introduction.md)」を参照してください。


### <a name="asset-inventory-tools-are-now-generally-available"></a>資産インベントリ ツールの一般提供開始

Azure Security Center の資産インベントリ ページを使用すると、Security Center に接続したリソースのセキュリティ態勢が 1 つのページに表示されます。

Security Center では、Azure リソースのセキュリティの状態が定期的に分析されて、潜在的なセキュリティ脆弱性が特定されます。 その後、これらの脆弱性を修正する方法に関する推奨事項が提供されます。

いずれかのリソースに未処理の推奨事項がある場合は、インベントリに表示されます。

詳細については、「[資産インベントリを使用してリソースの調査と管理を行う](asset-inventory.md)」を参照してください。



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>コンテナー レジストリと仮想マシンのスキャンに対する特定の脆弱性の検出結果の無効化

Azure Defender には、Azure Container Registry とお使いの仮想マシンのイメージをスキャンするための脆弱性スキャナーが含まれています。

組織のニーズとして、検出結果を修復するのではなく無視する必要がある場合は、必要に応じて検出結果を無効にできます。 無効化された検出結果は、セキュリティ スコアに影響を与えたり、不要なノイズを生成したりすることはありません。

無効化のルールで定義した条件と一致する検出結果は、検出結果の一覧には表示されません。

このオプションは、次の推奨事項の詳細ページから使用できます。

- **Azure Container Registry イメージの脆弱性を修復する必要があります**
- **仮想マシンの脆弱性を修復する必要がある**

詳細については、[コンテナー イメージの特定の検出結果の無効化](defender-for-container-registries-usage.md#disable-specific-findings-preview)と[仮想マシンの特定の検出結果の無効化](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview)に関するページを参照してください。


### <a name="exempt-a-resource-from-a-recommendation"></a>推奨事項からリソースを除外する

場合によっては、特定の推奨事項に関するリソースが、異常ではないはずなのに、異常として表示されることがあります (これによりセキュリティ スコアが低くなります)。 それは、Security Center によって追跡されていないプロセスによって修復された可能性があります。 あるいは、ご自身の組織が、その特定のリソースのリスクを受け入れることにしたのかもしれません。 

このような場合は、除外規則を作成して、今後そのリソースが異常なリソースの一覧に表示されないようにすることができます。 これらの規則には、以下で説明するように、文書化された妥当性を含めることができます。

詳細については、「[推奨事項とセキュリティ スコアからリソースを除外する](exempt-resource.md)」を参照してください。


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>Security Center の AWS および GCP コネクタによるマルチクラウド エクスペリエンスの実現

通常、クラウド ワークロードは複数のクラウド プラットフォームにまたがるため、クラウド セキュリティサービスもそうである必要があります。

Azure Security Center は、Azure、アマゾン ウェブ サービス (AWS)、および Google Cloud Platform (GCP) のワークロードを保護するようになりました。

AWS および GCP アカウントを Security Center にオンボードすると、AWS Security Hub、GCP Security Command Center、および Azure Security Center が統合されます。 

詳細については、[Azure Security Center への AWS アカウントの接続](quickstart-onboard-aws.md)および [Azure Security Center への GCP アカウントの接続](quickstart-onboard-gcp.md)に関するページを参照してください。


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Kubernetes ワークロード保護の推奨事項バンドル

Kubernetes ワークロードが既定で確実に保護されるように、Security Center によって、Kubernetes 受付制御を含む適用オプションなど、Kubernetes レベルの強化の推奨事項が追加されます。

AKS クラスターに Kubernetes 用 Azure Policy アドオンをインストールすると、Kubernetes API サーバーに対するすべての要求が、クラスターに保存される前に、事前定義された一連のベスト プラクティスを基準にして監視されます。 その後、ベスト プラクティスを適用し、それを将来のワークロードに対して要求するように構成できます。

たとえば、特権コンテナーが作成されないように要求することができます。また、今後の特権コンテナー作成要求はすべてブロックされます。

詳細については、[Kubernetes 受付制御を使用したワークロード保護のベスト プラクティス](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)に関するページを参照してください。


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>連続エクスポートで脆弱性評価の結果が利用可能

連続エクスポートを使用して、アラートと推奨事項をリアルタイムで Azure Event Hubs、Log Analytics ワークスペース、または Azure Monitor にストリーミングします。 そこから、このデータを SIEM (Azure Sentinel、Power BI、Azure Data Explorer など) と統合できます。

Security Center の統合された脆弱性評価ツールは、"仮想マシンの脆弱性を修復する必要がある" などの "親" の推奨事項内で、ご自身のリソースに関する結果を実行可能な推奨事項として返します。 

推奨事項を選択し **[セキュリティに関する調査結果を含める]** オプションを有効にすることで、連続エクスポートを介して、セキュリティに関する調査結果をエクスポートに利用できるようにるようになりました。

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="連続エクスポート構成でのセキュリティに関する調査結果トグルを含める" :::

関連するページ:

- [Azure 仮想マシンに対する Security Center の統合脆弱性評価](deploy-vulnerability-assessment-vm.md)
- [Azure Container Registry に対する Security Center の統合された脆弱性評価ソリューション](defender-for-container-registries-usage.md)
- [連続エクスポート](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>新しいリソースを作成するときに推奨事項を適用してセキュリティ構成ミスを防止

セキュリティ構成ミスは、セキュリティ インシデントの大きな原因です。 Security Center には、特定の推奨事項に関する新しいリソースの構成ミスを "*防止*" する際に役立つ機能が追加されました。 

この機能は、ワークロードの安全性を確保し、ご自身のセキュリティ スコアを安定させるうえで役立ちます。

特定の推奨事項に基づいたセキュリティで保護された構成は、次の 2 つのモードで提供されます。

- Azure Policy の **Deny** 効果を使用して、異常なリソースが作成されるのを防ぐことができます

- **適用** オプションを使用すると、Azure Policy の **DeployIfNotExist** 効果を利用して、作成時に非対応リソースを自動的に修復できます
 
これは、選択したセキュリティの推奨事項で利用でき、リソースの詳細ページの上部にあります。

詳細については、「[適用/拒否の推奨事項を使用した構成ミスの防止](prevent-misconfigurations.md)」を参照してください。

###  <a name="network-security-group-recommendations-improved"></a>ネットワーク セキュリティ グループ推奨事項の改善

ネットワーク セキュリティ グループに関連する次のセキュリティ推奨事項が、一部の擬陽性のインスタンスを減らすために改善されました。

- すべてのネットワーク ポートは、VM に関連付けられた NSG で制限する必要があります
- 仮想マシンの管理ポートを閉じておく必要がある
- インターネットに接続する仮想マシンは、ネットワーク セキュリティ グループを使用して保護する必要がある
- サブネットはネットワーク セキュリティ グループに関連付けられている必要がある


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>プレビューの AKS 推奨事項 "Kubernetes Services でポッドのセキュリティ ポリシーを定義する必要がある" の非推奨化

プレビューの推奨事項 "Kubernetes Services でポッドのセキュリティ ポリシーを定義する必要がある" は、[Azure Kubernetes Service](../aks/use-pod-security-policies.md) のドキュメントで説明されているように非推奨とされます。

ポッド セキュリティ ポリシー (プレビュー) 機能は非推奨になる予定です。2020 年 10 月 15 日を過ぎると使用できなくなるため、AKS 用の Azure Policy が推奨されます。

ポッド セキュリティ ポリシー (プレビュー) が非推奨となった後、今後のクラスター アップグレードを実行し、Azure サポート内に留まるには、非推奨の機能を使用する既存のクラスターでその機能を無効にする必要があります。


### <a name="email-notifications-from-azure-security-center-improved"></a>Azure Security Center からの電子メールの改善

セキュリティ アラートに関する次の電子メール領域が改善されました。 

- すべての重大度レベルのアラートに関する電子メール通知の送信機能を追加
- サブスクリプションのさまざまな Azure ロールのユーザーに通知する機能を追加
- (本当の侵害である可能性が高い) 重大度が高いアラートについては、サブスクリプションの所有者に事前に送信されます
- 電子メール通知の構成ページから電話番号フィールドが削除されました

詳細については、「[セキュリティ アラートのメール通知を設定する](security-center-provide-security-contact-details.md)」を参照してください。


### <a name="secure-score-doesnt-include-preview-recommendations"></a>セキュリティ スコアからのプレビューの推奨事項の除外 

Security Center は、セキュリティの問題について、リソース、サブスクリプション、および組織を継続的に評価します。 その後、すべての結果を 1 つのスコアに集約して、現在のセキュリティの状況を一目で確認できるようにします。スコアが高くなるほど、識別されたリスク レベルが低下します。

新しい脅威が発見されると、新しいセキュリティに関するアドバイスが、新しい推奨事項を介して Security Center で利用できるようになります。 突然セキュリティ スコアが変わることがないように、また、スコアが影響を受ける前に、新しい推奨事項を調べる猶予期間を確保できるように、**プレビュー** のフラグが設定されている推奨事項が、セキュリティ スコアの計算から除外されるようになりました。 それでも、その推奨事項はプレビュー期間が終了した時点でスコアに反映されるため、可能な限り修復しておく必要があります。

また、**プレビュー** の推奨事項によって、リソースの "異常" がレンダリングされることはありません。

プレビューの推奨事項の例を次に示します。

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="プレビュー フラグが設定された推奨事項":::

[セキュリティ スコアの詳細](secure-score-security-controls.md)。


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>推奨事項への重大度インジケーターと更新間隔の追加

推奨事項の詳細ページに、更新間隔インジケーター (関連する場合) が追加され、推奨事項の重大度が明確に表示されるようになりました。

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="更新間隔と重大度が表示されている推奨事項ページ":::



## <a name="august-2020"></a>2020 年 8 月

8 月の更新プログラムには次のものが含まれます。

- [資産インベントリ - 資産のセキュリティ態勢の強力な新しいビュー](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Azure Active Directory のセキュリティの既定値群 (多要素認証用) のサポートの追加](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [サービス プリンシパルに関する推奨事項の追加](#service-principals-recommendation-added)
- [VM の脆弱性評価 - 推奨事項とポリシーの統合](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [ASC_default イニシアティブに追加された新しい AKS セキュリティ ポリシー – プライベート プレビューのお客様のみ使用](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>資産インベントリ - 資産のセキュリティ態勢の強力な新しいビュー

Security Center の資産インベントリ (現在プレビュー) を使用すると、Security Center に接続したリソースのセキュリティ態勢を確認することができます。

Security Center では、Azure リソースのセキュリティの状態が定期的に分析されて、潜在的なセキュリティ脆弱性が特定されます。 その後、これらの脆弱性を修正する方法に関する推奨事項が提供されます。 いずれかのリソースに未処理の推奨事項がある場合は、インベントリに表示されます。

ビューとそのフィルターを使用して、セキュリティの態勢データを調査し、結果に基づいてさらにアクションを実行できます。

[資産インベントリ](asset-inventory.md)の詳細を確認してください。


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Azure Active Directory のセキュリティの既定値群 (多要素認証用) のサポートの追加

Security Center に、Microsoft の無料の ID セキュリティ保護である[セキュリティの既定値群](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)の完全なサポートが追加されました。

セキュリティの既定値群により、構成済みの ID セキュリティ設定が提供され、組織は一般的な ID 関連の攻撃から保護されます。 セキュリティの既定値群により、500 万個以上のテナント全体が既に保護されています。50,000 個のテナントも Security Center によって保護されています。

Security Center では現在、セキュリティの既定値群が有効になっていない Azure サブスクリプションを識別した時点で、セキュリティの推奨事項を提供します。 Security Center ではこれまで、Azure Active Directory (AD) Premium ライセンスの一部である条件付きアクセスを使用した多要素認証の有効化を推奨していました。 Azure AD Free をご利用のお客様には、セキュリティの既定値群を有効にすることを現在お勧めしています。 

私たちの目標は、より多くのお客様に対して、MFA を使用してクラウド環境をセキュリティで保護し、[セキュリティ スコア](secure-score-security-controls.md)に対しても最もインパクトの高いリスクの 1 つを軽減することを推奨することです。

[セキュリティの既定値群](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)の詳細を確認してください。


### <a name="service-principals-recommendation-added"></a>サービス プリンシパルに関する推奨事項の追加

管理証明書を使用してサブスクリプションを管理している Security Center のお客様に対してサービス プリンシパルへの切り替えを推奨するための、新しい推奨事項が追加されました。

推奨事項 **[管理証明書の代わりにサブスクリプションを保護するために、サービス プリンシパルを使用する必要があります]** は、サービスプリンシパルまたは Azure Resource Manager を使用してサブスクリプションをより安全に管理することをお勧めするものです。 

「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)」で詳細を確認してください。


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>VM の脆弱性評価 - 推奨事項とポリシーの統合

Security Center では、VM を調べて脆弱性評価ソリューションが実行されているかどうかを検出します。 脆弱性評価ソリューションが検出されない場合、Security Center によって、展開を簡略化するための推奨事項が示されます。

脆弱性が発見された場合、調査結果をまとめた推奨事項が Security Center によって示され、必要に応じて調査および修復できるようにします。

使用されているスキャナーの種類に関係なく、すべてのユーザーに一貫したエクスペリエンスを提供できるように、4 つの推奨事項を次の 2 つに統合しました。

|統合された推奨事項|変更の説明|
|----|:----|
|**脆弱性評価ソリューションを仮想マシンで有効にする必要がある**|次の 2 つの推奨事項の代わりとなります。<br> **•** 組み込みの脆弱性評価ソリューションを仮想マシンで有効にする (Qualys を利用) (現在は非推奨) (Standard レベルに組み込み)<br> **•** 脆弱性評価ソリューションを仮想マシンにインストールする必要がある (現在は非推奨) (Standard と Free レベル)|
|**仮想マシンの脆弱性を修復する必要がある**|次の 2 つの推奨事項の代わりとなります。<br>**•** 仮想マシンで検出された脆弱性を修復する (Qualys を利用) (現在は非推奨)<br>**•** 脆弱性評価ソリューションによって脆弱性を修復する必要がある (現在は非推奨)|
|||

これで、同じ推奨事項を利用して、Security Center の脆弱性評価拡張機能、または Qualys や Rapid7 などの、パートナーからプライベートにライセンス供与されたソリューション ("BYOL") をデプロイすることになります。

また、脆弱性が検出され、Security Center に報告された場合、その脆弱性を特定した脆弱性評価ソリューションに関係なく、1 つの推奨事項によって調査結果がアラートとして通知されます。

#### <a name="updating-dependencies"></a>依存関係の更新

以前の推奨事項またはポリシーのキー/名前を参照するスクリプト、クエリ、または自動化がある場合は、次の表を利用してその参照を更新します。

##### <a name="before-august-2020"></a>2020 年 8 月より前

|推奨|Scope|
|----|:----|
|**組み込みの脆弱性評価ソリューションを仮想マシンで有効にする (Qualys を利用)**<br>キー:550e890b-e652-4d22-8274-60b3bdb24c63|組み込み|
|**仮想マシンで検出された脆弱性を修復する (Qualys を利用)**<br>キー:1195afff-c881-495e-9bc5-1486211ae03f|組み込み|
|**脆弱性評価ソリューションを仮想マシンにインストールする必要があります**<br>キー:01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**脆弱性評価ソリューションによって脆弱性を修復する必要がある**<br>キー:71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|ポリシー|Scope|
|----|:----|
|**仮想マシンで脆弱性評価を有効にする必要がある**<br>ポリシー ID:501541f7-f7e7-4cd6-868c-4190fdad3ac9|組み込み|
|**脆弱性評価ソリューションによって脆弱性を修復する必要がある**<br>ポリシー ID:760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>2020 年 8 月以降

|推奨|Scope|
|----|:----|
|**脆弱性評価ソリューションを仮想マシンで有効にする必要がある**<br>キー: ffff0522-1e88-47fc-8382-2a80ba848f5d|組み込み + BYOL|
|**仮想マシンの脆弱性を修復する必要がある**<br>キー:1195afff-c881-495e-9bc5-1486211ae03f|組み込み + BYOL|
||||

|ポリシー|Scope|
|----|:----|
|[**仮想マシンで脆弱性評価を有効にする必要がある**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>ポリシー ID:501541f7-f7e7-4cd6-868c-4190fdad3ac9 |組み込み + BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>ASC_default イニシアティブに追加された新しい AKS セキュリティ ポリシー – プライベート プレビューのお客様のみ使用

Kubernetes ワークロードが既定で確実に保護されるように、Security Center によって、Kubernetes 受付制御を含む適用オプションなど、Kubernetes レベル ポリシーが追加され、レコメンデーションが強化されます。

このプロジェクトの早期段階には、プライベート プレビューが含まれ、(既定では無効になっている) 新しいポリシーが ASC_default イニシアティブに追加されます。

このようなポリシーは無視しても問題ありません。お使いの環境に影響が出ることはありません。 有効にする場合、 https://aka.ms/SecurityPrP でプレビューに新規登録し、次のオプションから選択します。

1. **シングル プレビュー** – このプライベート プレビューのみに参加します。 参加するプレビューとして "ASC Continuous Scan" を明示的に伝えます。
1. **継続的プログラム** – これと将来のプライベート プレビューに追加します。 プロファイルとプライバシー契約を完了する必要があります。