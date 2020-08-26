---
title: Azure Security Center での脅威の防止
description: このトピックでは、Azure Security Center の脅威の防止機能によって保護されるリソースについて説明します
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 69f439e102edc53207e44d63cb29396f64f59e0e
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272503"
---
# <a name="threat-protection-in-azure-security-center"></a>Azure Security Center での脅威の防止

Security Center は、ご自身の環境のいずれかの領域で脅威を検出すると、アラートを生成します。 これらのアラートでは、影響を受けるリソースや推奨される修復手順の詳細と、場合によっては、ロジック アプリを応答でトリガーするオプションが示されます。

Azure Security Center の脅威の防止によって、お使いの環境が包括的に保護されます。

* **Azure コンピューティング コンテナーの脅威の防止**: Windows マシン、Linux マシン、Azure App Service、および Azure コンテナー

* **Azure データ リソースの脅威の防止**: SQL Database と SQL Data Warehouse、Azure Storage、および Azure Cosmos DB

* **Azure サービス レイヤーの脅威の防止**: Azure ネットワーク レイヤー、Azure 管理レイヤー (Azure Resource Manager) (プレビュー)、および Azure Key Vault (プレビュー)

アラートは、Security Center によって生成されたか、別のセキュリティ製品の Security Center によって受信されたかにかかわらず、エクスポートすることができます。 アラートを Azure Sentinel、サードパーティの SIEM、またはその他の外部ツールにエクスポートする場合は、[SIEM へのアラートのエクスポート](continuous-export.md)に関するページの手順に従ってください。 

> [!NOTE]
> アラートの出力元によって、表示されるまでの時間が変わる場合があります。 たとえば、ネットワーク トラフィックの分析を必要とするアラートは、仮想マシンで実行されている疑わしいプロセスに関連するアラートよりも、表示されるまでより長い時間がかかる可能性があります。

> [!TIP]
> Security Center の脅威保護機能を有効にするには、Standard 価格レベルを、適用可能なワークロードを含むサブスクリプションに適用する必要があります。
>
> **Azure Storage アカウント**の脅威保護は、サブスクリプション レベルまたはリソース レベルで有効にできます。
> **Azure SQL Database SQL サーバー**の脅威保護は、サブスクリプション レベルまたはリソース レベルで有効にできます。
> **Azure Database for MariaDB/MySQL/PostgreSQL** の脅威保護は、リソース レベルでのみ有効にできます。



## <a name="threat-protection-for-windows-machines"></a>Windows マシンの脅威の防止 <a name="windows-machines"></a>

Azure Security Center は Azure サービスと統合し、Windows ベースのマシンの監視と保護が行われます。 Security Center は、これらすべてのサービスからのアラートと修復の提案を使いやすい形式で示します。

* **Microsoft Defender Advanced Threat Protection (ATP)** <a name="windows-atp"></a> - Security Center では、Microsoft Defender Advanced Threat Protection (ATP) と統合することで、そのクラウド ワークロード保護プラットフォームを拡張します。 同時に、包括的なエンドポイントの検出と対応 (EDR) 機能が提供されます。

    > [!IMPORTANT]
    > Microsoft Defender ATP センサーは、Security Center を使用する Windows サーバーで自動的に有効になります。

    Microsoft Defender ATP で脅威が検出されると、アラートがトリガーされます。 アラートは、[Security Center] ダッシュボードに表示されます。 ダッシュボードからは、Microsoft Defender ATP コンソールにピボットし、詳細な調査を実行して攻撃の範囲を明らかにすることができます。 Microsoft Defender ATP の詳細については、「[Microsoft Defender ATP サービスに対するサーバーのオンボード](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)」をご覧ください。

* **ファイルレス攻撃の検出** <a name="windows-fileless"></a> - ファイルレス攻撃は、ディスクベースのスキャン手法による検出を避けるために、悪意のあるペイロードをメモリに挿入します。 侵害されたプロセスのメモリ内に存続する攻撃者のペイロードにより、さまざまな悪意のあるアクティビティが実行されます。

    自動メモリ フォレンジック手法は、ファイルレス攻撃の検出を使用して、ファイルレス攻撃のツールキット、手法、および動作を識別します。 このソリューションでは、実行時にマシンが定期的にスキャンされて、プロセスのメモリから分析情報が直接抽出されます。 Linux の特定の分析情報には、次のものの識別が含まれます。 

    - よく知られているツールキットと暗号化マイニング ソフトウェア 
    - シェルコード。通常、ソフトウェアの脆弱性の悪用でペイロードとして使用される小さなコードです。
    - プロセスのメモリ内に挿入された悪意のある実行可能ファイル

    ファイルレス攻撃の検出では、ネットワーク アクティビティなどの追加のプロセス メタデータの記述を含む詳細なセキュリティ アラートが生成されます。 これにより、アラートのトリアージ、関連付け、およびダウンストリームの応答時間が短縮されます。 このアプローチにより、イベント ベースの EDR ソリューションが補完され、検出範囲が拡大します。

    ファイルレス攻撃の検出アラートの詳細については、[アラートのリファレンス表](alerts-reference.md#alerts-windows)に関するページを参照してください。

> [!TIP]
> Windows のアラートをシミュレートするには、「[Azure Security Center プレイブック:セキュリティ アラート](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)」をダウンロードします。






## <a name="threat-protection-for-linux-machines"></a>Linux マシンの脅威の防止 <a name="linux-machines"></a>

Security Center では、**auditd** (最も一般的な Linux 監査フレームワークの 1 つ) を使用して Linux マシンから監査レコードが収集されます。 auditd はメインライン カーネルに存在します。 

* **Linux auditd アラートと Log Analytics エージェントの統合** <a name="linux-auditd"></a> - auditd システムは、システム コールの監視を担当するカーネル レベルのサブシステムで構成されます。 指定されたルール セットによってそれらのフィルター処理が行われ、それらに対するメッセージがソケットに書き込まれます。 Security Center は、Log Analytics エージェント内で auditd パッケージの機能を統合します。 この統合により、前提条件なしで、すべてのサポートされている Linux ディストリビューションで auditd イベントの収集が可能になります。

    Linux 用 Log Analytics エージェントを使用して、auditd レコードの収集、拡充、およびイベントへの集約が行われます。 Security Center では、Linux のシグナルを使用してクラウドおよびオンプレミスの Linux マシン上で悪意のある動作を検出する新しい分析が、継続的に追加されています。 Windows の機能と同様に、これらの分析には、不審なプロセス、不審なサインインの試行、カーネル モジュールの読み込み、その他のアクティビティが含まれます。 これらのアクティビティは、マシンが攻撃を受けているか、侵害されたことを示している可能性があります。  

    Linux アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-linux)に関するページを参照してください。

> [!TIP]
> Linux のアラートをシミュレートするには、「[Azure Security Center プレイブック:Linux の検出](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)」をダウンロードします。





## <a name="threat-protection-for-azure-app-service"></a>Azure App Service の脅威の防止 <a name="app-services"></a>

> [!NOTE]
> 現在、Azure Government およびソブリン クラウド リージョンでは、このサービスを利用できません。

Security Center では、クラウドのスケールを使用して、App Service で実行されるアプリケーションをターゲットとした攻撃が識別されます。 攻撃者は、Web アプリケーションをプローブして弱点を発見し、悪用します。 Azure で実行されているアプリケーションに対する要求は、特定の環境にルーティングされる前に、複数のゲートウェイを通過し、そこで検査され、ログに記録されます。 その後、このデータは、悪用や攻撃者を特定し、後で使用される新しいパターンを学習するために使用されます。

Security Center では、Azure がクラウド プロバイダーとして備える可視性を使用して、App Service の内部ログが分析され、複数のターゲットに対する攻撃手法が識別されます。 たとえば、広範囲にわたるスキャンや分散型の攻撃などの手法です。 この種の攻撃は通常、IP の小さなサブセットから発生し、複数のホスト上の類似のエンドポイントをクロールするパターンを示します。 攻撃では脆弱なページやプラグインが検索され、単一のホストの観点からは攻撃を特定できません。

Windows ベースの App Service プランを実行している場合、Security Center では、基になるサンド ボックスおよび VM にアクセスすることもできます。 前述のログ データと組み合わせることで、インフラストラクチャは、流行している新しい攻撃から、お客様のマシンの侵害まで、状況を通知できます。 したがって、Web アプリが悪用された後に Security Center がデプロイされたとしても、進行中の攻撃を検出できる可能性があります。

Azure App Service アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-azureappserv)に関するページを参照してください。

App Service プランの詳細については、「[App Service プラン](https://azure.microsoft.com/pricing/details/app-service/plans/)」を参照してください。





## <a name="threat-protection-for-containers"></a>コンテナーの脅威の防止 <a name="azure-containers"></a>

### <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般公開|
|価格:|Standard レベル|
|必要なロールとアクセス許可:|**セキュリティ管理者**はアラートを無視できます。<br>**セキュリティ閲覧者**は、結果を表示できます。|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![No](./media/icons/no-icon.png) ナショナル/ソブリン (US Gov、China Gov、その他の Gov)|
|||

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>SQL Database および SQL Data Warehouse の脅威の防止 <a name="data-sql"></a>

Advanced Threat Protection for Azure SQL Database では、データベースへのアクセスや悪用を試みる、通常とは異なる、害を及ぼす可能性がある異常なアクティビティが検出されます。

疑わしいデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃、および異常なデータベース アクセスやクエリのパターンがある場合に、アラートが表示されます。

Advanced Threat Protection for Azure SQL Database and SQL は、高度な SQL セキュリティ機能を提供する [Advanced Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) 統合パッケージに含まれており、Azure SQL Database、Azure SQL Managed Instance、Azure SQL Data Warehouse データベース、Azure Virtual Machines 上の SQL サーバーに対応しています。

詳細については、次を参照してください。

* [Advanced Threat Protection for Azure SQL Database を有効にする方法](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Azure Virtual Machines 上の SQL サーバーに対して Advanced Threat Protection を有効にする方法](security-center-iaas-advanced-data.md)
* [SQL Database および SQL Data Warehouse 向け脅威保護アラートの一覧](alerts-reference.md#alerts-sql-db-and-warehouse)



## <a name="threat-protection-for-azure-storage"></a>Azure Storage の脅威の防止 <a name="azure-storage"></a>

### <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|[Blob Storage](https://azure.microsoft.com/services/storage/blobs/) (一般提供)<br>[Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) (プレビュー)<br>[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (プレビュー)|
|価格:|Standard レベル|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) China Gov、その他の Gov|
|||


### <a name="whats-protected"></a>保護対象

Azure Storage の脅威の防止では、お使いの Azure ストレージ アカウントにおいて有害である可能性があるアクティビティが検出されます。 BLOB コンテナー、ファイル共有、またはデータ レイクのいずれに格納されているデータでも保護できます。

この保護層により、セキュリティの専門家で "*なくても*" 脅威に対処し、セキュリティ監視システムを管理できます。

お使いのストレージ アカウントが保護されます 

### <a name="what-kind-of-alerts-does-threat-protection-for-azure-storage-provide"></a>Azure Storage に対する脅威の防止で提供されるアラートの種類

セキュリティ アラートは、次のような場合にトリガーされます。

- **疑わしいアクティビティ** - たとえば、ストレージ アカウントが Tor のアクティブな出口ノードとして知られている IP アドレスから正常にアクセスされました
- **異常な動作** - たとえば、ストレージ アカウントへのアクセス パターンの変化
- **マルウェアがアップロードされた可能性** - ハッシュ評価分析で、アップロードされたファイルにマルウェアが含まれていることが示されています

アラートには、それらをトリガーするインシデントの詳細と、脅威の調査や修復方法に関する推奨事項が含まれています。

### <a name="what-is-hash-reputation-analysis-for-malware"></a>マルウェアのハッシュ評価分析とは

アップロードされたファイルが疑わしいかどうかを判断するため、Azure Storage に対する脅威の防止では、[Microsoft 脅威インテリジェンス](https://go.microsoft.com/fwlink/?linkid=2128684)でサポートされているハッシュ評価分析が使用されます。 脅威の防止ツールでは、アップロードされたファイルがスキャンされるのではなく、ストレージ ログが調べられ、新しくアップロードされたファイルのハッシュと、既知のウイルス、トロイの木馬、スパイウェア、ランサムウェアのハッシュが比較されます。 

ファイルにマルウェアが含まれている疑いがある場合、Security Center ではアラートが表示されます。また、必要に応じて、疑わしいファイルの削除の承認を求めるメールをストレージの所有者に送信できます。 ハッシュ評価分析によってマルウェアが含まれることが示されているファイルの自動削除を設定するには、["マルウェアがストレージ アカウントにアップロードされた可能性" を含むアラートでトリガーするワークフローの自動化](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005)をデプロイします。



### <a name="next-steps"></a>次のステップ 

30 日間の無料試用など、価格の詳細については、[Azure Security Center の価格ページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。

詳細については、次を参照してください。

* [Advanced Threat Protection for Azure Storage を有効にする方法](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Azure Storage 向け脅威保護アラートの一覧](alerts-reference.md#alerts-azurestorage)
* [Microsoft の脅威インテリジェンスの機能](https://go.microsoft.com/fwlink/?linkid=2128684)

> [!TIP]
> [こちらのブログ記事](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)の手順に従って、ストレージのアラートをシミュレートできます。







## <a name="threat-protection-for-azure-cosmos-db"></a>Azure Cosmos DB の脅威の防止 <a name="cosmos-db"></a>

Azure Cosmos DB アラートは、通常とは異なる、害を及ぼす可能性がある、Azure Cosmos DB アカウントへのアクセスや悪用が試行された場合に生成されます。

詳細については、次を参照してください。

* [Azure Cosmos DB の Advanced Threat Protection (プレビュー)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Azure Cosmos DB 向け脅威保護アラートの一覧 (プレビュー)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Azure ネットワーク レイヤーの脅威の防止 <a name="network-layer"></a>

Security Center のネットワーク レイヤー分析は、サンプルの [IPFIX データ](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)に基づくものであり、Azure コア ルーターによって収集されるパケット ヘッダーです。 このデータ フィードに基づき、Security Center は機械学習モデルを使用して、悪意のあるトラフィック アクティビティを特定し、それらにフラグを設定します。 IP アドレスを強化するために、Security Center では、Microsoft の脅威インテリジェンス データベースも使用します。

ネットワーク構成によっては、Security Center が疑わしいネットワーク アクティビティに対してアラートを生成することを制限する場合があります。 Security Center でネットワーク アラートを生成するには、次のことを確認してください。

- 仮想マシンにパブリック IP アドレスがある (または、仮想マシンがパブリック IP アドレスを持つロード バランサー上にある)。

- 仮想マシンのネットワーク エグレス トラフィックが、外部 ID ソリューションによってブロックされていない。

- 不信な通信が発生した時間全体にわたって、仮想マシンに同じ IP アドレスが割り当てられている。 これは、管理サービスの一部として作成された VM (AKS、Databricks など) にも適用されます。

Azure ネットワーク レイヤー アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-azurenetlayer)に関するページを参照してください。




## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Azure 管理レイヤー (Azure Resource Manager) の脅威の防止 (プレビュー)<a name ="management-layer"></a>

Azure Resource Manager に基づく Security Center の保護レイヤーは、現在プレビュー段階です。

Security Center では、Azure のコントロール プレーンと見なされる Azure Resource Manager イベントを使用することで、追加の保護レイヤーが提供されます。 Security Center では、Azure Resource Manager のレコードを分析することで、Azure サブスクリプション環境での異常な、または害を及ぼす可能性のある操作を検出します。

Azure Resource Manager (プレビュー) アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-azureresourceman)に関するページを参照してください。



>[!NOTE]
> 上記のいくつかの分析では、Microsoft Cloud App Security が利用されています。 これらの分析を活用するには、Cloud App Security ライセンスをアクティブにする必要があります。 Cloud App Security ライセンスをお持ちの場合、これらのアラートは既定で有効になります。 アラートを無効にするには、次のようにします。
>
> 1. Security Center のメニューから、 **[価格と設定]** を選択します。
> 1. 変更するサブスクリプションを選択します。
> 1. **[脅威検出]** を選択します。
> 1. **[私のデータに Microsoft Cloud App Security がアクセスすることを許可します]** チェック ボックスをオフにして、 **[保存]** を選択します。

>[!NOTE]
>Security Center では、そのリソースと同じ地域でセキュリティ関連の顧客データが格納されます。 Microsoft によってまだリソースの地域に Security Center がデプロイされていない場合、米国でデータが格納されます。 Cloud App Security が有効になっている場合、この情報は、Cloud App Security の地域の場所のルールに従って格納されます。 詳細については、[非リージョン サービスのデータ ストレージ](https://azuredatacentermap.azurewebsites.net/)に関するページを参照してください。

1. エージェントをインストールするワークスペースを設定します。 ワークスペースが存在するサブスクリプションが Security Center で使用しているサブスクリプションと同じであること、またそのワークスペースに対する読み取り/書き込みのアクセス許可があることを確認します。

1. Standard 価格レベルを設定して、 **[保存]** を選択します。






## <a name="threat-protection-for-azure-key-vault-preview"></a>Azure Key Vault の脅威の防止 (プレビュー)<a name="azure-keyvault"></a>

> [!NOTE]
> 現在、Azure Government およびソブリン クラウド リージョンでは、このサービスを利用できません。

Azure Key Vault は、暗号化キーとシークレット (証明書、接続文字列、パスワードなど) を保護するクラウド サービスです。 

Azure Security Center には、Azure Key Vault 用に Azure ネイティブの高度な脅威保護機能が含まれており、セキュリティ インテリジェンスのレイヤーが追加されます。 Security Center では、異常であり、害を及ぼす可能性のある、Key Vault アカウントに対するアクセスまたは悪用の試みを検出できます。 この保護層により、セキュリティの専門家でなくても、サードパーティ製のセキュリティ監視システムを管理する必要なしに、脅威に対処することができます。  

異常なアクティビティが発生した場合、Security Center では、アラートが表示され、必要に応じてサブスクリプション管理者にメールが送信されます。 これらのアラートには、不審なアクティビティの詳細と、脅威の調査や修復方法に関する推奨事項が含まれます。 

Azure Key Vault アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-azurekv)に関するページを参照してください。





## <a name="threat-protection-for-other-microsoft-services"></a>他の Microsoft サービスの脅威の防止 <a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Azure WAF の脅威の防止 <a name="azure-waf"></a>

Azure Application Gateway は、一般的な脆弱性やその悪用から Web アプリケーションを一元的に保護する Web アプリケーション ファイアウォール (WAF) を提供します。

Web アプリケーションが、一般的な既知の脆弱性を悪用した悪意のある攻撃の標的になるケースが増えています。 Application Gateway の WAF は、Open Web Application Security Project のコア ルール セット 3.0 または 2.2.9 に基づいています。 WAF は、新たな脆弱性から保護するために自動的に更新されます。 

Azure WAF のライセンスをお持ちの場合は、WAF アラートが Security Center にストリーミングされます。その際に追加の構成は必要ありません。 WAF によって生成されるアラートの詳細については、[Web アプリケーション ファイアウォールの CRS 規則グループと規則](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)に関する記事をご覧ください。


### <a name="threat-protection-for-azure-ddos-protection"></a>Azure DDoS Protection の脅威の防止 <a name="azure-ddos"></a>

分散型サービス拒否 (DDoS) 攻撃は、簡単に実行できることが分かっています。 セキュリティの大きな不安材料になっており、アプリケーションをクラウドに移行する場合は特にそうです。 

DDoS 攻撃では、アプリケーションのリソースを使い果たし、正当なユーザーがアプリケーションを使用できなくなるようにすることが試みられます。 DDoS 攻撃では、インターネットを介して到達できるあらゆるエンドポイントが対象になる可能性があります。

DDoS 攻撃を防ぐには、Azure DDoS Protection のライセンスを購入し、アプリケーションの設計に関するベスト プラクティスに従っていることを確認します。 DDoS Protection では、各種のサービス レベルが提供されます。 詳細については、[Azure DDoS Protection の概要](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)に関する記事を参照してください。

Azure DDoS Protection アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-azureddos)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ
これらの脅威の防止機能からのセキュリティ アラートの詳細については、次の記事を参照してください。

* [Azure Security Center のすべてのアラートの参照テーブル](alerts-reference.md)
* [Security alerts in Azure Security Center](security-center-alerts-overview.md)
* [Azure Security Center でのセキュリティ アラートの管理と対応](security-center-managing-and-responding-alerts.md)
* [セキュリティ アラートと推奨事項のエクスポート (プレビュー)](continuous-export.md)