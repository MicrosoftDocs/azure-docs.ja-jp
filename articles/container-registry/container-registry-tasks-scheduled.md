---
title: Azure Container Registry タスクをスケジュールする
description: 定義したスケジュールで Azure Container Registry タスクが実行されるようにタイマーを設定します。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 06/27/2019
ms.author: danlep
ms.openlocfilehash: 680f0268e85d41f8061dc96db1779ab6c22b944a
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310542"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>定義したスケジュールで ACR タスクを実行する

この記事では、スケジュールに従って [ACR タスク](container-registry-tasks-overview.md)を実行する方法を示します。 1 つ以上の "*タイマー トリガー*" を設定することによってタスクをスケジュールします。 

タスクのスケジュールは、次のようなシナリオで役に立ちます。

* 予定メンテナンス操作に対するコンテナー ワークロードを実行します。 たとえば、コンテナー化されたアプリを実行して、不必要なイメージをレジストリから削除します。
* ライブサイトの監視の一環として、勤務日の間に運用イメージで一連のテストを実行します。

Azure Cloud Shell または Azure CLI のローカル インストールを使って、この記事の例を実行できます。 それをローカルで使う場合は、バージョン 2.0.68 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。


## <a name="about-scheduling-a-task"></a>タスクのスケジュールについて

* **Cron 式でのトリガー** - タスクのタイマー トリガーでは、"*Cron 式*" を使います。 式は、タスクをトリガーする分、時、日、月、曜日を指定する 5 つのフィールドを含む文字列です。 最大で 1 分ごとに 1 回の頻度までサポートされます。 

  たとえば、式 `"0 12 * * Mon-Fri"` では、各平日の UTC で正午にタスクがトリガーされます。 この記事の後半で[詳細](#cron-expressions)をご確認ください。
* **複数のタイマー トリガー** - スケジュールが異なる限り、1 つのタスクに複数のタイマーを追加できます。 
    * タスクを作成するときに複数のタイマー トリガーを指定するか、または後で追加します。
    * 必要に応じて、管理しやすいようにトリガーの名前を指定します。指定しないと、ACR タスクによって既定のトリガー名が提供されます。
    * 一度に複数のタイマー スケジュールが重なっている場合、ACR タスクでは各タイマーのスケジュールされた時刻にタスクがトリガーされます。 
* **その他のタスク トリガー** - タイマーによってトリガーされるタスクでは、[ソース コードのコミット](container-registry-tutorial-build-task.md)または[基本イメージの更新](container-registry-tutorial-base-image-update.md)に基づいてトリガーを有効にすることもできます。 他の ACR タスクと同様に、スケジュールされたタスクを[手動でトリガー][az-acr-task-run]することもできます。

## <a name="create-a-task-with-a-timer-trigger"></a>タイマー トリガーを含むタスクを作成する

[az acr task create][az-acr-task-create] コマンドでタスクを作成するときに、必要に応じて、タイマー トリガーを追加することができます。 `--schedule` パラメーターを追加し、タイマーの Cron 式を渡します。 

簡単な例として、次のコマンドでは、毎日 21:00 UTC に Docker Hub からの `hello-world` イメージの実行がトリガーされます。 そのタスクは、ソース コードのコンテキストなしで実行されます。

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --context /dev/null \
  --cmd hello-world \
  --schedule "0 21 * * *"
```

タイマー トリガーが構成されていることを確認するには、[az acr task show][az-acr-task-show] コマンドを実行します。 既定では、基本イメージ更新トリガーも有効になります。

```console
$ az acr task show --name mytask --registry registry --output table
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

正しく設定されていることを確認するには、[az acr task run][az-acr-task-run] を使って手動でタスクをトリガーします。

```azurecli
az acr task run --name mytask --registry myregistry
```

コンテナーが正常に実行された場合、出力は次のようになります。

```console
Queued a run with ID: cf2a
Waiting for an agent...
2019/06/28 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2019/06/28 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2019/06/28 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

スケジュールされた時刻の後で、想定どおりにタイマーでタスクがトリガーされたことを確認するには、[az acr task list-runs][az-acr-task-list-runs] コマンドを実行します。

```azurecli
az acr task list runs --name mytask --registry myregistry --output table
``` 

タイマーが成功している場合、次のような出力が表示されます。

```console
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```
            
## <a name="manage-timer-triggers"></a>タイマー トリガーを管理する

ACR タスクのタイマー トリガーを管理するには、[az acr task timer][az-acr-task-timer] コマンドを使います。

### <a name="add-or-update-a-timer-trigger"></a>タイマー トリガーを追加または更新する

タスクを作成した後は、必要に応じて、[az acr task timer add][az-acr-task-timer-add] コマンドを使ってタイマー トリガーを追加します。 次の例では、前に作成した *mytask* に、*timer2* という名前のタイマー トリガーが追加されます。 このタイマーでは、毎日 10:30 UTC にタスクがトリガーされます。

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

既存のトリガーのスケジュールを更新したり、その状態を変更したりするには、[az acr task timer update][az-acr-task-timer-update] コマンドを使います。 たとえば、*timer2* という名前のトリガーを、11:30 UTC にタスクをトリガーするように更新します。

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>タイマー トリガーを一覧表示する

[az acr task timer list][az-acr-task-timer-list] コマンドでは、タスクに設定されているタイマー トリガーが表示されます。

```azurecli
az acr task timer list --name mytask --registry myregistry
```

出力例:

```JSON
[
  {
    "name": "timer2",
    "schedule": "30 11 * * *",
    "status": "Enabled"
  },
  {
    "name": "t1",
    "schedule": "0 21 * * *",
    "status": "Enabled"
  }
]
```

### <a name="remove-a-timer-trigger"></a>タイマー トリガーを削除する 

タスクからタイマー トリガーを削除するには、[az acr task timer remove][az-acr-task-timer-remove] コマンドを使います。 次の例では、*mytask* から *timer2* トリガーが削除されます。

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Cron 式

ACR タスクでは、Cron 式を解釈するために [NCronTab](https://github.com/atifaziz/NCrontab) ライブラリが使われます。 ACR タスクでは、5 つの必須フィールドが空白で区切られた式がサポートされます。

`{minute} {hour} {day} {month} {day-of-week}`

Cron 式で使われるタイム ゾーンは、協定世界時 (UTC) です。 時間は 24 時間形式です。

> [!NOTE]
> ACR タスクでは、Cron 式の `{second}` または `{year}` フィールドはサポートされていません。 別のシステムで使われている Cron 式をコピーする場合、それらのフィールドが使われている場合は、忘れずに削除してください。

各フィールドは、次の種類の値のいずれかを持つことができます。

|Type  |例  |トリガーのタイミング  |
|---------|---------|---------|
|特定の値 |<nobr>"5 * * * *"</nobr>|毎時、正時から 5 分後|
|すべての値 (`*`)|<nobr>"* 5 * * *"</nobr>|5:00 UTC からの 1 時間の毎分 (1 日に 60 回 )|
|範囲 (`-` 演算子)|<nobr>"0 1-3 * * *"</nobr>|1 日 3 回、1:00、2:00、3:00 UTC|  
|値のセット (`,` 演算子)|<nobr>"20,30,40 * * * *"</nobr>|1 時間に 3 回、各時の 20 分、30 分、40 分|
|間隔値 (`/` 演算子)|<nobr>"*/10 * * * *"</nobr>|1 時間に 6 回、各時の 10 分、20 分など

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Cron の例

|例|トリガーのタイミング  |
|---------|---------|
|`"*/5 * * * *"`|5 分ごとに 1 回|
|`"0 * * * *"`|毎正時に 1 回|
|`"0 */2 * * *"`|2 時間に 1 回|
|`"0 9-17 * * *"`|9:00 から 17:00 UTC まで、1 時間に 1 回|
|`"30 9 * * *"`|毎日 9:30 UTC|
|`"30 9 * * 1-5"`|毎平日 9:30 UTC|
|`"30 9 * Jan Mon"`|1 月の毎週月曜日の 9:30 UTC|


## <a name="next-steps"></a>次の手順

ソース コード コミットまたは基本イメージ更新によってトリガーされるタスクの例については、[ACR タスク チュートリアル シリーズ](container-registry-tutorial-quick-task.md)に関するページをご覧ください。



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az-acr-task-timer-add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az-acr-task-timer-remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az-acr-task-timer-list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az-acr-task-timer-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli