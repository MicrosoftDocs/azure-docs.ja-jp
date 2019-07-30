---
title: Azure Service Fabric でコード パッケージ エラーを診断する | Microsoft Docs
description: Azure Service Fabric で一般的なコード パッケージ エラーのトラブルシューティングを行う方法について説明します
services: service-fabric
documentationcenter: .net
author: grzuber
manager: gkhanna
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 235952388d2c044cc141b3020c67944c4250ea3d
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276580"
---
# <a name="diagnose-common-code-package-errors-with-service-fabric"></a>Service Fabric で一般的なコード パッケージ エラーを診断する

この記事では、コード パッケージの予想外の終了が何を意味するかを示し、一般的なエラー コードの考えられる原因の洞察と、問題を緩和する可能性があるトラブルシューティング手順を提供します。

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>プロセスまたはコンテナーが予想外に終了するとき

Service Fabric でコード パッケージを開始する要求が受信されると、アプリとサービス マニフェストに設定された構成オプションに基づいて、ローカル システム上での環境の準備が開始されます。 これらの準備には、ネットワーク エンドポイントまたはリソースの予約、ファイアウォール規則の構成、またはリソースのガバナンス制約の設定が含まれる可能性があります。 環境が適切に構成された後、Service Fabric では、コード パッケージの起動が試行されます。 この手順では、OS またはコンテナーのランタイムで、プロセスまたはコンテナーが正常にアクティブ化されていると報告された場合に成功とみなされます。 アクティブ化が成功しなかった場合は、SFX に以下の正常性メッセージが表示されます。

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

コード パッケージが正常に起動した後、Service Fabric でその有効期間の監視が開始されます。 この時点で、プロセスまたはコンテナーがさまざまな理由で終了する可能性があります。 DLL の初期化の失敗や OS のデスクトップ ヒープ領域の不足などが発生する可能性があります。コード パッケージが終了した場合は、SFX に次の正常性メッセージが表示されます。

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

この正常性メッセージに提示されている終了コードは、プロセス/コンテナーが提供するプロセス/コンテナーが終了した理由を示す唯一の手掛かりであり、スタックのいずれかのレベルで生成されている可能性があります。 この終了コードが何に関連しているか (OS エラーや .NET の問題) や、お使いのコードによって発生したかを理解するのは容易ではありません。 その結果、終了元の終了コードと、可能性がある解決策を診断するための出発点としてこの記事を使用できますが、これらは一般的なシナリオに対する一般的なソリューションであり、実際に発生しているエラーには適用されない場合があることを覚えておいてください。

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>自分のコード パッケージによって Service Fabric が終了したかどうかを判断する方法

さまざまな理由によるコード パッケージの終了の責任が Service Fabric にある場合があります。 たとえば、負荷分散のためにコード パッケージを別のノードに配置するように決定している場合があります。 次の表の終了コードのいずれかが表示されている場合は、コード パッケージが Service Fabric によって終了されたと判断できます。

>[!NOTE]
> 次の表に含まれていない終了コードでプロセス/コンテナーが終了した場合、終了の責任は Service Fabric にはありません。

終了コード | 説明
--------- | -----------
7147 | これらのエラー コードは、Ctrl + C シグナルの送信によって、プロセス/コンテナーが Service Fabric によってシャットダウンされたことを示しています。
7148 | これらのエラー コードは、Service Fabric によってプロセス/コンテナーが終了したことを示しています。 場合によっては、このエラー コードは、Ctrl + C シグナルの送信後に、プロセス/コンテナーが適切なタイミングで応答しなかったために強制終了する必要があったことを示している可能性があります。


## <a name="other-common-error-codes-and-their-potential-fixes"></a>その他の一般的なエラー コードと考えられる解決策

終了コード | 16 進値 | 簡単な説明 | 根本原因 | 考えられる解決策
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | このエラーは、コンピューターでデスクトップ ヒープ領域が不足していることを意味している可能性があります。 これは、お使いのアプリケーションに属している多数のプロセスがノード上で実行されている場合に、特に起こりやすくなります。 | プログラムに Ctrl + C シグナルへの応答が組み込まれていない場合は、クラスター マニフェストで "EnableActivateNoWindow" 設定を有効にできます。 この設定の有効化が意味しているのは、コード パッケージを GUI ウィンドウなしで実行し、Ctrl + C シグナルを受信しないことですが、各プロセスによって消費されるデスクトップ ヒープ領域の容量が削減される可能性があります。 コード パッケージで Ctrl + C シグナルを受信する必要がある場合は、ノードのデスクトップ ヒープのサイズを増やすことができます。
3762504530 | 0xe0434352 | 該当なし | この値は、マネージド コード (つまり .NET ) からのハンドルされない例外用のエラー コードです。 | この終了コードが表示されている場合は、アプリケーションで発生した例外がハンドルされないままであり、それによってプロセスが終了したことを示唆しています。 アプリケーションのログとダンプをデバッグすることが、エラーの原因を判断する際の最初の手順になります。

## <a name="next-steps"></a>次の手順

* [他の一般的なシナリオの診断](service-fabric-diagnostics-common-scenarios.md)を確認します。
* Azure Monitor ログとそれが提供するサービスの詳しい概要について、[Azure Monitor ログとは何か](../operations-management-suite/operations-management-suite-overview.md)に関するページで確認します。
* 検出と診断に役立つ Azure Monitor ログの[アラート](../log-analytics/log-analytics-alerts.md)についてさらに学習します。
* Azure Monitor ログの一部として提供されている[ログ検索とクエリ](../log-analytics/log-analytics-log-searches.md)機能の詳細を確認します
