---
title: Azure オーサリング リソースに移行する
titleSuffix: Azure Cognitive Services
description: Azure オーサリング リソースに移行します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: diberry
ms.openlocfilehash: 7e79d3119f5d2d0fe1894de0ab9535dd988706f8
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935217"
---
# <a name="steps-to-migrate-to-the-azure-authoring-resource"></a>Azure オーサリング リソースに移行するための手順

Azure オーサリング リソースを使用するには、Language Understanding (LUIS) ポータルから、所有しているすべてのアプリを移行します。

## <a name="prerequisites"></a>前提条件

* **必要に応じて**、各アプリをエクスポートするかエクスポート [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) を使用して、LUIS ポータルのアプリの一覧からアプリをバックアップします。
* **必要に応じて**、各アプリのコラボレーターの一覧を保存します。 移行プロセスの一環として、すべてのコラボレーターに電子メールを送信できます。
* **必須**。[Azure サブスクリプション](https://azure.microsoft.com/free/)が必要です。 サブスクリプション プロセスの一部では、課金情報が必要です。 ただし、LUIS を使用する際には、無料 (F0) 価格レベルを使用できます。 使用量が増えるにつれて、最終的には有料レベルが必要になることがあります。 

Azure サブスクリプションをお持ちでない場合は、[サインアップ](https://azure.microsoft.com/free/)してください。 

## <a name="access-the-migration-process"></a>移行プロセスにアクセスする

毎週、アプリを移行するかどうかを確認するメッセージが表示されます。 移行せずにこのウィンドウをキャンセルすることもできます。 次にスケジュールされている期間より前に移行する場合は、LUIS ポータル上部のツール バーにある**ロック** アイコンから移行プロセスを開始できます。 

## <a name="app-owner-begins-the-migration-process"></a>アプリ所有者が移行プロセスを開始する

移行プロセスは、LUIS アプリの所有者である場合に実行できます。 

1. [LUIS ポータル](https://www.luis.ai) にサインインし、使用条件に同意します。
1. 移行のポップアップ ウィンドウでは、移行を続行するか、後で移行するかを選択できます。 **[Migrate now]\(今すぐ移行\)** を選択します。 後で移行することを選択した場合は、9 か月以内に Azure の新しいオーサリング キーに移行してください。

    ![移行プロセスの最初のポップアップ ウィンドウで、[Migrate now]\(今すぐ移行\) を選択します。](./media/migrate-authoring-key/migrate-now.png)

1. (省略可能) いずれかのアプリにコラボレーターがいる場合は、移行について知らせる**メールを送信する**よう求められます。 これは省略可能な手順です。 

    アカウントを Azure に移行すると、コラボレーターはアプリを利用できなくなります。

    コラボレーターとアプリごとに、既定のメール アプリケーションが開き、簡単な書式が設定されたメールが表示されます。 このメールは送信前に編集できます。

    メール テンプレートには、正確なアプリ ID とアプリ名が含まれています。 

    ```html
    Dear Sir/Madam,
    
    I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:
    
    App Id: <app-ID-omitted>
    App name: Human Resources
    
    Thank you
    ```

1. **[Start by creating an authoring resource to migrate your apps to]\(アプリの移行先となるオーサリング リソースの作成から開始します\)** を選択して、LUIS オーサリング リソースを作成することを選択します。 

    ![オーサリング リソースを作成する](./media/migrate-authoring-key/choose-authoring-resource.png)

1. 次のウィンドウで、リソース キーの情報を入力します。 情報を入力したら、 **[Create resource]\(リソースの作成\)** を選択します。 1 つのサブスクリプションにつき、リージョンごとに 10 個の無料オーサリング リソースを使用できます。

    ![オーサリング リソースを作成する](./media/migrate-authoring-key/choose-authoring-resource-form.png)

    **新しい作成リソースを作成する**ときは、次の情報を指定します。 

    * **[Resource name]\(リソース名\)** - 選択したカスタム名。オーサリングおよび予測エンドポイント クエリの URL の一部として使用されます。
    * **[Tenant]\(テナント\)** - Azure サブスクリプションが関連付けられているテナント。 
    * **[Subscription name]\(サブスクリプション名\)** - リソースに対して課金されるサブスクリプション。
    * **[Resource group]\(リソース グループ\)** - 選択または作成するカスタム リソース グループ名。 リソース グループを使用すると、アクセスと管理のために Azure リソースをグループ化できます。 
    * **[Location]\(場所\)** - 場所の選択肢は、**リソース グループ**の選択に基づいて決まります。
    * **[Pricing tier]\(価格レベル\)** - 価格レベルによって、1 秒および 1 か月あたりの最大トランザクション数が決まります。 

1. オーサリング リソースを検証し、**今すぐ移行**します。

    ![オーサリング リソースを作成する](./media/migrate-authoring-key/choose-authoring-resource-and-migrate.png)

1. オーサリング リソースが作成されると、成功メッセージが表示されます。 **[Close]\(閉じる\)** を選択してポップアップ ウィンドウを閉じます。

    ![オーサリング リソースが正常に作成されました。](./media/migrate-authoring-key/migration-success.png)

    **[My apps]\(マイ アプリ\)** の一覧に、新しいオーサリング リソースに移行されたアプリが表示されます。 

    LUIS ポータルでアプリの編集を続けるために、オーサリング リソースのキーの情報は必要ありません。 プログラムを使用してアプリを編集する場合は、オーサリング キーの値が必要です。 これらの値は、LUIS ポータルの **[Manage]\(管理\) -> [Azure resources]\(Azure リソース\)** ページのほか、Azure portal のそのリソースの **[キー]** ページでも確認できます。  

1. アプリにアクセスする前に、サブスクリプションと LUIS オーサリング リソースを選択して、自分が作成できるアプリを確認します。

    ![サブスクリプションと LUIS オーサリング リソースを選択して、自分が作成できるアプリを確認します。](./media/migrate-authoring-key/app-list-by-subscription-and-resource.png)


## <a name="app-contributor-begins-the-migration-process"></a>アプリ共同作成者が移行プロセスを開始する

移行については、アプリの所有者の場合と同じ手順を行います。 このプロセスでは、`LUIS.Authoring` タイプのオーサリング リソースが新規作成されます。 

移行されたアプリのうち、他者が所有するアプリに共同作成者として追加されるには、自分のアカウントを移行する必要があります。  

## <a name="after-the-migration-process-add-contributors-to-your-authoring-resource"></a>移行プロセスが完了した後、オーサリング リソースに共同作成者を追加する

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

[共同作成者を追加する方法](luis-how-to-collaborate.md)を確認してください。 

## <a name="troubleshooting-errors-with-the-migration-process"></a>移行プロセスに関するエラーのトラブルシューティング

移行プロセス中、赤い通知バーによって LUIS ポータルで `MissingSubscriptionRegistration` エラーを受け取った場合は、[Azure portal](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) または [Azure CLI](luis-how-to-azure-subscription.md#create-resources-in-azure-cli) で Cognitive Service リソースを作成します。 [このエラーの原因](../../azure-resource-manager/resource-manager-register-provider-errors.md#cause)について確認してください。

## <a name="next-steps"></a>次の手順


* オーサリング キーとランタイム キーの[概念](luis-concept-keys.md)について見直す
* [キーを割り当てる方法](luis-how-to-azure-subscription.md)と[共同作成者](luis-how-to-collaborate.md)を追加する方法について見直す
