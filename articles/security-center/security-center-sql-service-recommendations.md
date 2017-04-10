---
title: "Azure Security Center での Azure SQL サービスとデータの保護 | Microsoft Docs"
description: "このドキュメントでは、Azure Security Center での推奨事項に従ってデータと Azure SQL サービスを保護し、セキュリティ ポリシーを使用してコンプライアンスを順守する方法について説明します。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 7b5df25f46a2824acb361401ab90c960b8e5978f
ms.openlocfilehash: 8ce47dd649d1d945df506ed65c871cf3afe2f004


---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Azure Security Center での Azure SQL サービスとデータの保護
Azure セキュリティ センターは、Azure リソースのセキュリティの状態を分析します。 潜在的なセキュリティの脆弱性を識別すると、Security Center は、必要な管理を構成するプロセスを説明する推奨事項を作成します。  推奨事項は、仮想マシン (VM)、ネットワーク、SQL、データ、アプリケーションといった、Azure のリソースの種類に適用されます。

この記事では、Azure SQL サービスとデータに適用される推奨事項について説明します。 推奨事項は、Azure SQL サーバーおよびデータベースの監査と SQL データベースの暗号化を有効にすることと、Azure Storage アカウントの暗号化を有効にすることが中心です。  次の表を参考にすると、SQL サービスとデータに関する利用可能な推奨事項と、それぞれを適用した場合の結果を理解しやすくなります。

## <a name="available-sql-service-and-data-recommendations"></a>SQL サービスとデータに関する利用可能な推奨事項
| 推奨 | Description |
| --- | --- |
| [サーバーの SQL 監査の有効化](security-center-enable-auditing-on-sql-servers.md) |Azure SQL サーバーの監査を有効にすることをお勧めします (Azure SQL サービスのみ。仮想マシンで実行されている SQL を除く)。 |
| [データベースの SQL 監査の有効化](security-center-enable-auditing-on-sql-databases.md) |Azure SQL データベースの監査を有効にすることをお勧めします (Azure SQL サービスのみ。仮想マシンで実行されている SQL を除く)。 |
| [SQL データベースでの透過的なデータ暗号化の有効化](security-center-enable-transparent-data-encryption.md) |SQL データベース (Azure SQL のサービスのみ) に対して暗号化を有効にすることをお勧めします。 |
| [Azure Storage アカウント暗号化の有効化](security-center-enable-encryption-for-storage-account.md) | Azure Storage Service Encryption for Data at Rest を有効化することを推奨します。 Storage Service Encryption (SSE) は、データが Azure ストレージに書き込まれたときに暗号化し、取得される前に復号化します。 現在、SSE は Azure Blob service のみに対応し、ブロック BLOB、ページ BLOB、追加 BLOB で使用できます。 詳細については、「[Storage Service Encryption for Data at Rest](../storage/storage-service-encryption.md)」を参照してください。</br>SSE は Resource Manager ストレージ アカウントでのみサポートされます。 現在、クラシック ストレージ アカウントはサポートされていません。 クラシック デプロイ モデルと Resource Manager デプロイ モデルについて理解するには、[Azure デプロイ モデル](../azure-classic-rm.md)に関する記事を参照してください。 |

## <a name="see-also"></a>関連項目
その他の Azure リソースの種類に適用される推奨事項の詳細については、次をご覧ください。

* [Azure Security Center での仮想マシンの保護](security-center-virtual-machine-recommendations.md)
* [Azure Security Center でのアプリケーションの保護](security-center-application-recommendations.md)
* [Azure Security Center でのネットワークの保護](security-center-network-recommendations.md)

セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md) 」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」-- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」-- このサービスの使用に関してよく寄せられる質問が記載されています。



<!--HONumber=Jan17_HO1-->


