---
title: コンテンツ メタデータ プロパティ
titleSuffix: Azure Cognitive Search
description: ドキュメントのメタデータ プロパティを使用すると、検索インデックス内のフィールドにコンテンツを提供したり、実行時にインデックス作成動作を通知する情報を提供したりすることができます。 この記事では、Azure Cognitive Search でサポートされているメタデータ プロパティの一覧を示します。
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/22/2021
ms.openlocfilehash: cbb35f596a1d32816d1a73b462bf590d9dde0d52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668420"
---
# <a name="content-metadata-properties-used-in-azure-cognitive-search"></a>Azure Cognitive Search で使用されるコンテンツ メタデータ プロパティ

SharePoint Online と Azure Blob Storage にはさまざまなコンテンツを含めることができ、それらのコンテンツの種類の多くには、インデックス作成に役立つメタデータ プロパティがあります。 **`metadata_storage_name`** などの標準 BLOB プロパティの検索フィールドを作成するのと同じように、ドキュメント形式に固有のメタデータ プロパティのフィールドを作成できます。

## <a name="supported-document-formats"></a>サポートされるドキュメントの形式

Cognitive Search では、次のドキュメント形式の BLOB のインデックス作成と SharePoint Online ドキュメントのインデックス作成がサポートされています。

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="properties-by-document-format"></a>ドキュメント形式別のプロパティ

以下の表は、各ドキュメント形式に関して実行される処理と、BLOB インデクサーおよび SharePoint Online インデクサーによって抽出されるメタデータ プロパティをまとめたものです。

| ドキュメントの形式/コンテンツの種類 | 抽出されるメタデータ | 処理の詳細 |
| --- | --- | --- |
| HTML (text/html) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |HTML マークアップを削除し、テキストを抽出します。 |
| PDF (application/pdf) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |テキストを抽出します。埋め込みドキュメントも対象となります (画像を除く)。 |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| DOCM (application/vnd.ms-word.document.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| WORD XML (application/vnd.ms-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |XML マークアップを削除し、テキストを抽出します。 |
| WORD 2003 XML (application/vnd.ms-wordml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |XML マークアップを削除し、テキストを抽出します。 |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| XLSM (application/vnd.ms-excel.sheet.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| PPTM (application/vnd.ms-powerpoint.presentation.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |テキスト (添付ファイルから抽出されたテキストを含む) を抽出します。 `metadata_message_to_email`、`metadata_message_cc_email`、`metadata_message_bcc_email` は文字列コレクションであり、残りのフィールドは文字列です。|
| ODT (application/vnd.oasis.opendocument.text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| ODS (application/vnd.oasis.opendocument.spreadsheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| ODP (application/vnd.oasis.opendocument.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| ZIP (application/zip) |`metadata_content_type` |アーカイブ内のすべてのドキュメントからテキストを抽出します。 |
| GZ (application/gzip) |`metadata_content_type` |アーカイブ内のすべてのドキュメントからテキストを抽出します。 |
| EPUB (application/epub+zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |アーカイブ内のすべてのドキュメントからテキストを抽出します。 |
| XML (application/xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |XML マークアップを削除し、テキストを抽出します。 |
| JSON (application/json) |`metadata_content_type`<br/>`metadata_content_encoding` |テキストを抽出します<br/>注:JSON BLOB から複数のドキュメント フィールドを抽出する必要がある場合、詳細については、[JSON BLOB のインデックス作成](search-howto-index-json-blobs.md)に関する記事をご覧ください |
| EML (message/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |テキストを抽出します。添付ファイルも対象となります。 |
| RTF (アプリケーション/rtf) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | テキストを抽出します|
| プレーン テキスト (text/plain) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | テキストを抽出します|

## <a name="see-also"></a>関連項目

* [Azure Cognitive Search のインデクサー](search-indexer-overview.md)
* [AI を使用して BLOB を理解する](search-blob-ai-integration.md)
* [BLOB のインデックス作成の概要](search-blob-storage-integration.md)
* [SharePoint Online のインデックス作成](search-howto-index-sharepoint-online.md)