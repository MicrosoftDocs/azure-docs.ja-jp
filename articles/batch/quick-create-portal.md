---
title: Azure クイック スタート - Batch ジョブの実行 - ポータル
description: Azure Portal で Batch ジョブを実行する方法を簡単に説明します。
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.devlang: na
ms.topic: quickstart
ms.date: 07/03/2018
ms.author: lahugh
ms.custom: mvc
ms.openlocfilehash: 33455a95dff946676e3dfffbd2737ed36be59c64
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322360"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>クイック スタート:Azure portal で最初の Batch ジョブを実行する

このクイック スタートでは、Azure Portal を使用して Batch アカウント、コンピューティング ノード (仮想マシン) の "*プール*"、そのプールで基本的な "*タスク*" を実行する "*ジョブ*" を作成する方法を示します。 このクイック スタートを完了すると、Batch サービスの主要な概念を理解し、より大規模でより現実的なワークロードで Batch を試せるようになります。

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Azure へのサインイン 

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-a-batch-account"></a>Batch アカウントを作成する

テスト目的でサンプルの Batch アカウントを作成するには、次の手順に従います。 プールおよびジョブを作成するには Batch アカウントが必要です。 ここで示すように、Azure ストレージ アカウントを Batch アカウントとリンクできます。 このクイック スタートには必要ありませんが、ストレージ アカウントは、アプリケーションをデプロイしたり、ほとんどの実際のワークロードの入力データと出力データを保存したりする際に役立ちます。


1. **[リソースの作成]**  >  **[Compute]**  >  **[Batch サービス]** の順に選択します。 

   ![Marketplace での Batch][marketplace_portal]

2. **[アカウント名]** と **[リソース グループ]** に値を入力します。 アカウント名は、Azure の選択された**場所**内で一意である必要があります。また、アカウント名に使用できるのは、3 文字から 24 文字までの小文字と数字のみです。 

3. **[ストレージ アカウント]** で、既存のストレージ アカウントを選択するか、新しいストレージ アカウントを作成します。

4. 残りの設定は既定値のままにし、 **[作成]** を選択して、アカウントを作成します。

   ![Batch アカウントを作成する][account_portal]  

"**デプロイメントに成功しました**" メッセージが表示されたら、ポータルで Batch アカウントに移動します。

## <a name="create-a-pool-of-compute-nodes"></a>コンピューティング ノードのプールの作成

Batch アカウントが用意できたら、テスト目的で Windows コンピューティング ノードのサンプル プールを作成します。 この簡単な例のプールは、Azure Marketplace の Windows Server 2012 R2 イメージを実行している 2 つのノードで構成されます。


1. Batch アカウントで、 **[プール]**  >  **[追加]** の順に選択します。

2. *mypool* という**プール ID** を入力します。 

3. **[オペレーティング システム]** で、次の設定を選択します (他のオプションを見つけることができます)。
  
   |Setting  |値  |
   |---------|---------|
   |**イメージの種類**|Marketplace (Linux/Windows)|
   |**発行元**     |MicrosoftWindowsServer|
   |**プラン**     |WindowsServer|
   |**SKU**     |2012-R2-Datacenter-smalldisk|

   ![プールのオペレーティング システムの選択][pool_os] 

4. 下にスクロールして、 **[ノード サイズ]** と **[スケール]** の設定を入力します。 推奨されるノード サイズは、この簡単な例についてパフォーマンスとコストのバランスが取れています。
  
   |Setting  |値  |
   |---------|---------|
   |**ノード価格レベル**     |Standard_A1|
   |**ターゲットの専用ノード数**     |2|

   ![プール サイズの選択][pool_size] 

5. 残りの設定は既定値のままにし、 **[OK]** を選択して、プールを作成します。

Batch によってすぐにプールが作成されますが、コンピューティング ノードを割り当てて開始するには数分かかります。 この間は、プールの **[割り当ての状態]** が **[サイズ変更中]** になります。 プールのサイズ変更中に、先に進んでジョブやタスクを作成できます。 

![状態がサイズ変更中のプール][pool_resizing]

数分後、プールの状態は **[安定]** になり、ノードが開始されます。 **[ノード]** を選択して、ノードの状態を確認します。 ノードの状態が **[アイドル]** の場合は、タスクを実行する準備が整っています。 

## <a name="create-a-job"></a>ジョブを作成する

プールを作成できたら、そこで実行するジョブを作成します。 Batch ジョブは、1 つ以上のタスクの論理グループです。 ジョブには、優先度やタスクの実行対象プールなど、タスクに共通する設定が含まれています。 最初、ジョブにはタスクがありません。 

1. Batch アカウント ビューで、 **[ジョブ]**  >  **[追加]** の順に選択します。 

2. *myjob* という**ジョブ ID** を入力します。 **[プール]** で *mypool* を選択します。 残りの設定は既定値のままにして、 **[OK]** を選択します。

   ![ジョブを作成する][job_create]

ジョブが作成されると、 **[タスク]** ページが開きます。

## <a name="create-tasks"></a>タスクの作成

ここで、ジョブで実行するサンプル タスクを作成します。 通常、Batch がコンピューティング ノードで実行するためにキューに登録して分散するタスクを複数作成します。 この例では、同じタスクを 2 つ作成します。 各タスクでコマンド ラインを実行してコンピューティング ノードで Batch 環境変数を表示した後、90 秒待ちます。 

Batch を使用する場合、コマンド ラインは、アプリまたはスクリプトを指定する場所です。 Batch には、アプリやスクリプトをコンピューティング ノードにデプロイする方法が多数用意されています。 

最初のタスクを作成するには:

1. **[追加]** を選択します。

2. *mytask* という**タスク ID** を入力します。 

3. **[コマンド ライン]** に、「`cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`」と入力します。 残りの設定は既定値のままにして、 **[OK]** を選択します。

   ![タスクを作成します。][task_create]

作成したタスクは、プールで実行するために Batch によってキューに登録されます。 ノードが実行できるようになると、タスクが実行されます。

2 つ目のタスクを作成するには、手順 1 に戻ります。 別の**タスク ID** を入力しますが、同じコマンド ラインを指定します。 最初のタスクがまだ実行中の場合、Batch は、プール内の別のノードに対して 2 つ目のタスクを開始します。

## <a name="view-task-output"></a>タスク出力の表示

前述のタスクの例は数分で完了します。 完了したタスクの出力を表示するには、 **[ノード上のファイル]** を選択し、`stdout.txt` ファイルを選択します。 このファイルは、タスクの標準出力を示します。 内容は次のようになります。

![タスク出力の表示][task_output]

内容は、ノードで設定されている Azure Batch 環境変数を示します。 独自の Batch ジョブとタスクを作成すると、これらの環境変数は、タスクのコマンド ラインのほか、コマンド ラインにより実行されるプログラムとスクリプトで参照できます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Batch のチュートリアルとサンプルを続行する場合は、このクイック スタートで作成した Batch アカウントとリンクされているストレージ アカウントを使用します。 Batch アカウント自体の料金は発生しません。

ジョブがスケジュールされていない場合でも、ノードの実行中はプールに対して料金が発生します。 プールは不要になったら、削除してください。 アカウント ビューで、 **[プール]** およびプールの名前を選択します。 次に、 **[削除]** を選択します。  プールを削除すると、ノード上のタスク出力はすべて削除されます。 

リソース グループ、Batch アカウント、および関連するすべてのリソースは、不要になったら削除します。 これを行うには、Batch アカウントのリソース グループを選択し、 **[リソース グループの削除]** を選択してください。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Batch アカウント、Batch プール、Batch ジョブを作成しました。 このジョブによってサンプル タスクが実行されたため、いずれかのノード上に作成された出力が表示されました。 Batch サービスの主要な概念を理解できたので、より大規模でより現実的なワークロードを使用して Batch を試す準備が整いました。 Azure Batch の詳細については、Azure Batch のチュートリアルを続行してください。 

> [!div class="nextstepaction"]
> [Azure Batch のチュートリアル](./tutorial-parallel-dotnet.md)

[marketplace_portal]: ./media/quick-create-portal/marketplace-batch.png

[account_portal]: ./media/quick-create-portal/batch-account-portal.png

[account_keys]: ./media/quick-create-portal/batch-account-keys.png

[pool_os]: ./media/quick-create-portal/pool-operating-system.png

[pool_size]: ./media/quick-create-portal/pool-size.png

[pool_resizing]: ./media/quick-create-portal/pool-resizing.png

[job_create]: ./media/quick-create-portal/job-create.png

[task_create]: ./media/quick-create-portal/task-create.png

[task_output]: ./media/quick-create-portal/task-output.png