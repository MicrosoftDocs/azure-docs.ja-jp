---
title: よくあるご質問 - SAP ソリューション向け Azure Monitor | Microsoft Docs
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
ms.openlocfilehash: cf0366300c4fab18a0f6231a97ca050eddd50132
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85852183"
---
# <a name="azure-monitor-for-sap-solutions-faq-preview"></a>SAP ソリューション向け Azure Monitor に関する FAQ (プレビュー)
## <a name="frequently-asked-questions"></a>よく寄せられる質問

この記事では、SAP ソリューション向け Azure Monitor に関してよく寄せられる質問 (FAQ) に対する回答を示します。  

 - **SAP ソリューション向け Azure Monitor に対して料金を支払う必要がありますか?**  
SAP ソリューション向け Azure Monitor には、ライセンス料金は発生しません。  
ただし、お客様は管理対象リソース グループ コンポーネントの費用を負担する必要があります。  

 - **このサービスのパブリック プレビューは、どのリージョンで利用できますか?**  
このサービスのパブリック プレビューは、米国東部 2、米国西部 2、米国東部、西ヨーロッパで利用可能になります。  

 - **サブスクリプションで管理対象リソース グループのデプロイを許可するためのアクセス許可を付与する必要がありますか?**  
いいえ。明示的なアクセス許可は必要ありません。  

 - **コレクター VM はどこにありますか?**  
Microsoft では、SAP ソリューション向け Azure Monitor リソースをデプロイする時点で、お客様にご使用の SAP HANA サーバーと同じ VNET を監視リソースに選択することをお勧めします。 そのため、コレクター VM は、SAP HANA サーバーと同じ VNET に配置することをお勧めします。 お客様が HANA 以外のデータベースを使用している場合、コレクター VM は HANA 以外のデータベースと同じ VNET に存在します。  

 - **サポートされている HANA のバージョンを教えてください**  
HANA 1.0 SPS 12 (Rev. 120 以降) および HANA 2.0 SPS03 以降  

 - **サポートされている HANA のデプロイの構成を教えてください**  
次の構成がサポートされています。
   - 単一ノード (スケールアップ) とマルチノード (スケールアウト)  
   - Single Database Container (HANA 1.0 SPS 12) と Multiple Database Containers (HANA 1.0 SPS 12 または HANA 2.0)  
   - 自動ホスト フェールオーバー (n + 1) と HSR  

 - **サポートされている SQL Server のバージョンを教えてください**  
SQL Server 2012 SP4 以降。  

 - **サポートされている SQL Server の構成を教えてください**  
次の構成がサポートされています。
   - 仮想マシンの既定または名前付きスタンドアロン インスタンス  
   - クラスター化されたリソースの仮想名または AlwaysOn リスナー名のいずれかを使用する場合は、クラスター化されたインスタンスまたは AlwaysOn 構成のインスタンス。 現在、クラスターまたは AlwaysOn 固有のメトリックは収集されません    
   - Azure SQL Database (PAAS) は現在サポートされていません  

 - **管理対象リソース グループを誤って削除した場合はどうなりますか?**  
管理対象リソース グループは、既定でロックされています。 したがって、お客様が管理対象リソース グループを誤って削除する可能性はごくわずかです。  
お客様が管理対象リソース グループを削除すると、SAP ソリューション向け Azure Monitor が機能しなくなります。 お客様は、新しい SAP ソリューション向け Azure Monitor リソースをデプロイしてやり直していただく必要があります。  

 - **SAP ソリューション向け Azure Monitor リソースをデプロイするために、Azure サブスクリプションで必要なロールはどれですか?**  
共同作成者ロールです。  

 - **この製品の SLA はどのようなものですか?**  
プレビューは、サービス レベル アグリーメントから除外されています。 SAP ソリューション向け Azure Monitor のマーケットプレース イメージを通じて、完全なライセンス条項をお読みください。  

 - **このソリューションでランドスケープ全体を監視できますか?**  
パブリック プレビューでは現在、HANA データベース、基になるインフラストラクチャ、高可用性クラスター、Microsoft SQL Server を監視できます。  

 - **このサービスは SAP Solution Manager に代わるものですか?**  
いいえ。 お客様は、引き続き SAP Solution Manager を使用してビジネス プロセスの監視を行うことができます。  

 - **SAP HANA Cockpit/Studio などの従来のソリューションより優れているこのサービスの価値はどのようなものですか?**  
SAP ソリューション向け Azure Monitor は、HANA データベース固有のものではありません。 SAP ソリューション向け Azure Monitor では、AnyDB もサポートされています。  

## <a name="next-steps"></a>次のステップ

- 最初の SAP ソリューション向け Azure Monitor リソースを作成する。