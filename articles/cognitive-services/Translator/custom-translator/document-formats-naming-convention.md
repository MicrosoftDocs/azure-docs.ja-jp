---
title: ドキュメントの形式と名前付け規則 - Custom Translator
titleSuffix: Azure Cognitive Services
description: これは Custom Translator でのドキュメントの形式と名前付け規則に関するガイドです。 この概念を利用すると、ドキュメント名を管理しやすくなり、名前の競合を避けることができます。
author: swmachan
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 2f7a83be510e608bb3f630a2fb1860502d8e4475
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443421"
---
# <a name="document-formats-and-naming-convention-guidance"></a>ドキュメントの形式と名前付け規則のガイダンス

カスタム翻訳に使用するすべてのファイルは、**4** 文字以上の長さにする必要があります。

この表には、翻訳システムの構築に使用できる、サポートされているすべてのファイル形式がまとめられています。

| 形式            | Extensions   | 説明                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF             | .XLF、.XLIFF | 並列ドキュメント形式、翻訳メモリ システムのエクスポート。 使用される言語はファイル内で定義されています。                                                                                                                                                              |
| TMX               | .TMX         | 並列ドキュメント形式、翻訳メモリ システムのエクスポート。 使用される言語はファイル内で定義されています。                                                                                                                                                              |
| ZIP               | .ZIP         | ZIP はアーカイブのファイル形式です。                                                                                                                                                                                                        |
| Locstudio         | .LCL         | Microsoft の並列ドキュメントの形式                                                                                                                                                                                                                                      |
| Microsoft Word    | .DOCX        | Microsoft Word 文書                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | .PDF         | Adobe Acrobat ポータブル ドキュメント                                                                                                                                                                                                                                                |
| HTML              | .HTML、.HTM  | HTML ドキュメント                                                                                                                                                                                                                                                                  |
| テキスト ファイル         | .TXT         | UTF-16 または UTF-8 でエンコードされたテキスト ファイル。 このファイル名に日本語文字を含めることはできません。                                                                                                                                                                                        |
| アライン済みテキスト ファイル | .ALIGN       | 拡張子 `.ALIGN` は、ドキュメント ペア内の文が完全にアラインされていることがわかっている場合に使用できる特別な拡張子です。 `.ALIGN` ファイルを指定すると、Custom Translator では文が自動的にアラインされません。 |
| Excel ファイル        | .XLSX        | Excel ファイル (2013 以降)。 スプレッドシートの最初の行には言語コードを指定する必要があります。                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>辞書の形式

辞書の場合、カスタム翻訳ツールはトレーニング セットでサポートされているすべてのファイル形式をサポートしています。 Excel の辞書を使用している場合は、スプレッドシートの最初の行に言語コードを指定する必要があります。

## <a name="zip-file-formats"></a>zip ファイル形式

複数のドキュメントを 1 つの zip ファイルにまとめてアップロードすることができます。 Custom Translator は、zip ファイル形式 (ZIP、GZ、TGZ) をサポートしています。

TXT、HTML、HTM、PDF、DOCX、ALIGN の拡張子を持つ、ZIP ファイル内の各ドキュメントは、次の名前付け規則に従う必要があります。

{ドキュメント名}\_{言語コード}。この {ドキュメント名} はドキュメントの名前、{言語コード} は ISO LanguageID (2 文字) であり、ドキュメントにその言語の文が含まれていることを示します。 言語コードの前にアンダースコア (_) を指定する必要があります。

たとえば、英語からスペイン語へのシステム用の zip 内に 2 つの並列ドキュメントを含めてアップロードするには、ファイル名を "data_en" と "data_es" にする必要があります。

翻訳メモリ ファイル (TMX、XLF、XLIFF、LCL、XLSX) は、必ずしも特定の言語の名前付け規則に準拠している必要はありません。  

## <a name="next-steps"></a>次の手順

- [プロジェクト](workspace-and-project.md#what-is-a-custom-translator-project)の作成と管理について説明します。
