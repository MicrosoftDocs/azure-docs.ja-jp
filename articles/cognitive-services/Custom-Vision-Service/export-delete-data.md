---
title: データを表示また削除する - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: データの完全な制御を維持します。 この記事では、Custom Vision Service 内のデータを表示、エクスポートまたは削除する方法について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: fe17fa4349085be47201974b418493fe8d912ece
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "90527396"
---
# <a name="view-or-delete-user-data-in-custom-vision"></a>Custom Vision でユーザー データを表示または削除する

Custom Vision は、サービスを運用するためにユーザー データを収集しますが、そのデータの表示および削除は、ユーザーが Custom Vision の [Training API シリーズ](https://go.microsoft.com/fwlink/?linkid=865446)を使用して完全に制御できます。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Custom Vision のユーザー データを表示および削除する方法については、次の表をご覧ください。

| Data | 表示操作 | 削除操作 |
| ---- | ---------------- | ---------------- |
| アカウントの情報 (サブスクリプション キー) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Azure Portal を使用して削除します (Azure サブスクリプション)。 または、CustomVision.ai 設定ページ (Microsoft アカウント サブスクリプション) の [アカウントの削除] ボタンを使用します | 
| イテレーションの詳細 | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| イテレーションのパフォーマンスの詳細 | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| イテレーションの一覧 | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| プロジェクトおよびプロジェクトの詳細 | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) および [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| イメージ タグ | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) および [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| イメージ | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (画像のダウンロードに uri を提供) と [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (画像のダウンロードに uri を提供) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| エクスポートされたイテレーション | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | アカウントの削除時に削除 |
