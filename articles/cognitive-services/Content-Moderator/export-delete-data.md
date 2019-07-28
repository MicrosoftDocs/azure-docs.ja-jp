---
title: ユーザー データをエクスポートまたは削除する - Content Moderator
titlesuffix: Azure Cognitive Services
description: Content Moderator のデータをエクスポートまたは削除する方法について説明します。
services: cognitive-services
author: patrickfarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 1cd70bee22e56e2580b322b93e0f121261d97a94
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60699197"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Content Moderator のユーザー データをエクスポートまたは削除する

Content Moderator は、サービスの運用にユーザー データを収集しますが、データの参照、エクスポート、削除は、ユーザーが[レビュー ツール](https://contentmoderator.cognitive.microsoft.com/)と [Moderation API および Review API](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference) を使用して完全に制御できます。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Content Moderator のユーザー データをエクスポートおよび削除する方法については、次の表を参照してください。

| データ | エクスポート操作 | 削除操作。 |
| ---- | ---------------- | ---------------- |
| アカウントの情報 (サブスクリプション キー) | 該当なし | Azure Portal (Azure のサブスクリプション) を使用して削除します。 または、[[Review UI]\(UI の確認\)](https://contentmoderator.cognitive.microsoft.com/) チーム設定ページの **[チームの削除]** ボタンを使用します。 |
| カスタム照合用の画像 | [画像 ID の取得 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676) を呼び出します。 画像は、一方向の独自のハッシュ形式で格納され、実際の画像を抽出する方法はありません。 | [すべての画像を削除 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686) を呼び出します。 または、Azure Portal を使用して、Content Moderator のリソースを削除します。 |
| カスタム照合の用語 | [すべての用語を取得 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) を呼び出します。 | [すべての用語を削除 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d) を呼び出します。 または、Azure Portal を使用して、Content Moderator のリソースを削除します。 |
| Tags | 該当なし | [Review UI Tag]\(UI タグの確認\) 設定ページの **[削除]** アイコンを使用します。 または、[[Review UI]\(UI の確認\)](https://contentmoderator.cognitive.microsoft.com/) チーム設定ページの **[チームの削除]** ボタンを使用します。 |
| レビュー | [レビューの取得 API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) を呼び出します | [[Review UI]\(UI の確認\)](https://contentmoderator.cognitive.microsoft.com/) チーム設定ページの **[チームの削除]** ボタンを使用します。
| ユーザー | 該当なし | [[Review UI]\(UI の確認\)](https://contentmoderator.cognitive.microsoft.com/) チーム設定ページに各ユーザー向けに用意されている **[削除]** アイコンを使用します。 または、[[Review UI]\(UI の確認\)](https://contentmoderator.cognitive.microsoft.com/) チーム設定ページの **[チームの削除]** ボタンを使用します。 |

