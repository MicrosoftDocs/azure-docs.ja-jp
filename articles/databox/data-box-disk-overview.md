---
title: Microsoft Azure Data Box Disk の概要 | Microsoft Docs in data
description: 大量のデータを Azure に転送できるクラウド ソリューションである Azure Data Box Disk について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 06/18/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand what Data Box Disk is and how it works so I can use it to import on-premises data into Azure.
ms.openlocfilehash: ea9353ed8c1938fa9b33585a0650b4507c671451
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92125032"
---
# <a name="what-is-azure-data-box-disk"></a>Azure Data Box Disk とは

Microsoft Azure Data Box Disk ソリューションを使用すると、迅速かつ安価な信頼性の高い方法で、数テラバイトのオンプレミス データを Azure に送信できます。 1 から 5 台のソリッドステート ディスク (SSD) を出荷することで、安全なデータ転送が促進されます。 これらの暗号化された 8 TB ディスクは、地域の運送業者を通じてデータセンターに届けられます。

Azure portal で Data Box サービスを使用して、ディスクの構成、接続、ロック解除をすばやく行うことができます。 データをディスクにコピーしたら、ディスクを Azure に返送します。 Azure データセンターでは、高速のプライベート ネットワーク アップロード リンクを使用して、データがドライブからクラウドに自動的にアップロードされます。

## <a name="use-cases"></a>ユース ケース

ネットワーク接続が制限されていないシナリオで、Data Box Disk を使用して数 TB のデータを転送します。 データは 1 回だけ移動することも、定期的に移動することもできます。また、初期一括データ転送の後に定期的な転送を行うこともできます。

- **1 回限りの移行** - 大量のオンプレミス データを Azure に移動する場合。 たとえば、オフライン テープのデータをアーカイブ データとして Azure クール ストレージに移動します。
- **増分転送** - Data Box Disk (シード) を使用して初期一括転送が実行された後に、ネットワーク経由で増分転送を行う場合。 たとえば、Commvault と Data Box Disk を使用して、バックアップ コピーを Azure に移動します。 この移行の後、増分データをネットワーク経由で Azure Storage にコピーします。
- **定期的なアップロード** - 定期的に生成される大量のデータを Azure に移動する必要がある場合。 たとえば、石油掘削装置や風力発電地帯でビデオ コンテンツが生成されるエネルギー探査でこれを実行します。

### <a name="ingestion-of-data-from-data-box"></a>Data Box からのデータの取り込み

Azure プロバイダーと非 Azure プロバイダーは、Azure Data Box からデータを取り込むことができます。 Azure Data Box からのデータ インジェストを提供する Azure サービスの例を次に示します。

- **SharePoint Online** - Azure Data Box と SharePoint 移行ツール (SPMT) を使用して、ファイル共有の内容を SharePoint Online に移行します。 Data Box を使用することで、WAN リンク経由でデータを転送する必要がなくなります。 詳細については、「[Azure Data Box Heavy を使用してファイル共有のコンテンツを SharePoint Online に移行する](data-box-heavy-migrate-spo.md)」を参照してください。

- **Azure File Sync** -  Data Box から Azure ファイル共有にファイルをレプリケートします。データへのローカル アクセスを維持すると共に、Azure におけるファイル サービスを一元化することができます。 詳細については、「[Azure File Sync のデプロイ](../storage/files/storage-sync-files-deployment-guide.md)」を参照してください。

- **HDFS ストア** - Data Box を使用して、Hadoop クラスターのオンプレミス Hadoop 分散ファイル システム (HDFS) ストアから Azure Storage にデータを移行します。 詳細については、「[Azure Data Box を使用してオンプレミスの HDFS ストアから Azure Storage に移行する](../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md)」を参照してください。

- **Azure Backup** - 重要なエンタープライズ データの大規模なバックアップをオフライン メカニズムを使用して Azure Recovery Services コンテナーに移動することができます。 詳細については、[Azure Backup の概要](../backup/backup-overview.md)に関するページを参照してください。

Data Box のデータは、Azure サービス以外の多くのプロバイダーでご利用いただけます。 次に例を示します。

- **[Commvault](http://documentation.commvault.com/commvault/v11/article?p=97276.htm)** - Azure Data Box を使用して大量のデータを Microsoft Azure に移行できます。
- **[Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/osr_adding_data_box.html?ver=100)** - Hyper-V マシンから Data Box に大量のデータをバックアップしたりレプリケートしたりすることができます。

その他、Data Box と連動する非 Azure サービス プロバイダーの一覧については、「[Azure Data Box パートナー](https://cloudchampions.blob.core.windows.net/db-partners/PartnersTable.pdf)」を参照してください。

## <a name="the-workflow"></a>ワークフロー

一般的なフローには次の手順が含まれます。

1. **注文** - Azure portal で注文を作成し、配送情報とデータのコピー先 Azure Storage アカウントを指定します。 ディスクが利用可能な場合は、Azure によってディスクが暗号化され、準備されて、出荷追跡 ID が割り当てられたディスクが出荷されます。

2. **受け取り** - ディスクが届いたら、梱包を解き、コピーするデータがあるコンピューターにディスクを接続します。 ディスクのロックを解除します。

3. **データのコピー** - ドラッグ アンド ドロップして、データをディスクにコピーします。

4. **返却** - ディスクを準備して、Azure データセンターに返送します。

5. **アップロード** - ディスクから Azure にデータが自動的にコピーされます。 ディスクは、米国国立標準技術研究所 (NIST) のガイドラインに従って安全に消去されます。

このプロセス全体を通じて、状態のすべての変更について電子メールで通知されます。 フローの詳細については、[Azure portal での Data Box Disk の展開](data-box-disk-quickstart-portal.md)に関する記事をご覧ください。

## <a name="benefits"></a>メリット

Data Box Disk は、ネットワークに影響を与えずに大量のデータを Azure に移動することを目的としています。 このソリューションには次の利点があります。

- **速度** - Data Box Disk では、USB 3.0 接続を使用して、最大 35 TB のデータを 1 週間足らずで Azure に移動します。

- **使いやすさ** - Data Box は使いやすいソリューションです。

  - ディスクでは、セットアップ時間をほとんど必要としない USB 接続が使用されます。
  - ディスクは扱いやすい小型フォーム ファクターです。
  - ディスクには外部電源要件はありません。
  - ディスクは、データセンター サーバー、デスクトップ、またはノート PC で使用できます。
  - このソリューションでは、Azure portal を使用してエンド ツー エンドの追跡が行われます。

- **セキュリティ保護** - Data Box Disk には、ディスク、データ、サービスのセキュリティ保護が組み込まれています。
  - ディスクは改ざん防止機能を備え、セキュリティ保護された更新機能をサポートしています。
  - ディスク上のデータは、AES 128 ビット暗号化によって常にセキュリティ保護されています。
  - ディスクは、Azure portal で提供されるキーでのみロックを解除できます。
  - このサービスは、Azure のセキュリティ機能によって保護されています。
  - データが Azure にアップロードされたら、NIST 800-88r1 規格に従って、ディスクが完全にワイプされます。  

詳細については、[Azure Data Box Disk のセキュリティとデータ保護](data-box-disk-security.md)に関する記事をご覧ください。

## <a name="features-and-specifications"></a>機能と仕様

| 仕様                                          | 説明              |
|---------------------------------------------------------|--------------------------|
| Weight                                                  | ボックスあたり 約 900 g (2 lbs.) 未満。 ボックスに最大 5 台のディスク                |
| Dimensions                                              | ディスク - 2.5 インチ SSD |
| ケーブル                                                  | ディスクごとに USB 3.1 ケーブルが 1 本|
| 1 注文あたりのストレージ容量                              | 40 TB (使用可能な容量は最大 35 TB)|
| ディスク ストレージ容量                                   | 8 TB (使用可能な容量は最大 7 TB)|
| データ インターフェイス                                          | USB   |
| Security                                                | BitLocker とセキュリティ保護された更新を使用して事前に暗号化 <br> パスキーでディスクを保護 <br> データを常に暗号化  |
| データ転送速度                                      | ファイル サイズに応じて最大 430 MBps      |
|管理                                               | Azure portal |

## <a name="region-availability"></a>利用可能なリージョン

リージョン別の提供状況については、[リージョン別の利用可能な Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)に関する記事を参照してください。 Data Box Disk は、Azure Government クラウドにデプロイすることもできます。 詳細については、「[What is Azure Government? (Azure Government とは)](../azure-government/documentation-government-welcome.md)」を参照してください。

## <a name="pricing"></a>価格

価格については、[価格に関するページ](https://azure.microsoft.com/pricing/details/databox/disk/)を参照してください。

## <a name="next-steps"></a>次のステップ

- [Data Box Disk の要件](data-box-disk-system-requirements.md)を確認する。
- [Data Box Disk の制限事項](data-box-disk-limits.md)を理解する。
- Azure portal で [Azure Data Box Disk](data-box-disk-quickstart-portal.md) をすばやく展開する。