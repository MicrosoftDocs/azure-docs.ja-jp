---
title: "Azure Stack Storage : 違いと考慮事項"
description: "Azure Stack のデプロイに関する考慮事項と一緒に、Azure Stack Storage と Azure Storage の相違点について説明します。"
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: 381950321ac3a5ea8a43b76f3fba868da4be4682
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Azure Stack Storage : 違いと考慮事項

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack Storage は、Microsoft Azure Stack 内のストレージ クラウド サービスのセットです。 Azure Stack Storage では、Azure と整合性のあるセマンティクスによって BLOB、テーブル、キュー、アカウント管理機能が提供されます。

この記事では、Azure Stack Storage と Azure Storage について明らかになっている相違点をまとめています。 また、Azure Stack をデプロイするときに留意するその他の考慮事項についてもまとめています。 Azure Stack と Azure の違いの概要については、[重要な考慮事項](azure-stack-considerations.md)のトピックを参照してください。

## <a name="cheat-sheet-storage-differences"></a>チート シート: ストレージの相違点

| 機能 | Azure (グローバル) | Azure Stack |
| --- | --- | --- |
|File Storage|クラウド ベースの SMB ファイル共有のサポート|まだサポートされていません
|保存データの暗号化|256 ビット AES 暗号化|まだサポートされていません
|ストレージ アカウントの種類|汎用アカウントと Azure Blob ストレージ アカウント|汎用目的のみ
|レプリケーション オプション|ローカル冗長ストレージ、geo 冗長ストレージ、読み取りアクセス geo 冗長ストレージ、およびゾーン冗長ストレージ|ローカル冗長ストレージ
|Premium Storage|完全にサポートされます|プロビジョニング可能ですがパフォーマンス制限や保証がありません
|管理ディスク|Premium および標準がサポートされます|まだサポートされていません
|BLOB 名|1,024 文字 (2,048 バイト)|880 文字 (1,760 バイト)
|ブロック BLOB の最大サイズ|4.75 TB (100 MB X 50,000 ブロック)|50,000 x 4 MB (約 195 GB)
|ページ BLOB の増分スナップショットのコピー|Premium および標準の Azure ページ BLOB がサポートされます|まだサポートされていません
|ページ BLOB の最大サイズ|8 TB|1 TB (テラバイト)
|ページ BLOB のページ サイズ|512 バイト|4 KB
|テーブルのパーティション キーと行キーのサイズ|1,024 文字 (2,048 バイト)|400 文字 (800 バイト)

### <a name="metrics"></a>メトリック
ストレージ メトリックにもいくつかの相違点があります。
* ストレージ メトリックのトランザクション データでは、内部と外部のネットワーク帯域幅が区別されません。
* ストレージ メトリックのトランザクション データには、マウントされたディスクへの仮想マシンのアクセスは含まれません。

## <a name="api-version"></a>API バージョン
Azure Stack Storage では以下のバージョンがサポートされます。

* Azure Storage データ サービス: [2015-04-05 REST API バージョン](https://docs.microsoft.com/rest/api/storageservices/Version-2015-04-05?redirectedfrom=MSDN)
* Azure Storage 管理サービス: [2015-05-01-preview、2015-06-15、および 2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN) 

## <a name="next-steps"></a>次のステップ

* [Azure Stack Storage の開発ツールの概要](azure-stack-storage-dev.md)
* [Azure Stack Storage の概要](azure-stack-storage-overview.md)

