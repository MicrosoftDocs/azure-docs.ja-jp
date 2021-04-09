---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 10/30/2020
ms.topic: include
ms.openlocfilehash: e006f804b8ab6411f4949424147acf567dc2ed24
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97371315"
---
## <a name="sign-in-to-luis-portal"></a>LUIS ポータルにサインインする

[!INCLUDE [Note about portal deprecation](luis-portal-note.md)]

LUIS の新しいユーザーは、次の手順に従う必要があります。

1. [LUIS ポータル](https://www.luis.ai)にサインインし、国/地域を選択して利用規約に同意します。 代わりに **[マイ アプリ]** が表示される場合は、LUIS リソースが既に存在しているため、次の手順に進んでアプリを作成してください。 次の 2 つの方法でサインアップできます。

    * Azure リソースの使用 (推奨) - LUIS アカウントを新規または既存の Azure 作成リソースにリンクすることができます。 これは、既に移行済みのサインアップと同じです。 後で[移行プロセス](../luis-migration-authoring.md#what-is-migration)を実行する必要はありません。

    * 試用キーの使用。 これにより、設定する必要のない試用版リソースを使って LUIS にサインインできます。 このオプションを選択した場合は、最終的に[アカウントを移行](../luis-migration-authoring.md#migration-steps)し、アプリケーションを作成リソースにリンクする必要があります。

1. 表示された **[Choose an authoring]\(作成の選択\)** ウィンドウで、Azure サブスクリプションと LUIS 作成リソースを見つけます。 リソースがない場合は、新しいものを作成できます。

    :::image type="content" source="../media/luis-how-to-azure-subscription/choose-authoring-resource.png" alt-text="Language Understanding の作成リソースの種類を選択します。":::
    
    新しい作成リソースを作成するときは、次の情報を指定します。
    * **[テナント名]** - Azure サブスクリプションが関連付けられているテナント。
    * **[Azure サブスクリプション名]** - リソースに対して課金されるサブスクリプション。
    * **[Azure リソース グループ名]** - 選択または作成するカスタム リソース グループ名。 リソース グループを使用すると、アクセスと管理のために Azure リソースをグループ化できます。
    * **[Azure リソース名]** - 選択するカスタム名。作成および予測エンドポイント クエリの URL の一部として使用されます。
    * **[Pricing tier]\(価格レベル\)** - 価格レベルによって、1 秒および 1 か月あたりの最大トランザクション数が決まります。


