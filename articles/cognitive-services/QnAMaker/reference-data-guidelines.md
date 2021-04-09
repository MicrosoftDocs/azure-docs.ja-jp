---
title: データのインポートとエクスポートのリファレンス - QnA Maker
description: ナレッジ ベースのバックアップ、ストレージ、および置換に最適な結果を得るには、このインポートおよびエクスポートのリファレンスを使用してください。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: d73fd6c7f49c10c8aca1060e91fc69b0e581738b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "97707505"
---
# <a name="import-and-export-data-reference"></a>データのインポートとエクスポートのリファレンス

ナレッジ ベースのバックアップ、ストレージ、および置換に最適な結果を得るには、このインポートおよびエクスポートのリファレンスを確認してください。

## <a name="import-and-export-knowledge-base"></a>ナレッジ ベースのインポートとエクスポート

エクスポートされたナレッジ ベースからの **TSV ファイルと XLS ファイル** は、QnA Maker ポータルの **[設定]** ページからファイルをインポートすることによってのみ使用できます。 ナレッジベースの作成中に、または **[設定]** ページの **[+ ファイルの追加]** 機能または **[+ URL の追加]** 機能から、それらをデータ ソースとして使用することはできません。 

これらの **TSV および XLS ファイル** を通じてナレッジ ベースをインポートする場合、QnA ペアは、エクスポートされたナレッジ ベースでの QnA の抽出元のソースではなく、編集ソースに追加されます。 
