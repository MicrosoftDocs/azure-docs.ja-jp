---
title: Azure Import/Export サービスの要件 | Microsoft Azure
description: Azure Import/Export サービスのソフトウェアとハードウェアの要件を理解します。
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 448d416ce4915aeae048639f6021197ed8c1d334
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967871"
---
# <a name="azure-importexport-system-requirements"></a>Azure Import/Export のシステム要件

この記事では、Azure Import/Export サービスの重要な要件について説明します。 Import/Export サービスを使用する前に以下の情報を確認し、操作中にも必要に応じて参照することをお勧めします。

## <a name="supported-operating-systems"></a>サポートされているオペレーティング システム

WAImportExport ツールを使用してハード ドライブを準備するために、以下の **BitLocker ドライブ暗号化をサポートする 64 ビット OS** がサポートされます。


|プラットフォーム |Version |
|---------|---------|
|Windows     | Windows 7 Enterprise、Windows 7 Ultimate <br> Windows 8 Pro、Windows 8 Enterprise、Windows 8.1 Pro、Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012、Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Windows クライアントに必要なその他のソフトウェア

|プラットフォーム |Version |
|---------|---------|
|.NET Framework    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>サポートされるストレージ アカウント

Azure Import/Export サービスでは、以下の種類のストレージ アカウントがサポートされます。

- Standard General Purpose v2 ストレージ アカウント (ほとんどのシナリオで推奨)
- BLOB ストレージ アカウント
- General Purpose v1 ストレージ アカウント (クラシックまたは Azure Resource Manager の両方のデプロイ) 

ストレージ アカウントについて詳しくは、「[Azure ストレージ アカウントの概要](storage-account-overview.md)」をご覧ください。

各ジョブを使用できるのは、1 つのストレージ アカウントとの間でのデータ転送だけです。 言い換えると、1 つのインポート/エクスポート ジョブを、複数のストレージ アカウントに対して使用することはできません。 新しいストレージ アカウントの作成については、「 [ストレージ アカウントの作成方法](storage-quickstart-create-account.md)」を参照してください。

> [!IMPORTANT] 
> Azure Import/Export サービスでは、[仮想ネットワーク サービス エンドポイント](../../virtual-network/virtual-network-service-endpoints-overview.md) 機能が有効になっているストレージ アカウントはサポートされません。 

## <a name="supported-storage-types"></a>サポートされるストレージの種類

Azure Import/Export サービスでは、次の一覧のストレージの種類をサポートされます。


|ジョブ  |ストレージ サービス |サポートされています  |サポートされていません  |
|---------|---------|---------|---------|
|[インポート]     |  Azure BLOB ストレージ <br><br> Azure File ストレージ       | ブロック BLOB と ページ BLOB をサポート <br><br> Files をサポート          |
|エクスポート     |   Azure BLOB ストレージ       | ブロック BLOB、ページ BLOB、および追加 BLOB をサポート         | Azure Files はサポートされない


## <a name="supported-hardware"></a>サポートされるハードウェア 

Azure Import/Export サービスでは、データのコピーをサポートしているディスクが必要です。

### <a name="supported-disks"></a>サポートされるディスク

Import/Export サービスでは、次のディスクの一覧の使用がサポートされます。


|ディスクの種類  |Size  |サポートされています |
|---------|---------|---------|
|SSD    |   2.5"      |SATA III          | 
|HDD     |  2.5"<br>3.5"       |SATA II、SATA III         |

次のディスクの種類はサポートされていません。
- USB。
- USB アダプターが組み込まれた外部 HDD。
- 外部 HDD のケース内にあるディスク。

1 つのインポート/エクスポート ジョブは、以下を使用できます。
- 最大 10 台の HDD/SSD。
- 任意のサイズの HDD/SSD の組み合わせ。

多数のドライブを複数のジョブに分散でき、また、作成可能なジョブの数に制限はありません。 インポート ジョブの場合は、ドライブの最初のデータ ボリュームだけが処理されます。 データ ボリュームは NTFS でフォーマットされている必要があります。

WAImportExport ツールを使用してハード ドライブの準備とデータのコピーを行うときに、外部 USB アダプターを使用できます。 市販の USB 3.0 以降のアダプターの大半が機能します。 


## <a name="next-steps"></a>次の手順

* [WAImportExport ツールを設定する](storage-import-export-tool-how-to.md)
* [AzCopy コマンド ライン ユーティリティを使ったデータの転送](storage-use-azcopy.md)
* [Azure Import Export REST API サンプル](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

