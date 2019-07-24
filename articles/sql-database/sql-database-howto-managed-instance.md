---
title: Azure SQL Database マネージド インスタンスを構成する方法 | Microsoft Docs
description: Azure SQL Database マネージド インスタンスを構成して管理する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 93be58ac231ff2b88c6618c549c9d1975977b91f
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59360097"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>Azure SQL Database でマネージド インスタンスを作成する方法

この記事では、自分のマネージド インスタンスを管理して構成するのに役立つさまざまなガイド、スクリプト、説明を確認することができます。

## <a name="migration"></a>移行

- [マネージド インスタンスへの移行](sql-database-managed-instance-migrate.md) – マネージド インスタンスへの移行に関して推奨される移行プロセスとツールについて説明します。

- [マネージド インスタンスへの TDE 証明書の移行](sql-database-managed-instance-migrate-tde-certificate.md) – お客様の SQL Server データベースが Transparent Data Encryption (TDE) を使用して保護される場合は、お客様が Azure に格納するバックアップの暗号化を解除するためにマネージド インスタンスによって使用される証明書を移行する必要があります。

## <a name="network-configuration"></a>ネットワーク構成

- [マネージド インスタンス サブネットのサイズの決定](sql-database-managed-instance-determine-size-vnet-subnet.md) – お客様が内部にリソースを追加すると、マネージド インスタンスはサイズ変更できない専用サブネットに配置されます。 そのため、お客様がサブネット内にデプロイしたいインスタンスの数と種類に従って、サブネットに必要となるアドレスの IP 範囲を計算する必要があります。
- [マネージド インスタンス用の新しい VNet とサブネットの作成](sql-database-managed-instance-create-vnet-subnet.md) – お客様が自分のマネージド インスタンスをデプロイする Azure VNet およびサブネットは、[こちらで説明されているネットワーク要件](sql-database-managed-instance-connectivity-architecture.md#network-requirements)に従って構成しなければなりません。 このガイドでは、マネージド インスタンス用に正しく構成された新しい VNet とサブネットを作成する最も簡単な方法を確認できます。
- [マネージド インスタンス用の既存の VNet とサブネットの構成](sql-database-managed-instance-configure-vnet-subnet.md) – お客様の既存の VNet とサブネットを構成して内部にマネージド インスタンスをデプロイする場合は、[ネットワーク要件](sql-database-managed-instance-connectivity-architecture.md#network-requirements)をチェックし、その要件に従ってお客様のサブネットを構成するためのスクリプトをこちらで確認できます。
- [カスタム DNS の構成](sql-database-managed-instance-custom-dns.md) – お客様のマネージド インスタンスからデータベース メール プロファイルのリンク サーバー経由でカスタム ドメイン上の外部リソースにアクセスする場合は、カスタム DNS を構成する必要があります。
- [ネットワーク構成の同期](sql-database-managed-instance-sync-network-configuration.md) - 場合によっては、[自分のアプリを Azure Virtual Network と統合した](../app-service/web-sites-integrate-with-vnet.md)にもかかわらず、マネージド インスタンスへの接続を確立できないことがあります。 その場合の対処の 1 つとして、使用するサービス プラン用にネットワーク構成を更新するという対処方法があります。
- [管理エンドポイント IP アドレスの検索](sql-database-managed-instance-find-management-endpoint-ip-address.md) – マネージド インスタンスでは、管理目的にのみパブリック エンドポイントが使用されます。 こちらで説明されているスクリプトを使用して、管理エンドポイントの IP アドレスを特定することができます。
- [組み込みのファイアウォール保護の確認](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) – マネージド インスタンスは、必要なポートに対してのみトラフィックを許可する組み込みのファイアウォールによって保護されます。 このガイドで説明されているスクリプトを使用して、組み込みのファイアウォール規則をチェックして確認できます。
- [アプリケーションの接続](sql-database-managed-instance-connect-app.md) – マネージド インスタンスは、プライベート IP アドレスを備えた独自のプライベート Azure VNet に配置されます。 アプリケーションをお客様のマネージド インスタンスに接続するさまざまなパターンについて説明します。

## <a name="feature-configuration"></a>機能の構成

- [トランザクション レプリケーション](replication-with-sql-database-managed-instance.md)では、マネージド インスタンス間でご利用のデータをレプリケートしたり、オンプレミスの SQL Server からマネージド インスタンスまたはその逆にデータをレプリケートしたりすることができます。 トランザクション レプリケーションの使用方法と構成方法の詳細については、このガイドを参照してください。
- [脅威検出の構成](sql-database-managed-instance-threat-detection.md) – [脅威検出](sql-database-threat-detection-overview.md)は、SQL インジェクションや疑わしい場所からのアクセスなど、さまざまな潜在的な攻撃を検出する組み込みの Azure SQL Database 機能です。 このガイドでは、マネージド インスタンス用に[脅威検出](sql-database-threat-detection-overview.md)を有効にして構成する方法を学習できます。

## <a name="next-steps"></a>次の手順

- [単一データベースの攻略ガイド](sql-database-howto-single-database.md)の詳細を確認する