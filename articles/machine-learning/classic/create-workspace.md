---
title: 'ML Studio (classic): ワークスペースの作成 - Azure'
description: Azure Machine Learning Studio (クラシック) を使用するには、Machine Learning Studio (クラシック) ワークスペースが必要です。 このワークスペースには、実験を管理および公開するのに必要なツールが用意されています。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 9caac94c90aa18f8661ab46a0ae72d49500f39cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520546"
---
# <a name="create-and-share-an-machine-learning-studio-classic-workspace"></a>Machine Learning Studio (classic) ワークスペースを作成して共有する

**適用対象:** ![これはチェック マークです。つまり、この記事は Machine Learning Studio (クラシック) を対象としています。](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (クラシック)   ![これは × 印です。つまり、この記事は Azure Machine Learning を対象としています。](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Azure Machine Learning Studio (クラシック) を使用するには、Machine Learning Studio (クラシック) ワークスペースが必要です。 このワークスペースには、実験を管理および公開するのに必要なツールが用意されています。

## <a name="create-a-studio-classic-workspace"></a>Studio (クラシック) ワークスペースを作成する

Machine Learning Studio (クラシック) でワークスペースを開くには、ワークスペースを作成する際に使用した Microsoft アカウントにサインインするか、所有者からワークスペースへの参加の招待を受け取る必要があります。 ワークスペースの管理は Azure Portal で実行できます。アクセス許可もポータルで構成できます。

1. [Azure ポータル](https://portal.azure.com/)

    > [!NOTE]
    > サインインして Studio (クラシック) ワークスペースを作成するには、Azure サブスクリプション管理者である必要があります。 
    >
    > 

2. **[+新規]** をクリックします。

3. 検索ボックスに「**Machine Learning Studio (クラシック) ワークスペース**」と入力し、一致する項目を選びます。 次に、ページ下部にある **[作成]** をクリックします。

4. ワークスペースの情報を入力します。

   - *[ワークスペース名]* に使用できる最大文字数は 260 文字です。末尾に空白文字は使用しないでください。 また、`< > * % & : \ ? + /` の各文字は使用できません。
   - このワークスペースから Web サービスをデプロイした場合、選択 (または作成) した "*Web サービス プラン*" とそれに関連して選択した "*価格レベル*" が使用されます。

     ![新しい Studio (クラシック) ワークスペースを作成する](./media/create-workspace/create-new-workspace.png)

5. **Create** をクリックしてください。

   現在、Machine Learning は一部のリージョンでのみ利用できます。 サブスクリプションに対象のリージョンが含まれていない場合は、"許可されたリージョンにサブスクリプションがありません" というエラー メッセージが表示される可能性があります。  サブスクリプションへのリージョンの追加を要求するには、Azure Portal で Microsoft サポート要求を新規作成し、問題の種類として **[課金]** を選択して、プロンプトに従って要求を送信します。


> [!NOTE]
> Machine Learning Studio (クラシック) では、ワークフローの実行時に中間データを保存するときにユーザーが指定した Azure ストレージ アカウントが使用されます。 ワークスペースの作成後に、ストレージ アカウントが削除された場合、またはアクセスキーが変更された場合、ワークスペースは機能しなくなり、そのワークスペースのすべての実験は失敗します。
誤ってストレージ アカウントを削除した場合は、削除されたストレージ アカウントと同じリージョンに同じ名前のストレージ アカウントを再作成し、アクセス キーを再同期します。 ストレージ アカウントのアクセス キーを変更した場合は、Azure Portal を使用してワークスペースのアクセス キーを再同期します。

デプロイしたワークスペースは、Machine Learning Studio (クラシック) で開くことができます。

1. [https://studio.azureml.net/](https://studio.azureml.net/) で Machine Learning Studio (クラシック) を参照します。

2. 右上隅で該当するワークスペースを選択します。

    ![ワークスペースを選択](./media/create-workspace/open-workspace.png)

3. **[my experiments (実験)]** をクリックします。

    ![実験を開く](./media/create-workspace/my-experiments.png)

Studio (クラシック) ワークスペースの管理方法の詳細については、「[Azure Machine Learning Studio (クラシック) ワークスペースの管理](manage-workspace.md)」をご覧ください。
ワークスペースの作成で問題が発生した場合は、「[トラブルシューティング ガイド: Machine Learning Studio (クラシック) ワークスペースの作成と接続](index.yml)」を参照してください。


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Azure Machine Learning Studio (クラシック) ワークスペースを共有する
Machine Learning Studio (classic) ワークスペースを作成した後、ユーザーをワークスペースに招待し、ワークスペースとその実験、データセットなどのすべてに対するアクセスを共有できます。ユーザーは、次のいずれかの役割で追加することができます。

* **ユーザー** - ワークスペース内の実験やデータセットの作成、表示、変更、削除を行うことができます。
* **所有者** - ワークスペース ユーザーの招待と削除、ユーザーが実行できる操作の登録を行うことができます。

> [!NOTE]
> ワークスペースを作成した管理者アカウントは、ワークスペースの所有者としてワークスペースに自動的に追加されます。 ただしそのサブスクリプションに含まれる他の管理者またはユーザーについては、ワークスペースへのアクセス権が自動的には付与されません。明示的に招待する必要があります。
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Studio (クラシック) ワークスペースを共有するには

1. [https://studio.azureml.net/Home](https://studio.azureml.net/Home) で Machine Learning Studio (クラシック) にサインインします。

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

## <a name="troubleshoot-storage-accounts"></a>ストレージ アカウントのトラブルシューティング


Machine Learning サービスでは、データを格納するためのストレージ アカウントが必要です。 既存のストレージ アカウントを使用するか、新しい Machine Learning Studio (クラシック) ワークスペースの作成時に新しいストレージ アカウントを作成することができます (新しいストレージ アカウントを作成するためのクォータがある場合)。

新しい Machine Learning Studio (クラシック) ワークスペースの作成後は、ワークスペースの作成に使用した Microsoft アカウントで Machine Learning Studio (クラシック) にサインインできます。 エラー メッセージ "ワークスペースが見つかりません" (次のスクリーン ショットに類似) が発生する場合は、次の手順に従ってブラウザーの Cookie を削除します。

![Workspace not found](media/troubleshooting-creating-ml-workspace/screen3.png)

**ブラウザーの Cookie を削除するには**

1. Internet Explorer を使用している場合は、右上隅の **[ツール]** をクリックして、 **[インターネット オプション]** を選択します。  

   ![[インターネット オプション]](media/troubleshooting-creating-ml-workspace/screen4.png)

2. **[全般]** タブで、 **[削除]** をクリックします。

   ![[全般] タブ](media/troubleshooting-creating-ml-workspace/screen5.png)

3. **[閲覧の履歴の削除]** ダイアログ ボックスで、 **[クッキーと Web サイト データ]** が選択されていることを確認し、 **[削除]** をクリックします。

   ![Cookie の削除](media/troubleshooting-creating-ml-workspace/screen6.png)

Cookie が削除されたら、ブラウザーを再起動し、[Microsoft Azure Machine Learning Studio (クラシック)](https://studio.azureml.net) ページに移動します。 ユーザー名とパスワードの入力が求められたら、ワークスペースの作成時に使用したアカウントと同じ Microsoft アカウントを入力します。


## <a name="next-steps"></a>次のステップ

ワークスペースの管理方法の詳細については、「[Azure Machine Learning Studio (クラシック) ワークスペースの管理](manage-workspace.md)」をご覧ください。