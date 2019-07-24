---
title: Machine Learning Studio ワークスペースの作成
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio を使用するには、Machine Learning Studio ワークスペースが必要です。 このワークスペースには、実験を管理および公開するのに必要なツールが用意されています。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 7aeee4f24f6c7133ad978bc0c6c7fb8853bc4c35
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58086141"
---
# <a name="create-and-share-an-azure-machine-learning-studio-workspace"></a>Azure Machine Learning Studio ワークスペースの作成と共有

Azure Machine Learning Studio を使用するには、Machine Learning Studio ワークスペースが必要です。 このワークスペースには、実験を管理および公開するのに必要なツールが用意されています。

## <a name="create-a-studio-workspace"></a>Studio ワークスペースを作成する

1. [Azure ポータル](https://portal.azure.com/)

    > [!NOTE]
    > サインインして Studio ワークスペースを作成するには、Azure サブスクリプション管理者である必要があります。 
    >
    > 

2. **[+新規]** をクリックします。

3. 検索ボックスに「**Machine Learning Studio ワークスペース**」と入力し、一致する項目を選びます。 次に、ページ下部にある **[作成]** をクリックします。

4. ワークスペースの情報を入力します。

   - *[ワークスペース名]* に使用できる最大文字数は 260 文字です。末尾に空白文字は使用しないでください。 また、`< > * % & : \ ? + /` の各文字は使用できません。
   - このワークスペースから Web サービスをデプロイした場合、選択 (または作成) した "*Web サービス プラン*" とそれに関連して選択した "*価格レベル*" が使用されます。

     ![新しい Studio ワークスペースを作成する](./media/create-workspace/create-new-workspace.png)

5. **Create** をクリックしてください。

> [!NOTE]
> Machine Learning Studio では、ワークフローの実行時に中間データを保存するときにユーザーが指定した Azure ストレージ アカウントを使用します。 ワークスペースの作成後に、ストレージ アカウントが削除された場合、またはアクセスキーが変更された場合、ワークスペースは機能しなくなり、そのワークスペースのすべての実験は失敗します。
誤ってストレージ アカウントを削除した場合は、削除されたストレージ アカウントと同じリージョンに同じ名前のストレージ アカウントを再作成し、アクセス キーを再同期します。 ストレージ アカウントのアクセス キーを変更した場合は、Azure Portal を使用してワークスペースのアクセス キーを再同期します。

デプロイしたワークスペースは、Machine Learning Studio で開くことができます。

1. [https://studio.azureml.net/](https://studio.azureml.net/) で Machine Learning Studio を参照します。

2. 右上隅で該当するワークスペースを選択します。

    ![ワークスペースを選択](./media/create-workspace/open-workspace.png)

3. **[my experiments (実験)]** をクリックします。

    ![実験を開く](./media/create-workspace/my-experiments.png)

Studio ワークスペースの管理方法の詳細については、「[Azure Machine Learning Studio ワークスペースの管理](manage-workspace.md)」をご覧ください。
ワークスペースの作成で問題が発生した場合は、「[トラブルシューティング ガイド: Machine Learning Studio ワークスペースの作成と接続](troubleshooting-creating-ml-workspace.md)」を参照してください。


## <a name="share-an-azure-machine-learning-studio-workspace"></a>Azure Machine Learning Studio ワークスペースを共有する
Machine Learning Studio ワークスペースを作成した後、ユーザーをワークスペースに招待し、ワークスペースとその実験、データセット、ノートブックなどのすべてに対するアクセスを共有できます。ユーザーは、次のいずれかの役割で追加することができます。

* **ユーザー** - ワークスペース内の実験やデータセットの作成、表示、変更、削除を行うことができます。
* **所有者** - ワークスペース ユーザーの招待と削除、ユーザーが実行できる操作の登録を行うことができます。

> [!NOTE]
> ワークスペースを作成した管理者アカウントは、ワークスペースの所有者としてワークスペースに自動的に追加されます。 ただしそのサブスクリプションに含まれる他の管理者またはユーザーについては、ワークスペースへのアクセス権が自動的には付与されません。明示的に招待する必要があります。
> 
> 

### <a name="to-share-a-studio-workspace"></a>Studio ワークスペースを共有するには

1. [https://studio.azureml.net/Home](https://studio.azureml.net/Home) で Machine Learning Studio にサインインします。

2. 左側のパネルの **[設定]** をクリックします。

3. **[ユーザー]** タブをクリックします。

4. ページ下部の **[その他のユーザーの招待]** をクリックします。

    ![Studio の設定](./media/create-workspace/settings.png)

5. 電子メール アドレスを少なくとも 1 つ入力します。 ユーザーには、有効な Microsoft アカウントまたは (Azure Active Directory の) 組織アカウントが必要です。

6. ユーザーを所有者として追加するか、ユーザーとして追加するかを選択します。

7. チェックマークが表示された **[OK]** ボタンをクリックします。

追加した各ユーザーには、共有ワークスペースへのサインイン方法が記載された電子メールが送信されます。

> [!NOTE]
> このワークスペースの Web サービスをデプロイしたり管理したりするユーザーは、Azure サブスクリプションの共同作成者または管理者である必要があります。 



