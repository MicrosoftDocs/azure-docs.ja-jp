---
title: "Azure Files のデプロイの計画 | Microsoft Docs"
description: "Azure Files のデプロイを計画するときの考慮事項について説明します。"
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: wgries
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 25d8f2bf5c42c54f1b9da330d7d3d6cbcc5dacb1
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="planning-for-an-azure-files-deployment"></a>Azure Files のデプロイの計画
[Azure Files](storage-files-introduction.md) はクラウドで、業界標準の SMB プロトコルを介してアクセスできる、完全に管理されたファイル共有を提供します。 Azure Files は完全に管理されているため、運用環境へのデプロイは、ファイル サーバーまたは NAS デバイスをデプロイして管理するよりはるかに簡単です。 この記事では、組織内で運用するために Azure ファイル共有をデプロイするときの考慮事項を説明します。

## <a name="management-concepts"></a>管理の概念
 次の図は、Azure Files の管理の構造を示しています。

![ファイル構造](./media/storage-files-introduction/files-concepts.png)

* **ストレージ アカウント**: Azure Storage にアクセスするときは必ずストレージ アカウントを使用します。 ストレージ アカウントの容量の詳細については、[拡張性とパフォーマンスのターゲット](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)に関するページを参照してください。

* **共有**: File Storage 共有は、Azure 内の SMB ファイル共有です。 ディレクトリとファイルはすべて親の共有に作成する必要があります。 アカウントに含まれる共有の数と、共有に格納できるファイル数には制限がなく、ファイル共有の合計容量 5 TiB まで増やすことができます。

* **ディレクトリ**: ディレクトリの階層 (オプション)。

* **ファイル**: 共有内のファイル。 ファイルのサイズの上限は 1 TiB です。

* **URL の形式**: ファイル REST プロトコルで Azure ファイル共有に対する要求を行う場合、次の URL 形式を使ってファイルをアドレス指定できます。

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/directories>/<file>
    ```

## <a name="data-access-method"></a>データ アクセスの方法
Azure Files には 2 つの便利なデータ アクセス方法が組み込まれており、単独で、または組み合わせて使って、データにアクセスできます。

1. **クラウドへの直接アクセス**: 業界標準のサーバー メッセージ ブロック (SMB) プロトコルまたはファイル REST API を使って、[Windows](storage-how-to-use-files-windows.md)、[macOS](storage-how-to-use-files-mac.md)、[Linux](storage-how-to-use-files-linux.md) で Azure ファイル共有をマウントできます。 SMB では、共有上のファイルの読み取り/書き込みは、Azure のファイル共有に対して直接行われます。 Azure の VM によってマウントするには、OS の SMB クライアントが少なくとも SMB 2.1 をサポートしている必要があります。 ユーザーのワークステーションなどのオンプレミスにマウントするには、ワークステーションでサポートされている SMB クライアントが、少なくとも SMB 3.0 (暗号化付き) をサポートしている必要があります。 SMB に加えて、新しいアプリケーションまたはサービスはファイル REST を使ってファイル共有に直接アクセスできます。ファイル REST は、簡単でスケーラブルなソフトウェア開発用アプリケーション プログラミング インターフェイスを備えています。
2. **Azure ファイル同期** (プレビュー): Azure ファイル同期を使うと、オンプレミスまたは Azure の Windows Server に共有をレプリケートできます。 ユーザーは、SMB や NFS 共有などを使って Windows Server 経由でファイル共有にアクセスします。 この方法は、ブランチ オフィスなどの Azure データ センターから離れた場所にあるデータにアクセスして変更する場合に便利です。 複数のブランチ オフィス間など、複数の Windows Server エンドポイント間でデータをレプリケートできます。 最後に、Azure Files にデータを階層化できます。このようにすると、Server 経由ですべてのデータにアクセスできることは変わりませんが、Server にデータが完全にコピーされることはありません。 ユーザーがファイルを開くと、データはシームレスに回収されます。

次の表では、ユーザーおよびアプリケーションが Azure ファイル共有にアクセスできる方法を示します。

| | クラウドへの直接アクセス | Azure ファイル同期 |
|------------------------|------------|-----------------|
| 使う必要があるプロトコル | Azure Files は、SMB 2.1、SMB 3.0、ファイル REST API をサポートします。 | Windows Server でサポートされているプロトコル (SMB、NFS、FTPS など) を使って、Azure ファイル共有にアクセスします。 |  
| ワークロードを実行する場所 | **Azure 内**: Azure Files はデータへの直接アクセスを提供します。 | **低速ネットワークのオンプレミス**: Windows、Linux、および macOS のクライアントは、ローカルなオンプレミスの Windows ファイル共有を、Azure ファイル共有の高速キャッシュとしてマウントすることができます。 |
| 必要な ACL のレベル | 共有とファイルのレベル。 | 共有、ファイル、ユーザーのレベル。 |

## <a name="data-security"></a>データのセキュリティ
Azure Files には、データのセキュリティを確保するための複数の組み込みオプションがあります。

* ネットワーク プロトコルでの暗号化のサポート: SMB 3.0 暗号化と HTTPS 経由のファイル REST の両方。 既定での動作は次のとおりです。 
    * SMB 3.0 暗号化をサポートするクライアントは、暗号化されたチャネルでデータを送受信します。
    * SMB 3.0 をサポートしていないクライアントは、暗号化を行わない SMB 2.1 または SMB 3.0 経由でデータ センター内の通信を行うことができます。 クライアントは暗号化なしで SMB 2.1 または SMB 3.0 を使ってデータ センター間通信をできないことに注意してください。
    * クライアントは、HTTP または HTTPS を使ってファイル REST 経由で通信できます。
* オプションの転送中のデータの暗号化要件: 選択すると、Azure Files は暗号化されていないチャネルでのデータへのアクセスを許可しません。 具体的には、暗号化接続を使う HTTPS と SMB 3.0 だけが許可されます。 

    > [!Important]  
    > データのセキュリティで保護された転送を要求すると、暗号化ありで SMB 3.0 と通信する機能のない古い SMB クライアントは失敗します。 詳しくは、[Windows でのマウント](storage-how-to-use-files-windows.md)、[Linux でのマウント](storage-how-to-use-files-linux.md)、[macOS でのマウント](storage-how-to-use-files-mac.md)に関するページをご覧ください。

最大限のセキュリティのため、保存時の暗号化と、最新のクライアントを使ってデータにアクセスするときの転送中のデータの暗号化を、両方とも常に有効にすることを強くお勧めします。 たとえば、SMB 2.1 のみをサポートする Windows Server 2008 R2 VM に共有をマウントする必要がある場合は、SMB 2.1 は暗号化をサポートしていないため、ストレージ アカウントへの暗号化されていないトラフィックを許可する必要があります。

Azure ファイル同期を使って Azure ファイル共有にアクセスする場合は、保存時のデータの暗号化が要求されているかどうかにかかわらず、Windows Server へのデータの同期には、暗号化ありの HTTPS と SMB 3.0 が常に使われます。

## <a name="data-redundancy"></a>データの冗長性
Azure Files は、データ冗長性オプションとして、ローカル冗長ストレージ (LRS) と geo 冗長ストレージ (GRS) の 2 つをサポートします。 次のセクションでは、ローカル冗長ストレージと geo 冗長ストレージの違いについて説明します。

### <a name="locally-redundant-storage"></a>ローカル冗長ストレージ
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="geo-redundant-storage"></a>geo 冗長ストレージ
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="data-growth-pattern"></a>データ増加パターン
現在、Azure ファイル共有の最大サイズは、共有スナップショットを含めて 5 TiB です。 この現在の制限のため、Azure ファイル共有をでデプロイするときは、予想されるデータの増加を考慮する必要があります。 Azure Storage アカウントは複数の共有を格納でき、すべての共有で合計 500 TiB を保持できることに注意してください。

Azure ファイル同期を使って複数の Azure ファイル共有を 1 つの Windows ファイル サーバーに同期することができます。これにより、オンプレミスにある古くて非常に大きいファイル共有を Azure ファイル同期に取り込むことができます。詳しくは、「[Azure Files のデプロイの計画](storage-files-planning.md)」をご覧ください。

## <a name="data-transfer-method"></a>データ転送方法
オンプレミスのファイル共有などの既存のファイル共有から Azure Files にデータを一括転送するには、多くの簡単なオプションがあります。 以下は一般的な方法の一部です (すべてではありません)。

* **Azure ファイル同期**: Azure ファイル共有 ("クラウド エンドポイント") と Windows ディレクトリ名前空間 ("サーバー エンドポイント") の間での最初の同期の一部として、Azure ファイル同期はすべてのデータを既存のファイル共有から Azure Files にレプリケートします。
* **[Azure Import/Export](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: Azure Import/Export サービスを使うと、ハード ディスク ドライブを Azure データ センターに送付することで、大量のデータを Azure ファイル共有に安全に転送できます。 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**: Robocopy は、Windows および Windows Server に付属するよく知られたコピー ツールです。 Robocopy では、ファイル共有をローカルにマウントした後、マウントした場所を Robocopy コマンドのコピー先として使って、Azure Files にデータを転送できます。
* **[AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#upload-files-to-an-azure-file-share)**: AzCopy は、最高のパフォーマンスの単純なコマンドを使って Azure Files および Azure Blob Storage との間で双方向にデータをコピーするために設計された、コマンドライン ユーティリティです。 AzCopy は Windows と Linux で使うことができます。

## <a name="next-steps"></a>次のステップ
* [Azure File Sync のデプロイの計画](storage-sync-files-planning.md)
* [Azure Files のデプロイ方法](storage-files-deployment-guide.md)
* [Azure File Sync のデプロイ方法](storage-sync-files-deployment-guide.md)
