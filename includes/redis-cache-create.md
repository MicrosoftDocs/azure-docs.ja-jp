---
title: インクルード ファイル
description: インクルード ファイル
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 0f1decae5fb3ec4a07f01c5bff7475f3d73a3cbb
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111904"
---
1. キャッシュを作成するには、まず [Azure portal](https://portal.azure.com) にサインインします。 **[リソースの作成]** > **[データベース]** > **[Azure Cache for Redis]** を選択します。

    ![新しいキャッシュ](media/redis-cache-create/redis-cache-new-cache-menu.png)

2. **[New Azure Cache for Redis]\(新規 Azure Cache for Redis\)** で、新しいキャッシュの設定を構成します。

    | Setting      | 推奨値  | 説明 |
    | ------------ |  ------- | -------------------------------------------------- |
    | **DNS 名** | グローバルに一意の名前 | キャッシュ名。 1 から 63 文字の文字列で、数字、英字、`-` 文字のみを使用する必要があります。 キャッシュ名の先頭と末尾には `-` 文字を使用できません。また、連続する `-` 文字は無効です。  | 
    | **サブスクリプション** | 該当するサブスクリプション | この新しい Azure Cache for Redis インスタンスが作成されるサブスクリプション。 | 
    | **[リソース グループ]** |  *TestResources* | その中にキャッシュを作成する新しいリソース グループの名前。 アプリのすべてのリソースを 1 つのグループ内に配置することで、それらを一緒に管理できます。 たとえば、リソース グループを削除すると、そのアプリに関連付けられているすべてのリソースが削除されます。 | 
    | **場所** | 米国東部 | キャッシュを使用する他のサービスの近くの[リージョン](https://azure.microsoft.com/regions/)を選択します。 |
    | **[価格レベル](https://azure.microsoft.com/pricing/details/cache/)** |  Basic C0 (250 MB のキャッシュ) |  価格レベルによって、キャッシュに使用できるのサイズ、パフォーマンス、および機能が決まります。 詳細については、[Azure Cache for Redis の概要](../articles/azure-cache-for-redis/cache-overview.md)に関するページを参照してください。 |
    | **ダッシュボードにピン留めする** |  オン | 新しいキャッシュをダッシュボードにピン留めし、簡単に見つけられるようにします。 |

    ![キャッシュの作成](media/redis-cache-create/redis-cache-cache-create.png) 

3. 新しいキャッシュ設定を構成したら、**[作成]** を選択します。 

    キャッシュが作成されるまで数分かかる場合があります。 状態を確認するには、ダッシュボードで進行状況を監視してください。 キャッシュが作成されると、**実行中**の状態が表示され、使用できるようになります。

    ![作成されたキャッシュ](media/redis-cache-create/redis-cache-cache-created.png)

