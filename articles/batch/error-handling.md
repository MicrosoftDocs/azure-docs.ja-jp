---
title: Azure Batch でのエラー処理と検出
description: Batch サービスのワークフローでのエラー処理について、開発の観点から説明します。
ms.topic: article
ms.date: 05/15/2020
ms.openlocfilehash: 3bd460598dae08fa18415e1c9865249f3ca4c9c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85964279"
---
# <a name="error-handling-and-detection-in-azure-batch"></a>Azure Batch でのエラー処理と検出

ご自分の Batch ソリューション内で、タスク エラーとアプリケーション エラーを処理することが必要になる場合があります。 この記事では、エラーの種類とその解決方法について説明します。

## <a name="error-codes"></a>エラー コード

一般的なエラーの種類は、次のとおりです。

- Batch に到達しなかった要求や、Batch 応答が時間内にクライアントに到達しなかった要求によるネットワーク エラー。
- 標準の 5xx の状態コードの HTTP 応答による内部サーバー エラー。
- Retry-after ヘッダーが付いた 429 や 503 の状態コードの HTTP 応答などの、調整関連のエラー。
- AlreadyExists と InvalidOperation などの 4xx エラー。 これは、状態遷移でリソースが正しい状態にないことを意味します。

REST API、Batch サービス、ジョブ タスク/スケジュールのエラー コードなどの具体的なエラー コードについては、「[バッチの状態とエラー コード](/rest/api/batchservice/batch-status-and-error-codes)」を参照してください。

## <a name="application-failures"></a>アプリケーション エラー

アプリケーションの実行中に、問題のトラブルシューティングに利用できる診断情報が生成される場合があります。 「[ファイルとディレクトリ](files-and-directories.md)」で説明したように、Batch サービスは、コンピューティング ノードのタスク ディレクトリにある `stdout.txt` ファイルと `stderr.txt` ファイルに標準出力と標準エラー出力を書き込みます。

これらのファイルは Azure Portal またはいずれかの Batch SDK を使用してダウンロードすることができます。 たとえば、Batch .NET ライブラリの [ComputeNode.GetNodeFile](/dotnet/api/microsoft.azure.batch.computenode) や [CloudTask.GetNodeFile](/dotnet/api/microsoft.azure.batch.cloudtask) でこれらのファイルを取得して、トラブルシューティングに利用できます。

## <a name="task-errors"></a>タスク エラー

タスク エラーは、いくつかのカテゴリに分類できます。

### <a name="pre-processing-errors"></a>前処理エラー

タスクを開始できなかった場合、そのタスクには前処理エラーが設定されます。  

前処理エラーの原因には、タスクのリソース ファイルが移動された、Storage アカウントが利用できなくなった、ノードへのファイルのコピーに支障をきたす別の問題が発生した、などがあります。

### <a name="file-upload-errors"></a>ファイルのアップロード エラー

タスクに対して指定されたファイルのアップロードがなんらかの理由で失敗すると、そのタスクにはファイルのアップロード エラーが設定されます。

ファイル アップロード エラーの原因には、Azure Storage にアクセスするために指定された SAS が無効であるか書き込み権限がない、ストレージ アカウントが利用できなくなった、ノードからのファイルのコピーに支障をきたす別の問題が発生した、などがあります。

### <a name="application-errors"></a>アプリケーション エラー

タスクのコマンド ラインで指定されたプロセスも失敗することがあります。 タスクで実行されたプロセスによってゼロ以外の終了コードが返された場合、プロセスが失敗したと見なされます (次のセクションの「 *タスクの終了コード* 」を参照)。

アプリケーション エラーについては、Batch を構成して、指定した回数まで自動的にタスクが再試行されるようにできます。

### <a name="constraint-errors"></a>制約エラー

ジョブまたはタスクの最大実行期間を指定する制約である *maxWallClockTime* を設定することができます。 これは、処理に失敗したタスクを終了させる場合に便利です。

最大実行時間を超過したタスクは "*完了*" としてマークされますが、終了コードは `0xC000013A` に設定され、*schedulingError* フィールドは `{ category:"ServerError", code="TaskEnded"}` としてマークされます。

## <a name="task-exit-codes"></a>タスクの終了コード

前述したように、タスクによって実行されたプロセスからゼロ以外の終了コードが返された場合、そのタスクには、失敗したことを示すマークが Batch サービスによって設定されます。 タスクでプロセスが実行されると、Batch によって、そのプロセスのリターン コードがそのタスクの終了コード プロパティに設定されます。

タスクの終了コードを決めるのは Batch サービスではないことに注意してください。 タスクの終了コードは、プロセス自体またはそのプロセスを実行したオペレーティング システムによって決定されます。

## <a name="task-failures-or-interruptions"></a>タスクのエラーや中断

タスクは、エラーが発生したり中断されたりする場合があります。 タスク アプリケーション自体にエラーが発生したり、タスクが実行されているノードが再起動されることがあります。また、プールの割り当て解除ポリシーがタスクの完了を待たずに直ちにノードを削除するように設定されている場合は、サイズ変更操作中にノードがプールから削除されることもあります。 どのようなケースでも、Batch によってタスクを自動的にキューに戻し、別のノードで実行することができます。

断続的に発生する問題によって、タスクが応答を停止したり、実行に長い時間がかかるようになったりする場合もあります。 このような場合は、タスクに最大実行間隔を設定することができます。 最大実効間隔を超過すると、Batch サービスによってタスク アプリケーションが中断されます。

## <a name="connect-to-compute-nodes"></a>コンピューティング ノードへの接続

リモートからコンピューティング ノードにサインインすることによって、さらに踏み込んだデバッグやトラブルシューティングを実行できます。 Azure Portal を使用して、Windows ノードのリモート デスクトップ プロトコル (RDP) ファイルをダウンロードしたり、Linux ノードの Secure Shell (SSH) 接続情報を取得したりすることができます。 このような操作は、Batch API ([Batch .NET](/dotnet/api/microsoft.azure.batch.computenode) や [Batch Python](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh) など) で実行できます。

> [!IMPORTANT]
> RDP や SSH を通じてノードに接続するには、まず、ノード上にユーザーを作成する必要があります。 Azure portal から Batch REST API を使用して[ユーザー アカウントをノードに追加](/rest/api/batchservice/computenode/adduser)し、Batch .NET の [ComputeNode.CreateComputeNodeUser](/dotnet/api/microsoft.azure.batch.computenode) メソッドを呼び出すか、Batch Python モジュールの [add_user](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh) メソッドを呼び出してください。

計算ノードへの RDP アクセスまたは SSH アクセスを制限または無効にする必要がある場合は、「[Configure or disable remote access to compute nodes in an Azure Batch pool (Azure Batch プールの計算ノードへのリモート アクセスを構成または無効にする)](pool-endpoint-configuration.md)」を参照してください。

## <a name="troubleshoot-problem-nodes"></a>問題のあるノードのトラブルシューティング

一部のタスクが失敗する場合は、Batch クライアント アプリケーションまたはサービスで、失敗したタスクのメタデータを調べて、正常に動作していないノードを特定できます。 プール内のノードにはそれぞれ一意の ID があり、タスクが実行されるノードはタスクのメタデータに含まれています。 問題のあるノードが特定できたら、そのノードで以下に示すいくつかの対策を実行します。

- **ノードを再起動する** ([REST](/rest/api/batchservice/computenode/reboot) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.reboot)))

    ノードを再起動すると、途中で停止したプロセスやクラッシュしたプロセスなどの潜在的な問題が解決することがあります。 プールで開始タスクを使用している場合や、ジョブでジョブ準備タスクを使用している場合、それらはノードの再起動時に実行されます。
- **ノードを再イメージ化する** ([REST](/rest/api/batchservice/computenode/reimage) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.reimage))

    この操作では、ノード上のオペレーティング システムが再インストールされます。 ノードの再起動と同様、開始タスクとジョブ準備タスクはノードの再イメージ化後に再実行されます。
- **プールからノードを削除する** ([REST](/rest/api/batchservice/pool/removenodes) | [.NET](/dotnet/api/microsoft.azure.batch.pooloperations))

    場合によっては、プールからノードを完全に削除する必要があります。
- **ノードでタスク スケジュールを無効にする** ([REST](/rest/api/batchservice/computenode/disablescheduling) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.disablescheduling))

    この操作では、ノードが実質的にオフラインになります。そのため、これ以上タスクが割り当てられなくなりますが、ノードをプール内で稼働したままにできます。 これにより、失敗したタスクのデータが失われず、これ以上ノードでタスクが失敗することもなくなり、エラーの原因を詳しく調査できます。 たとえば、ノードでタスク スケジュールを無効にした後、リモートからサインインして、ノードのイベント ログを確認したり、他のトラブルシューティングを実行したりできます。 ご自分の調査が完了したら、タスク スケジュール ([REST](/rest/api/batchservice/computenode/enablescheduling) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.enablescheduling)) を有効にしたり、前述のいずれかのアクションを実行することで、ノードをオンラインに戻すことができます。

> [!IMPORTANT]
> 前述のアクションでは、ノードで現在実行中のタスクをアクションの実行時にどのように処理するかを指定できます。 たとえば、Batch .NET クライアント ライブラリを使用してノードでタスク スケジュールを無効にするときに、[DisableComputeNodeSchedulingOption](/dotnet/api/microsoft.azure.batch.common.disablecomputenodeschedulingoption) 列挙値を指定して、実行中のタスクを終了するか (**Terminate**)、他のノードでスケジュールするためにキューに再登録するか (**Requeue**)、実行中のタスクが完了してからアクションを実行するか (**TaskCompletion**) を指定できます。

## <a name="retry-after-errors"></a>エラー後の再試行

Batch API では、発生したエラーが通知されます。 これらはすべて再試行でき、これらにはすべてその目的用のグローバル再試行ハンドラーが含まれています。 この組み込みのメカニズムを使用することをお勧めします。

エラーが発生した場合、少し待ってから再試行してください (次の再試行まで数秒待機します)。 再試行の回数が多すぎる場合、または再試行が早すぎる場合は、再試行ハンドラーは調整されます。

## <a name="next-steps"></a>次のステップ

- [プールおよびノード エラーを確認する](batch-pool-node-error-checking.md)方法について学習します。
- [ジョブおよびタスク エラーを確認する](batch-job-task-error-checking.md)方法について学習します。
- [Batch の状態とエラー コード](/rest/api/batchservice/batch-status-and-error-codes)の一覧を確認します。
