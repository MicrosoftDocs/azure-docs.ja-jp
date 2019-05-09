---
title: Azure Import/Export ジョブの診断とエラーからの回復 | Microsoft Docs
description: Microsoft Azure Import/Export サービス ジョブの詳細ログを有効にする方法について説明します。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 306b94fbe23e0ae92dcd59f7a87b7bb58ef7c3b6
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55454659"
---
# <a name="diagnostics-and-error-recovery-for-azure-importexport-jobs"></a>Azure Import/Export ジョブの診断とエラーからの回復
Azure Import/Export サービスでは、処理対象の各ドライブについて、関連付けられたストレージ アカウント内にエラー ログが作成されます。 [Put Job](/rest/api/storageimportexport/jobs) または [Update Job Properties](/rest/api/storageimportexport/jobs) 操作を呼び出すときに `LogLevel`プロパティを `Verbose` に設定すれば、詳細ログを有効にすることもできます。

 既定では、ログは `waimportexport` という名前のコンテナーに書き込まれます。 `Put Job` または `Update Job Properties` 操作を呼び出す際に `DiagnosticsPath` プロパティを設定することで、異なる名前を指定できます。 ログは、次の命名規則を使用してブロック BLOB として保存されます: `waies/jobname_driveid_timestamp_logtype.xml`。

 ジョブのログの URI は、[Get Job](/rest/api/storageimportexport/jobs) 操作を呼び出すことで取得できます。 詳細ログの URI は、各ドライブの `VerboseLogUri` プロパティで返されます (エラー ログの URI は、`ErrorLogUri` プロパティで返されます)。

ログ データは、次の問題を特定するために使用できます。

## <a name="drive-errors"></a>ドライブ エラー

ドライブ エラーとして分類される項目は次のとおりです。

-   マニフェスト ファイルへのアクセス時や読み取り時に発生したエラー

-   不適切な BitLocker キー

-   ドライブの読み取り/書き込みエラー

## <a name="blob-errors"></a>BLOB エラー

BLOB エラーとして分類される項目は次のとおりです。

-   不適切または競合する BLOB や名前

-   不足しているファイル

-   見つからない BLOB

-   切り捨てられたファイル (ディスク上のファイルが、マニフェストで指定されているよりも小さい)

-   破損したファイル コンテンツ (インポート ジョブの場合、MD5 チェックサムの不一致により検出)

-   破損した BLOB メタデータとプロパティ ファイル (MD5 チェックサムの不一致により検出)

-   BLOB プロパティ/メタデータ ファイルに対する不正なスキーマ

インポートまたはエクスポート ジョブの一部が正常に完了しなかった場合でも、全体的なジョブは完了する場合があります。 その場合は、データの不足部分をネットワーク経由でアップロードまたはダウンロードするか、新しいジョブを作成してデータを転送することができます。 ネットワーク経由でデータを修復する方法については、[Azure Import/Export ツールのリファレンス](storage-import-export-tool-how-to-v1.md)をご覧ください。

## <a name="next-steps"></a>次の手順

* [Import/Export サービス REST API の使用](storage-import-export-using-the-rest-api.md)
