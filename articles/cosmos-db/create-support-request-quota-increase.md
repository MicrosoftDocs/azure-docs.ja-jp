---
title: Azure Cosmos DB リソースのクォータの増加を要求する方法
description: Azure Cosmos DB リソースのクォータの増加を要求する方法について説明します。 また、サブスクリプションがリージョンにアクセスできるようにする方法について説明します。
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: e7ec71220b75647e789508c760e50957b3b497fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93090037"
---
# <a name="how-to-request-quota-increase-for-azure-cosmos-db-resources"></a>Azure Cosmos DB リソースのクォータの増加を要求する方法
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB のリソースには[既定のクォータ/制限](concepts-limits.md)があります。 ただし、ワークロードに既定値よりも多くのクォータが必要な場合があります。 そのような場合は、Azure Cosmos DB チームに連絡してクォータの増加を要求する必要があります。 この記事では、Azure Cosmos DB リソースのクォータの増加を要求する方法について説明します。 また、サブスクリプションがリージョンにアクセスできるようにする方法について説明します。

## <a name="create-a-new-support-request"></a>新しいサポート リクエストを作成する

クォータの増加を要求するには、ワークロードの詳細を記載した新しいサポート リクエストを作成する必要があります。 その後 Azure Cosmos DB チームが、その要求を評価して承認します。 Azure portal から新しいサポート リクエストを作成するには、次の手順に従います。

1. Azure portal にサインインします。

1. 左側のメニューから **[ヘルプとサポート]** を選択し、 **[新しいサポート リクエスト]** を選択します。

1. **[基本]** タブで、以下の詳細を入力します。

   * **[問題の種類]** で、 **[サービスとサブスクリプションの制限 (クォータ)]** を選択します
   * **[サブスクリプション]** で、クォータを増やすサブスクリプションを選択します。
   * **[クォータの種類]** で、 **[Cosmos DB]** を選択します

   :::image type="content" source="./media/create-support-request-quota-increase/create-quota-increase-request.png" alt-text="クォータ増加のための新しい Cosmos DB サポート リクエストを作成する":::

1. **[詳細]** タブで、クォータ要求に対応する詳細を入力します。 このタブで指定された情報は、問題をより詳しく評価し、サポート エンジニアによる問題のトラブルシューティングに役立てるために使用されます。

1. このフォームで、以下の詳細を入力します。

   * **説明**:既定値ではなぜ不十分であるか、実際のワークロードなど、要求の簡単な説明を入力します。 クォータの増加対象のリソースの種類に基づいて、以下の詳細を **[説明]** フィールドに入力することが必須となっています。

     **[Regions requests] (リージョンの要求)** : 要求が、許可リストにリージョンを追加することに該当する場合、以下の値を入力する必要があります。

        * リージョン名
        * サブスクリプション ID

     **[Throughput limit requests] (スループット制限要求)** : 要求が、スループットのクォータの増加に該当する場合は、必ず以下の値を入力してください。

        * データベース名
        * サブスクリプション ID
        * スループットの新しい制限値

     **[Database account limit requests] (データベース アカウント制限要求)** : 要求が、サブスクリプション内のデータベース アカウントの数に関するクォータの増加に該当する場合は、必ず以下の値を入力してください。

       * サブスクリプション ID
       * データベース アカウントの新しい制限値

   * **[ファイルのアップロード]** :サポート リクエストに関連すると思われる、診断ファイルやその他のファイルをアップロードします。 ファイルのアップロードに関するガイダンスの詳細については、[Azure サポート]( ../azure-portal/supportability/how-to-manage-azure-support-request.md#upload-files)に関する記事を参照してください。

   * **[重大度]** :ビジネスへの影響に基づいて、選択可能ないずれかの重大度レベルを選択します。

   * **[ご希望の連絡方法]** :**メール** または **電話** のいずれかによる連絡を選択できます。

1. フォームに、連絡可能な時間帯、サポート言語、連絡先情報、メール、電話番号など、残りの詳細情報を入力します。

1. **[Next:Review+Create] (次へ: 確認して作成)** を選択します。 入力した情報を検証し、 **[作成]** を選択してサポート リクエストを作成します。

24 時間以内に、Azure Cosmos DB サポート チームが要求を評価して、お客様に返信します。

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB の既定のサービス クォータ](concepts-limits.md)に関するページを参照します
