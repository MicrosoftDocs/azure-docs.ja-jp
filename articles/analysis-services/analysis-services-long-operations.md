---
title: Azure Analysis Services の実行時間の長い操作に関するベスト プラクティス | Microsoft Docs
description: この記事では、実行時間の長い操作に関するベスト プラクティスについて説明します。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: owend
ms.openlocfilehash: 4e069effae0cb7f834b2c3dac696d05304d841a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92014836"
---
# <a name="best-practices-for-long-running-operations"></a>実行時間の長い操作に関するベスト プラクティス

Azure Analysis Services では、*ノード* は、サーバー リソースが実行されているホスト仮想マシンを表します。 サーバー リソースが別のノードに移動した場合、実行時間の長いクエリ、更新操作、クエリのスケールアウト同期など、一部の操作が失敗する可能性があります。 このシナリオでの一般的なエラー メッセージには、次のようなものがあります。

- "An error has occurred while trying to locate a long running XMLA request. (実行時間の長い XMLA 要求の特定中にエラーが発生しました。) The request might have been interrupted by service upgrade or server restart. (サービスのアップグレードまたはサーバーの再起動によって要求が中断された可能性があります。)"
- "Job with ID '<guid>for model '<database>' was canceled due to service error (inactivity) with message 'Cancelling the refresh request since it was stuck without any updates. (ID 'for model' のジョブはサービス エラーのためキャンセルされました。メッセージは次のとおりです。処理が停止して更新を実行できないため更新要求をキャンセルしています。) This is an internal service issue. (これは内部サービスの問題です。) Please resubmit the job or file a ticket to get help if this issue happens repeatedly. (この問題が解決しない場合は、ジョブを再送信するか、サポートを受けるためのチケットを送信してください。)"

実行時間の長い操作が中断される理由は多数あります。 たとえば、次のような Azure の更新プログラムがあります。 
- オペレーティング システムの修正プログラム 
- セキュリティ更新プログラム
- Azure Analysis Services サービスの更新プログラム
- Service Fabric の更新プログラム。 Service Fabric は、Azure Analysis Services を含む多数の Microsoft クラウド サービスによって使用されるプラットフォーム コンポーネントです。

サービスで発生する更新以外にも、負荷分散によってノード間でサービスの自然な移動が発生します。 ノードの移動はクラウド サービスで想定されている動作です。 Azure Analysis Services はノードの移動の影響を最小限に抑えようとしますが、完全に排除することはできません。 

ノードの移動だけでなく、他にも障害が発生する可能性があります。 たとえば、データ ソースのデータベース システムが停止したり、ネットワーク接続が失われたりする場合があります。 更新中に、1,000 万行のパーティションの 900 万行目で障害が発生したような場合、障害の発生箇所から更新を再開する方法はありません。 サービスを最初から再開する必要があります。 

## <a name="refresh-rest-api"></a>REST API を更新する

サービスの中断により、データ更新のように実行時間の長い操作に重大な支障をきたす可能性があります。 Azure Analysis Services には、サービス中断の悪影響の軽減に役立つ REST API が含まれています。 詳細については、「[REST API を使用した非同期更新](analysis-services-async-refresh.md)」を参照してください。
 
REST API 以外にも、実行時間の長い更新操作中に起こりうる問題の最小化に使用できるその他のアプローチがあります。 主な目標は、更新操作を最初から再開する必要をなくし、その代わりに、段階的にコミットできる小さなバッチで更新を実行することです。 
 
REST API はそのような再開を可能にしますが、パーティションの作成と削除という完全な柔軟性は提供しません。 シナリオで複雑なデータ管理操作が必要な場合、ソリューションのロジックに何らかの形式のバッチ処理を含める必要があります。 たとえば、トランザクションを使用して、複数の独立したバッチでデータを処理すれば、障害が発生しても最初から再開する必要はなく、中間のチェックポイントから処理を再開することができます。 
 
## <a name="scale-out-query-replicas"></a>スケールアウト クエリのレプリカ

REST とカスタム ロジックのどちらを使用している場合でも、バッチの処理中、クライアント アプリケーション クエリが一貫性のない結果や中間結果を返す可能性は残ります。 処理の発生中、クライアント アプリケーション クエリによって返される一貫性のあるデータが必要であり、モデル データが中間状態にある場合、[スケールアウト](analysis-services-scale-out.md)を読み取り専用クエリ レプリカと共に使用できます。

読み取り専用クエリ レプリカを使用することにより、更新がバッチで実行されている間、クライアント アプリケーションのユーザーは引き続き、読み取り専用レプリカ上のデータの古いスナップショットに対してクエリを実行できます。 更新が完了したら、同期操作を実行して読み取り専用レプリカを最新の状態にすることができます。


## <a name="next-steps"></a>次のステップ

[REST API を使用した非同期更新](analysis-services-async-refresh.md)  
[Azure Analysis Services のスケールアウト](analysis-services-scale-out.md)  
[Analysis Services の高可用性](analysis-services-bcdr.md)  
[Azure サービスの再試行ガイダンス](/azure/architecture/best-practices/retry-service-specific)