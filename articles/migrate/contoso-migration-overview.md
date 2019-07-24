---
title: Contoso での Azure への移行の概要 | Microsoft Docs
description: Contoso が自社のオンプレミスのデータセンターを Azure に移行するときに使用する移行戦略とシナリオの概要について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 8c1b5cc8a9f1c1246bd1973539e3bd00b340a657
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57890952"
---
# <a name="contoso-migration-overview"></a>Contoso の移行: 概要


この記事では、架空の組織 Contoso がオンプレミスのインフラストラクチャを [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) クラウドに移行する方法について説明します。 

このドキュメントは、架空の会社 Contoso が Azure に移行する方法を説明するシリーズ記事の最初です。 このシリーズには、インフラストラクチャの移行を設定する方法や、さまざまな種類の移行を実施する方法を示した情報とシナリオが含まれています。 シナリオが複雑になってきているため、さらに記事が追加される予定です。 これらの記事は Contoso 社が移行作業を遂行する方法を示すものですが、全体を通じて、一般読者向けのヒントや特定の手順も記載しています。

## <a name="introduction"></a>はじめに

Azure には、クラウド サービスの包括的なセットが用意されています。 開発者や IT プロフェッショナルは、これらのサービスを利用し、データセンターのグローバルなネットワークを通じて、さまざまなツールやフレームワークをベースにアプリケーションを構築、デプロイ、および管理することができます。 ビジネスはデジタル シフトに関連する課題に直面しているため、Azure クラウドはリソースや操作の最適化、お客様や従業員との連携、および製品の変換の方法を理解できるように支援します。

ただし、速度と柔軟性、最小限のコスト、パフォーマンス、および信頼性の面でのクラウドのすべての利点にもかかわらず、多くの組織ではまだしばらくの間、オンプレミス データセンターを実行する必要があることも認識しています。 クラウドの採用の障壁に対処するために、Azure では、オンプレミスのデータセンターと Azure パブリック クラウドの間に橋を構築するハイブリッド クラウド戦略を提供しています。 たとえば、Azure Backup などの Azure クラウド リソースを使用してオンプレミスのリソースを保護したり、Azure 分析を使用してオンプレミスのワークロードを把握したりすることができます。 

ハイブリッド クラウド戦略の一環として、Azure ではオンプレミスのアプリやワークロードをクラウドに移行するためのソリューションを用意しています。 簡単な手順で、オンプレミス リソースを包括的に評価し、Azure クラウドではどのように実行されるかを把握することができます。 次に、入手した詳しい評価に基づき、自信を持ってリソースを Azure に移行することができます。 Azure でリソースを稼働させたら、リソースを最適化して、アクセス、柔軟性、セキュリティ、および信頼性を保持したり向上させたりすることができます。

## <a name="migration-strategies"></a>移行方法

クラウドへの移行戦略は、リホスト、リファクター、リアーキテクト、リビルドという 4 つの大きなカテゴリに分類できます。 どの戦略を採用するかは、ビジネス ドライバーや移行の目標によって変わってきます。 複数の戦略を採用する場合もあります。 たとえば、単純なアプリやビジネス上重要でないアプリはリホスト (リフトアンドシフト) し、より複雑でビジネスに不可欠なアプリはリアーキテクトするという方針も採用できます。 ここからは、戦略について見ていきましょう。


**戦略** | **定義** | **いつ使用するか** 
--- | --- | --- 
**リホスト** | 多くの場合、"リフトアンドシフト" の移行と呼ばれます。 このオプションでは、コードを変更せずに、既存のアプリをすばやく Azure に移行できます。 各アプリはそのままの状態で移行されるので、コード変更に伴うリスクとコストなしにクラウドのメリットを享受できます。 | アプリをクラウドにすばやく移動する必要がある場合。<br/><br/> アプリを変更せずに移動したい場合。<br/><br/> アプリが移行後に [Azure IaaS](https://azure.microsoft.com/overview/what-is-iaas/) のスケーラビリティを活かせるように設計されている場合。<br/><br/> ビジネスにとって重要なアプリであるが、アプリの機能をすぐに変更する必要がない場合。
**リファクター** | 多くの場合 "再パッケージ化" と呼ばれます。リファクタリングでは、アプリを [Azure PaaS](https://azure.microsoft.com/overview/what-is-paas/) に接続してクラウド サービスを使用できるようにするために、アプリに最小限の変更を加える必要があります。<br/><br/> たとえば、既存のアプリを Azure App Service または Azure Kubernetes Service (AKS) に移行できます。<br/><br/> また、リレーショナル データベースおよび非リレーショナル データベースを、Azure SQL Database Managed Instance、Azure Database for MySQL、Azure Database for PostgreSQL、Azure Cosmos DB などのオプションにリファクタリングすることもできます。 | Azure で動作するようにアプリを簡単に再パッケージ化できる場合。<br/><br/> Azure が提供する革新的な DevOps プラクティスを適用したい場合、またはワークロード向けのコンテナー戦略を使用した DevOps を検討している場合。<br/><br/> リファクタリングでは、既存のコード ベースの移植性と使用可能な開発スキルを考慮する必要があります。
**リアーキテクト** | 移行のためのリアーキテクトは、アプリのアーキテクチャを最適化してクラウドのスケーラビリティを活かすために、アプリの機能とコード ベースを変更、拡張することを中心とした作業となります。<br/><br/> たとえば、モノリシック アプリケーションを、相互に連携し、簡単にスケーリングできるマイクロサービスのグループに分割できます。<br/><br/> また、リレーショナル データベースと非リレーショナル データベースを、Azure SQL Database Managed Instance、Azure Database for MySQL、Azure Database for PostgreSQL、Azure Cosmos DB など、フル マネージドの DBaaS ソリューションにリアーキテクトすることもできます。 | アプリにメジャー リビジョンを施し、新機能を組み込んだり、クラウド プラットフォームで作業を効果的に行ったりする必要がある場合。<br/><br/> 既存のアプリケーションへの投資を活かしたい場合、スケーラビリティ要件を満たしたい場合、革新的な Azure DevOps プラクティスを適用したい場合、仮想マシンの使用を最小限に抑えたい場合。
**リビルド** | リビルドでは、Azure のクラウド テクノロジを利用してアプリを最初からリビルドし、進化させることができます。<br/><br/> たとえば、Azure Functions、Azure AI、Azure SQL Database Managed Instance、Azure Cosmos DB などの[クラウドネイティブ](https://azure.com/cloudnative) テクノロジを活用して、新しいアプリを構築することができます。 | 開発は迅速に行いたいが、既存のアプリの機能と寿命が限られている場合。<br/><br/> ビジネスのイノベーションの加速 (Azure が提供する DevOps のプラクティスを含む)、クラウドネイティブ テクノロジを活用した新しいアプリケーションの構築、AI、ブロックチェーン、IoT の進化の活用に対する準備が整っている場合。

## <a name="migration-articles"></a>移行に関する記事

シリーズの記事を次の表にまとめています。  

- 各移行シナリオは、少しずつ異なるビジネス目標に沿って作成されています。このビジネス目標によって移行戦略が決まります。
- 各デプロイ シナリオでは、ビジネス ドライバーと目標、提案されたアーキテクチャ、移行を実施するための手順、クリーンアップの推奨事項、移行完了後の次の手順についての情報が記載されています。

**記事** | **詳細** | **状態**
--- | --- | ---
記事 1: 概要 | 記事シリーズ、Contoso の移行戦略およびシリーズで使用されているサンプル アプリの概要です。 | この記事の内容は次のとおりです。
[記事 2: Azure インフラストラクチャをデプロイする](contoso-migration-infrastructure.md) | Contoso がオンプレミス インフラストラクチャと Azure インフラストラクチャを移行に向けて準備します。 このシリーズの移行に関するすべての記事で同じインフラストラクチャを使用します。 | 使用可能
[記事 3: Azure への移行についてオンプレミスのリソースを評価する](contoso-migration-assessment.md)  | Contoso が、VMware で実行されているオンプレミスの SmartHotel360 アプリを評価します。 Contoso では、アプリの VM は Azure Migrate サービスを使用して評価し、アプリの SQL Server データベースは Data Migration Assistant を使用して評価します。 | 使用可能
[記事 4: Azure VM および SQL Database Managed Instance でのアプリのリホスト](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso が、オンプレミスの SmartHotel360 アプリの Azure へのリフトアンドシフト移行を実行します。 Contoso は、[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) を使用してアプリのフロントエンド VM を移行します。 アプリ データベースの Azure SQL Database Managed Instance への移行には、[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) を使用します。 | 使用可能   
[記事 5:Azure VM 上でアプリをリホストする](contoso-migration-rehost-vm.md) | Contoso が Site Recovery サービスを使用して SmartHotel360 アプリの VM を Azure VM に移行します。 | 使用可能
[記事 6:Azure VM 上および SQL Server AlwaysOn 可用性グループ内でアプリをリホストする](contoso-migration-rehost-vm-sql-ag.md) | Contoso が SmartHotel360 アプリを移行します。 Contoso は、Site Recovery を使用してアプリの VM を移行します。 Contoso は、Database Migration Service を使用して、AlwaysOn 可用性グループで保護されている SQL Server クラスターにアプリのデータベースを移行します。 | 使用可能 [記事 7: Linux アプリの Azure VM への再ホスト](contoso-migration-rehost-linux-vm.md) 
[記事 8:Azure VM および Azure Database for MySQL での Linux アプリのリホスト](contoso-migration-rehost-linux-vm-mysql.md) | Contoso が Site Recovery を使用して Linux osTicket アプリを Azure VM に移行します。 アプリ データベースを Azure Database for MySQL に移行するために MySQL Workbench を使用します。 | 使用可能
[記事 9:Azure Web App と Azure SQL Database でのアプリのリファクター](contoso-migration-refactor-web-app-sql.md) | Contoso が SmartHotel360 アプリを Azure Web アプリに移行します。また、Database Migration Assistant を使用して、アプリ データベースを Azure SQL Server インスタンスに移行します。 | 使用可能    
[記事 10:Azure Web アプリと Azure Database for MySQL 内で Linux アプリをリファクタリングする](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso が Azure Traffic Manager を使用し、その Linux osTicket アプリを、複数の Azure リージョンの Azure Web アプリに移行します。この Azure Web アプリは、継続的デリバリーを目的として GitHub と統合されます。 Contoso は、アプリ データベースを Azure Database for MySQL インスタンスに移行します。 | 使用可能
[記事 11:Azure DevOps サービスでの Team Foundation Server をリファクタリングします](contoso-migration-tfs-vsts.md) | Contoso がそのオンプレミスの Team Foundation Server の展開を Azure の Azure DevOps Services に移行します。 | 使用可能
[記事 12:Azure コンテナーと Azure SQL Database でのアプリの再構築](contoso-migration-rearchitect-container-sql.md) | Contoso が SmartHotel アプリを Azure に移行します。 その後、アプリの Web 階層を Azure Service Fabric 内で動作する Windows コンテナーとして再構築し、さらに、Azure SQL Database を使用してデータベースを再構築します。 | 使用可能    
[記事 13:Azure でのアプリのリビルド](contoso-migration-rebuild.md) | Contoso が Azure のさまざまな機能とサービス (Azure App Service、Azure Kubernetes Service (AKS)、Azure Functions、Azure Cognitive Services、Azure Cosmos DB など) を使用して SmartHotel アプリをリビルドします。 | 使用可能 
[記事 14:Azure への移行のスケーリング](contoso-migration-scale.md) | 移行の組み合わせを試した後、Contoso は Azure への完全移行に向けてスケーリングを準備します。 | 使用可能

この記事では、Contoso はすべての移行シナリオを完了するために必要な、すべてのインフラストラクチャ要素を設定します。 







### <a name="demo-apps"></a>デモ アプリ

この記事では、SmartHotel360 と osTicket の 2 つのデモ アプリを使用します。

- **SmartHotel360**: このアプリは、Azure を操作する際に使用できるテスト アプリとして Microsoft が開発したものです。 オープン ソースとして提供されており、[GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードできます。 これは、SQL Server データベースに接続されている ASP.NET アプリです。 現在、このアプリは、Windows Server 2008 R2 と SQL Server 2008 R2 が実行されている 2 つの VMware VM 上に存在しています。 アプリの VM はオンプレミスでホストされており、vCenter Server で管理されています。
- **osTicket**: Linux 上で稼働する、オープン ソースのサービス デスク向け発券アプリです。 [GitHub](https://github.com/osTicket/osTicket) からダウンロードできます。 現在、このアプリは、Ubuntu 16.04 LTS が稼働している 2 つの VMware VM 上に存在し、Apache 2、PHP 7.0、MySQL 5.7 が使用されています。
    

## <a name="next-steps"></a>次の手順

Contoso が移行に備えて、オンプレミスと Azure インフラストラクチャをどのように設定するかを[説明します](contoso-migration-infrastructure.md)。



