---
title: Service Fabric を使用して一般的なコード パッケージ エラーを診断する
description: Azure Service Fabric で一般的なコード パッケージ エラーのトラブルシューティングを行う方法について説明します
author: grzuber
ms.topic: article
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 400651b240f0518a85b9deb7a7293a77a88b0861
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591710"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>Service Fabric を使用して一般的なコード パッケージ エラーを診断する

この記事では、コード パッケージが予期せず終了した場合に、それが何を意味するかについて説明します。 具体的には、一般的なエラー コードについての考えられる原因と、トラブルシューティングの手順について説明します。

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>プロセスまたはコンテナーが予想外に終了するとき

Azure Service Fabric でコード パッケージを開始する要求が受信されると、アプリとサービス マニフェストに設定されたオプションにしたがって、ローカル システム上での環境の準備が開始されます。 これらの準備には、ネットワーク エンドポイントまたはリソースの予約、ファイアウォール規則の構成、またはリソースのガバナンス制約の設定が含まれる可能性があります。 

環境が適切に構成された後、Service Fabric では、コード パッケージの起動が試行されます。 この手順では、OS またはコンテナーのランタイムで、プロセスまたはコンテナーが正常にアクティブ化されていると報告された場合に成功とみなされます。 アクティブ化が成功しなかった場合は、SFX に次のような正常性メッセージが表示されます。

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

コード パッケージが正常にアクティブ化された後、Service Fabric でその有効期間の監視が開始されます。 この時点で、プロセスまたはコンテナーがさまざまな理由で終了する可能性があります。 たとえば、DLL の初期化に失敗したり、OS のデスクトップ ヒープ領域が不足したりする可能性があります。 コード パッケージが終了した場合は、SFX に次の正常性メッセージが表示されます。

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

この正常性メッセージの終了コードは、プロセスまたはコンテナーが終了した理由を調べるための唯一の手掛かりとなります。 これは、どのレベルのスタックによっても生成される可能性があります。 たとえば、この終了コードは OS エラーや .NET の問題に関連している可能性もあれば、コードによって発生した可能性もあります。 この記事は、終了の原因と、考えられる解決方法を診断するための出発点として使用してください。 ただし、これらは一般的なシナリオに対する一般的な解決策であり、表示されているエラーには該当しない可能性があることに注意してください。

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>自分のコード パッケージによって Service Fabric が終了したかどうかを判断する方法

さまざまな理由によるコード パッケージの終了の責任が Service Fabric にある場合があります。 たとえば、負荷分散のためにコード パッケージを別のノードに配置するように決定している場合があります。 次の表の終了コードのいずれかが表示されている場合は、コード パッケージが Service Fabric によって終了されたと判断できます。

>[!NOTE]
> 次の表に含まれていない終了コードでプロセスやコンテナーが終了した場合、終了の責任は Service Fabric にはありません。

終了コード | 説明
--------- | -----------
7147 | Ctrl + C シグナルの送信によって、プロセスまたはコンテナーが Service Fabric によってシャットダウンされたことを示します。
7148 | Service Fabric によってプロセスまたはコンテナーが終了したことを示します。 場合によっては、このエラー コードは、Ctrl + C シグナルの送信後に、プロセスまたはコンテナーが適切なタイミングで応答しなかったために終了する必要があったことを示します。


## <a name="other-common-error-codes-and-their-potential-fixes"></a>その他の一般的なエラー コードと考えられる解決策

終了コード | 16 進数値 | 簡単な説明 | 根本原因 | 考えられる解決策
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | このエラーは、場合によっては、コンピューターでデスクトップ ヒープ領域が不足していることを意味します。 これは、お使いのアプリケーションに属しているプロセスがノード上で多数実行されている場合に、特に起こりやすくなります。 | プログラムに Ctrl + C シグナルへの応答が組み込まれていない場合は、クラスター マニフェストで **EnableActivateNoWindow** 設定を有効にできます。 この設定を有効にすると、コード パッケージは GUI ウィンドウなしで実行され、Ctrl + C シグナルを受け取ることはありません。 この操作により、各プロセスが使用するデスクトップヒープ領域の量も少なくなります。 コード パッケージで Ctrl + C シグナルを受信する必要がある場合は、ノードのデスクトップ ヒープのサイズを増やすことができます。
3762504530 | 0xe0434352 | 該当なし | この値は、マネージド コード (つまり .NET ) からのハンドルされない例外用のエラー コードを表します。 | この終了コードは、アプリケーションで発生した例外がハンドルされないままであり、それによってプロセスが終了したことを示唆しています。 このエラーの原因を特定するための最初の手順として、アプリケーションのログとダンプ ファイルをデバッグしてください。

## <a name="next-steps"></a>次のステップ

* [他の一般的なシナリオの診断](service-fabric-diagnostics-common-scenarios.md)を確認します。
* Azure Monitor ログとそれらが提供するサービスの詳しい概要について、「[Azure Monitor の概要](../azure-monitor/overview.md)」で確認します。
* 検出と診断に役立つ Azure Monitor ログの[アラート](../azure-monitor/alerts/alerts-overview.md)についてさらに学習します。
* Azure Monitor ログの一部として提供されている[ログ検索とクエリ](../azure-monitor/logs/log-query-overview.md)機能の詳細を確認します。
