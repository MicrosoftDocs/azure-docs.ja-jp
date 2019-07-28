---
title: Azure Health Analytics のブループリント
description: HIPAA/HITRUST Health Analytics のブループリントのデプロイに関するガイダンス
services: security
documentationcenter: na
author: rajeevrangappa
ms.assetid: 26566e0a-0a54-49f4-a91d-48e20b7cef71
ms.service: security
ms.topic: article
ms.date: 07/23/2018
ms.author: rarangap
ms.openlocfilehash: 70721b8bfbecaf554a9502b9ec3417fc8e561b3f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60609730"
---
# <a name="azure-security-and-compliance-blueprint---hipaahitrust-health-data-and-ai"></a>Azure のセキュリティとコンプライアンスのブループリント - HIPAA/HITRUST のヘルス データと AI

## <a name="overview"></a>概要

**Azure のセキュリティとコンプライアンスのブループリント - HIPAA/HITRUST のヘルス データと AI は、業界のコンプライアンス要件を満たしながら、ヘルス データの取り込み、保存、分析、操作、識別、およびソリューションのデプロイを安全に行う方法を示す Azure PaaS および IaaS ソリューションのターンキー デプロイを提供します。このブループリントは、規制されたデータを使用するお客様のために、クラウドの導入と利用の促進を支援します。**

エンド ツー エンド ソリューションとしてデプロイされる、Azure のセキュリティとコンプライアンスのブループリント - HIPAA/HITRUST のヘルス データと AI ブループリントは、セキュリティで保護された多層クラウド環境で、個人および個人以外の医療記録の取り込み、保存、分析、操作を行うための、セキュリティで保護された、HIPAA (医療保険の携行性と責任に関する法律) および HITRUST (Health Information Trust Alliance) 対応 PaaS (サービスとしてのプラットフォーム) 環境の展開に役立つツールとガイダンスを提供します。 

IaaS ソリューションは、オンプレミスの SQL ベースのソリューションを Azure に移行する方法と、クラウドベースのサービスとソリューションを安全に管理するために Privileged Access Workstation (PAW) を実装する方法を示します。 IaaS SQL Server データベースは、SQL IaaS VM にインポートされる潜在的な実験データを追加し、その VM が MSI 認証アクセスを使用して SQL Azure PaaS サービスと対話します。これらはどちらも一般的な参照アーキテクチャを示すもので、Microsoft Azure の導入を簡略化するよう設計されています。 提供されるアーキテクチャは、デプロイの負担を軽減し、コストを削減するクラウドベースのアプローチを求めている組織のニーズを満たすソリューションを示しています。

![](images/components.png)

このソリューションは、医療情報を電子的に交換するための世界標準である Fast Healthcare Interoperability Resources (FHIR) を使用してフォーマットされたサンプル データ セットを使用し、これを安全に保存するように設計されています。 お客様は、Azure Machine Learning Studio を使用し、強力なビジネスおよびインテリジェンス ツールと分析機能を活用して、サンプル データで行われた予測を確認できます。 Azure Machine Learning Studio が推進できる実験の種類の例として、ブループリントには、病院施設での患者の入院期間を予測するためのサンプル データ セット、スクリプト、ツールが含まれています。 

このブループリントは、臨床および運用ユース ケース シナリオを解決するために Azure Machine Learning の新しい実験を開発することで、お客様が特定の要件に合わせて調整するモジュールの基盤として機能することを目的としています。 デプロイ時にセキュリティとコンプライアンスを維持するように設計されていますが、ロールの適切な構成と変更の実装はお客様が行う必要があります。 以下の点に注意してください。

-   このブループリントでは、お客様が HITRUST/HIPAA 環境で Microsoft Azure を使用する際に役立つベースラインを提供します。

-   ブループリントは (Common Security Framework (CSF) を使用して) HIPAA および HITRUST に合わせて設計されていますが、HIPAA と HITRUST の認定要件に従って外部の監査担当者が認定するまで準拠と見なさないでください。

-   この基本アーキテクチャを使用して構築されたソリューションのセキュリティとコンプライアンスの適切なレビューは、お客様が実施する必要があります。

## <a name="deploying-the-automation"></a>Automation のデプロイ

- ソリューションをデプロイするには、[デプロイ ガイダンス](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/deployment.md)に記載されている手順に従います。 

- このソリューションの機能の概要を簡単に把握するために、ソリューションのデプロイの説明とデモンストレーションを行っている[このビデオ](https://aka.ms/healthblueprintvideo)をご覧ください。

- よくある質問については、[FAQ](https://aka.ms/healthblueprintfaq) ガイダンスをご覧ください。

-   **アーキテクチャ図**: この図は、ブループリントとユース ケース シナリオの例で使用される参照アーキテクチャを示します。

-   [IaaS 拡張機能](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/README%20IaaS.md) - このソリューションは、オンプレミスの SQL ベースのソリューションを Azure に移行する方法と、クラウドベースのサービスとソリューションを安全に管理するために Privileged Access Workstation を実装する方法を示します。 

## <a name="solution-components"></a>ソリューションのコンポーネント


この基本アーキテクチャは次のコンポーネントで構成されています。

-   **[脅威モデル](https://aka.ms/healththreatmodel)** : [Microsoft Threat Modeling Tool](https://www.microsoft.com/en-us/download/details.aspx?id=49168) で使用するために、包括的な脅威モデルが tm7 形式で提供され、ソリューションのコンポーネント、コンポーネント間のデータ フロー、信頼境界が示されます。 このモデルは、Machine Learning Studio コンポーネントや他の変更点を開発するときに、システム インフラストラクチャの潜在的なリスク箇所を理解するのに役立ちます。

-   **[お客様の実装マトリックス](https://aka.ms/healthcrmblueprint)** : Microsoft Excel ブックに、関連する HITRUST の要件が示され、各要件を満たす際に Microsoft とお客様が負う責任が記載されます。

-   **[ヘルス レビュー。](https://aka.ms/healthreviewpaper)** このソリューションは、Coalfire systems, Inc. によってレビューされました。「Health Compliance (HIPAA, and HITRUST) Review and guidance for implementation」(ヘルプ コンプライアンス (HIPAA/HITRUST) レビューおよび実装のためのガイダンス) には、監査担当者によるソリューションのレビューと、このブループリントを実稼働可能なデプロイに変える際の考慮事項が記載されています。

## <a name="architectural-diagram"></a>アーキテクチャ図


![](images/ra2.png)

## <a name="roles"></a>ロール


このブループリントでは、管理ユーザー (オペレーター) の 2 つのロールと、病院管理および患者ケアに従事するユーザーの 3 つのロールが定義されています。 6 番目の役割は、HIPAA や他の規制の遵守を評価する監査担当者向けに定義されています。 Azure のロールベースのアクセス制御 (RBAC) は、組み込みロールとカスタム ロールによって、ソリューションの各ユーザーの厳密に対象を絞ったアクセス管理を実現します。 RBAC、ロール、アクセス許可の詳細については、「[Azure Portal でのロールベースのアクセス制御の基礎を確認する](https://docs.microsoft.com/azure/role-based-access-control/overview)」および「[Azure ロールベースのアクセス制御の組み込みロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)」をご覧ください。

### <a name="site-administrator"></a>サイト管理者


サイト管理者は、顧客の Azure サブスクリプションに対して責任を負います。 全体的なデプロイを管理しますが、患者記録にアクセスすることはできません。

-   既定のロールの割り当て:[Owner](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)

-   カスタム ロールの割り当て:該当なし

-   スコープ:サブスクリプション

### <a name="database-analyst"></a>データベース アナリスト

データベース アナリストは、SQL Server インスタンスおよびデータベースを管理します。
患者記録にアクセスすることはできません。

-   組み込みロールの割り当て:[SQL DB 共同作業者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-db-contributor)、[SQL Server 共同作業者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-server-contributor)

-   カスタム ロールの割り当て:該当なし

-   スコープ:ResourceGroup

### <a name="data-scientist"></a>データ サイエンティスト


データ サイエンティストは、Azure Machine Learning Studio サービスを運用します。 データのインポート、エクスポート、管理を行い、レポートを実行できます。 データ サイエンティストは患者データにアクセスできますが、管理特権はありません。

-   組み込みロールの割り当て:[Storage Account Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor)

-   カスタム ロールの割り当て:該当なし

-   スコープ:ResourceGroup

### <a name="chief-medical-information-officer-cmio"></a>最高医療情報責任者 (CMIO)


CMIO は、医療機関において情報科学/技術の専門家と医療従事者の境界線上に位置します。 通常、CMIO の任務には、分析を使用して、医療機関内でリソースが適切に割り当てられているかどうかを判断することが含まれます。

-   組み込みロールの割り当て:なし

### <a name="care-line-manager"></a>ケア ライン マネージャー


ケア ライン マネージャーは、患者のケアに直接関与します。
このロールは、個々の患者の状態を監視し、患者の特定のケア要件に対応するためのスタッフを確保する必要があります。 ケア ライン マネージャーは、患者記録を追加および更新する役割を担います。

-   組み込みロールの割り当て:なし

-   カスタム ロールの割り当て:HealthcareDemo.ps1 を実行して、患者の入院と退院の両方を処理する特権があります。

-   スコープ:ResourceGroup

### <a name="auditor"></a>監査担当者


監査担当者は、コンプライアンスについてソリューションを評価します。 ネットワークに直接アクセスすることはできません。

-   組み込みロールの割り当て:[Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)

-   カスタム ロールの割り当て:該当なし

-   スコープ:サブスクリプション

## <a name="example-use-case"></a>ユース ケースの例


このブループリントに含まれているユース ケースの例は、ブループリントを使用してクラウド内のヘルス データで機械学習と分析を可能にする方法を示しています。 Contosoclinic は米国にある小さな病院です。 病院ネットワーク管理者は、運用ワークロードの効率を高め、提供できるケアの質を向上させるために、Azure Machine Learning Studio を使用して患者の入院時に入院期間をより正確に予測したいと考えています。

### <a name="predicting-length-of-stay"></a>入院期間の予測


ユース ケース シナリオの例では、Azure Machine Learning Studio を使用して、問診時に入手した医療情報を以前の患者の集計済み履歴データと比較することによって、新しく入院した患者の入院期間を予測します。
ブループリントには、ソリューションのトレーニングおよび予測機能を示すために、大量の匿名化された医療記録が含まれています。 運用環境では、環境、施設、患者の詳細を反映したより正確な予測を行うために、お客様独自の記録を使用してソリューションをトレーニングします。

### <a name="users-and-roles"></a>ユーザーと役割


**サイト管理者 -- Alex**

*電子メール:Alex\_SiteAdmin*

Alex の仕事は、オンプレミス ネットワークの管理の負担を軽減し、管理コストを削減できるテクノロジを評価することです。 Alex はしばらくの間 Azure を評価していましたが、患者データをクラウドに保存する際の HiTrust のコンプライアンス要件を満たすために必要なサービスを構成しようと苦戦していました。 Alex は、Azure Health AI を選択してコンプライアンス対応ヘルス ソリューションをデプロイすることで、HiTrust の顧客要件を満たすための要件に対処しました。

**データ サイエンティスト -- Debra**

*電子メール:Debra\_DataScientist*

Debra は、医療記録を分析して患者ケアに関する洞察を示すモデルの使用と作成を担当しています。 Debra は、SQL と R 統計プログラミング言語を使用してモデルを作成しています。

**データベース アナリスト -- Danny**

*電子メール:Danny\_DBAnalyst*

Danny は、Contosoclinic のすべての患者データを格納する Microsoft SQL Server に関することに対応する主要連絡担当者です。 Danny は経験豊富な SQL Server 管理者であり、最近、Azure SQL Database について理解を深めました。

**最高医療情報責任者 -- Caroline**

Caroline は、ケア ライン マネージャーの Chris およびデータ サイエンティストの Debra と協力して、患者の入院期間に影響を与える要素の特定に取り組んでいます。
Caroline は、入院期間 (LOS) ソリューションによる予測を使用して、病院ネットワーク内でリソースが適切に割り当てられているかどうかを判断しています。 たとえば、このソリューションで提供されるダッシュボードを使用します。

**ケア ライン マネージャー -- Chris**

*電子メール:Chris\_CareLineManager*

Contosoclinic で患者の入院と退院の管理を直接担当する Chris は、LOS ソリューションによって生成される予測を使用して、入院中に患者にケアを提供する適切なスタッフを確保しています。

**監査担当者 -- Han**

*電子メール:Han\_Auditor*

Han は、ISO、SOC、HiTrust の監査経験がある認定監査担当者です。 Han は、Contosoclinc のネットワークを調査するために採用されました。 Han は、ブループリントと LOS ソリューションを使用して機密性の高い個人データを保存、処理、表示できるように、ソリューションで提供されるお客様の責任マトリックスを確認できます。


## <a name="design-configuration"></a>設計の構成


このセクションでは、ブループリントに組み込まれている既定の構成とセキュリティ対策について詳しく説明します。

- FHIR データ ソースを含めた生データ ソースの**取り込み**
- 機密情報の**保存**
- 結果の**分析**と予測
- 結果と予測の**操作**
- ソリューションの **ID** 管理
- **セキュリティ**が有効な機能


## <a name="identity"></a>ID 

### <a name="azure-active-directory-and-role-based-access-control-rbac"></a>Azure Active Directory とロールベースのアクセス制御 (RBAC)


**認証:**

-   [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/)\' は、Microsoft が提供する、マルチテナントに対応したクラウドベースのディレクトリおよび ID 管理サービスです。 ソリューションのすべてのユーザー (SQL Database にアクセスするユーザーを含む) は、Azure Active Directory で作成されています。



-   アプリケーションに対する認証は Azure AD を使用して行われます。 詳細については、「[Azure Active Directory とアプリケーションの統合](/azure/active-directory/develop/active-directory-integrating-applications)」をご覧ください。

-   [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) は、組織の ID に影響を及ぼす可能性のある脆弱性を検出し、検出された、組織の ID に関連する疑わしいアクションに対する自動応答を構成します。さらに、疑わしいインシデントを調査し、適切なアクションを実行してそれらを解決します。

-   [Azure のロールベースのアクセス制御 (RBAC)](/azure/role-based-access-control/role-assignments-portal) は、Azure の厳密に対象を絞ったアクセス管理を実現します。 サブスクリプションへのアクセスはサブスクリプション管理者に制限され、Azure Key Vault へのアクセスはサイト管理者に制限されます。 強力なパスワード (12 文字以上で、大文字/小文字、数字、および特殊文字を少なくとも 1 つ含む) が必要です。

-   デプロイ時に -enableMFA スイッチが有効になっている場合、多要素認証がサポートされます。

-   デプロイ時に -enableADDomainPasswordPolicy スイッチが有効になっている場合、60 日後にパスワードの有効期限が切れます。

**ロール:**

-   ソリューションでは、[組み込みロール](/azure/role-based-access-control/built-in-roles)を使用してリソースへのアクセスを管理します。

-   すべてのユーザーに、特定の組み込みロールが既定で割り当てられます。

### <a name="azure-key-vault"></a>Azure Key Vault

-   Key Vault に格納されるデータは次のとおりです。

    -   Application Insight キー
    -   患者データのストレージ アクセス キー
    -   患者の接続文字列
    -   患者データ テーブル名
    -   Azure ML Web サービス エンドポイント
    -   Azure ML サービス API キー

-   必要に応じて、高度なアクセス ポリシーが構成されます。
-   Key Vault のアクセス ポリシーは、キーとシークレットに対する最低限必要なアクセス許可で定義されています。
-   Key Vault のすべてのキーとシークレットに有効期限があります。
-   Key Vault のすべてのキーは、HSM \[キーの種類 = HSM で保護された 2048 ビットのRSA キー\] で保護されています。
-   ロールベースのアクセス制御 (RBAC) を使用して、すべてのユーザー/ID に最低限必要なアクセス許可が付与されます。
-   アプリケーションは、相互に信頼し、実行時に同じシークレットへのアクセスを必要とする場合を除き、Key Vault を共有しません。
-   Key Vault の診断ログは、少なくとも 365 日のリテンション期間で有効になっています。
-   キーの許可される暗号化操作は必要なものに制限されます。

## <a name="ingest"></a>取り込み 

### <a name="azure-functions"></a>Azure Functions
ソリューションは、[Azure Functions](/azure/azure-functions/) を使用して、分析デモで使用されるサンプルの入院期間データを処理するように設計されています。 関数の 3 つの機能が作成されました。

**1.顧客データ (phi データ) の一括インポート**

デモ スクリプト .\\HealthcareDemo.ps1 を、**BulkPatientAdmission** スイッチを指定して使用すると (**デモのデプロイと実行**に関するページで概説)、次の処理パイプラインが実行されます。
1. **Azure Blob Storage** - 患者データのサンプル .csv ファイルがストレージにアップロードされます。
2. **Event Grid** - イベントにより、Azure Functions にデータが発行されます (一括インポート - BLOB イベント)。
3. **Azure Functions** - 処理を実行し、セキュリティで保護された event(type; blob url) 関数を使用してデータを SQL ストレージに格納します。
4. **SQL DB** - 分類にタグを使用する、患者データのデータベース ストア。トレーニング実験を実行するために、ML プロセスが開始されます。

![](images/dataflow.png)

さらに、Azure 関数は、次のタグを使用してサンプル データ セットの指定された機密データを読み取り、保護するように設計されています。
- dataProfile => “ePHI”
- owner => \<Site Admin UPN\>
- environment => “Pilot”
- department => “Global Ecosystem" タグ付けは、患者の "名前" がクリア テキストと見なされたサンプル データ セットに適用されました。

**2.新しい患者の入院**

デモ スクリプト .\\HealthcareDemo.ps1 を、**BulkPatientadmission** スイッチを指定して使用すると (**デモのデプロイと実行**に関するページで概説)、次の処理パイプラインが実行されます。![](images/securetransact.png)
**1.Azure Functions** がトリガーされ、関数が Azure Active Directory の[ベアラー トークン](/rest/api/)を要求します。

**2.Key Vault** が、要求したトークンに関連付けられているシークレットを要求します。

**3.Azure ロール**が要求を検証し、Key Vault へのアクセス要求を承認します。

**4.Key Vault** がシークレットを返します。この例では、SQL DB 接続文字列です。

**5.Azure Functions** は、この接続文字列を使用して SQL Database に安全に接続し、ePHI データを格納するための処理を続行します。

データの保存を実現するために、Fast Healthcare Interoperability Resources (FHIR、"ファイア" と発音) に従って共通の API スキーマが実装されました。 関数には、次の FHIR 交換要素が提供されました。

-   [Patient スキーマ](https://www.hl7.org/fhir/patient.html): 患者に関する詳細情報を扱います。

-   [Observation スキーマ](https://www.hl7.org/fhir/observation.html): 診断の支援、進行状況の監視、ベースラインとパターンの特定、人口統計学的特性の把握に使用される、医療の中心的な要素を扱います。 

-   [Encounter スキーマ](https://www.hl7.org/fhir/encounter.html): 外来、救急、家庭医療、入院、仮想診察などの外来診察の種類を扱います。

-   [Condition スキーマ](https://www.hl7.org/fhir/condition.html): 健康状態、問題、診断、または懸念があるレベルに達した他の事象、状況、課題、または臨床的概念に関する詳細情報を扱います。  



### <a name="event-grid"></a>Event Grid


ソリューションでは、1 つのサービスで任意の送信元から送信先へのすべてのイベントのルーティングを管理できる Azure Event Grid をサポートしています。Event Grid は次の機能を提供します。

-   [セキュリティと認証](/azure/event-grid/security-authentication)

-   イベント サブスクリプションの一覧表示、新しいサブスクリプションの作成、キーの生成などのさまざまな管理操作のための[ロールベースのアクセス制御](/azure/event-grid/security-authentication#management-access-control)

-   監査

## <a name="store"></a>保存 

### <a name="sql-database-and-server"></a>SQL Database と SQL Server 


-   [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) は、Azure Key Vault に格納されたキーを使用して、Azure SQL Database に格納されているデータをリアルタイムで暗号化および解読します。

-   [SQL の脆弱性評価](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)では、データベースの潜在的な脆弱性を検出、追跡、修復できるツールを簡単に構成できます。

-   [SQL Database の脅威検出](/azure/sql-database/sql-database-threat-detection)が有効になっています。

-   [SQL Database の監査](/azure/sql-database/sql-database-auditing)が有効になっています。

-   [SQL Database のメトリックと診断ログ](/azure/sql-database/sql-database-metrics-diag-logging)が有効になっています。

-   [サーバー レベルおよびデータベース レベルのファイアウォール規則](/azure/sql-database/sql-database-firewall-configure)が強化されました。

-   [Always Encrypted 列](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)を使用して、患者の名、ミドル ネーム、姓が保護されます。
    さらに、データベース列の暗号化でも、Azure SQL Database に対してアプリケーションを認証するために Azure Active Directory (AD) が使用されます。

-   SQL Database と SQL Server へのアクセスは、最小限の特権の原則に従って構成されています。

-   必要な IP アドレスにのみ、SQL ファイアウォールを介したアクセスが許可されます。

### <a name="storage-accounts"></a>ストレージ アカウント


-   [移動中のデータは、必ず TLS/SSL を使用して転送されます](/azure/storage/common/storage-require-secure-transfer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json)。

-   コンテナーでは、匿名アクセスは許可されていません。

-   匿名アクティビティを追跡するために、アラート ルールが構成されています。

-   ストレージ アカウント リソースへのアクセスには HTTPS が必要です。

-   認証要求データがログに記録され、監視されます。

-   Blob Storage のデータは保存時に暗号化されます。

## <a name="analyze"></a>分析

### <a name="machine-learning"></a>Machine Learning


- Machine Learning Studio Web サービスでは、[ログが有効](/azure/machine-learning/studio/web-services-logging)になっています。
- [Machine Learning Studio](/azure/machine-learning/studio/what-is-ml-studio) を使用するには、ソリューション セットに予測機能を提供するために、実験を開発する必要があります。

## <a name="security"></a>セキュリティ

### <a name="azure-security-center"></a>Azure Security Center
- [Azure Security Center](https://azure.microsoft.com/services/security-center/) は、すべての Azure リソースのセキュリティ状態を一元的に提供します。 セキュリティの制御が適切かつ正しく構成されているかを一目で確認し、注意が必要なリソースを素早く特定できます。 

- [Azure Advisor](/azure/advisor/advisor-overview) は、ベスト プラクティスに従って Azure デプロイメントを最適化できるようにする、個人用に設定されたクラウド コンサルタントです。 Azure のリソースの構成と利用統計情報を分析し、Azure リソースの費用対効果、パフォーマンス、高可用性、およびセキュリティを向上させるために役立つソリューションを推奨します。

### <a name="application-insights"></a>Application Insights
- [Application Insights](/azure/application-insights/app-insights-overview) は、複数のプラットフォームで使用できる Web 開発者向けの拡張可能なアプリケーション パフォーマンス管理 (APM) サービスです。 このサービスを使用して、実行中の Web アプリケーションを監視することができます。 パフォーマンスの異常が検出されます。 組み込まれている強力な分析ツールを使えば、問題を診断し、ユーザーがアプリを使用して実行している操作を把握できます。 Application Insights は、パフォーマンスやユーザビリティを継続的に向上させるうえで役立つように設計されています。

### <a name="azure-alerts"></a>Azure アラート
- [アラート](/azure/azure-monitor/platform/alerts-metric)は Azure サービスの監視方法の 1 つであり、データに対する条件を構成できます。 また、アラートの条件が監視データと一致したときに、通知を受け取ることができます。

### <a name="azure-monitor-logs"></a>Azure Monitor ログ
[Azure Monitor ログ](/azure/operations-management-suite/operations-management-suite-overview)は、管理サービスのコレクションです。

-   Security Center でワークスペースが有効になっています。

-   ワークロード監視でワークスペースが有効になっています。

-   以下に対してワークロード監視が有効になっています。

    -   ID とアクセス

    -   [Security and Audit]

    -   Azure SQL DB Analytics

    -   [Azure WebApp Analytics](/azure/log-analytics/log-analytics-azure-web-apps-analytics) ソリューション

    -   Key Vault Analytics

    -   変更の追跡

-   [Application Insights Connector (プレビュー)](/azure/log-analytics/log-analytics-app-insights-connector) が有効になっています。

-   [アクティビティ ログ分析](/azure/log-analytics/log-analytics-activity)が有効になっています。
