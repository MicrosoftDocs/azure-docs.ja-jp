---
title: "Azure Service Fabric Reliable State Manager および Reliable Collection の内部 | Microsoft Docs"
description: "Azure Service Fabric における Reliable Collection の概念と設計について詳細に調べます。"
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: mcoskun
ms.openlocfilehash: d607449a16e886337ab1bd96213fbb4231124353
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-service-fabric-reliable-state-manager-and-reliable-collection-internals"></a>Azure Service Fabric Reliable State Manager および Reliable Collection の内部
このドキュメントでは、Reliable State Manager と Reliable Collection の内部を詳細に調べ、背後でコア コンポーネントがどのように動作しているかを確認します。

> [!NOTE]
> このドキュメントは作成途中です。 さらに詳細に知りたいトピックがある場合は、この記事にコメントを追加してください。
>

##  <a name="local-persistence-model-log-and-checkpoint"></a>ローカル永続性モデル: ログとチェックポイント
Reliable State Manager および Reliable Collection は、ログとチェックポイントと呼ばれる永続性モデルに従います。
このモデルでは、各状態の変化が最初にディスクにログ記録され、次にメモリ内に適用されます。
完全な状態自体はごくまれに永続化されます ( チェックポイントとしても知られています)。
このモデルの利点は、差分がディスクに対する追加専用のシーケンシャル書き込みに変換されるため、パフォーマンスが向上することです。

ログとチェックポイント モデルをよく理解するために、まず無限のディスクのシナリオを見てみましょう。
Reliable State Manager は、すべての操作をレプリケートされる前にログに記録します。
ログ記録により、Reliable Collection は操作をメモリ内にのみ適用できます。
ログは永続化されているため、レプリカに障害が発生して再起動が必要になった場合でも、Reliable State Manager のログ内には、レプリカによって失われたすべての操作を再生するための十分な情報があります。
ディスクは無限であるためログ レコードを削除する必要はなく、Reliable Collection が実行する必要があるのは、メモリ内にある状態を管理することだけです。

次に、有限のディスクのシナリオを見ていきましょう。
ログ レコードが蓄積すると、Reliable State Manager が使用するディスク領域が不足します。
そのような不足が発生する前に、Reliable State Manager は、ログを切り捨てて新しいレコードを格納する領域を確保する必要があります。
Reliable State Manager は、メモリ内の状態をディスクにチェックポイントとして記録するよう Reliable Collection に要求します。
この時点で、Reliable Collection はそのメモリ内の状態を永続化します。
Reliable Collection がチェックポイントを完了すると、Reliable State Manager はログを切り捨ててディスク領域を解放できます。
レプリカを再起動する必要がある場合、Reliable Collection はチェックポイントとして記録された状態を回復し、Reliable State Manager は、最後のチェックポイント以降に発生したすべての状態の変化を回復して再生します。

チェックポイント機能の別の付加価値として、一般的なシナリオでの回復時間の向上があります。 ログには、最後のチェックポイント以降に発生したすべての操作が含まれています。
そのため、Reliable Dictionary 内の特定の行の複数の値のように、項目の複数のバージョンが含まれている可能性があります。
これに対して、Reliable Collection は、キーの各値の最新バージョンのみをチェックポイントとして記録します。

## <a name="next-steps"></a>次のステップ
* [トランザクションとロック](service-fabric-reliable-services-reliable-collections-transactions-locks.md)

