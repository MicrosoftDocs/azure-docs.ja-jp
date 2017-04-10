---
title: "Azure Government ドキュメントの概要 | Microsoft Docs"
description: "Azure Government アプリケーションの機能の比較と開発におけるガイダンスを示します。"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: 56d84e26-947e-4f3b-8e33-18247f1c7944
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/10/2017
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 8e5dd57f4ff55e350aebce339f9f723cc6ca917b
ms.openlocfilehash: 2a1e9c6e001280451ae01330ee659d1e27c2d89a


---
# <a name="azure-government-documentation-overview"></a>Azure Government ドキュメントの概要
## <a name="introduction-to-azure-government-documentation"></a>Azure Government ドキュメントの紹介
このサイトでは、 [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) サービスの機能について説明すると共に、すべてのお客様に該当する一般的な指針を提供しています。 明示的に規制されたデータを Azure Government サブスクリプションで扱う場合は、あらかじめ、Azure Government の機能を十分に理解し、不明点があればアカウント チームに問い合わせるようにしてください。

特定の適格性認定や規制に対応している Azure Government サービスについては、[Microsoft Azure セキュリティ センター コンプライアンス ページ](http://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx)を参照し、最新情報を確認してください。 その他の Microsoft サービスが利用できる可能性もありますが、Azure Government によって保護されるサービスの範囲には含まれておらず、本ドキュメントでは取り上げません。 また、Azure Government サービスでは、その他さまざまなリソース、アプリケーション、サービスを使用することが認められている場合があります。そうしたリソースやアプリケーション、サービスは、サード パーティまたは Microsoft が、独立した使用条件とプライバシー ポリシーの下で提供するものであり、本ドキュメントの対象外となります。 そうした "付加的" サービス (Marketplace サービスなど) については、付随する条項をご自身の責任ですべて確認し、コンプライアンスに関する要件を確実に満たしてください。

Azure Government は、扱うデータが特定の政府規制や要件 (NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4、CJIS など) の対象となる事業体で利用できます。そのような事業体では、規制に準拠するために Azure Government が必要となります。 Azure Government をご利用のお客様は、適格性検証の対象となります。

Azure Government の適格性に関してご不明な点がある場合は、担当のアカウント チームにお問い合わせください。

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

## <a name="protecting-customer-data-using-encryption"></a>暗号化を使用してお客様のデータを保護する
リスクの緩和と規制義務の遵守が求められる中で、データの暗号化はしだいに注目を集め、その重要性はますます大きくなってきています。 効果的な暗号化システムを使用することで、現在のネットワークやアプリケーションのセキュリティ対策を強化し、クラウド環境の全体的なリスクを軽減することができます。

### <a name="a-nameoverviewaencryption-at-rest"></a><a name="Overview"></a>保存時の暗号化
ディスク ストレージに格納されているカスタマー コンテンツの保護には、保存データの暗号化が適用されます。 その実現にはいくつかの方法があります。

### <a name="a-nameoverviewastorage-service-encryption"></a><a name="Overview"></a>Storage Service Encryption
Azure Storage Service Encryption はストレージ アカウント レベルで有効になります。したがってブロック BLOB とページ BLOB は、Azure Storage への書き込み時に自動的に暗号化されます。 Azure Storage からデータを読み取ると、Storage サービスによって復号化されてから、返されます。 そのため、データをセキュリティで保護するためにコードを変更したり、アプリケーションにコードを追加したりする必要はありません。

### <a name="a-nameoverviewaazure-disk-encryption"></a><a name="Overview"></a>Azure Disk Encryption
Azure Virtual Machine に使用される OS ディスクとデータ ディスクを暗号化するには、Azure Disk Encryption を使用します。 Azure Key Vault との統合によって、ディスクの暗号化キーを制御し、効率よく管理することができます。

### <a name="a-nameoverviewaclient-side-encryption"></a><a name="Overview"></a>クライアント側の暗号化
クライアント側の暗号化は Java と .NET ストレージ クライアント ライブラリに組み込まれているので、Azure Key Vault API を使用して簡単に実装できます。 Azure Key Vault では、Azure Active Directory を使用して、Azure Key Vault 内のシークレットへのアクセス権を特定のユーザー用に取得します。

### <a name="a-nameoverviewaencryption-in-transit"></a><a name="Overview"></a>転送中の暗号化
Azure Government への接続用に用意されている基本的な暗号化は、Transport Level Security (TLS) 1.2 プロトコルと X.509 証明書をサポートします。 Azure Government のデータセンター間におけるインフラストラクチャ ネットワーク接続には、Federal Information Processing Standard (FIPS) 140-2 Level 1 暗号アルゴリズムも使用されます。  Windows Server 2012 R2 と Windows 8 + VM、および Azure ファイル共有では、VM とファイル共有間の暗号化に SMB 3.0 を使用できます。 クライアント アプリケーションのストレージに転送する前のデータを暗号化したり、ストレージから転送された後のデータを復号化したりするには、クライアント側の暗号化を使用します。

### <a name="a-nameoverviewabest-practices-for-encryption"></a><a name="Overview"></a>暗号化のベスト プラクティス
* IaaS VM: Azure Disk Encryption を使用します。 ディスクを Azure Storage にバックアップする際に使用する VHD ファイルは、Storage Service Encryption を有効にすることで暗号化します。ただし、その場合、暗号化されるのは、新しく書き込まれたデータだけです。 つまり、VM を作成してから、VHD ファイルを保持するストレージ アカウントで Storage Service Encryption を有効にすると、元の VHD ファイルではなく、変更のみが暗号化されます。
* クライアント側の暗号化: データは転送前に暗号化され、保存データも暗号化されるので、データを暗号化するうえで最も安全な方法といえます。 ただし、ストレージを使用してアプリケーションにコードを追加する必要があります。 ストレージを追加したくない場合は、転送中のデータに HTTPS を使用し、Storage Service Encryption を使用して保存データを暗号化する方法があります。 クライアント側の暗号化は、クライアントにかかる負荷が増加するため、スケーラビリティの計画でこの点を考慮する必要があります。特に、大量のデータを暗号化したり転送したりする場合に重要です。

Azure における暗号化オプションの詳細については、「 [Storage Security Guide (ストレージ セキュリティ ガイド)](https://docs.microsoft.com/en-us/azure/storage/storage-security-guide)」を参照してください。

## <a name="protecting-customer-data-by-managing-secrets"></a>シークレットを管理することでお客様のデータを保護する
クラウドにあるデータを保護するうえで、安全なキー管理は不可欠です。 キーの管理を省力化すると共に、クラウド アプリケーションやクラウド サービスでデータを暗号化する際に使用されるキーの管理に絶えず気を配るよう努めてください。

### <a name="a-nameoverviewabest-practices-for-managing-secrets"></a><a name="Overview"></a>シークレットを管理するうえでのベスト プラクティス
* ハードコーディングされた構成ファイルやスクリプト、ソース コード内からシークレットが漏えいするリスクを Key Vault を使用して最小限に抑えます。 Azure Key Vault は、FIPS 140-2 Level 2 認定のハードウェア セキュリティ モジュール (HSM) にキー (Azure Disk Encryption 用の暗号化キーなど) やシークレット (パスワードなど) を格納することで、それらを暗号化します。 さらに安心感を高めたい場合には、それらの HSM でキーのインポートや生成を行うことができます。
* アプリケーション コードやテンプレートに記述する必要があるのは、シークレットへの URI 参照のみです。つまり、実際のシークレットは、コード リポジトリ、構成リポジトリ、ソース コード リポジトリには存在しません。 これにより、GitHub での収集ボットのような、内外のリポジトリへのキー フィッシング攻撃を防ぐことができます。
* Key Vault 内で強力な RBAC 制御を利用します。 信任されていたオペレーターが退職したり、社内の別の部署に異動になったりした場合は、そのオペレーターのシークレットへのアクセスを禁止する必要があります。  

詳細については、 [Azure Government の Key Vault](/azure-government/azure-government-tech-keyvault)

## <a name="isolation-to-restrict-data-access"></a>分離によってデータ アクセスを制限する
分離の本質は、境界、区分、コンテナーを使用して、権限のあるユーザーやサービス、アプリケーションにデータへのアクセスを限定することです。 たとえば Microsoft Azure のようなマルチテナント型のクラウド プラットフォームでは、テナントどうしを分離することが、セキュリティ上の機構として不可欠になります。 論理上の分離によって、テナントが互いの運用に支障を及ぼすことを防ぐことができます。

### <a name="a-nameoverviewaenvironment-isolation"></a><a name="Overview"></a>環境の分離
Azure Government 環境は、Microsoft の他のネットワークから切り離された物理インスタンスです。 これは、生体認証デバイスやカメラを使用した物理的な境界による保護など、一連の物理的制御と論理的制御によって実現されています。  運用環境への論理アクセスを必要とする Microsoft の社員は、特定の資格情報と多要素認証を使用しています。  Azure Government のサービス インフラストラクチャはすべて米国内に置かれています。

#### <a name="a-nameoverviewaper-customer-isolation"></a><a name="Overview"></a>顧客ごとの分離
Azure は、VLAN 分離、ACL、ロード バランサー、および IP フィルターによるネットワーク アクセス制御と分離を実装しています。

サブスクリプションやリソース グループ、仮想ネットワーク、サブネットによって、お客様はそのリソースの分離度をさらに高めることができます。

Microsoft Azure における分離の詳細については、 [Azure セキュリティ ガイドの分離に関するセクション](/azure-security-getting-started/#isolation)を参照してください。

補足情報と最新情報については、<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government ブログ</a>を随時ご覧ください。




<!--HONumber=Jan17_HO5-->


