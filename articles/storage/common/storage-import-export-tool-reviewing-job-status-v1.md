---
title: Azure Import/Export ジョブの状態の確認 - v1 | Microsoft Docs
description: インポート ジョブまたはエクスポート ジョブによって作成されたログ ファイルを使用してジョブの状態を確認する方法について説明します。
author: twooley
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/26/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: c9093af25e621b3ab9a60d9894f0a875340fda45
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88525671"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>コピー ログ ファイルによる Azure Import/Export ジョブの状態の確認
Microsoft Azure Import/Export サービスは、インポート ジョブまたはエクスポート ジョブに関連付けられたドライブを処理する場合に、BLOB のインポートまたはエクスポートに使用されたストレージ アカウントにコピー ログ ファイルを書き込みます。 ログ ファイルには、インポートまたはエクスポートされた各ファイルの状態の詳細が含まれます。 完了したジョブの状態を問い合わせると、サービスによって各コピー ログ ファイルの URL が返されます。 詳細については、[ジョブの取得](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get)に関するページを参照してください。  

## <a name="example-urls"></a>URL の例

2 つのドライブを使用したインポート ジョブのコピー ログ ファイルの URL の例は次の通りです。  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 コピー ログの形式とステータス コードの一覧は、「[Azure Import/Export サービスのログ ファイルの形式](../storage-import-export-file-format-log.md)」をご覧ください。  

## <a name="next-steps"></a>次のステップ

 * [Azure Import/Export ツールの設定](storage-import-export-tool-setup-v1.md)   
 * [インポート ジョブ用のハード ドライブを準備する](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [インポート ジョブの修復](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [エクスポート ジョブの修復](../storage-import-export-tool-repairing-an-export-job-v1.md)
