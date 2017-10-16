---
title: "Azure Import-Export ツール - v1 の使用 | Microsoft Docs"
description: "Import-Export ツールを使用してインポート ジョブのハード ドライブを準備したり、インポート ジョブやエクスポート ジョブを修復する方法について説明します。"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f77535bb-d577-438a-bdd3-e15a82e0c543
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/15/2017
ms.author: muralikk
ms.openlocfilehash: 4ce2273cc0dcc456c2edc8c5dd2fc22496f20380
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
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

## <a name="next-steps"></a>次のステップ

* [Setting up the WAImportExport tool](../storage-import-export-tool-how-to.md) (WAImportExport ツールの設定)