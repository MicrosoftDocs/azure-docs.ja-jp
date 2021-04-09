---
title: SAP HANA バックアップのサポート マトリックス
description: この記事では、Azure Backup を使用して Azure VM 上の SAP HANA データベースをバックアップするときにサポートされるシナリオと制限事項について説明します。
ms.topic: conceptual
ms.date: 11/7/2019
ms.custom: references_regions
ms.openlocfilehash: cbf910a0291e90965c9698a8b2a43c587cbfe0b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101707236"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Azure VM 上の SAP HANA データベースのバックアップに関するサポート マトリックス

Azure Backup では、Azure への SAP HANA データベースのバックアップがサポートされます。 この記事では、Azure Backup を使用して Azure VM 上の SAP HANA データベースをバックアップするときにサポートされるシナリオと適用される制限事項についてまとめます。

> [!NOTE]
> ログ バックアップの頻度を最大 15 分ごとに設定できるようになりました。 ログ バックアップでは、データベースの完全バックアップが正常に完了した後にのみ、フローが開始されます。

## <a name="scenario-support"></a>シナリオのサポート

| **シナリオ**               | **サポートされている構成**                                | **サポートされていない構成**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **トポロジ**               | Azure Linux VM 実行されている SAP HANA のみ                    | HANA ラージ インスタンス (HLI)                                   |
| **リージョン**                   | **一般公開:**<br> **アメリカ** – 米国中部、米国東部 2、米国東部、米国中北部、米国中南部、米国西部 2、米国中西部、米国西部、カナダ中部、カナダ東部、ブラジル南部 <br> **アジア太平洋** – オーストラリア中部、オーストラリア中部 2、オーストラリア東部、オーストラリア南東部、東日本、西日本、韓国中部、韓国南部、東アジア、東南アジア、インド中部、インド南部、インド西部、中国東部、中国北部、中国東部 2、中国北部 2 <br> **ヨーロッパ** – 西ヨーロッパ、北ヨーロッパ、フランス中部、英国南部、英国西部、ドイツ北部、ドイツ中西部、スイス北部、スイス西部、中央スイス北部、ノルウェー東部、ノルウェー西部 <br> **アフリカ/中東** – 南アフリカ北部、南アフリカ西部、アラブ首長国連邦北部、アラブ首長国連邦中部  <BR>  **Azure Government リージョン** | フランス南部、ドイツ中部、ドイツ北東部、US Gov アイオワ |
| **OS のバージョン**            | SLES 12 SP2、SP3、SP4、SP5、SLES 15 SP0、SP1、SP2 <br><br>  RHEL 7.4、7.6、7.7、8.1、および 8.2                |                                             |
| **HANA のバージョン**          | HANA 1.x の場合は SDC、HANA 2.x の SPS04、SPS05 Rev 53 以下の場合は MDC (暗号化が有効なシナリオについても検証済み)      |                                                            |
| **HANA のデプロイメント**       | 単一の Azure VM 上の SAP HANA - スケールアップのみ。 <br><br> 高可用性のデプロイでは、2 つの異なるマシン上の両方のノードが、個別のデータ チェーンを持つ個々のノードとして扱われます。               | スケールアウト <br><br> 高可用性のデプロイでは、バックアップはセカンダリ ノードに自動的にフェールオーバーされません。 バックアップの構成は、ノードごとに個別に行う必要があります。                                           |
| **HANA インスタンス**         | 単一の Azure VM 上の単一の SAP HANA インスタンス – スケールアップのみ | 単一の VM 上の複数の SAP HANA インスタンス。 これらの複数のインスタンスは一度に 1 つしか保護できません。                  |
| **HANA データベースの種類**    | 1\.x の場合は SDC (Single Database Container)、2.x の場合は MDC (Multi-Database Container) | HANA 1.x での MDC                                              |
| **HANA データベースのサイズ**     | HANA データベースのサイズ <= 8 TB (これは HANA システムのメモリ サイズではありません)               |                                                              |
| **バックアップの種類**           | 完全バックアップ、差分バックアップ、増分バックアップ、ログ バックアップ                          |  スナップショット                                       |
| **復元の種類**          | サポートされている復元の種類については、SAP HANA ノート [1642148](https://launchpad.support.sap.com/#/notes/1642148) を参照してください |                                                              |
| **バックアップの制限**          | SAP HANA インスタンスごとに最大 8 TB の完全バックアップ サイズ (ソフト制限)         |                                                              |
| **特殊な構成** |                                                              | SAP HANA + Dynamic Tiering <br>  LaMa を使用した複製        |

------

>[!NOTE]
>Azure Backup では、Azure VM で実行されている SAP HANA データベースをバックアップしている場合、夏時間変更に合わせた自動調整は行われません。
>
>必要に応じて手動でポリシーを変更してください。

> [!NOTE]
> Azure portal で、HANA ネイティブ クライアント (SAP HANA Studio、コックピット、DBA コックピット) からトリガーされた[バックアップ ジョブと (同じマシンへの) 復元ジョブを監視](./sap-hana-db-manage.md#monitor-manual-backup-jobs-in-the-portal)できるようになりました。

## <a name="next-steps"></a>次のステップ

* [Azure VM で稼働している SAP HANA データベースをバックアップする](./backup-azure-sap-hana-database.md)方法を学習する
* [Azure VM で稼働している SAP HANA データベースを復元する](./sap-hana-db-restore.md)方法を学習する
* [Azure Backup を使用してバックアップされた SAP HANA データベースを管理する](sap-hana-db-manage.md)方法を学習する
* [SAP HANA データベースのバックアップ時に発生する一般的な問題をトラブルシューティングする](./backup-azure-sap-hana-database-troubleshoot.md)方法を学習する
