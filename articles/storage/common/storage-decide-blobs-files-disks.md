---
title: "Azure BLOB、Azure Files、Azure データ ディスクの使い分け"
description: "Azure にデータを格納したりそのデータにアクセスしたりするための各種の方法とテクノロジの使い分けのヒントについて説明します。"
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: robinsh
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 452030e2e55ebeae55be2bd858c59e45428c7621
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---

# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-data-disks"></a>Azure BLOB、Azure Files、Azure データ ディスクの使い分け

Microsoft Azure の Azure Storage には、クラウドにデータを格納したりそのデータにアクセスしたりするためのいくつかの機能が用意されています。 この記事では、Azure Files、Azure BLOB、Azure データ ディスクについて取り上げると共に、これらの機能をうまく使い分けるためのヒントを紹介しています。

## <a name="scenarios"></a>シナリオ

次の表は、Azure Files、Azure BLOB、Azure データ ディスクの比較と、それぞれの機能に適したシナリオの例です。

| 機能 | Description | 使用時の注意 |
|--------------|-------------|-------------|
| **Azure Files** | 格納されているファイルにどこからでもアクセスできる [REST インターフェイス](/rest/api/storageservices/file-service-rest-api)、SMB インターフェイス、クライアント ライブラリを備えています。 | 既にネイティブ ファイル システム API を使用し、Azure で稼働している他のアプリケーションとの間でデータを共有しているアプリケーションをクラウドに "リフト アンド シフト" する。<br/><br/>多くの仮想マシンからアクセスする必要のある開発ツールとデバッグ ツールを格納する。 |
| **Azure BLOB** | 大規模な非構造化データをブロック BLOB に格納してアクセスできる [REST インターフェイス](/rest/api/storageservices/blob-service-rest-api)とクライアント ライブラリを備えています。 | アプリケーションでストリーミングとランダム アクセスのシナリオに対応する。<br/><br/>アプリケーションのデータにどこからでもアクセスできるようにする。 |
| **Azure データ ディスク** | アタッチされた仮想ハード ディスクにデータを永続的に格納し、アクセスできる [REST インターフェイス](/rest/api/compute/virtualmachines/virtualmachines-create-or-update)とクライアント ライブラリを備えています。 | 永続ディスクに対しネイティブ ファイル システム API を使用してデータの読み取りと書き込みを行うアプリケーションをリフト アンド シフトする。<br/><br/>外部 (ディスクがアタッチされている仮想マシンの外) からのアクセスが不要なデータを格納する。 |

## <a name="comparison-files-and-blobs"></a>比較: Azure Files と Azure BLOB

次の表で Azure Files と Azure BLOB を比較します。  
  
||||  
|-|-|-|  
|**属性**|**Azure BLOB**|**Azure Files**|  
|持続性オプション|LRS、ZRS、GRS (さらに高い可用性に対応した RA-GRS も含む)|LRS、GRS|  
|アクセシビリティ|REST API|REST API<br /><br /> SMB 2.1 と SMB 3.0 (標準的なファイル システムの API)|  
|接続|REST API -- ワールドワイド|REST API -- ワールドワイド<br /><br /> SMB 2.1 -- リージョン内<br /><br /> SMB 3.0 -- ワールドワイド|  
|エンドポイント|`http://myaccount.blob.core.windows.net/mycontainer/myblob`|`\\myaccount.file.core.windows.net\myshare\myfile.txt`<br /><br /> `http://myaccount.file.core.windows.net/myshare/myfile.txt`|  
|ディレクトリ|フラットな名前空間|純粋なディレクトリ オブジェクト|  
|名前の大文字と小文字の区別|大文字小文字は区別される|大文字小文字は区別されないが、保持される|  
|容量|最大 500 TB のコンテナー|5 TB のファイル共有|  
|スループット|ブロック BLOB あたり最大 60 MB/秒|共有あたり最大 60 MB/秒|  
|オブジェクト サイズ|ブロック BLOB あたり最大 200 GB|ファイルあたり最大 1 TB|  
|課金対象の容量|書き込みバイト数に基づく|ファイル サイズに基づく|  
|クライアント ライブラリ|複数言語|複数言語|  
  
## <a name="comparison-files-and-data-disks"></a>比較: Azure Files と Azure データ ディスク

Azure Files は Azure データ ディスクを補完するものです。 データ ディスクは、同時に複数の Azure 仮想マシンにアタッチすることができません。 データ ディスクは、Azure Storage にページ BLOB として格納される固定形式の VHD であり、仮想マシンで永続的データを格納する際に使用されます。 Azure Files 内のファイル共有は、ローカル ディスクに (ネイティブ ファイル システム API を使って) アクセスするときと同じ方法でアクセスでき、多数の仮想マシンの間で共有することができます。  
 
次の表で Azure Files と Azure データ ディスクを比較します。  
 
||||  
|-|-|-|  
|**属性**|**Azure データ ディスク**|**Azure Files**|  
|スコープ|1 台の仮想マシン限定|複数の仮想マシンの間で共有アクセス|  
|スナップショットとコピー|あり|いいえ|  
|構成|仮想マシンの起動時に接続|仮想マシンの起動後に接続|  
|認証|ビルトイン|net use で設定|  
|クリーンアップ|自動|マニュアル|  
|REST を使用したアクセス|VHD 内のファイルにはアクセス不可|共有場所に格納されたファイルにアクセス可|  
|最大サイズ|1 TB ディスク|5 TB のファイル共有と 1 TB のファイル (共有内)|  
|最大 8 KB IOPS|500 IOPS|1,000 IOPS|  
|スループット|ディスクあたり最大 60 MB/秒|ファイル共有あたり最大 60 MB/秒|  

## <a name="next-steps"></a>次のステップ

データの格納方法とアクセス方法を決めるときには、それに伴うコストも考慮する必要があります。 詳細については、[Azure Storage の価格](https://azure.microsoft.com/pricing/details/storage/)に関するページを参照してください。
  
一部の SMB 機能はクラウドでは利用できません。 詳細については、「[Features not supported by the Azure File service (Azure File Service でサポートされていない機能)](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)」を参照してください。
  
データ ディスクの詳細については、[ディスクとイメージの管理](../../virtual-machines/windows/about-disks-and-vhds.md)に関するページと[データ ディスクを Windows 仮想マシンにアタッチする方法](../../virtual-machines/windows/classic/attach-disk.md)に関するページを参照してください。
