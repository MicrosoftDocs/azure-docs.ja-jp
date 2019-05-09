---
title: プールとノードのエラーのチェック - Azure Batch
description: エラーを確認してプールとノードを作成するときに回避する方法
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 9/25/2018
ms.topic: conceptual
ms.openlocfilehash: 8d8df9935e935ac8d5a1194cfab103a006cf5546
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791343"
---
# <a name="check-for-pool-and-node-errors"></a>プールとノードのエラーのチェック

Azure Batch プールを作成して管理するとき、いくつかの操作は直ちに行われます。 ただし、一部の操作は非同期で、バック グラウンドで実行されます。 これらは完了までに数分かかる場合があります。

API、CLI、UI で直ちにエラーが返されるので、すぐに実行される操作のエラーを検出するのは簡単です。

この記事では、プールとプールのノードに対して発生することがあるバック グラウンド操作について説明します。 ここでは、障害を検出して回避する具体的な方法を示します。

## <a name="pool-errors"></a>プールのエラー

### <a name="resize-timeout-or-failure"></a>タイムアウトまたはエラーのサイズ変更

新しいプール作成する場合、または既存のプールのサイズ変更を行う場合、ノードのターゲット数はユーザーが指定します。  操作はすぐに完了しますが、新しいノードを実際に割り当てたり既存のノードを削除したりするには数分かかる場合があります。  サイズ変更タイムアウトを[作成](https://docs.microsoft.com/rest/api/batchservice/pool/add)または[サイズ変更](https://docs.microsoft.com/rest/api/batchservice/pool/resize) API で指定します。 サイズ変更タイムアウトの期間中にバッチがターゲット数のノードを取得できない場合、バッチは操作を停止します。 プールは安定した状態になり、サイズ変更エラーを報告します。

最新の評価の [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) プロパティでは、サイズ変更タイムアウトが報告され、発生したエラーが一覧表示されます。

サイズ変更タイムアウトの一般的な原因は次のとおりです。

- サイズ変更タイムアウトが短すぎる
  - ほとんどの状況では、プールのノードを割り当てまたは削除するための時間は、既定のタイムアウトである 15 分あれば十分です。
  - 割り当てるノードの数が多い場合は、サイズ変更タイムアウトを 30 分に設定することをお勧めします。 たとえば、Azure Marketplace イメージから 1,000 を超えるノードにサイズ変更したり、カスタム VM イメージから 300 を超えるノードにサイズ変更したりする場合が該当します。
- 不十分なコア クォータ
  - バッチアカウントでは、すべてのプール全体に割り当て可能なコア数が制限されています。 そのクォータに達すると、バッチはノードの割り当てを停止します。 バッチがさらに多くのノードを割り当てられるようにするために、コア クォータを[増やすことができます](https://docs.microsoft.com/azure/batch/batch-quota-limit)。
- [プールが仮想ネットワーク内にある](https://docs.microsoft.com/azure/batch/batch-virtual-network)場合の不足しているサブネット IP アドレス
  - 仮想ネットワーク サブネットには、要求されたすべてのプールのノードに割り当てるための未割り当ての IP アドレスが十分に存在する必要があります。 そうでない場合、ノードを作成できません。
- [プールが仮想ネットワーク](https://docs.microsoft.com/azure/batch/batch-virtual-network)の時の不十分なリソース
  - ロード バランサー、パブリック IP、ネットワーク セキュリティ グループなどのリソースを、バッチ アカウントと同じサブスクリプションに作成する場合があります。 これらのリソースのためのサブスクリプション クォータが十分であるか確認してください。
- カスタム VM イメージを使用した大規模なプール
  - カスタム VM イメージを使用する大規模なプールは割り当てに時間がかかり、タイムアウトのサイズ変更が発生することがあります。  制限と構成に関する推奨事項については、[カスタム イメージを使用して仮想マシンのプールを作成する](https://docs.microsoft.com/azure/batch/batch-custom-images)を参照してください。

### <a name="automatic-scaling-failures"></a>自動スケールの失敗

プール内のノード数を自動的に拡大縮小するように Azure Batch を設定することもできます。 [プール用の自動スケールの数式](https://docs.microsoft.com/azure/batch/batch-automatic-scaling)のパラメーターを定義します。 Batch サービスは数式を使用して、プール内のノード数を定期的に評価し、新しいターゲット数を設定します。 次の種類の問題が発生する可能性があります。

- 自動スケール評価が失敗する。
- 結果のサイズ変更操作が失敗してタイムアウトになることがある。
- 自動スケールの数式に問題があるため、ノードのターゲット値が正しくなくなる。 サイズ変更は動作するかタイムアウトになる。

[autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) プロパティを使用して、最後の自動スケール評価に関する情報を取得することができます。 このプロパティは、評価期間、値と結果、およびパフォーマンス エラーを報告します。

[プールのサイズ変更完了イベント](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event)により、すべての評価に関する情報がキャプチャされます。

### <a name="delete"></a>削除

ノードを含むプールを削除するとき、Batch はまずノードを削除します。 次にプール オブジェクト自体を削除します。 プールのノードを削除するまでに数分かかります。

Batch は削除プロセス中に[プールの状態](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate)を **deleting** に設定します。 プールの削除時間がかかりすぎている場合、呼び出しアプリケーションが **state** と **stateTransitionTime** プロパティを使用して検出します。

## <a name="pool-compute-node-errors"></a>プールのコンピューティング ノードエラー

Batch がプール内のノードを正常に割り当てた場合でも、さまざまな問題が原因で一部のノードが異常な状態になったり使用できなくなる場合があります。 これらのノードには料金が発生します。 使用できないノードへの支払いをなくすために、問題を検出することが重要です。

### <a name="start-task-failure"></a>タスクの開始に失敗

プールについて、省略可能な[タスクの開始](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)を指定する場合もあります。 すべてのタスクと同じように、ストレージからダウンロードするためにコマンドラインとリソース ファイルを指定できます。 タスクの開始は各ノードの開始後にノードごとに実行されます。 **WaitForSuccess** プロパティは、Batch がすべてのタスクをノードにスケジュールする前に、タスクの開始が正常に完了するまで Batch が待機するかどうかを指定します。

タスクの開始が正しく完了するまで待機するようノードを構成したが、タスクの開始が失敗した場合はどうなるでしょうか。 その場合、ノードは使用できませんが、料金は発生します。

タスクの開始の失敗は、最上位レベルの [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) ノードプロパティの [result](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) と [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) プロパティを使って検出できます。

また、**waitForSuccess** を **true** に設定した場合、タスクの開始に失敗すると、Batch はノードの[状態](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate)を **starttaskfailed** に設定します。

すべてのタスクと同様、タスクの開始の失敗には多くの原因があります。  トラブルシューティングするには stdout、stderr、さらにタスク固有のログ ファイルをチェックしてください。

### <a name="application-package-download-failure"></a>アプリケーション パッケージのダウンロード エラー

プール用の 1 つ以上のアプリケーション パッケージを指定できます。 Batch は指定されたパッケージ ファイルを各ノードにダウンロードし、ノードが開始した後、タスクがスケジュールされる前にファイルを圧縮解除します。 アプリケーション パッケージと組み合わせてタスクの開始のコマンドラインを使用することが一般的です。 たとえば、別の場所にファイルをコピーしたり、セットアップを実行したりする場合が該当します。

ノード[エラー](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror)プロパティは、ダウンロードやアプリケーション パッケージの圧縮解除に失敗したノードを報告します。 Batch はノードの状態を**使用不可**に設定します。

### <a name="node-in-unusable-state"></a>ノードは使用できない状態

Azure Batch はさまざまな理由で[ノード状態](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate)を**使用不可**に設定します。 ノード状態が**使用不可**に設定された場合、ノードにタスクをスケジュールできませんが、料金は発生します。

Batch は、使用できないノードの回復を常に試みますが、原因によっては、回復の可能性がある場合とない場合があります。

Batch が原因を特定できる場合、ノードの[エラー](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror)プロパティは原因を報告します。

**使用できない** ノードが発生する原因として、次のような例もあります。

- カスタム VM イメージが無効である。 たとえば、イメージが適切に作成されていない場合があります。
- インフラストラクチャの障害または低レベルのアップグレードのため、VM が移動した。 Batch はノードを回復します。

### <a name="node-agent-log-files"></a>ノード エージェント ログ ファイル

各プールのノードで実行される Batch エージェント プロセスはログ ファイルを作成でき、このログ ファイルは、プールのノード上の問題についてサポートに連絡する必要があるときに役立つ場合があります。 ノードのログ ファイルは、Azure portal、Batch Explorer、または [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs) 経由でアップロードできます。 ログ ファイルをアップロードして保存することをお勧めします。 実行中のノードのコストを節約するために、ノードまたはプールを後で削除できます。

## <a name="next-steps"></a>次の手順

包括的なエラー チェック (特に非同期操作に対するエラー チェック) を実装するようにアプリケーションを設定したことを確認します。 これは問題をすばやく検出して診断するために欠かない場合があります。
