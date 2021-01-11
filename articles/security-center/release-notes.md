---
title: Azure Security Center のリリース ノート
description: Azure Security Center の新機能と変更点の説明。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2020
ms.author: memildin
ms.openlocfilehash: c7df035aec199953bdf9a6bd56262af70a5a77e7
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723964"
---
# <a name="whats-new-in-azure-security-center"></a>Azure Security Center の最新情報

Azure のセキュリティは精力的な開発の下、継続的に改善されています。 常に最新の開発情報を把握していただけるよう、このページでは以下に関する情報を提供します。

- 新機能
- バグの修正
- 非推奨の機能

このページは頻繁に更新されるため、定期的にアクセスしてご確認ください。 6 か月以上前の項目を探す場合は、「[Azure Security Center の最新情報のアーカイブ](release-notes-archive.md)」をご覧ください。


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

Security Center に、Microsoft の無料の ID セキュリティ保護である[セキュリティの既定値群](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)の完全なサポートが追加されました。

セキュリティの既定値群により、構成済みの ID セキュリティ設定が提供され、組織は一般的な ID 関連の攻撃から保護されます。 セキュリティの既定値群により、500 万個以上のテナント全体が既に保護されています。50,000 個のテナントも Security Center によって保護されています。

Security Center では現在、セキュリティの既定値群が有効になっていない Azure サブスクリプションを識別した時点で、セキュリティの推奨事項を提供します。 Security Center ではこれまで、Azure Active Directory (AD) Premium ライセンスの一部である条件付きアクセスを使用した多要素認証の有効化を推奨していました。 Azure AD Free をご利用のお客様には、セキュリティの既定値群を有効にすることを現在お勧めしています。 

私たちの目標は、より多くのお客様に対して、MFA を使用してクラウド環境をセキュリティで保護し、[セキュリティ スコア](https://docs.microsoft.com/azure/security-center/secure-score-security-controls)に対しても最もインパクトの高いリスクの 1 つを軽減することを推奨することです。

[セキュリティの既定値群](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)の詳細を確認してください。


### <a name="service-principals-recommendation-added"></a>サービス プリンシパルに関する推奨事項の追加

管理証明書を使用してサブスクリプションを管理している Security Center のお客様に対してサービス プリンシパルへの切り替えを推奨するための、新しい推奨事項が追加されました。

推奨事項 **[管理証明書の代わりにサブスクリプションを保護するために、サービス プリンシパルを使用する必要があります]** は、サービスプリンシパルまたは Azure Resource Manager を使用してサブスクリプションをより安全に管理することをお勧めするものです。 

「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)」で詳細を確認してください。


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


## <a name="july-2020"></a>2020 年 7 月

7 月の更新プログラムには次のものが含まれます。
- [仮想マシンの脆弱性評価をマーケットプレース以外のイメージで使用可能に](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Azure Files と Azure Data Lake Storage Gen2 を含むように拡張された Azure Storage の脅威の防止 (プレビュー)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [脅威の防止機能を有効にするための 8 つの新しい推奨事項](#eight-new-recommendations-to-enable-threat-protection-features)
- [コンテナーのセキュリティ強化 - レジストリのスキャンの高速化とドキュメントの更新](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [新しい推奨事項とパス規則でのワイルドカードのサポートによって適応型アプリケーション制御を更新](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [非推奨となった SQL の高度なデータ セキュリティの 6 つのポリシー](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>仮想マシンの脆弱性評価をマーケットプレース以外のイメージで使用可能に

脆弱性評価ソリューションを展開する際、以前は展開前に Security Center によって検証チェックが実行されていました。 このチェックは、接続先の仮想マシンのマーケットプレース SKU を確認するために行われていました。 

今回の更新プログラムから、このチェックは削除され、脆弱性評価ツールを 'カスタム' Windows および Linux マシンに展開できるようになりました。 カスタム イメージは、マーケットプレースの既定値からユーザーが変更したものです。

統合された脆弱性評価拡張機能 (Qualys を使用) をより多くのマシンに展開できるようになりましたが、サポートは、「[Qualys の組み込み脆弱性スキャナーのデプロイ](built-in-vulnerability-assessment.md#deploying-the-qualys-built-in-vulnerability-scanner)」に記載されている OS を使用している場合にのみ受けることができます。

[仮想マシン向けの統合された脆弱性スキャナー (Standard レベルのみ)](built-in-vulnerability-assessment.md) の詳細について確認してください。

Qualys または Rapid7 からプライベートにライセンス提供された独自の脆弱性評価ソリューションを使用する方法の詳細については、「[パートナーの脆弱性スキャン ソリューションをデプロイする](partner-vulnerability-assessment.md)」を参照してください。


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Azure Files と Azure Data Lake Storage Gen2 を含むように拡張された Azure Storage の脅威の防止 (プレビュー)

Azure Storage の脅威の防止では、Azure Storage アカウント上の潜在的に有害なアクティビティを検出します。 Security Center では、ストレージ アカウントへのアクセスまたはストレージ アカウントの悪用の試行が検出されたときにアラートが表示されます。 

BLOB コンテナー、ファイル共有、またはデータ レイクのいずれに格納されているデータでも保護できます。 

詳細については、「[Azure Storage の脅威の防止](threat-protection.md#threat-protection-for-azure-storage-)」を参照してください。




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>脅威の防止機能を有効にするための 8 つの新しい推奨事項

次のリソース タイプに対して Azure Security Center の脅威の防止機能を簡単に提供するため、新たに 8 つの推奨事項が追加されました: 仮想マシン、App Service プラン、Azure SQL Database サーバー、マシン上の SQL Server、Azure Storage アカウント、Azure Kubernetes Service クラスター、Azure Container Registry レジストリ、および Azure Key Vault ボールト。

新しい推奨事項は次のとおりです。

- **Azure SQL Database サーバーで Advanced Data Security を有効にする必要がある**
- **マシン上の SQL Server で Advanced Data Security を有効にする必要がある**
- **Azure App Service プランで Advanced Threat Protection を有効にする必要がある**
- **Azure Container Registry レジストリで Advanced Threat Protection を有効にする必要がある**
- **Azure Key Vault コンテナーで Advanced Threat Protection を有効にする必要がある**
- **Azure Kubernetes Service クラスターで Advanced Threat Protection を有効にする必要がある**
- **Azure Storage アカウントで Advanced Threat Protection を有効にする必要がある**
- **仮想マシンで Advanced Threat Protection を有効にする必要がある**

これらの新しい推奨事項は、セキュリティ コントロール **Advanced Threat Protection を有効にする**に属しています。

推奨事項には、クイック修正機能も含まれています。 

> [!IMPORTANT]
> これらの推奨事項を 1 つでも修復すると、関連するリソースを保護するための料金が発生します。 現在のサブスクリプションに関連するリソースがある場合、直ちに料金が発生します。 後で追加した場合は、後で行われます。
> 
> たとえば、サブスクリプションに Azure Kubernetes Service クラスターがない状態で脅威の防止を有効にした場合、料金は発生しません。 将来、同じサブスクリプションにクラスターを追加すると、そのクラスターは自動的に保護され、その時点で料金が発生します。

各項目の詳細については、[セキュリティに関する推奨事項のリファレンス ページ](recommendations-reference.md)を参照してください。

詳細については、「[Azure Security Center での脅威の防止](https://docs.microsoft.com/azure/security-center/threat-protection)」を参照してください。




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>コンテナーのセキュリティ強化 - レジストリのスキャンの高速化とドキュメントの更新

コンテナーのセキュリティ ドメインへの継続的な投資の一環として、Security Center では Azure Container Registry に保存されているコンテナー イメージの動的スキャンにおける大幅なパフォーマンス向上を実現しました。 スキャンは、通常約 2 分で完了するようになりました。 場合によっては、最大 15 分かかることがあります。

Azure Security Center のコンテナーのセキュリティ機能に関する明確さとガイダンスを改善するために、コンテナーのセキュリティに関するドキュメントのページも更新しました。 

Security Center でのコンテナー セキュリティの詳細については、次の記事を参照してください。

- [Security Center のコンテナーのセキュリティ機能の概要](https://docs.microsoft.com/azure/security-center/container-security)
- [Azure Container Registry との統合の詳細](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration)
- [Azure Kubernetes Service との統合の詳細](https://docs.microsoft.com/azure/security-center/azure-kubernetes-service-integration)
- [レジストリをスキャンして Docker ホストを強化する方法](https://docs.microsoft.com/azure/security-center/monitor-container-security)
- [Azure Kubernetes Service クラスターの脅威防止機能からのセキュリティ アラート](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)
- [Azure Kubernetes Service ホストの脅威防止機能からのセキュリティ アラート](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost)
- [コンテナーに関するセキュリティの推奨事項](https://docs.microsoft.com/azure/security-center/recommendations-reference#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>新しい推奨事項とパス規則でのワイルドカードのサポートによって適応型アプリケーション制御を更新

適応型アプリケーション制御の機能に、2 つの重要な更新プログラムが適用されています。

* 新しい推奨事項により、以前は許可されていなかった、正当である可能性のある動作が識別されます。 新しい推薦事項の「**適応型アプリケーション制御ポリシーの許可リスト ルールを更新する必要がある**」により、既存のポリシーに新しいルールを追加して、適応型アプリケーション制御違反アラートの擬陽性の数を減らすよう求めるメッセージが表示されます。

* パス規則でワイルドカードがサポートされるようになっています。 この更新プログラムから、ワイルドカードの使用が許可されたパス規則を構成できます。 サポートされているシナリオは 2 つあります。

    * パスの末尾でワイルドカードを使用し、そのフォルダーとサブフォルダー内のすべての実行可能ファイルを許可します

    * パスの途中でワイルドカードを使用し、変化するフォルダー名を持つ既知の実行可能ファイルの名前 (既知の実行可能ファイルを含む個人ユーザー フォルダー、自動生成されたフォルダー名など) を有効にします。


適応型アプリケーション制御の詳細については、[こちら](security-center-adaptive-application.md)をご覧ください。



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>非推奨となった SQL の高度なデータ セキュリティの 6 つのポリシー

SQL マシンの高度なデータ セキュリティに関連する 6 つのポリシーが非推奨になります。

- SQL Managed Instance の高度なデータ セキュリティ設定で、Advanced Threat Protection の種類を [すべて] に設定する必要がある
- SQL Server の高度なデータ セキュリティ設定で、Advanced Threat Protection の種類を [すべて] に設定する必要がある
- SQL Managed Instance の高度なデータ セキュリティ設定に、セキュリティ アラートを受信するためのメール アドレスが含まれている必要がある
- SQL Server の高度なデータ セキュリティ設定に、セキュリティ アラートを受信するためのメール アドレスが含まれている必要がある
- SQL Managed Instance の Advanced Data Security 設定で管理者とサブスクリプションの所有者に対するメール通知を有効にする必要がある
- SQL Server の Advanced Data Security 設定で、管理者とサブスクリプションの所有者に対するメール通知を有効にする必要がある

詳細については、[組み込みのポリシー](security-center-policy-definitions.md)に関するページを参照してください。





## <a name="june-2020"></a>2020 年 6 月

6 月の更新プログラムには次のものが含まれます。
- [セキュリティ スコア API (プレビュー)](#secure-score-api-preview)
- [SQL マシン向けの Advanced Data Security (Azure、他のクラウド、オンプレミス) (プレビュー)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview)
- [Log Analytics エージェントを Azure Arc マシンにデプロイするための 2 つの新しい推奨事項 (プレビュー)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [連続エクスポートとワークフローの自動化の構成を大規模に作成するための新しいポリシー](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [NSG を使用してインターネットに接続していない仮想マシンを保護するための新しい推奨事項](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [脅威の防止と Advanced Data Security を有効にするための新しいポリシー](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>セキュリティ スコア API (プレビュー)

[セキュリティ スコア API](https://docs.microsoft.com/rest/api/securitycenter/securescores/) (現在プレビュー段階) を使用して、スコアにアクセスできるようになりました。 この API メソッドにより、より柔軟にデータに対してクエリを実行したり、一定期間のセキュリティ スコアをレポートする独自のメカニズムを構築できるようになります。 たとえば、**Secure Scores** API を使用して、特定のサブスクリプションのスコアを取得できます。 また、**Secure Score Controls** API を使用して、サブスクリプションのセキュリティ コントロールと現在のスコアを一覧表示できます。

セキュリティ スコア API で実現可能な外部ツールの例については、「[GitHub コミュニティのセキュリティ スコア エリア](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)」を参照してください。

詳細については、[Azure Security Center のセキュリティ スコアとセキュリティ コントロール](secure-score-security-controls.md)に関するページを参照してください。



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview"></a>SQL マシン向けの Advanced Data Security (Azure、他のクラウド、オンプレミス) (プレビュー)

Azure Security Center の SQL マシン向けの Advanced Data Security により、Azure、他のクラウド環境、さらにはオンプレミスのマシン でホストされている SQL Server も保護されるようになりました。 これにより、Azure ネイティブの SQL Server の保護が拡張され、ハイブリッド環境を完全にサポートできるようになります。

Advanced Data Security では、SQL マシンに対して、場所によらず脆弱性評価と高度な脅威の防止が提供されます。

セットアップには、次の 2 つの手順があります。

1. Log Analytics エージェントを SQL Server のホスト マシンにデプロイして、Azure アカウントへの接続を提供します。

1. Security Center の [価格と設定] ページで、オプションのバンドルを有効にします。

詳細については、[SQL マシン向けの Advanced Data Security](security-center-iaas-advanced-data.md) に関するページを参照してください。



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Log Analytics エージェントを Azure Arc マシンにデプロイするための 2 つの新しい推奨事項 (プレビュー)

[Log Analytics エージェント](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)を Azure Arc マシンにデプロイし、Azure Security Center によって保護されていることを確認する、2 つの新しい推奨事項が追加されました。

- **Log Analytics エージェントを Windows ベースの Azure Arc マシンにインストールする必要がある (プレビュー)**
- **Log Analytics エージェントを Linux ベースの Azure Arc マシンにインストールする必要がある (プレビュー)**

これらの新しい推奨事項は、既存の (関連する) 推奨事項である、「**お使いのマシンに監視エージェントをインストールする必要があります**」と同じ、セキュリティ構成の修復、適応型アプリケーション制御の適用、システムの更新プログラムの適用、Endpoint Protection を有効にするという 4 つのセキュリティ コントロールに表示されます。

推奨事項には、デプロイ プロセスを高速化するためのクイック修復機能も含まれています。 

これら 2 つの新しい推奨事項の詳細については、「[コンピューティングとアプリの推奨事項](recommendations-reference.md#recs-computeapp)」テーブルを参照してください。

Azure Security Center でエージェントを使用する方法の詳細については、「[Log Analytics エージェントとは](https://docs.microsoft.com/azure/security-center/faq-data-collection-agents#what-is-the-log-analytics-agent)」を参照してください。

詳細については、[Azure Arc マシンの拡張機能](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal)に関するページを参照してください。



### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>連続エクスポートとワークフローの自動化の構成を大規模に作成するための新しいポリシー

組織の監視とインシデント対応プロセスを自動化すると、セキュリティ インシデントの調査と軽減にかかる時間を大幅に短縮できます。

組織全体に自動化の構成をデプロイするには、次の組み込みの "DeployIfdNotExist" Azure ポリシーを使用して、[連続エクスポート](continuous-export.md)および[ワークフローの自動化](workflow-automation.md)手順を作成して構成します。

ポリシーは Azure ポリシーで確認できます。


|目標  |ポリシー  |ポリシー ID  |
|---------|---------|---------|
|イベント ハブへの連続エクスポート|[Azure Security Center アラートおよび推奨事項についてイベント ハブへのエクスポートをデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Log Analytics ワークスペースへの連続エクスポート|[Azure Security Center アラートおよび推奨事項について Log Analytics ワークスペースへのエクスポートをデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|セキュリティ アラートのワークフローの自動化|[Azure Security Center アラートに対してワークフローの自動化をデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|セキュリティに関する推奨事項のワークフローの自動化|[Azure Security Center 推奨事項に対してワークフローの自動化をデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

[ワークフローの自動化テンプレート](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)を使用して開始するには、こちらを参照してください。

2 つのエクスポート ポリシーを使用する方法の詳細については、「[ポリシーを使用して Azure Security Center のアラートと推奨事項を連続エクスポートする](https://techcommunity.microsoft.com/t5/azure-security-center/continuously-export-azure-security-center-alerts-and/ba-p/1440745)」を参照してください。


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>NSG を使用してインターネットに接続していない仮想マシンを保護するための新しい推奨事項

"セキュリティのベスト プラクティスの実装" セキュリティ コントロールに、次の新しい推奨事項が含まれるようになりました。

- **インターネットに接続されていない仮想マシンをネットワーク セキュリティ グループで保護する必要がある**

既存の推奨事項である「**インターネットに接続する仮想マシンは、ネットワーク セキュリティ グループを使用して保護する必要がある**」では、インターネットに接続された仮想マシンとインターネットに接続されていない仮想マシンが区別されていませんでした。 どちらの場合でも、VM がネットワーク セキュリティ グループに割り当てられていないと、重大度の高い推奨事項が生成されていました。 この新しい推奨事項では、インターネットに接続されていないマシンを分離することで擬陽性を減らし、不必要な重要度の高いアラートを回避します。

詳細については、「[ネットワークの推奨事項](recommendations-reference.md#recs-network)」テーブルを参照してください。




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>脅威の防止と Advanced Data Security を有効にするための新しいポリシー

次の新しいポリシーが、ASC の既定のイニシアティブに追加されました。これらは関連するリソースの種類に対して、脅威の防止または Advanced Data Security の有効化を促進するよう設計されています。

ポリシーは Azure ポリシーで確認できます。


| ポリシー                                                                                                                                                                                                                                                                | ポリシー ID                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [Azure SQL Database サーバーで Advanced Data Security を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [マシン上の SQL Server で Advanced Data Security を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Azure Storage アカウントで Advanced Threat Protection を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [Azure Key Vault コンテナーで Advanced Threat Protection を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [Azure App Service プランで Advanced Threat Protection を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [Azure Container Registry レジストリで Advanced Threat Protection を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [Azure Kubernetes Service クラスターで Advanced Threat Protection を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [Virtual Machines で Advanced Threat Protection を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

詳細については、「[Azure Security Center での脅威の防止](https://docs.microsoft.com/azure/security-center/threat-protection)」を参照してください。





## <a name="may-2020"></a>2020 年 5 月

5 月の更新プログラムには次のものが含まれます。
- [アラート抑制ルール (プレビュー)](#alert-suppression-rules-preview)
- [仮想マシンに対する脆弱性評価の一般提供の開始](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Just-In-Time (JIT) 仮想マシン (VM) へのアクセスの変更](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [別のセキュリティ コントロールへのカスタム推奨事項の移動](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [推奨事項をコントロールまたは単純なリストで表示するためのトグルの追加](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [拡張されたセキュリティ コントロール "セキュリティのベスト プラクティスの実装"](#expanded-security-control-implement-security-best-practices)
- [カスタム メタデータを使用したカスタム ポリシーの一般提供の開始](#custom-policies-with-custom-metadata-are-now-generally-available)
- [ファイルレス攻撃検出へのクラッシュ ダンプ分析機能の移行](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>アラート抑制ルール (プレビュー)

この新機能 (現在プレビュー段階) は、アラート疲れを減らすのに役立ちます。 ルールを使用して、無害であることが判明している、または組織内の通常の活動に関連しているアラートを自動的に非表示にします。 これにより、最も重要な脅威に集中できます。 

有効にした抑制ルールと一致するアラートは、引き続き生成されますが、その状態は "無視" に設定されます。 状態は Azure portal でも確認できますが、Security Center のセキュリティ アラートにアクセスして確認することもできます。

抑制ルールでは、アラートが自動的に無視される条件を定義します。 通常は、次のような場合に抑制ルールを使用します。

- ユーザーが擬陽性と判断したアラートを抑制する

- あまりにも頻繁にトリガーされるため有用ではないアラートを抑制する

詳しくは、「[Azure Security Center の脅威防止からのアラートの抑制](alerts-suppression-rules.md)」を参照してください。


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>仮想マシンに対する脆弱性評価の一般提供の開始

Security Center の Standard レベルに、追加料金なしで仮想マシンの統合脆弱性評価が追加されました。 この拡張機能には Qualys が利用されていますが、結果は Security Center に直接レポートされます。 Qualys ライセンスも Qualys アカウントも必要ありません。すべてが Security Center 内でシームレスに処理されます。

この新しいソリューションでは、お客様の仮想マシンを継続的にスキャンし、脆弱性を検出してその結果を Security Center に表示できます。 

ソリューションをデプロイするには、新しいセキュリティの推奨事項を使用します。

"組み込みの脆弱性評価ソリューションを仮想マシンで有効にする (Qualys を利用)"

詳しくは、[仮想マシンに対する Security Center の統合脆弱性評価](built-in-vulnerability-assessment.md)に関するページを参照してください。



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Just-In-Time (JIT) 仮想マシン (VM) へのアクセスの変更

Security Center には、VM の管理ポートを保護するオプションの機能が備わっています。 これにより、最も一般的な形式のブルート フォース攻撃を防ぐことができます。

この更新では、この機能に次の変更が加えられました。

- VM での JIT の有効化を勧める推奨事項の名前が変更されました。 以前の "仮想マシンに Just-In-Time ネットワーク アクセス制御を適用する必要があります" から次のように変わりました: "仮想マシンの管理ポートは、Just-In-Time ネットワーク アクセス制御によって保護されている必要があります"。

- この推奨事項は、オープンな管理ポートがある場合にのみトリガーされます。

詳しくは、[JIT アクセス機能](security-center-just-in-time.md)に関するページを参照してください。


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>別のセキュリティ コントロールへのカスタム推奨事項の移動

強化されたセキュア スコアと共に導入されたセキュリティ コントロールの 1 つが、"セキュリティのベスト プラクティスの実装" です。 お客様のサブスクリプションで作成されたカスタム推奨事項は自動的に、このコントロールに配置されていました。 

カスタム推奨事項は見つけやすいように、専用のセキュリティ コントロールである "カスタム推奨事項" に移行しました。 このコントロールは、お客様のセキュア スコアに影響しません。

セキュリティ コントロールの詳細については、「[Azure Security Center の強化されたセキュア スコア (プレビュー)](secure-score-security-controls.md)」を参照してください。


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>推奨事項をコントロールまたは単純なリストで表示するためのトグルの追加

セキュリティ コントロールは、関連するセキュリティ推奨事項の論理グループです。 これらはお客様の脆弱な攻撃対象領域を反映します。 コントロールは、セキュリティに関する一連の推奨事項と、これらの推奨事項を実装するための指示内容です。

組織が個々の攻撃対象領域をどの程度セキュリティで保護しているかをすぐに確認するには、各セキュリティ コントロールのスコアを確認します。

既定では、推奨事項がセキュリティ コントロールに表示されます。 この更新プログラムから、それらをリストとしても表示できるようになりました。 影響を受けるリソースの正常性状態で並べ替えられた単純なリストとして表示するには、新しいトグル "コントロールによるグループ化" を使用します。 このトグルはポータル内のリストの上にあります。

セキュリティ コントロール (およびこのトグル) は、新しいセキュア スコア エクスペリエンスの一部です。 ポータル内からフィードバックをお送りいただけますとさいわいです。

セキュリティ コントロールの詳細については、「[Azure Security Center の強化されたセキュア スコア (プレビュー)](secure-score-security-controls.md)」を参照してください。

![“コントロールによるグループ化“ の推奨設定のトグル](\media\secure-score-security-controls\recommendations-group-by-toggle.gif)

### <a name="expanded-security-control-implement-security-best-practices"></a>拡張されたセキュリティ コントロール "セキュリティのベスト プラクティスの実装" 

強化されたセキュア スコアと共に導入されたセキュリティ コントロールの 1 つが、"セキュリティのベスト プラクティスの実装" です。 このコントロールに推奨事項があっても、セキュア スコアに影響はありません。 

この更新により、3 つの推奨事項が当初配置されていたコントロールからこのベスト プラクティス コントロールに移動されました。 この操作が行われたのは、これら 3 つの推奨事項のリスクが当初考えていたよりも低いことが判明したためです。

さらに、2 つの新しい推奨事項が導入され、このコントロールに追加されました。

移動された 3 つの推奨事項は次のとおりです。

- **ご利用のサブスクリプションに対して読み取りアクセス許可があるアカウントでは、MFA を有効にする必要があります** (当初は "MFA の有効化" コントロール内)
- **読み取りアクセス許可がある外部アカウントは、ご使用のサブスクリプションから削除する必要があります** (当初は "アクセスとアクセス許可の管理" コントロール内)
- **お使いのサブスクリプションに、最大 3 人の所有者を指定する必要があります** (当初は "アクセスとアクセス許可の管理" コントロール内)

コントロールに追加された 2 つの新しい推奨事項は次のとおりです。

- **ゲスト構成拡張機能が Windows 仮想マシンにインストールされている必要がある (プレビュー)** - [Azure Policy ゲスト構成](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)を使用することで、サーバーとアプリケーションの設定に対して仮想マシンが可視化されます (Windows のみ)。

- **お使いのマシンで Windows Defender Exploit Guard を有効にする必要がある (プレビュー)** - Windows Defender Exploit Guard では、Azure Policy ゲスト構成エージェントが利用されます。 Exploit Guard には、さまざまな攻撃ベクトルに対してデバイスをロックダウンし、マルウェア攻撃でよく使用される動作をブロックするよう設計された 4 つのコンポーネントがありますが、企業がセキュリティ リスクと生産性の要件のバランスをとれるようになっています (Windows のみ)。

Windows Defender Exploit Guard の詳細については、「[Exploit Guard ポリシーの作成と展開](https://docs.microsoft.com/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy)」を参照してください。

セキュリティ コントロールの詳細については、[強化されたセキュリティ スコア (プレビュー)](secure-score-security-controls.md) に関するページを参照してください。



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>カスタム メタデータを使用したカスタム ポリシーの一般提供の開始

カスタム ポリシーは、Security Center の推奨事項エクスペリエンス、セキュア スコア、規制コンプライアンス標準ダッシュボードに含まれました。 この機能の一般提供が開始され、お客様は Security Center で組織のセキュリティ評価の範囲を拡大できるようになりました。 

Azure Policy でカスタム イニシアチブを作成し、それにポリシーを追加して Azure Security Center にオンボードし、推奨事項として視覚化します。

さらに、カスタム推奨事項のメタデータを編集するオプションも追加されました。 メタデータ オプションには、重要度、修復手順、脅威情報などが含まれます。  

詳しくは、「[詳細情報でのカスタム推奨事項の拡張](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information)」を参照してください。



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>ファイルレス攻撃検出へのクラッシュ ダンプ分析機能の移行 

Microsoft は Windows クラッシュ ダンプ (CDA) 検出機能を[ファイルレス攻撃検出](https://docs.microsoft.com/azure/security-center/threat-protection#windows-fileless)に移行しています。 ファイルレス攻撃検出分析では、Windows マシンで次のセキュリティ アラートの高度なバージョンを利用できます。コード インジェクションの検出、Windows モジュールの偽装の検出、シェルコードの検出、疑わしいコード セグメントの検出。

この移行のメリットをいくつか紹介します。

- **プロアクティブかつタイムリーなマルウェア検出** - CDA のアプローチでは、クラッシュが発生して初めて分析を実行し、悪意のあるアーティファクトを検出する必要がありました。 ファイルレス攻撃検出を使用すると、メモリ内の脅威が活動しているのをプロアクティブに特定できます。 

- **強化されたアラート** - ファイルレス攻撃検出のセキュリティ アラートには、CDA では利用できない強化が加わっています (例: アクティブなネットワーク接続情報)。 

- **アラートの集計** - CDA では、単一のクラッシュ ダンプ内で複数の攻撃パターンを検出した場合に、複数のセキュリティ アラートをトリガーしていました。 ファイルレス攻撃検出では、同じプロセスから特定された攻撃パターンをすべて単一のアラートにまとめて、複数のアラートを関連付ける必要をなくしています。

- **Log Analytics ワークスペースでの要件の減少** - 機密データを含んでいる可能性があるクラッシュ ダンプは、Log Analytics ワークスペースにアップロードされなくなります。



## <a name="april-2020"></a>2020 年 4 月

4 月の更新プログラムには次のものが含まれます。
- [動的コンプライアンス パッケージの一般提供の開始](#dynamic-compliance-packages-are-now-generally-available)
- [Azure Security Center Free レベルへの ID に関する推奨事項の追加](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>動的コンプライアンス パッケージの一般提供の開始

Azure Security Center の規制コンプライアンス ダッシュボードに、追加の業界および規制の標準を追跡するための**動的コンプライアンス パッケージ**が追加されました (一般提供開始)。

動的コンプライアンス パッケージは、Security Center のセキュリティ ポリシー ページからサブスクリプションまたは管理グループに追加できます。 標準またはベンチマークをオンボードすると、評価として、マップされた関連するすべてのコンプライアンス データと共に、標準が規制コンプライアンス ダッシュボードに表示されます。 オンボードされた標準の概要レポートがダウンロードできるようになります。

次のような標準を追加できるようになりました。

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK OFFICIAL および UK NHS**
- **カナダ連邦の PBMM**
- **Azure CIS 1.1.0 (新規)** (Azure CIS 1.1.0 のより完全な表現)

さらに、**Azure セキュリティ ベンチマーク**が最近追加されました。これは Microsoft が作成したもので、一般的なコンプライアンス フレームワークに基づくセキュリティとコンプライアンスのベスト プラクティスに関する Azure 固有のガイドラインです。 追加の標準が使用可能になると、ダッシュボードでサポートされます。  
 
詳しくは、「[規制コンプライアンス ダッシュボードでの動的コンプライアンス パッケージへの更新](update-regulatory-compliance-packages.md)」を参照してください。


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Azure Security Center Free レベルへの ID に関する推奨事項の追加

Azure Security Center Free レベルで、ID とアクセスのセキュリティに関する推奨事項の一般提供が開始されました。 これは、クラウドのセキュリティ体制管理 (CSPM) 機能を無料化する取り組みの一環です。 これまで、これらの推奨事項を利用できるのは、Standard 価格レベルだけでした。

ID とアクセスに関する推奨事項の例を次に示します。

- "ご利用のサブスクリプションに対して所有者アクセス許可があるアカウントでは、MFA を有効にする必要があります。"
- "お使いのサブスクリプションに、最大 3 人の所有者を指定する必要があります。"
- "非推奨のアカウントは、ご利用のサブスクリプションから削除する必要があります。"

Free 価格レベルのサブスクリプションをお持ちのお客様は、ID とアクセスのセキュリティについて評価されることがなかったため、この変更により、セキュア スコアに影響が生じます。

詳しくは、「[ID とアクセスの推奨事項](recommendations-reference.md#recs-identity)」を参照してください。

詳しくは、[ID とアクセスの監視](security-center-identity-access.md)に関するページを参照してください。


## <a name="march-2020"></a>2020 年 3 月

3 月の更新プログラムには次のものが含まれます。
- [ワークフロー自動化の一般提供の開始](#workflow-automation-is-now-generally-available)
- [Azure Security Center と Windows Admin Center の統合](#integration-of-azure-security-center-with-windows-admin-center)
- [Azure Kubernetes Service の保護](#protection-for-azure-kubernetes-service)
- [Just-In-Time エクスペリエンスの改善](#improved-just-in-time-experience)
- [Web アプリケーションに関する 2 つのセキュリティ推奨事項の非推奨化](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>ワークフロー自動化の一般提供の開始

Azure Security Center のワークフロー自動化機能の一般提供が開始されました。 これを使用すると、セキュリティ アラートと推奨事項が出されたときにロジック アプリを自動でトリガーできます。 さらに、クイック修復オプションが利用可能なすべての推奨事項とアラートでは、手動のトリガーが使用可能です。

すべてのセキュリティ プログラムには、インシデント対応のための複数のワークフローが含まれています。 これらのプロセスには、直接の利害関係者への通知、変更管理プロセスの開始、および特定の修復手順の適用が含まれます。 これらのプロシージャの手順をできるだけ多く自動化することがセキュリティの専門家によって推奨されています。 自動化を行うと、オーバーヘッドが減少します。また、迅速かつ一貫した方法で、定義済みの要件に従ってプロセスの手順が実行されるようにして、セキュリティを向上させることができます。

ワークフローを実行するための Security Center の自動および手動機能の詳細については、「[ワークフローの自動化](workflow-automation.md)」を参照してください。

詳しくは、[ロジック アプリの作成](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)に関するページを参照してください。


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Azure Security Center と Windows Admin Center の統合

Windows Admin Center から Azure Security Center にオンプレミスの Windows サーバーを直接移行できるようになりました。 これで、オンプレミスのサーバー、仮想マシン、追加の PaaS ワークロードなど、すべての Windows Admin Center リソースのセキュリティ情報を Security Center で一元的に確認できるようになります。

Windows Admin Center から Azure Security Center にサーバーを移行すると、以下が可能になります。

- Windows Admin Center の Security Center 拡張機能で、セキュリティに関するアラートと推奨事項を確認する。
- Azure portal 内の Security Center で (または API を使用して)、セキュリティ体制を確認し、Windows Admin Center で管理されているサーバーの追加の詳細情報を取得する。

詳しくは、[Azure Security Center と Windows Admin Center を統合する方法](windows-admin-center-integration.md)に関するページを参照してください。


### <a name="protection-for-azure-kubernetes-service"></a>Azure Kubernetes Service の保護

Azure Security Center では、Azure Kubernetes Service (AKS) を保護するコンテナー セキュリティ機能を拡張しています。

Kubernetes は人気のオープンソース プラットフォームとして広く普及しており、現在コンテナー オーケストレーションの業界標準となっています。 その実装が広まっているにもかかわらず、Kubernetes 環境をセキュリティで保護する方法については、まだ十分に理解されていません。 コンテナー化されたアプリケーションの攻撃対象領域を保護するには、潜在的な脅威を確実かつ継続的に監視するようインフラストラクチャを構成するための専門知識が必要です。

Security Center には以下の防御機能があります。

- **検出と可視化** - Security Center に登録されているサブスクリプション内のマネージド AKS インスタンスの継続的な検出。
- **セキュリティに関する推奨事項** - AKS のセキュリティ ベストプラクティスに準拠するのに役立つ、実行可能な推奨事項。 組織のセキュリティ体制の一環として確認できるように、これらの推奨事項はお客様のセキュア スコアに含まれています。 AKS に関する推奨事項の例としては、"Kubernetes Service クラスターへのアクセスを制限するには、ロールベースのアクセス制御を使用する必要があります" などがあります。
- **脅威の防止** - Security Center では、AKS デプロイの継続的な分析によって、ホストおよび AKS クラスター レベルで検出された脅威や悪意のあるアクティビティについてアラートを通知します。

詳しくは、「[Azure Kubernetes Service と Security Center の統合](azure-kubernetes-service-integration.md)」を参照してください。

詳しくは、[Security Center のコンテナー セキュリティ機能](container-security.md)に関するページを参照してください。


### <a name="improved-just-in-time-experience"></a>Just-In-Time エクスペリエンスの改善

お客様の管理ポートを保護する Azure Security Center の Just-In-Time ツールの機能、操作、UI が、次のように強化されました。 

- **理由のフィールド** - Azure portal の Just-In-Time ページを使用して仮想マシン (VM) へのアクセスを要求する際に、要求の理由を入力するためにオプションの新しいフィールドを使用できます。 このフィールドに入力された情報は、アクティビティ ログで追跡できます。 
- **冗長な Just-In-Time (JIT) 規則の自動クリーンアップ** - JIT ポリシーを更新するたびに、クリーンアップ ツールが自動的に実行され、規則セット全体の有効性がチェックされます。 このツールでは、ポリシー内の規則と NSG 内の規則の不一致が検索されます。 クリーンアップ ツールで不一致が検出されると、原因が特定され、削除した方が安全と見なされる場合には不要な組み込み規則が削除されます。 ユーザーが作成した規則がこのツールによって削除されることはありません。 

詳しくは、[JIT アクセス機能](security-center-just-in-time.md)に関するページを参照してください。


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Web アプリケーションに関する 2 つのセキュリティ推奨事項の非推奨化

Web アプリケーションに関する 2 つのセキュリティ推奨事項が非推奨になります。 

- IaaS NSG 上の Web アプリケーションに対する規則を強化する必要があります。
    (関連ポリシー:IaaS 上の Web アプリケーションに対する NSG 規則を強化する必要がある)

- App Services へのアクセスを制限する必要があります。
    (関連ポリシー:App Services へのアクセスを制限する必要があります (プレビュー))

これらの推奨事項は、Security Center の推奨事項リストに表示されなくなります。 関連ポリシーは、"Security Center の既定" という名前のイニシアチブに含まれなくなります。

詳しくは、[セキュリティに関する推奨事項](recommendations-reference.md)についてのページを参照してください。

