---
title: Azure Security Center の最新情報のアーカイブ
description: 6 か月前以前の、Azure Security Center の新機能と変更点の説明。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: a00c11924d2c0f6860c297ab7e58da21da5e1975
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102634704"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Azure Security Center の最新情報のアーカイブ

「[What's new in Azure Security Center? (Azure Security Center の最新情報)](release-notes.md)」という主要リリース ノート ページには、過去 6 か月間の更新情報が含まれていますが、このページにはそれ以前の項目が含まれています。

このページでは、以下に関する情報を提供します。

- 新機能
- バグの修正
- 非推奨の機能



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

Security Center のポータル ページの UI が更新されました。 新しいページには、新しい概要ページと、セキュリティ スコア、資産インベントリ、および Azure Defender の各ダッシュボードが含まれています。

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

統合された脆弱性評価拡張機能 (Qualys を使用) をより多くのマシンに展開できるようになりましたが、サポートは、「[統合された脆弱性スキャナーを Standard レベルの VM にデプロイする](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)」に記載されている OS を使用している場合にのみ受けることができます

詳細については、[仮想マシン向けの統合された脆弱性スキャナー (Azure Defender が必要)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner) に関するページを参照してください。

Qualys または Rapid7 からプライベートにライセンス提供された独自の脆弱性評価ソリューションを使用する方法の詳細については、「[パートナーの脆弱性スキャン ソリューションをデプロイする](deploy-vulnerability-assessment-vm.md)」を参照してください。


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Azure Files と Azure Data Lake Storage Gen2 を含むように拡張された Azure Storage の脅威の防止 (プレビュー)

Azure Storage の脅威の防止では、Azure Storage アカウント上の潜在的に有害なアクティビティを検出します。 Security Center では、ストレージ アカウントへのアクセスまたはストレージ アカウントの悪用の試行が検出されたときにアラートが表示されます。 

BLOB コンテナー、ファイル共有、またはデータ レイクのいずれに格納されているデータでも保護できます。




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>脅威の防止機能を有効にするための 8 つの新しい推奨事項

次のリソース タイプに対して Azure Security Center の脅威の防止機能を簡単に提供するため、新たに 8 つの推奨事項が追加されました: 仮想マシン、App Service プラン、Azure SQL Database サーバー、マシン上の SQL Server、Azure Storage アカウント、Azure Kubernetes Service クラスター、Azure Container Registry レジストリ、および Azure Key Vault ボールト。

新しい推奨事項は次のとおりです。

- **Azure SQL Database サーバーで Advanced Data Security を有効にする必要がある**
- **マシン上の SQL サーバーで Advanced Data Security を有効にする必要がある**
- **Azure App Service プランで Advanced Threat Protection を有効にする必要がある**
- **Azure Container Registry レジストリで Advanced Threat Protection を有効にする必要がある**
- **Azure Key Vault コンテナーで Advanced Threat Protection を有効にする必要がある**
- **Azure Kubernetes Service クラスターで Advanced Threat Protection を有効にする必要がある**
- **Azure Storage アカウントで Advanced Threat Protection を有効にする必要がある**
- **仮想マシンで Advanced Threat Protection を有効にする必要がある**

これらの新しい推奨事項は、 **[Azure Defender を有効にする]** セキュリティ コントロールに属しています。

推奨事項には、クイック修正機能も含まれています。 

> [!IMPORTANT]
> これらの推奨事項を 1 つでも修復すると、関連するリソースを保護するための料金が発生します。 現在のサブスクリプションに関連するリソースがある場合、直ちに料金が発生します。 後で追加した場合は、後で行われます。
> 
> たとえば、サブスクリプションに Azure Kubernetes Service クラスターがない状態で脅威の防止を有効にした場合、料金は発生しません。 将来、同じサブスクリプションにクラスターを追加すると、そのクラスターは自動的に保護され、その時点で料金が発生します。

各項目の詳細については、[セキュリティに関する推奨事項のリファレンス ページ](recommendations-reference.md)を参照してください。

詳細については、「[Azure Security Center での脅威の防止](azure-defender.md)」を参照してください。




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>コンテナーのセキュリティ強化 - レジストリのスキャンの高速化とドキュメントの更新

コンテナーのセキュリティ ドメインへの継続的な投資の一環として、Security Center では Azure Container Registry に保存されているコンテナー イメージの動的スキャンにおける大幅なパフォーマンス向上を実現しました。 スキャンは、通常約 2 分で完了するようになりました。 場合によっては、最大 15 分かかることがあります。

Azure Security Center のコンテナーのセキュリティ機能に関する明確さとガイダンスを改善するために、コンテナーのセキュリティに関するドキュメントのページも更新しました。 

Security Center でのコンテナー セキュリティの詳細については、次の記事を参照してください。

- [Security Center のコンテナーのセキュリティ機能の概要](container-security.md)
- [Azure Container Registry との統合の詳細](defender-for-container-registries-introduction.md)
- [Azure Kubernetes Service との統合の詳細](defender-for-kubernetes-introduction.md)
- [レジストリをスキャンして Docker ホストを強化する方法](container-security.md)
- [Azure Kubernetes Service クラスターの脅威防止機能からのセキュリティ アラート](alerts-reference.md#alerts-akscluster)
- [Azure Kubernetes Service ホストの脅威防止機能からのセキュリティ アラート](alerts-reference.md#alerts-containerhost)
- [コンテナーに関するセキュリティの推奨事項](recommendations-reference.md#recs-compute)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>新しい推奨事項とパス規則でのワイルドカードのサポートによって適応型アプリケーション制御を更新

適応型アプリケーション制御の機能に、2 つの重要な更新プログラムが適用されています。

* 新しい推奨事項により、以前は許可されていなかった、正当である可能性のある動作が識別されます。 新しい推薦事項の「**適応型アプリケーション制御ポリシーの許可リスト ルールを更新する必要がある**」により、既存のポリシーに新しいルールを追加して、適応型アプリケーション制御違反アラートの擬陽性の数を減らすよう求めるメッセージが表示されます。

* パス規則でワイルドカードがサポートされるようになっています。 この更新プログラムから、ワイルドカードの使用が許可されたパス規則を構成できます。 サポートされているシナリオは 2 つあります。

    * パスの末尾でワイルドカードを使用し、そのフォルダーとサブフォルダー内のすべての実行可能ファイルを許可します

    * パスの途中でワイルドカードを使用し、変化するフォルダー名を備えた既知の実行可能ファイルの名前 (既知の実行可能ファイルを含む個人ユーザー フォルダー、自動生成されたフォルダー名など) を有効にします。


適応型アプリケーション制御の詳細については、[こちら](security-center-adaptive-application.md)をご覧ください。



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>非推奨となった SQL の高度なデータ セキュリティの 6 つのポリシー

SQL マシンの高度なデータ セキュリティに関連する 6 つのポリシーが非推奨になります。

- SQL マネージド インスタンスの高度なデータ セキュリティ設定で、Advanced Threat Protection の種類を [すべて] に設定する必要がある
- SQL Server の高度なデータ セキュリティ設定で、Advanced Threat Protection の種類を [すべて] に設定する必要がある
- SQL マネージド インスタンスの高度なデータ セキュリティ設定に、セキュリティ アラートを受信するためのメール アドレスが含まれている必要がある
- SQL Server の高度なデータ セキュリティ設定に、セキュリティ アラートを受信するためのメール アドレスが含まれている必要がある
- SQL マネージド インスタンスの Advanced Data Security 設定で管理者とサブスクリプションの所有者に対するメール通知を有効にする必要がある
- SQL Server の Advanced Data Security 設定で、管理者とサブスクリプションの所有者に対するメール通知を有効にする必要がある

詳細については、[組み込みのポリシー](./policy-reference.md)に関するページを参照してください。



## <a name="june-2020"></a>2020 年 6 月

6 月の更新プログラムには次のものが含まれます。

- [セキュリティ スコア API (プレビュー)](#secure-score-api-preview)
- [SQL マシン向けの Advanced Data Security (Azure、他のクラウド、オンプレミス) (プレビュー)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview)
- [Log Analytics エージェントを Azure Arc マシンにデプロイするための 2 つの新しい推奨事項 (プレビュー)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [連続エクスポートとワークフローの自動化の構成を大規模に作成するための新しいポリシー](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [NSG を使用してインターネットに接続していない仮想マシンを保護するための新しい推奨事項](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [脅威の防止と Advanced Data Security を有効にするための新しいポリシー](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>セキュリティ スコア API (プレビュー)

[セキュリティ スコア API](/rest/api/securitycenter/securescores/) (現在プレビュー段階) を使用して、スコアにアクセスできるようになりました。 この API メソッドにより、より柔軟にデータに対してクエリを実行したり、一定期間のセキュリティ スコアをレポートする独自のメカニズムを構築できるようになります。 たとえば、**Secure Scores** API を使用して、特定のサブスクリプションのスコアを取得できます。 また、**Secure Score Controls** API を使用して、サブスクリプションのセキュリティ コントロールと現在のスコアを一覧表示できます。

セキュリティ スコア API で実現可能な外部ツールの例については、「[GitHub コミュニティのセキュリティ スコア エリア](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)」を参照してください。

詳細については、[Azure Security Center のセキュリティ スコアとセキュリティ コントロール](secure-score-security-controls.md)に関するページを参照してください。



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview"></a>SQL マシン向けの Advanced Data Security (Azure、他のクラウド、オンプレミス) (プレビュー)

Azure Security Center の SQL マシン向けの Advanced Data Security により、Azure、他のクラウド環境、さらにはオンプレミスのマシン でホストされている SQL Server も保護されるようになりました。 これにより、Azure ネイティブの SQL Server の保護が拡張され、ハイブリッド環境を完全にサポートできるようになります。

Advanced Data Security では、SQL マシンに対して、場所によらず脆弱性評価と高度な脅威の防止が提供されます。

セットアップには、次の 2 つの手順があります。

1. Log Analytics エージェントを SQL Server のホスト マシンにデプロイして、Azure アカウントへの接続を提供します。

1. Security Center の [価格と設定] ページで、オプションのバンドルを有効にします。

詳細については、[SQL マシン向けの Advanced Data Security](defender-for-sql-usage.md) に関するページを参照してください。



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Log Analytics エージェントを Azure Arc マシンにデプロイするための 2 つの新しい推奨事項 (プレビュー)

[Log Analytics エージェント](../azure-monitor/agents/log-analytics-agent.md)を Azure Arc マシンにデプロイし、Azure Security Center によって保護されていることを確認する、2 つの新しい推奨事項が追加されました。

- **Log Analytics エージェントを Windows ベースの Azure Arc マシンにインストールする必要がある (プレビュー)**
- **Log Analytics エージェントを Linux ベースの Azure Arc マシンにインストールする必要がある (プレビュー)**

これらの新しい推奨事項は、既存の (関連する) 推奨事項である、「**お使いのマシンに監視エージェントをインストールする必要があります**」と同じ、セキュリティ構成の修復、適応型アプリケーション制御の適用、システムの更新プログラムの適用、Endpoint Protection を有効にするという 4 つのセキュリティ コントロールに表示されます。

推奨事項には、デプロイ プロセスを高速化するためのクイック修復機能も含まれています。 

これら 2 つの新しい推奨事項の詳細については、「[コンピューティングとアプリの推奨事項](recommendations-reference.md#recs-compute)」テーブルを参照してください。

Azure Security Center でエージェントを使用する方法の詳細については、「[Log Analytics エージェントとは](faq-data-collection-agents.md#what-is-the-log-analytics-agent)」を参照してください。

詳細については、[Azure Arc マシンの拡張機能](../azure-arc/servers/manage-vm-extensions.md)に関するページを参照してください。


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

2 つのエクスポート ポリシーの使用の詳細については、「[提供されているポリシーを使用してワークフローの自動化を大規模に構成する](workflow-automation.md#configure-workflow-automation-at-scale-using-the-supplied-policies)」と「[連続エクスポートを設定する](continuous-export.md#set-up-a-continuous-export)」を参照してください。


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>NSG を使用してインターネットに接続していない仮想マシンを保護するための新しい推奨事項

"セキュリティのベスト プラクティスの実装" セキュリティ コントロールに、次の新しい推奨事項が含まれるようになりました。

- **インターネットに接続されていない仮想マシンをネットワーク セキュリティ グループで保護する必要がある**

既存の推奨事項である「**インターネットに接続する仮想マシンは、ネットワーク セキュリティ グループを使用して保護する必要がある**」では、インターネットに接続された仮想マシンとインターネットに接続されていない仮想マシンが区別されていませんでした。 どちらの場合でも、VM がネットワーク セキュリティ グループに割り当てられていないと、重大度の高い推奨事項が生成されていました。 この新しい推奨事項では、インターネットに接続されていないマシンを分離することで擬陽性を減らし、不必要な重要度の高いアラートを回避します。

詳細については、「[ネットワークの推奨事項](recommendations-reference.md#recs-networking)」テーブルを参照してください。




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>脅威の防止と Advanced Data Security を有効にするための新しいポリシー

次の新しいポリシーが、ASC の既定のイニシアティブに追加されました。これらは関連するリソースの種類に対して、脅威の防止または Advanced Data Security の有効化を促進するよう設計されています。

ポリシーは Azure ポリシーで確認できます。


| ポリシー                                                                                                                                                                                                                                                                | ポリシー ID                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [Azure SQL Database サーバーで Advanced Data Security を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [マシン上の SQL サーバーで Advanced Data Security を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Azure Storage アカウントで Advanced Threat Protection を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [Azure Key Vault コンテナーで Advanced Threat Protection を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [Azure App Service プランで Advanced Threat Protection を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [Azure Container Registry レジストリで Advanced Threat Protection を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [Azure Kubernetes Service クラスターで Advanced Threat Protection を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [Virtual Machines で Advanced Threat Protection を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

詳細については、「[Azure Security Center での脅威の防止](azure-defender.md)」を参照してください。


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

詳しくは、[仮想マシンに対する Security Center の統合脆弱性評価](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)に関するページを参照してください。



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

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="推奨設定の &quot;コントロールによるグループ化&quot; トグル":::

### <a name="expanded-security-control-implement-security-best-practices"></a>拡張されたセキュリティ コントロール "セキュリティのベスト プラクティスの実装" 

強化されたセキュア スコアと共に導入されたセキュリティ コントロールの 1 つが、"セキュリティのベスト プラクティスの実装" です。 このコントロールに推奨事項があっても、セキュア スコアに影響はありません。 

この更新により、3 つの推奨事項が当初配置されていたコントロールからこのベスト プラクティス コントロールに移動されました。 この操作が行われたのは、これら 3 つの推奨事項のリスクが当初考えていたよりも低いことが判明したためです。

さらに、2 つの新しい推奨事項が導入され、このコントロールに追加されました。

移動された 3 つの推奨事項は次のとおりです。

- **ご利用のサブスクリプションに対して読み取りアクセス許可があるアカウントでは、MFA を有効にする必要があります** (当初は "MFA の有効化" コントロール内)
- **読み取りアクセス許可がある外部アカウントは、ご使用のサブスクリプションから削除する必要があります** (当初は "アクセスとアクセス許可の管理" コントロール内)
- **お使いのサブスクリプションに、最大 3 人の所有者を指定する必要があります** (当初は "アクセスとアクセス許可の管理" コントロール内)

コントロールに追加された 2 つの新しい推奨事項は次のとおりです。

- **ゲスト構成拡張機能が Windows 仮想マシンにインストールされている必要がある (プレビュー)** - [Azure Policy ゲスト構成](../governance/policy/concepts/guest-configuration.md)を使用することで、サーバーとアプリケーションの設定に対して仮想マシンが可視化されます (Windows のみ)。

- **お使いのマシンで Windows Defender Exploit Guard を有効にする必要がある (プレビュー)** - Windows Defender Exploit Guard では、Azure Policy ゲスト構成エージェントが利用されます。 Exploit Guard には、さまざまな攻撃ベクトルに対してデバイスをロックダウンし、マルウェア攻撃でよく使用される動作をブロックするよう設計された 4 つのコンポーネントがありますが、企業がセキュリティ リスクと生産性の要件のバランスをとれるようになっています (Windows のみ)。

Windows Defender Exploit Guard の詳細については、「[Exploit Guard ポリシーの作成と展開](/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy)」を参照してください。

セキュリティ コントロールの詳細については、[強化されたセキュリティ スコア (プレビュー)](secure-score-security-controls.md) に関するページを参照してください。



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>カスタム メタデータを使用したカスタム ポリシーの一般提供の開始

カスタム ポリシーは、Security Center の推奨事項エクスペリエンス、セキュア スコア、規制コンプライアンス標準ダッシュボードに含まれました。 この機能の一般提供が開始され、お客様は Security Center で組織のセキュリティ評価の範囲を拡大できるようになりました。 

Azure Policy でカスタム イニシアチブを作成し、それにポリシーを追加して Azure Security Center にオンボードし、推奨事項として視覚化します。

さらに、カスタム推奨事項のメタデータを編集するオプションも追加されました。 メタデータ オプションには、重要度、修復手順、脅威情報などが含まれます。  

詳しくは、「[詳細情報でのカスタム推奨事項の拡張](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information)」を参照してください。



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>ファイルレス攻撃検出へのクラッシュ ダンプ分析機能の移行 

Microsoft は Windows クラッシュ ダンプ (CDA) 検出機能を[ファイルレス攻撃検出](defender-for-servers-introduction.md#what-are-the-benefits-of-azure-defender-for-servers)に移行しています。 ファイルレス攻撃検出分析では、Windows マシンで次のセキュリティ アラートの高度なバージョンを利用できます。コード インジェクションの検出、Windows モジュールの偽装の検出、シェルコードの検出、疑わしいコード セグメントの検出。

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

Azure Security Center の規制コンプライアンス ダッシュボードに、追加の業界および規制の標準を追跡するための **動的コンプライアンス パッケージ** が追加されました (一般提供開始)。

動的コンプライアンス パッケージは、Security Center のセキュリティ ポリシー ページからサブスクリプションまたは管理グループに追加できます。 標準またはベンチマークをオンボードすると、評価として、マップされた関連するすべてのコンプライアンス データと共に、標準が規制コンプライアンス ダッシュボードに表示されます。 オンボードされた標準の概要レポートがダウンロードできるようになります。

次のような標準を追加できるようになりました。

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK OFFICIAL および UK NHS**
- **カナダ連邦の PBMM**
- **Azure CIS 1.1.0 (新規)** (Azure CIS 1.1.0 のより完全な表現)

さらに、**Azure セキュリティ ベンチマーク** が最近追加されました。これは Microsoft が作成したもので、一般的なコンプライアンス フレームワークに基づくセキュリティとコンプライアンスのベスト プラクティスに関する Azure 固有のガイドラインです。 追加の標準が使用可能になると、ダッシュボードでサポートされます。  
 
詳しくは、「[規制コンプライアンス ダッシュボードでの動的コンプライアンス パッケージへの更新](update-regulatory-compliance-packages.md)」を参照してください。


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Azure Security Center Free レベルへの ID に関する推奨事項の追加

Azure Security Center Free レベルで、ID とアクセスのセキュリティに関する推奨事項の一般提供が開始されました。 これは、クラウドのセキュリティ体制管理 (CSPM) 機能を無料化する取り組みの一環です。 これまで、これらの推奨事項を利用できるのは、Standard 価格レベルだけでした。

ID とアクセスに関する推奨事項の例を次に示します。

- "ご利用のサブスクリプションに対して所有者アクセス許可があるアカウントでは、MFA を有効にする必要があります。"
- "お使いのサブスクリプションに、最大 3 人の所有者を指定する必要があります。"
- "非推奨のアカウントは、ご利用のサブスクリプションから削除する必要があります。"

Free 価格レベルのサブスクリプションをお持ちのお客様は、ID とアクセスのセキュリティについて評価されることがなかったため、この変更により、セキュア スコアに影響が生じます。

詳しくは、「[ID とアクセスの推奨事項](recommendations-reference.md#recs-identityandaccess)」を参照してください。

詳細については、「[サブスクリプションでの多要素認証 (MFA) の実施を管理する](security-center-identity-access.md)」を参照してください。



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

詳しくは、[ロジック アプリの作成](../logic-apps/logic-apps-overview.md)に関するページを参照してください。


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

詳しくは、「[Azure Kubernetes Service と Security Center の統合](defender-for-kubernetes-introduction.md)」を参照してください。

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




## <a name="february-2020"></a>2020 年 2 月

### <a name="fileless-attack-detection-for-linux-preview"></a>Linux 向けのファイルレス攻撃検出 (プレビュー)

検出を回避するためにステルス性の高い方法を利用する攻撃者が増えているので、Azure Security Center では、Windows 向けに加えて Linux 向けのファイルレス攻撃検出を拡張しています。 ファイルレス攻撃では、ソフトウェアの脆弱性が悪用されて、悪意のあるペイロードが正常なシステム プロセスに挿入され、メモリ内に隠されます。 これらの手法には以下のようなものがあります。

- ディスク上のマルウェアの痕跡を最小化または排除する
- ディスクベースのマルウェア スキャン ソリューションによる検出の可能性を大幅に下げる

この脅威に対処するために、Azure Security Center では 2018 年 10 月にファイルレス攻撃検出をリリースしました。そしてこのたび、ファイルレス攻撃検出は Linux にも拡張されました。 



## <a name="january-2020"></a>2020 年 1 月

### <a name="enhanced-secure-score-preview"></a>強化されたセキュア スコア (プレビュー)

Azure Security Center のセキュア スコア機能の強化バージョンが、プレビュー版で利用できるようになりました。 このバージョンでは、複数の推奨事項が、脆弱な攻撃対象領域をより正確に反映するセキュリティ コントロールにグループ化されています (例: 管理ポートへのアクセスの制限)。

プレビュー段階でセキュア スコアの変更に慣れていただけるほか、ご自分の環境のセキュリティを高めるのに役立つその他の修復機能をご確認いただけます。

詳細については、[強化されたセキュア スコア (プレビュー)](secure-score-security-controls.md) に関するページを参照してください。



## <a name="november-2019"></a>2019 年 11 月

11 月の更新プログラムには次のものが含まれます。
 - [北米リージョンで Azure Key Vault 用 Threat Protection (プレビュー)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [Azure Storage 用 Threat Protection にマルウェア評価スクリーニングを追加](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Logic Apps を使用したワークフローの自動化 (プレビュー)](#workflow-automation-with-logic-apps-preview)
 - [大量のリソースのクイック修正の一般提供を開始](#quick-fix-for-bulk-resources-generally-available)
 - [コンテナー イメージの脆弱性スキャン (プレビュー)](#scan-container-images-for-vulnerabilities-preview)
 - [規制コンプライアンス標準の追加 (プレビュー)](#additional-regulatory-compliance-standards-preview)
 - [Azure Kubernetes Service 用 Threat Protection (プレビュー)](#threat-protection-for-azure-kubernetes-service-preview)
 - [仮想マシンの脆弱性評価 (プレビュー)](#virtual-machine-vulnerability-assessment-preview)
 - [Azure Virtual Machines の SQL Server 向け Advanced Data Security (プレビュー)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [カスタム ポリシーのサポート (プレビュー)](#support-for-custom-policies-preview)
 - [コミュニティおよびパートナー向けのプラットフォームで Azure Security Center のカバレッジを拡大](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [推奨事項とアラートのエクスポートによる高度な統合 (プレビュー)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [Windows Admin Center から Security Center へのオンプレミス サーバーのオンボード (プレビュー)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>北米リージョンで Azure Key Vault 用 Threat Protection (プレビュー)

Azure Key Vault は、キー、シークレット、暗号化キー、ポリシーをクラウドで一元管理する機能を提供することによって、データを保護し、クラウド アプリケーションのパフォーマンスを向上させる非常に重要なサービスです。 Azure Key Vault には、機密性の高い、ビジネスに不可欠なデータが格納されているため、キー コンテナーとそこに格納されるデータの最大限のセキュリティが必要となります。

Azure Security Center による Azure Key Vault 用 Threat Protection のサポートにより、キー コンテナーへのアクセスやキー コンテナーの悪用を試みる、害を及ぼす可能性のある通常とは異なる試行を検出する、セキュリティ インテリジェンスのレイヤーが追加されます。 この新しい保護レイヤーにより、お客様は、セキュリティ専門家でなくてもキー コンテナーに対する脅威に対処することが可能となり、セキュリティ監視システムを管理できるようになります。 この機能は、北米リージョンでパブリック プレビュー段階にあります。


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Azure Storage 用 Threat Protection にマルウェア評価スクリーニングを追加

Azure Storage 用 Threat Protection は、Microsoft 脅威インテリジェンスを利用した新しい検出機能を提供します。これらの機能では、ハッシュ評価分析を使用して Azure Storage へのマルウェアのアップロードを検出したり、アクティブな Tor 出口ノード (匿名化プロキシ) からの不審なアクセスを検出したりすることができます。 Azure Security Center を使用して、ストレージ アカウント全体で検出されたマルウェアを表示できるようになりました。


### <a name="workflow-automation-with-logic-apps-preview"></a>Logic Apps を使用したワークフローの自動化 (プレビュー)

セキュリティと IT/運用が一元管理されている組織では、環境で不一致が見つかった場合に組織内で必要なアクションを実行する内部ワークフロー プロセスを実装しています。 多くの場合、これらのワークフローは反復可能なプロセスであるため、自動化によって組織内のプロセスを大幅に効率化できます。

このたび、Azure Logic Apps を利用して自動化構成を作成し、推奨事項やアラートなど、ASC の特定の結果に基づいてそれらを自動的にトリガーするポリシーを作成できる新しい機能が Security Center に導入されます。 Azure Logic App は、Logic App コネクタの広範なコミュニティによってサポートされているカスタム アクションを実行するように構成することも、メールの送信や ServiceNow™ チケットのオープンなど、Security Center が提供するテンプレートのいずれかを使用するように構成することもできます。

ワークフローを実行するための Security Center の自動および手動機能の詳細については、「[ワークフローの自動化](workflow-automation.md)」をご覧ください。

Logic Apps の作成方法については、[Azure Logic Apps](../logic-apps/logic-apps-overview.md) に関する記事をご覧ください。


### <a name="quick-fix-for-bulk-resources-generally-available"></a>大量のリソースのクイック修正の一般提供を開始

セキュリティ スコアの一部として、ユーザーには多くのタスクが示されるため、大規模なフリートで問題を効果的に修復するのが困難になる可能性があります。

セキュリティの構成の誤りを簡単に修復し、大量のリソースに関する推奨事項をすばやく修復できるようにして、セキュリティ スコアを向上させるには、クイック修正を使用します。

この操作により、ユーザーは修復を適用するリソースを選択し、ユーザーに代わって設定を構成する修復アクションを起動できます。

このたび、Security Center の推奨事項ページの一部として、クイック修正の一般提供が開始されました。

クイック修正が有効になっている推奨事項については、[セキュリティの推奨事項のリファレンス ガイド](recommendations-reference.md)をご覧ください。


### <a name="scan-container-images-for-vulnerabilities-preview"></a>コンテナー イメージの脆弱性スキャン (プレビュー)

Azure Security Center で、Azure Container Registry にあるコンテナー イメージの脆弱性をスキャンできるようになりました。

イメージのスキャンでは、コンテナー イメージ ファイルを解析し、(Qualys を利用して) 既知の脆弱性が存在するかどうかが確認されます。

スキャン自体は、新しいコンテナー イメージを Azure Container Registry にプッシュすると自動的にトリガーされます。 検出された脆弱性は Security Center の推奨事項として表示され、修正プログラムを適用してそれらの脆弱性による攻撃対象領域を削減する方法に関する情報と共に Azure セキュリティ スコアに含められます。


### <a name="additional-regulatory-compliance-standards-preview"></a>規制コンプライアンス標準の追加 (プレビュー)

規制コンプライアンス ダッシュボードは、Security Center の評価に基づいて、コンプライアンス体制に関する分析情報を提供します。 ダッシュボードでは、お使いの環境が特定の規制基準や業界ベンチマークで指定されたコントロールと要件にどのように準拠しているかが示され、これらの要件に対処する方法に関する規範的な推奨事項が提供されます。

これまで、規制コンプライアンス ダッシュボードでは、Azure CIS 1.1.0、PCI-DSS、ISO 27001、SOC-TSP の 4 つの組み込み標準がサポートされていました。 このたび、Microsoft は、サポート対象となる追加の標準として、NIST SP 800-53 R4、SWIFT CSP CSCF v2020、Canada Federal PBMM、UK Official および UK NHS のパブリック プレビュー リリースを発表しました。 また、Azure CIS 1.1.0 の更新バージョンもリリースし、この標準のより多くのコントロールに対応して拡張性を強化します。

規制コンプライアンス ダッシュボードでの標準セットのカスタイマイズの詳細については、[こちら](update-regulatory-compliance-packages.md)をご覧ください。


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Azure Kubernetes Service 用 Threat Protection (プレビュー)

Kubernetes は、ソフトウェアをクラウドにデプロイして管理するための新しい標準になりつつあります。 Kubernetes の豊富な経験を持つ人はほとんどおらず、多くの人が一般的なエンジニアリングと管理にのみ注目し、セキュリティ面を見落としています。 コンテナーに焦点を合わせた攻撃対象領域が無防備な状態で攻撃者にさらされないように、Kubernetes 環境のセキュリティを慎重に構成する必要があります。 Security Center では、コンテナー領域でのサポートを、Azure で最も急成長しているサービスの 1 つである Azure Kubernetes Service (AKS) まで拡大しています。

このパブリック プレビュー リリースの新しい機能は次のとおりです。

- **検出と可視化** - Security Center の登録済みサブスクリプション内のマネージド AKS インスタンスの継続的な検出。
- **セキュリティ スコアの推奨事項** - お客様が AKS のセキュリティに関するベストプラクティスに準拠し、セキュリティ スコアを上げるのに役立つ実用的な項目。 「Kubernetes Service クラスターへのアクセスを制限するには、ロールベースの Access Control を使用する必要がある」などの項目を含む推奨事項。
- **脅威の検出** - ホストおよびクラスターベースの分析 (例: "特権コンテナーが検出されました")。


### <a name="virtual-machine-vulnerability-assessment-preview"></a>仮想マシンの脆弱性評価 (プレビュー)

多くの場合、仮想マシンにインストールされているアプリケーションには、仮想マシンの侵害につながるおそれのある脆弱性が存在する可能性があります。 Microsoft は、Security Center Standard レベルに、追加料金なしで仮想マシンの脆弱性評価を組み込むことを発表しました。 Qualys を利用した脆弱性評価 (パブリック プレビュー) を使用すると、仮想マシンにインストールされているすべてのアプリケーションを継続的にスキャンして脆弱なアプリケーションを検出し、Security Center ポータルのエクスペリエンスに結果を表示できます。 Security Center がすべてのデプロイ操作を処理するため、ユーザーが追加の作業を行う必要はありません。 今後、お客様固有のビジネス ニーズに対応する脆弱性評価オプションを提供する予定です。

Azure Virtual Machines の脆弱性評価の詳細については、[こちら](deploy-vulnerability-assessment-vm.md)をご覧ください。


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Azure Virtual Machines の SQL Server 向け Advanced Data Security (プレビュー)

IaaS VM 上で実行されている SQL DB の脅威からの保護と脆弱性評価の、Azure Security Center によるサポートのプレビューが開始されました。

[脆弱性評価](../azure-sql/database/sql-vulnerability-assessment.md)では、データベースの潜在的な脆弱性を検出、追跡、修復できるサービスを簡単に構成できます。 Azure セキュリティ スコアの一部としてセキュリティ体制が可視化され、セキュリティの問題を解決してデータベースの防御機能を強化するための手順が含まれます。

[Advanced Threat Protection](../azure-sql/database/threat-detection-overview.md) では、SQL Server へのアクセスや SQL Server の悪用を試みる、害を及ぼす可能性のある通常とは異なる試行を示す異常なアクティビティが検出されます。 データベースでの不審なアクティビティを継続的に監視し、異常なデータベース アクセス パターンに対してアクション指向のセキュリティ通知を提供します。 このようなアラートからは、不審なアクティビティの詳細と、脅威の調査や危険性の軽減のために推奨されるアクションが提示されます。


### <a name="support-for-custom-policies-preview"></a>カスタム ポリシーのサポート (プレビュー)

Azure Security Center でカスタム ポリシーがサポートされるようになりました (プレビュー)。

お客様は、Azure Policy で作成したポリシーに基づく独自のセキュリティ評価によって、Security Center の現在のセキュリティ評価の対象範囲を拡張することを求めてきました。 カスタム ポリシーのサポートにより、これが可能になりました。

これらの新しいポリシーは、Security Center の推奨事項エクスペリエンス、セキュリティ スコア、規制コンプライアンス標準ダッシュボードに含まれるようになります。 カスタム ポリシーのサポートにより、Azure Policy でカスタム イニシアチブを作成し、Security Center にポリシーとして追加して、推奨事項として表示できるようになりました。


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>コミュニティおよびパートナー向けのプラットフォームで Azure Security Center のカバレッジを拡大

Security Center を使用して、Microsoft だけでなく、Check Point、Tenable、CyberArk などのパートナーの既存のソリューションからも推奨事項を受け取ることができます。今後、さらに多くの統合が提供される予定です。  Security Center のシンプルなオンボード フローにより、既存のソリューションを Security Center に接続することで、セキュリティ体制に関する推奨事項を 1 か所で表示し、統合レポートを実行することが可能になります。組み込みの推奨事項とパートナーの推奨事項の両方に対して、Security Center のすべての機能を活用することもできます。 また、Security Center の推奨事項をパートナー製品にエクスポートすることもできます。

Microsoft インテリジェント セキュリティ アソシエーションの詳細については、[こちら](https://www.microsoft.com/security/partnerships/intelligent-security-association)をご覧ください。



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>推奨事項とアラートのエクスポートによる高度な統合 (プレビュー)

Security Center 上でエンタープライズ レベルのシナリオに対応するために、Azure portal または API 以外の場所でも Security Center のアラートと推奨事項を使用できるようになりました。 これらは、イベント ハブおよび Log Analytics ワークスペースに直接エクスポートできます。 これらの新しい機能を使用して作成できるワークフローは次のとおりです。

- Log Analytics ワークスペースにエクスポートすることにより、PowerBI でカスタム ダッシュボードを作成できます。
- イベント ハブにエクスポートすることにより、Security Center のアラートと推奨事項をサード パーティの SIEM、リアルタイムのサード パーティ ソリューション、または Azure Data Explorer にエクスポートできるようになります。


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Windows Admin Center から Security Center へのオンプレミス サーバーのオンボード (プレビュー)

Windows Admin Center は、Azure にデプロイされていない Windows サーバー用の管理ポータルであり、バックアップやシステム更新などの Azure 管理機能を提供します。 最近、これらの非 Azure サーバーをオンボードして、Windows Admin Center のエクスペリエンスから直接 ASC で保護する機能が追加されました。

この新しいエクスペリエンスにより、ユーザーは WAC サーバーを Azure Security Center にオンボードし、Windows Admin Center のエクスペリエンスでセキュリティのアラートや推奨事項を直接表示できるようになります。


## <a name="september-2019"></a>2019 年 9 月

9 月の更新プログラムには次のものが含まれます。

 - [適応型アプリケーション制御のルール管理の改善](#managing-rules-with-adaptive-application-controls-improvements)
 - [Azure Policy を使用してコンテナーのセキュリティの推奨事項を制御](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>適応型アプリケーション制御のルール管理の改善

適応型アプリケーション制御を使用した仮想マシンのルール管理のエクスペリエンスが改善されました。 Azure Security Center の適応型アプリケーション制御を使用すると、仮想マシン上で実行できるアプリケーションを制御できます。 ルール管理の全般的な改善に加え、新たに追加される機能により、新しいルールを追加するときに保護するファイルの種類を制御できるようになります。

適応型アプリケーション制御の詳細については、[こちら](security-center-adaptive-application.md)をご覧ください。


### <a name="control-container-security-recommendation-using-azure-policy"></a>Azure Policy を使用してコンテナーのセキュリティの推奨事項を制御

コンテナーのセキュリティの脆弱性を修復するための Azure Security Center の推奨事項を、Azure Policy を使用して有効または無効にすることができるようになりました。

有効なセキュリティ ポリシーを表示するには、Security Center から [セキュリティ ポリシー] ページを開きます。


## <a name="august-2019"></a>2019 年 8 月

8 月の更新プログラムには次のものが含まれます。

 - [Azure Firewall 用 Just-In-Time (JIT) VM アクセス](#just-in-time-jit-vm-access-for-azure-firewall)
 - [セキュリティ体制を強化するシングル クリック修復 (プレビュー)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [テナント間の管理](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Azure Firewall 用 Just-In-Time (JIT) VM アクセス 

Azure Firewall 用 Just-In-Time (JIT) VM アクセスの一般提供が開始されました。 これを使用することで、NSG で保護された環境に加え、Azure Firewall で保護された環境もセキュリティで保護できます。

JIT VM アクセスでは、NSG と Azure Firewall の規則を使用して、必要な場合にのみ、VM への制御されたアクセスを提供することで、ネットワーク帯域幅消費型攻撃にさらされるリスクを低減します。

VM で JIT を有効にする場合は、保護するポート、ポートを開放しておく時間、これらのポートにアクセスできる承認済み IP アドレスを指定したポリシーを作成します。 このポリシーは、ユーザーがアクセスを要求したときに実行できる操作を制御するのに役立ちます。

要求は Azure アクティビティ ログに記録されるので、アクセスの監視と監査を簡単に行うことができます。 Just-In-Time ページは、JIT が有効になっている既存の VM や JIT が推奨される VM をすばやく特定する際にも役立ちます。

Azure Firewall の詳細については、[こちら](../firewall/overview.md)をご覧ください。


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>セキュリティ体制を強化するシングル クリック修復 (プレビュー)

セキュリティ スコアは、ワークロードのセキュリティに対する姿勢を評価するのに役立つツールです。 セキュリティの推奨事項が見直され、優先順位が自動的に示されるので、どの推奨事項を最初に実行すべきかがわかります。 これは、最も重大なセキュリティの脆弱性を見つけて、調査に優先順位を付けるのに役立ちます。

セキュリティの構成の誤りを簡単に修復し、セキュリティ スコアを速やかに向上させるために、大量のリソースに関する推奨事項をシングル クリックで修復できる新しい機能が追加されました。

この操作により、ユーザーは修復を適用するリソースを選択し、ユーザーに代わって設定を構成する修復アクションを起動できます。

クイック修正が有効になっている推奨事項については、[セキュリティの推奨事項のリファレンス ガイド](recommendations-reference.md)をご覧ください。


### <a name="cross-tenant-management"></a>テナント間の管理

Security Center では、Azure Lighthouse の一部として、テナント間の管理のシナリオがサポートされるようになりました。 これにより、Security Center で複数のテナントのセキュリティ体制を可視化し、管理できるようになります。 

テナント間の管理エクスペリエンスの詳細については、[こちら](security-center-cross-tenant-management.md)をご覧ください。


## <a name="july-2019"></a>2019 年 7 月

### <a name="updates-to-network-recommendations"></a>ネットワークの推奨事項の更新

Azure Security Center (ASC) で新しいネットワークの推奨事項が導入され、既存の推奨事項が改善されました。 Security Center を使用することで、リソースのネットワーク保護をさらに強化できます。 

ネットワークの推奨事項の詳細については、[こちら](recommendations-reference.md#recs-networking)をご覧ください。


## <a name="june-2019"></a>2019 年 6 月

### <a name="adaptive-network-hardening---generally-available"></a>アダプティブ ネットワークのセキュリティ強化機能 - 一般提供の開始

パブリック クラウドで実行されているワークロードの最大の攻撃対象領域の 1 つは、パブリック インターネットとの間の接続です。 Azure ワークロードを必要なソース範囲でのみ利用できるようにするために、どのネットワーク セキュリティ グループ (NSG) 規則を設定すればよいかをお客様が把握するのは困難です。 この機能を使用すると、Security Center が Azure ワークロードのネットワーク トラフィックと接続のパターンを学習し、インターネットに接続する仮想マシンに対して、NSG 規則の推奨事項を提供します。 これにより、お客様は、ネットワーク アクセス ポリシーをより適切に構成し、攻撃にさらされるリスクを抑えることができます。 

アダプティブ ネットワークのセキュリティ強化機能の詳細については、[こちら](security-center-adaptive-network-hardening.md)をご覧ください。