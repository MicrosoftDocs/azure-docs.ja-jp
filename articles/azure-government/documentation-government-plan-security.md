---
title: "Azure Government のセキュリティ | Microsoft Docs"
description: "Azure Government で利用可能なサービスの概要を紹介します"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: c3645bda-bf35-4232-a54d-7a0bfab2d594
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: a9cc2bc044caddec63645932067f7a346945db78
ms.openlocfilehash: 11d0a67a4c368b50e2f2e8648c4fb2db8e0421c2


---
# <a name="security"></a>セキュリティ
## <a name="principles-for-securing-customer-data-in-azure-government"></a>Azure Government でお客様のデータを保護するための原則
Azure Government には、規制対象データに関する固有の要件を満たすクラウド ソリューションを構築するための一連の機能とサービスが用意されています。 データのセキュリティに関する慣例をしっかりと守れば、特別な設定をしなくても Azure Government の機能を効果的に実装するだけで、規制に準拠した独自のソリューションを構築することができます。

Azure Government でソリューションをホストした場合、こうした要件の多くは、クラウド インフラストラクチャ レベルで Microsoft が対応します。

次の図は、Azure の多層防御モデルを示しています。 たとえば、お客様側の機能 (アプリケーション DDOS 攻撃に対するお客様固有の要件に対応するセキュリティ アプライアンスなど) と併せて、Microsoft では、クラウド インフラストラクチャによる基本的な DDOS 防御機構を提供します。

![alt text](./media/azure-government-Defenseindepth.png)

このページでは、ご利用のサービスとアプリケーションを保護するうえでの根本原則について簡単に説明し、そうした原則を適用する際の指針やベスト プラクティス、つまり Azure Government を賢く利用しながら、ITAR 情報を扱うソリューションに課せられる義務や責任を満たす方法を紹介しています。

 お客様のデータを保護するうえでの最大の原則は次のとおりです。

* 暗号化によってデータを保護する
* シークレットを管理する
* 分離によってデータ アクセスを制限する

### <a name="protecting-customer-data-using-encryption"></a>暗号化を使用してお客様のデータを保護する
リスクの緩和と規制義務の遵守が求められる中で、データの暗号化はしだいに注目を集め、その重要性はますます大きくなってきています。 効果的な暗号化システムを使用することで、現在のネットワークやアプリケーションのセキュリティ対策を強化し、クラウド環境の全体的なリスクを軽減することができます。

#### <a name="encryption-at-rest"></a>保存時の暗号化
ディスク ストレージに格納されているカスタマー コンテンツの保護には、保存データの暗号化が適用されます。 その実現にはいくつかの方法があります。

#### <a name="storage-service-encryption"></a>Storage Service Encryption
Azure Storage Service Encryption はストレージ アカウント レベルで有効になります。したがってブロック BLOB とページ BLOB は、Azure Storage への書き込み時に自動的に暗号化されます。 Azure Storage からデータを読み取ると、Storage サービスによって復号化されてから、返されます。 そのため、データをセキュリティで保護するためにコードを変更したり、アプリケーションにコードを追加したりする必要はありません。

#### <a name="client-side-encryption"></a>クライアント側の暗号化
クライアント側の暗号化は Java と .NET ストレージ クライアント ライブラリに組み込まれているので、Azure Key Vault API を使用して簡単に実装できます。 Azure Key Vault では、Azure Active Directory を使用して、Azure Key Vault 内のシークレットへのアクセス権を特定のユーザー用に取得します。

#### <a name="encryption-in-transit"></a>転送中の暗号化
Azure Government への接続用に用意されている基本的な暗号化は、Transport Level Security (TLS) 1.2 プロトコルと X.509 証明書をサポートします。 Azure Government のデータセンター間におけるインフラストラクチャ ネットワーク接続には、Federal Information Processing Standard (FIPS) 140-2 Level 1 暗号アルゴリズムも使用されます。  Windows Server 2012 R2 と Windows 8 + VM、および Azure ファイル共有では、VM とファイル共有間の暗号化に SMB 3.0 を使用できます。 クライアント アプリケーションのストレージに転送する前のデータを暗号化したり、ストレージから転送された後のデータを復号化したりするには、クライアント側の暗号化を使用します。

#### <a name="best-practices-for-encryption"></a>暗号化のベスト プラクティス
* IaaS VM: Azure Disk Encryption を使用します。 ディスクを Azure Storage にバックアップする際に使用する VHD ファイルは、Storage Service Encryption を有効にすることで暗号化します。ただし、その場合、暗号化されるのは、新しく書き込まれたデータだけです。 つまり、VM を作成してから、VHD ファイルを保持するストレージ アカウントで Storage Service Encryption を有効にすると、元の VHD ファイルではなく、変更のみが暗号化されます。
* クライアント側の暗号化: データは転送前に暗号化され、保存データも暗号化されるので、データを暗号化するうえで最も安全な方法といえます。 ただし、ストレージを使用してアプリケーションにコードを追加する必要があります。 ストレージを追加したくない場合は、転送中のデータに HTTPS を使用し、Storage Service Encryption を使用して保存データを暗号化する方法があります。 クライアント側の暗号化は、クライアントにかかる負荷が増加するため、スケーラビリティの計画でこの点を考慮する必要があります。特に、大量のデータを暗号化したり転送したりする場合に重要です。

### <a name="protecting-customer-data-by-managing-secrets"></a>シークレットを管理することでお客様のデータを保護する
クラウドにあるデータを保護するうえで、安全なキー管理は不可欠です。 キーの管理を省力化すると共に、クラウド アプリケーションやクラウド サービスでデータを暗号化する際に使用されるキーの管理に絶えず気を配るよう努めてください。

#### <a name="best-practices-for-managing-secrets"></a>シークレットを管理するうえでのベスト プラクティス
* ハードコーディングされた構成ファイルやスクリプト、ソース コード内からシークレットが漏えいするリスクを Key Vault を使用して最小限に抑えます。 Azure Key Vault は、FIPS 140-2 Level 2 認定のハードウェア セキュリティ モジュール (HSM) にキー (Azure Disk Encryption 用の暗号化キーなど) やシークレット (パスワードなど) を格納することで、それらを暗号化します。 さらに安心感を高めたい場合には、それらの HSM でキーのインポートや生成を行うことができます。
* アプリケーション コードやテンプレートに記述する必要があるのは、シークレットへの URI 参照のみです。つまり、実際のシークレットは、コード リポジトリ、構成リポジトリ、ソース コード リポジトリには存在しません。 これにより、GitHub での収集ボットのような、内外のリポジトリへのキー フィッシング攻撃を防ぐことができます。
* Key Vault 内で強力な RBAC 制御を利用します。 信任されていたオペレーターが退職したり、社内の別の部署に異動になったりした場合は、そのオペレーターのシークレットへのアクセスを禁止する必要があります。

詳細については、[Azure Key Vault のパブリック ドキュメント](../key-vault/index.md)をご覧ください。

### <a name="isolation-to-restrict-data-access"></a>分離によってデータ アクセスを制限する
分離の本質は、境界、区分、コンテナーを使用して、権限のあるユーザーやサービス、アプリケーションにデータへのアクセスを限定することです。 たとえば Microsoft Azure のようなマルチテナント型のクラウド プラットフォームでは、テナントどうしを分離することが、セキュリティ上の機構として不可欠になります。 論理上の分離によって、テナントが互いの運用に支障を及ぼすことを防ぐことができます。

#### <a name="environment-isolation"></a>環境の分離
Azure Government 環境は、Microsoft の他のネットワークから切り離された物理インスタンスです。 これを実現する一連の物理的制御と論理的制御には、たとえば次の手段が使用されています。

* 物理的な境界は生体認証デバイスやカメラを使用して保護されています。
* 運用環境への論理アクセスを必要とする Microsoft の社員は、特定の資格情報と多要素認証を使用しています。
* Azure Government のサービス インフラストラクチャはすべて米国内に置かれています。

#### <a name="per-customer-isolation"></a>顧客ごとの分離
Azure は、VLAN 分離、ACL、ロード バランサー、および IP フィルターによるネットワーク アクセス制御と分離を実装しています。

サブスクリプションやリソース グループ、仮想ネットワーク、サブネットによって、お客様はそのリソースの分離度をさらに高めることができます。

## <a name="screening"></a>スクリーニング
FedRAMP High と国防総省 (DoD) Impact Level 4 の認定が最近発表されました。 これにより、Azure Government 環境でのセキュリティとコンプライアンスの基準が引き上げられています。

Microsoft では現在、すべてのオペレーターに対して、DoD のクラウド コンピューティング セキュリティ要件ガイド (SRG) のセクション 5.6.2.2 の規定に従い、National Agency Check with Law and Credit (NACLC) によるスクリーニングを行っています。

> [!NOTE]
> "重大ではない機密性" (たとえば DoD の ADP-2) に基づく Level 4 および 5 の情報にアクセスできる CSP 担当者に必要な最小限のバックグラウンド調査は、National Agency Check with Law and Credit (NACLC) ("重大ではない機密性" の契約者向け)、または Moderate Risk Background Investigation (MBI) ("中程度のリスク" の指定職位向け) です。
> 
> 

次の表は、Azure Government のオペレーターに対して現在実施しているスクリーニングをまとめたものです。

| Azure Government スクリーニングおよびバックグラウンド調査 | 説明 |
| --- | --- |
| 米国市民権 |米国市民権の検証。 |
| Microsoft によるクラウド バックグラウンド調査 (2 年ごと) |社会保障番号の検索、犯罪歴の確認、外国資産管理局 (OFAC) のリスト、産業安全保障局 (BIS) のリスト、防衛通商管理局の除外者リスト。 |
| National Agency Check with Law and Credit (NACLC) (5 年ごと) |FBI データベースとの指紋の照合によるバック グラウンド調査を追加。 詳細については、<a href="https://www.opm.gov/investigations/background-investigations/federal-investigations-notices/1997/fin97-02/">Office Personnel Management のサイト</a>を参照してください。 |
| <a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/CJIS">刑事裁判情報サービス (CJIS)</a> |CJIS は、州政府、地方自治体、FBI によるスクリーニング サービスであり、重要な刑事裁判情報 (CJI) データにアクセスできる可能性のある運用スタッフについて、指紋記録の処理と犯罪歴の検証を行います。  各州では独自のバックグラウンド調査が実施された後、CJI にアクセスできる可能性のあるすべての職員が承認を受けます。 |

Azure の運用担当者には、以下のアクセス原則が適用されます。

* 職務は明確に定義され、変更の要求、承認、展開についての責任が職務別に付与されます。
* アクセスは、特定の機能を持つ定義済みのインターフェイスを通じて行われます。
* アクセスは Just-In-Time (JIT) で行われ、アクセス権はインシデント単位で、または特定のメンテナンス イベント用に、常に一定期間だけ付与されます。
* アクセスはルールベースであり、定義済みの役割にトラブルシューティングに必要なアクセス許可のみが割り当てられます。

スクリーニング基準には、Microsoft のすべてのサポート スタッフおよび運用スタッフの米国市民権の検証が含まれ、Azure Government でホストされるシステムにアクセス権が付与される前に行われます。 データの転送操作を必要とするサポート担当者は、Azure Government 内のセキュリティで保護された機能を使用します。 セキュリティで保護されたデータ転送には、アクセス権を取得するために個別の認証資格情報セットが必要です。 たとえば、システム メタデータにアクセスする場合、運用担当者は特定の Web ベースの内部管理ツール、読み取り専用 API、JIT 昇格を使用してアクセスを行います。

## <a name="next-steps"></a>次のステップ
補足情報と最新情報については、<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government ブログ</a>を随時ご覧ください。




<!--HONumber=Nov16_HO3-->


