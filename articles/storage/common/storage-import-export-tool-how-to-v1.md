---
title: Azure Import-Export ツール - v1 の使用 | Microsoft Docs
description: Import-Export ツールを使用してインポート ジョブのハード ドライブを準備したり、インポート ジョブやエクスポート ジョブを修復する方法について説明します。
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 1/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 4cd5fde2f47cca1b03efbc217d7764ac429d15fb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973936"
---
# <a name="using-the-azure-importexport-tool-classic-deployment-model"></a>Azure Import/Export ツールの使用 (クラシック デプロイ モデル)

Azure Import/Export ツール (WAImportExport.exe) は、Azure Import/Export サービスのジョブを作成、管理するために使用されます。これによりお客様は、Azure Blob Storage との間で大量のデータを転送できるようになります。

このドキュメントは、Azure Import/Export ツールのクラシック デプロイ モデルを対象としています。 同ツールの最新バージョンの使用方法については、「[Azure Import/Export ツールの使用](../storage-import-export-tool-how-to.md)」をご覧ください。

次の記事では、以下の方法について説明します。

- Import/Export ツールをインストールし設定する。
- お客様のドライブから Azure Blob Storage にデータをインポートするジョブのために、ハード ドライブを準備する。
- コピー ログ ファイルを使用して、ジョブの状態を確認する。 
- インポート ジョブを修復する。 
- エクスポート ジョブを修復する。 
- Azure Import/Export ツールのトラブルシューティングを行う (処理中に問題が発生した場合)。 

## <a name="next-steps"></a>次の手順

* [Setting up the WAImportExport tool](../storage-import-export-tool-how-to.md) (WAImportExport ツールの設定)
