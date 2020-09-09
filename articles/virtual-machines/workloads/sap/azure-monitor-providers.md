---
title: SAP ソリューション向け Azure Monitor のプロバイダー | Microsoft Docs
description: この記事では、SAP ソリューション向け Azure Monitor に関してよく寄せられる質問に対する回答を示します
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 267d600270e834cf4f1f077452fda7459fac3029
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86525451"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>SAP ソリューション向け Azure Monitor のプロバイダー (プレビュー)

## <a name="overview"></a>概要  

SAP ソリューション向け Azure Monitor のコンテキストにおいて、"*プロバイダーの種類*" は、特定の "*プロバイダー*" を指します。 たとえば、*SAP HANA* は、SAP HANA データベースなど、SAP ランドスケープ内の特定のコンポーネント用に構成されています。 プロバイダーは、対応するコンポーネントの接続情報を含み、そのコンポーネントから利用統計情報を収集するのに役立ちます。 1 つの SAP ソリューション向け Azure Monitor リソース (SAP モニター リソースとも呼ばれます) に対して、プロバイダーの種類が同じ複数のプロバイダーを構成することも、プロバイダーの種類が異なる複数のプロバイダーを構成することもできます。
   
お客様は、SAP ランドスケープの対応するコンポーネントからのデータ収集を可能にするために、さまざまなプロバイダーの種類を構成することを選択できます。 たとえば、お客様は、SAP HANA プロバイダーの種類用に 1 つのプロバイダーを構成する一方で、高可用性クラスター プロバイダーの種類用に別のプロバイダーを構成することができます。  

また、お客様は、同じ SAP モニター リソースと関連するマネージド グループを再利用するために、特定のプロバイダーの種類のプロバイダーを複数構成することもできます。 マネージド リソース グループについてさらに学習してください。 パブリック プレビューでは、次のプロバイダーの種類がサポートされています。   
- SAP HANA
- 高可用性クラスター
- Microsoft SQL Server

![SAP ソリューション向け Azure Monitor のプロバイダー](./media/azure-monitor-sap/azure-monitor-providers.png)

SAP モニター リソースをデプロイするときに、使用可能なプロバイダーの種類から少なくとも 1 つのプロバイダーを構成することをお勧めします。 お客様は、プロバイダーを構成することによって、プロバイダーが構成されている対応するコンポーネントからのデータ収集を開始します。   

SAP モニター リソースのデプロイ時にお客様がプロバイダーを構成しなかった場合、SAP モニター リソースは正常にデプロイされますが、利用統計情報は収集されません。 お客様は、Azure portal 内で SAP モニター リソースを介して、デプロイ後にプロバイダーを追加することができます。 お客様は、いつでも SAP モニター リソースのプロバイダーを追加または削除できます。

> [!Tip]
> 特定のプロバイダーの実装を Microsoft に希望する場合は、このドキュメントの最後にあるリンクからフィードバックを送信するか、アカウント チームにお問い合わせください。  

## <a name="provider-type-sap-hana"></a>プロバイダーの種類: SAP HANA

お客様は、プロバイダーの種類 *SAP HANA* のプロバイダーを 1 つ以上構成して、SAP HANA データベースからのデータ収集を有効にすることができます。 SAP HANA プロバイダーは、SQL ポートを介して SAP HANA データベースに接続し、データベースから利用統計情報をプルして、顧客のサブスクリプションの Log Analytics ワークスペースにプッシュします。 SAP HANA プロバイダーは、SAP HANA データベースから 1 分ごとにデータを収集します。  

パブリック プレビューでは、SAP HANA プロバイダーを使用して次のデータを確認できます。基になるインフラストラクチャの使用率、SAP HANA ホストの状態、SAP HANA システム レプリケーション、および SAP HANA バックアップの利用統計情報。 SAP HANA プロバイダーを構成するには、ホスト IP アドレス、HANA SQL ポート番号、および SYSTEMDB のユーザー名とパスワードが必要です。 SYSTEMDB に対しては SAP HANA プロバイダーを構成することをお勧めします。ただし、他のデータベース テナントに対して追加のプロバイダーを構成することもできます。

![SAP ソリューション向け Azure Monitor のプロバイダー - SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-high-availability-cluster"></a>プロバイダーの種類: 高可用性クラスター
お客様は、プロバイダーの種類 "*高可用性クラスター*" のプロバイダーを 1 つ以上構成して、SAP ランドスケープ内の Pacemaker クラスターからのデータ収集を有効にすることができます。 高可用性クラスター プロバイダーは、[ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) エンドポイントを使用して Pacemaker に接続し、データベースから利用統計情報をプルして、顧客のサブスクリプションの Log Analytics ワークスペースにプッシュします。 高可用性クラスター プロバイダーは、Pacemaker から 60 秒ごとにデータを収集します。  

パブリック プレビューでは、高可用性クラスター プロバイダーを使用して次のデータを確認できます。   
 - ノードとリソースの状態のロールアップとして表示されるクラスターの状態 
 - [その他](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![SAP ソリューション向け Azure Monitor のプロバイダー - 高可用性クラスター](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

高可用性クラスター プロバイダーを構成するには、次の 2 つの主要な手順が必要です。 
1. Pacemaker クラスター内の "*各*" ノードに [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) をインストールする 
    - お客様は、Azure Automation スクリプトを使用して、高可用性クラスターをデプロイできます。 スクリプトを実行すると、各クラスター ノードに [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) がインストールされます。  
    - または、お客様は、[このページ](https://github.com/ClusterLabs/ha_cluster_exporter)の手順に従って手動インストールを実行できます 
2. Pacemaker クラスター内の "*各*" ノードで高可用性クラスター プロバイダーを構成する  
  高可用性クラスター プロバイダーを構成するには、Prometheus の URL、クラスター名、ホスト名、およびシステム ID が必要です。   
  クラスター ノードごとに 1 つのプロバイダーを構成することをお勧めします。   

## <a name="provider-type-microsoft-sql-server"></a>プロバイダーの種類: Microsoft SQL Server

お客様は、プロバイダーの種類 *Microsoft SQL Server* のプロバイダーを 1 つ以上構成して、[SQL Server on Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/) からのデータ収集を有効にすることができます。 SQL Server プロバイダーは、SQL ポートを介して Microsoft SQL Server に接続し、データベースから利用統計情報をプルして、顧客のサブスクリプションの Log Analytics ワークスペースにプッシュします。 SQL Server は SQL 認証用に構成する必要があります。また、プロバイダーの既定のデータベースとして SAP DB を使用して SQL Server ログインを作成する必要があります。 SQL Server プロバイダーは、SQL サーバーから 60 秒から最大 1 時間ごとにデータを収集します。  

パブリック プレビューでは、SQL Server プロバイダーを使用して、基になるインフラストラクチャの使用率、順位が上位の SQL ステートメント、一番大きなテーブル、SQL Server エラー ログに記録された問題、ブロック プロセスなどのデータを確認できます。  

Microsoft SQL Server プロバイダーを構成するには、SAP システム ID、ホスト IP アドレス、SQL Server ポート番号、および SQL Server のログイン名とパスワードが必要です。

![SAP ソリューション向け Azure Monitor のプロバイダー - SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="next-steps"></a>次のステップ

- 最初の SAP ソリューション向け Azure Monitor リソースを作成する。
- SAP ソリューション向け Azure Monitor に関する質問がありますか。 [FAQ](./azure-monitor-faq.md) のセクションを確認してください
