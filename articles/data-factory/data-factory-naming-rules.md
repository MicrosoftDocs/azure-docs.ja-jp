---
title: "Azure Data Factory エンティティに名前を付けるときの規則 | Microsoft Docs"
description: "Data Factory エンティティの名前付け規則について説明します。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/24/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: dd8a68029449ad013c4df9a46c558efaefd20e96
ms.openlocfilehash: 18c212867ac6e380d3ee01c590b22f9168f762db
ms.lasthandoff: 01/24/2017


---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - 名前付け規則
次の表に、Data Factory アーティファクトの名前付け規則を示します。

| 名前 | 名前の一意性 | 検証チェック |
|:--- |:--- |:--- |
| Data Factory |Microsoft Azure 全体で一意です。 名前の大文字と小文字を区別されません。つまり、MyDF と mydf は同じデータ ファクトリを表します。 |<ul><li>各データ ファクトリは、厳密に&1; つの Azure サブスクリプションに関連付けられます。</li><li>オブジェクト名は英文字または数字で始まり、英文字、数字、ダッシュ (-) 文字のみを含めることができます。</li><li>すべてのダッシュ (-) 文字は、その直前または直後に文字または数字が使用されている必要があります。 連続するダッシュ文字はコンテナー名では使用できません。</li><li>名前は 3 ～ 63 文字の長さにすることができます。</li></ul> |
| リンクされたサービス/テーブル/パイプライン |データ ファクトリ内で一意です。 名前の大文字と小文字は区別されません。 |<ul><li>テーブル名の最大文字数: 260。</li><li>オブジェクト名は、文字、数字、アンダー スコア (_) のいずれかで始める必要があります。</li><li>次の文字は使用できません: "."、"+"、"?"、"/"、"<"、">"、"*"、"%"、"&"、":"、"\\"</li></ul> |
| [リソース グループ] |Microsoft Azure 全体で一意です。 名前の大文字と小文字は区別されません。 |<ul><li>最大文字数: 1,000。</li><li>名前には、文字、数字、および、次の文字を含めることができます。"-"、"_"、"," および "."。</li></ul> |


