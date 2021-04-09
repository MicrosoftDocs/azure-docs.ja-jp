---
title: Microsoft Azure Data Box Disk のシステム要件| Microsoft Docs
description: Azure Data Box Disk のソフトウェア要件とネットワーキング要件について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 02/22/2021
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 750ed8f65db04199ea284e69693bced65a1dc8d9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703135"
---
::: zone target="docs"

# <a name="azure-data-box-disk-system-requirements"></a>Azure Data Box Disk のシステム要件

この記事では、Microsoft Azure Data Box Disk ソリューション、および Data Box Disk に接続するクライアントのシステム要件のうち、重要なものについて説明します。 この情報は、Data Box Disk をデプロイする前によく確認し、デプロイ時やそれ以降の操作時にも、必要に応じて繰り返し参照することをお勧めします。

システム要件には、ディスクに接続するクライアントでサポートされるプラットフォーム、サポートされるストレージ アカウント、およびストレージの種類が含まれます。

::: zone-end

::: zone target="chromeless"

## <a name="review-prerequisites"></a>前提条件を確認する

1. 次を使用して Data Box Disk を注文済みであること。「[チュートリアル: Azure Data Box Disk を注文する](data-box-disk-deploy-ordered.md)」 ディスクと、ディスクごとに 1 つの接続ケーブルを受けとっていること。
2. データのコピー元として使用できるクライアント コンピューターがあること。 クライアント コンピューターの要件は以下のとおりです。

    - サポート対象のオペレーティング システムが実行されていること。
    - その他の必須ソフトウェアがインストールされていること。

::: zone-end

## <a name="supported-operating-systems-for-clients"></a>クライアントでサポートされるオペレーティング システム

Data Box Disk に接続するクライアント経由でディスクのロック解除、およびデータのコピー操作を行う場合にサポートされるオペレーティング システムの一覧を次に示します。

| **オペレーティング システム** | **テスト済みのバージョン** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows (64 ビット) |7、8、10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04、16.04、18.04 <br> 8.11、9 <br> 7.0 <br> 6.5、6.9、7.0、7.5 |  

## <a name="other-required-software-for-windows-clients"></a>Windows クライアントに必要なその他のソフトウェア

Windows クライアントでは、次のものもインストールする必要があります。

| **ソフトウェア**| **Version** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |5.1|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Linux クライアントに必要なその他のソフトウェア

Linux クライアントでは、Data Box Disk のツールセットが次の必要なソフトウェアをインストールします:

- dislocker
- OpenSSL

## <a name="supported-connection"></a>サポートされる接続

データを格納するクライアント コンピューターには、USB 3.0 またはそれ以降のポートが必要です。 提供されているケーブルを使用して、ディスクをこのクライアントに接続します。

## <a name="supported-storage-accounts"></a>サポートされるストレージ アカウント

Data Box Disk でサポートされるストレージの種類の一覧を次に示します。

| **ストレージ アカウント** | **メモ** |
| --- | --- |
| クラシック | Standard |
| General Purpose  |標準。V1 と V2 の両方がサポートされます。 また、ホット層とクール層の両方がサポートされます。 |
| BLOB ストレージ アカウント | |

> [!IMPORTANT]
> Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポートは、Data Box Disk ではサポートされていません。

## <a name="supported-storage-types-for-upload"></a>アップロード用にサポートされているストレージの種類

以下は、Data Box Disk を使用した Azure へのアップロードのためにサポートされているストレージの種類の一覧です。

| **ファイル形式** | **メモ** |
| --- | --- |
| Azure ブロック BLOB | |
| Azure ページ BLOB  | |
| Azure Files  | |
| Managed Disks | |

::: zone target="docs"

## <a name="next-step"></a>次のステップ

* [Azure Data Box Disk をデプロイする](data-box-disk-deploy-ordered.md)

::: zone-end

