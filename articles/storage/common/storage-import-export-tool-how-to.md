---
title: Azure Import/Export ツールの使用 | Microsoft Docs
description: Import-Export ツールを使用してインポート ジョブのハード ドライブを準備したり、インポート ジョブやエクスポート ジョブを修復する方法について説明します。
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 19614570369e3814658f9ca6e50635507f094712
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977320"
---
# <a name="using-the-azure-importexport-tool"></a>Azure Import/Export ツールの使用 

Azure Import/Export ツール (WAImportExport.exe) は、Azure Import/Export サービスのジョブを作成、管理するために使用されます。これによりお客様は、Azure Blob Storage との間で大量のデータを転送できるようになります。

このドキュメントは、Azure Import/Export ツールの最新バージョンを対象としています。 クラシック デプロイ モデルの使用方法については、「[Azure Import/Export ツール (v1) の使用](storage-import-export-tool-how-to-v1.md)」をご覧ください。

次の記事では、以下の方法について説明します。  

- Azure Import/Export ツールをインストールし設定する。
- お客様のドライブから Azure Blob Storage にデータをインポートするジョブのために、ハード ドライブを準備する。
- コピー ログ ファイルを使用して、ジョブの状態を確認する。 
- インポート ジョブを修復する。 
- エクスポート ジョブを修復する。 
- Azure Import/Export ツールのトラブルシューティングを行う。 

## <a name="next-steps"></a>次の手順

* [Setting up the WAImportExport tool](storage-import-export-tool-setup.md) (WAImportExport ツールの設定)
