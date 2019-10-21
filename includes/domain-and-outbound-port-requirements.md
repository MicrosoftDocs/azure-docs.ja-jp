---
title: インクルード ファイル
description: インクルード ファイル
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: f634ebb4d8c3f443b5d843b0093c955b384a9f5f
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285592"
---
| ドメイン名                  | 送信ポート | 説明                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | セルフホステッド統合ランタイムが Data Factory のデータ移動サービスに接続するために必要です。 |
| `*.frontend.clouddatahub.net` | 443            | セルフホステッド統合ランタイムが Data Factory サービスに接続するために必要です。 |
| `download.microsoft.com`    | 443            | セルフホステッド統合ランタイムが更新プログラムをダウンロードするために必要です。 自動更新を無効にした場合は、これをスキップできます。 |
| `*.core.windows.net`          | 443            | [ステージング コピー](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)機能を使用する場合に、セルフホステッド統合ランタイムが Azure ストレージ アカウントに接続するために使用します。 |
| `*.database.windows.net`      | 1433           | (オプション) Azure SQL Database または Azure SQL Data Warehouse との間でコピーするときに必要です。 ステージング コピー機能を使用すると、ポート 1433 を開かずに Azure SQL Database または Azure SQL Data Warehouse にデータをコピーします。 |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | (オプション) Azure Data Lake Store との間でコピーするときに必要です。 |
