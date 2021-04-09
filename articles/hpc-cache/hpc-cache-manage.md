---
title: Azure HPC Cache を管理して更新する
description: Azure portal または Azure CLI を使用して Azure HPC Cache を管理および更新する方法
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/08/2021
ms.author: v-erkel
ms.openlocfilehash: b34beb65bb8c4136887651d8365c937b17718572
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103471878"
---
# <a name="manage-your-cache"></a>キャッシュを管理する

Azure portal のキャッシュの概要ページには、お使いのキャッシュに関して、プロジェクトの詳細、キャッシュの状態、基本的な統計が表示されます。 キャッシュの停止または開始、キャッシュの削除、長期ストレージへのデータのフラッシュ、ソフトウェア更新を行うためのコントロールも用意されています。

この記事では、Azure CLI を使用してこれらの基本的なタスクを実行する方法についても説明します。

概要ページを開くには、Azure portal でお使いのキャッシュのリソースを選択します。 たとえば、 **[すべてのリソース]** ページを読み込んで、キャッシュ名をクリックします。

![Azure HPC Cache インスタンスの [概要] ページのスクリーンショット](media/hpc-cache-overview.png)

ページの上部のボタンは、キャッシュを管理する助けになります。

* **[開始]** と [ **[停止]**](#stop-the-cache) - キャッシュ操作を再開または中断します
* [ **[フラッシュ]**](#flush-cached-data) - 変更されたデータをストレージ ターゲットに書き込みます
* [ **[フラッシュ]**](#upgrade-cache-software) - キャッシュ ソフトウェアを更新します
* [ **[診断の収集]**](#collect-diagnostics) - デバッグ情報をアップロードします
* **[最新の情報に更新]** - 概要ページを再読み込みします
* [ **[削除]**](#delete-the-cache) - キャッシュを完全に破棄します

これらのオプションの詳細については、以下をご覧ください。

キャッシュ管理タスクをデモンストレーションする[ビデオ](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)を視聴するには、次の画像をクリックしてください。

[![ビデオのサムネイル:Azure HPC Cache:管理 (クリックしてビデオ ページにアクセス)](media/video-5-manage.png)](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)

## <a name="stop-the-cache"></a>キャッシュを停止する

キャッシュを停止して、アクティブでない期間中のコストを削減することができます。 キャッシュが停止している間はアップタイムに対して課金されませんが、キャッシュの割り当て済みディスク ストレージに対しては課金されます。 (詳細については、[価格](https://aka.ms/hpc-cache-pricing)のページを参照してください。)

停止したキャッシュはクライアント要求に応答しません。 キャッシュを停止する前に、クライアントのマウントを解除する必要があります。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

**[停止]** ボタンで、アクティブなキャッシュが中断されます。 **[停止]** ボタンは、キャッシュの状態が **[正常]** または **[低下]** の場合に使用できます。

![[停止] が強調表示された上部のボタンと、'続行しますか?' とたずねている停止アクションのポップアップ メッセージのスクリーンショット [はい] (既定) および [いいえ] ボタンで '続行しますか?' とたずねているポップアップ メッセージのスクリーンショット](media/stop-cache.png)

[はい] をクリックしてキャッシュの停止を確認すると、キャッシュの内容がストレージ ターゲットに自動的にフラッシュされます。 この処理には時間がかかることがありますが、データの一貫性が確保されます。 最後に、キャッシュの状態が **[Stopped]\(停止\)** に変わります。

停止しているキャッシュを再アクティブ化するには、 **[開始]** ボタンをクリックします。 確認は必要ありません。

![[開始] が強調表示されている上部のボタンのスクリーンショット](media/start-cache.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache 向けに Azure CLI を設定します](./az-cli-prerequisites.md)。

[az hpc-cache stop](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-stop) コマンドを使用して、一時的にキャッシュを一時停止します。 このアクションが有効になるのは、キャッシュの状態が **[正常]** または **[低下]** の場合のみです。

停止するまで、キャッシュの内容がストレージ ターゲットに自動的にフラッシュされます。 この処理には時間がかかることがありますが、データの一貫性が確保されます。

アクションが完了すると、キャッシュの状態が **[Stopped]\(停止\)** に変わります。

停止しているキャッシュを再アクティブ化するには、[az hpc-cache start](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-start) を使用します。

開始コマンドまたは停止コマンドを実行すると、操作が完了するまでコマンド ラインに "Running" というステータス メッセージが表示されます。

```azurecli
$ az hpc-cache start --name doc-cache0629
 - Running ..
```

完了すると、メッセージが "Finished" に更新され、リターン コードとその他の情報が表示されます。

```azurecli
$ az hpc-cache start --name doc-cache0629
{- Finished ..
  "endTime": "2020-07-01T18:46:43.6862478+00:00",
  "name": "c48d320f-f5f5-40ab-8b25-0ac065984f62",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-01T18:40:28.5468983+00:00",
  "status": "Succeeded"
}
```

---

## <a name="flush-cached-data"></a>キャッシュ データをフラッシュする

概要ページの **[フラッシュ]** ボタンは、キャッシュに格納されている変更されたすべてのデータを、バックエンド ストレージ ターゲットに直ちに書き込むようにキャッシュに指示します。 キャッシュはデータをストレージ ターゲットに定期的に保存するため、バックエンド ストレージ システムが最新の状態であることを確認する場合を除き、これを手動で行う必要はありません。 たとえば、ストレージのスナップショットを取ったり、データ セットのサイズを調べたりする前に **[フラッシュ]** を使用することがあります。

> [!NOTE]
> フラッシュ プロセスの間、キャッシュはクライアント要求を処理できません。 キャッシュ アクセスは一時停止され、操作の完了後に再開されます。

キャッシュのフラッシュ操作を開始すると、キャッシュはクライアント要求の受け付けを停止し、概要ページのキャッシュの状態は **[Flashing] (フラッシュ中)** に変わります。

キャッシュ内のデータは、適切なストレージ ターゲットに保存されます。 フラッシュする必要があるデータの量によっては、このプロセスに数分または 1 時間以上かかることがあります。

すべてのデータがストレージ ターゲットに保存された後、キャッシュは再度、クライアント要求の取得を自動的に開始します。 キャッシュの状態は **[正常]** に戻ります。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

キャッシュをフラッシュするには、 **[フラッシュ]** ボタンをクリックし、 **[はい]** をクリックして操作を確定します。

![[フラッシュ] が強調表示された一番上のボタンと、フラッシュ アクションが記述され、 [はい] (既定) および [いいえ] ボタンで '続行しますか?' とたずねているポップアップ メッセージのスクリーンショット](media/hpc-cache-flush.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache 向けに Azure CLI を設定します](./az-cli-prerequisites.md)。

[az hpc-cache flush](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-flush) を使用して、すべての変更されたデータをキャッシュからストレージ ターゲットに書き込むよう強制します。

例:

```azurecli
$ az hpc-cache flush --name doc-cache0629 --resource-group doc-rg
 - Running ..
```

フラッシュが完了すると、成功メッセージが返されます。

```azurecli
{- Finished ..
  "endTime": "2020-07-09T17:26:13.9371983+00:00",
  "name": "c22f8e12-fcf0-49e5-b897-6a6e579b6489",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-09T17:25:21.4278297+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="upgrade-cache-software"></a>キャッシュ ソフトウェアをアップグレードする

新しいソフトウェア バージョンが利用可能な場合は、 **[アップグレード]** ボタンがアクティブになります。 また、ページの上部にソフトウェアの更新に関するメッセージも表示されます。

![[アップグレード] ボタンが有効になった状態の、ボタンの最上位行のスクリーンショット](media/hpc-cache-upgrade-button.png)

ソフトウェアのアップグレード中にクライアント アクセスは中断されませんが、キャッシュのパフォーマンスが低下します。 ピーク以外の使用時間帯や計画済みのメンテナンス期間にソフトウェアをアップグレードするよう計画してください。

ソフトウェアの更新は数時間かかることがあります。 スループットが高い構成のキャッシュは、ピーク スループット値が低いキャッシュよりもアップグレードするのに時間がかかります。

ソフトウェア アップグレードが入手可能になった場合、それを手動で適用するには 1 週間程度かかります。 終了日は、アップグレード メッセージに記載されています。 ユーザーがその期間中にアップグレードしないと、Azure が自動的に、更新プログラムをキャッシュに適用します。 自動アップグレードのタイミングは構成可能ではありません。 キャッシュ パフォーマンスへの影響が懸念される場合は、有効期限が切れる前に、ソフトウェアを自分でアップグレードする必要があります。

有効期限が切れ、お使いのキャッシュが停止している場合、次回の起動時にキャッシュによってソフトウェアが自動的にアップグレードされます。 (更新プログラムは直ちに開始されない場合もありますが、最初の 1 時間で開始されます)。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

**[アップグレード]** ボタンをクリックしてソフトウェアの更新を開始します。 操作が完了するまで、キャッシュの状態は **[アップグレード中]** に変わります。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache 向けに Azure CLI を設定します](./az-cli-prerequisites.md)。

Azure CLI では、キャッシュの状態レポートの最後に新しいソフトウェア情報が含まれています。 (確認するには、[az hpc-cache show](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-show) を使用します。)メッセージで "upgradeStatus" という文字列を探します。

[az hpc-cache upgrade-firmware](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-upgrade-firmware) を使用して、更新プログラム (存在する場合) を適用します。

利用可能な更新プログラムがない場合、この操作による影響はありません。

この例では、キャッシュの状態 (利用可能な更新プログラムが無い) と、upgrade-firmware コマンドの結果を示します。

```azurecli
$ az hpc-cache show --name doc-cache0629
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },

<...>

  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.61",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-06-29T22:18:32.004822+00:00",
    "pendingFirmwareVersion": null
  }
}
$ az hpc-cache upgrade-firmware --name doc-cache0629
$
```

---

## <a name="collect-diagnostics"></a>診断を収集する

**[診断の収集]** ボタンをクリックすると、トラブルシューティングのために、システム情報を収集して Microsoft Service and Support にアップロードするプロセスを手動で開始できます。 重大なキャッシュの問題が発生した場合、キャッシュによって同じ診断情報が自動的に収集され、アップロードされます。

Microsoft Service and Support に要請された場合は、このコントロールを使用します。

ボタンをクリックした後に、 **[はい]** をクリックしてアップロードを確定します。

![[診断収集の開始] ポップアップ確認メッセージのスクリーンショット。 既定のボタン [はい] が強調表示されています。](media/diagnostics-confirm.png)

## <a name="delete-the-cache"></a>キャッシュの削除

**[削除]** ボタンでキャッシュが破棄されます。 キャッシュを削除すると、そのリソースすべてが破棄され、アカウントの料金が発生しなくなります。

キャッシュを削除しても、ストレージ ターゲットとして使用されたバックエンド ストレージ ボリュームは影響を受けません。 後で新しいキャッシュに追加することも、個別に使用を停止することもできます。

> [!NOTE]
> Azure HPC Cache は、キャッシュを削除する前に、変更されたデータをキャッシュからバックエンド ストレージ システムに自動的に書き込みません。
>
> キャッシュ内のすべてのデータが長期ストレージに書き込まれるようにするには、[キャッシュを停止](#stop-the-cache)してから、削除します。 削除する前に、ステータス **[Stopped]\(停止\)** が表示されていることを確認します。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

キャッシュを停止した後、 **[削除]** ボタンをクリックしてキャッシュを完全に削除します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache 向けに Azure CLI を設定します](./az-cli-prerequisites.md)。

キャッシュを完全に削除するには、Azure CLI コマンド [az hpc-cache delete](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-delete) を使用します。

例:
```azurecli
$ az hpc-cache delete --name doc-cache0629
 - Running ..

<...>

{- Finished ..
  "endTime": "2020-07-09T22:24:35.1605019+00:00",
  "name": "7d3cd0ba-11b3-4180-8298-d9cafc9f22c1",
  "startTime": "2020-07-09T22:13:32.0732892+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="cache-metrics-and-monitoring"></a>キャッシュのメトリックと監視

概要ページには、キャッシュのスループット、1 秒あたりの操作数、待機時間など、いくつかの基本的なキャッシュ統計情報のグラフが表示されます。

![サンプル キャッシュについて前述の統計情報を示している、3 つの折れ線グラフのスクリーンショット](media/hpc-cache-overview-stats.png)

これらのグラフは、Azure の組み込みの監視および分析ツールの一部です。 その他のツールとアラートは、ポータルのサイドバーにある **[監視]** という見出しの下にあるページから利用できます。 詳細については、[Azure 監視のドキュメント](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-in-the-azure-portal)のポータルに関するセクションを参照してください。

## <a name="view-warnings"></a>警告の表示

キャッシュが異常な状態になった場合は、 **[警告]** ページを確認してください。 このページに表示されるキャッシュ ソフトウェアから送られる通知は、その状態を理解するのに役立ちます。

これらの通知は Azure portal によって制御されないため、アクティビティ ログには表示されません。 これらは、多くがカスタム設定に関連付けられています。

ここに表示される警告の種類は、次のようなものです。

* キャッシュは NTP サーバーにアクセスできません
* キャッシュは拡張グループのユーザー名情報をダウンロードできませんでした
* カスタム DNS 設定がストレージ ターゲットで変更されました

![拡張グループのユーザー名をダウンロードできなかったことを示すメッセージが表示された [監視] > [警告] ページのスクリーンショット](media/warnings-page.png)

## <a name="next-steps"></a>次のステップ

* [Azure のメトリックと統計ツール](../azure-monitor/index.yml)について確認する
* [Azure HPC Cache に関する支援](hpc-cache-support-ticket.md)を依頼する
