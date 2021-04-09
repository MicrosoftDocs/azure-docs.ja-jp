---
title: Synapse RBAC ロール
description: この記事では、組み込みの Synapse RBAC ロールについて説明します。
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 35f66732fa9cb48b94f80bab203534c9d04b7a7b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100102123"
---
# <a name="synapse-rbac-roles"></a>Synapse RBAC ロール

この記事では、組み込みの Synapse RBAC ロール、それらに付与されているアクセス許可、それらを使用できるスコープについて説明します。  

## <a name="whats-changed-since-the-preview"></a>プレビュー以降の変更点
プレビュー期間中に提供された Synapse RBAC ロールに精通しているユーザーの場合は、次の変更が適用されます。
- ワークスペース管理者は **Synapse 管理者** という名前に変更されました。
- Apache Spark 管理者は **Synapse Apache Spark 管理者** という名前に変更され、公開されているすべてのコード成果物 (SQL スクリプトを含む) を表示するためのアクセス許可が与えられます。  このロールでは、Synapse 資格情報ユーザー ロールに必要なワークスペース MSI を使用するためのアクセス許可が与えられなくなりました。  このアクセス許可は、パイプラインを実行するために必要です。 
- SQL 管理者は、**Synapse SQL 管理者** という名前に変更され、公開されているすべてのコード成果物 (Spark のノートブックやジョブを含む) を表示するためのアクセス許可が与えられます。  このロールでは、Synapse 資格情報ユーザー ロールに必要なワークスペース MSI を使用するためのアクセス許可が与えられなくなりました。 このアクセス許可は、パイプラインを実行するために必要です。
- 特定の分析ランタイムではなく、開発と運用のペルソナのサポートに焦点を絞った、**新しいきめ細かな Synapse RBAC ロール** が導入されました。  
- いくつかのロールのための、**新しいより低いレベルのスコープ** が導入されました。  これらのスコープを使用すると、ロールを特定のリソースまたはオブジェクトに制限できます。

>[!Note]
>**新しい Synapse RBAC ロールとより低いレベルのスコープは現在プレビュー段階です**。  完全にサポートされているこれらの新しいロールとスコープを使用し、それらの使用に関するフィードバックを提供することをお勧めします。

## <a name="built-in-synapse-rbac-roles-and-scopes"></a>組み込みの Synapse RBAC ロールとスコープ

次の表では、組み込みロールと、それらを使用できるスコープについて説明します。

>[!Note]
> 任意のスコープの任意の Synapse RBAC ロールを持つユーザーには、ワークスペース スコープの Synapse ユーザー ロールが自動的に割り当てられます。 

|Role |アクセス許可|スコープ|
|---|---|-----|
|Synapse 管理者  |サーバーレス SQL プール、Apache Spark プール、統合ランタイムへの Synapse のフル アクセス。  公開されているすべてのコード成果物への作成、読み取り、更新、削除のアクセスが含まれます。  ワークスペース システム ID の資格情報に対するコンピューティング オペレーター、リンクされた Data Manager、資格情報ユーザーのアクセス許可が含まれます。  Synapse RBAC ロールの割り当てが含まれます。 Synapse 管理者だけでなく、Azure 所有者も Synapse RBAC ロールを割り当てることができます。 コンピューティング リソースを作成、削除、管理するには、Azure のアクセス許可が必要です。 </br></br>_成果物の読み取りと書き込みができる</br> Spark アクティビティに対するすべてのアクションを実行できる</br>Spark プールのログを表示できる</br> 保存されたノートブックとパイプライン出力を表示できる </br> リンクされたサービスまたは資格情報によって格納されたシークレットを使用できる</br> SQL の `db_datareader`、`db_datawriter`、`connect`、`grant` アクセス許可を使用して SQL サーバーレス エンドポイントに接続できる </br> 現在のスコープの Synapse RBAC ロールを割り当てたり、取り消したりできる_|ワークスペース </br> Spark プール<br/>統合ランタイム </br>リンクされたサービス</br>資格情報 |
|Synapse Apache Spark 管理者</br>|Apache Spark プールへの Synapse のフル アクセス。  公開されている Spark ジョブ定義、ノートブック、その出力、およびライブラリ、リンクされたサービス、資格情報への作成、読み取り、更新、削除のアクセス。  その他の公開されているすべてのコード成果物への読み取りアクセスが含まれます。 資格情報を使用したり、パイプラインを実行したりするためのアクセス許可は含まれません。 アクセス権の付与は含まれません。 </br></br>_Spark 成果物に対するすべてのアクションを実行できる</br>Spark アクティビティに対するすべてのアクションを実行できる_|ワークスペース</br>Spark プール|
|Synapse SQL 管理者|サーバーレス SQL プールへの Synapse のフル アクセス。  公開されている SQL スクリプト、資格情報、リンクされたサービスへの作成、読み取り、更新、削除のアクセス。  その他の公開されているすべてのコード成果物への読み取りアクセスが含まれます。  資格情報を使用したり、パイプラインを実行したりするためのアクセス許可は含まれません。 アクセス権の付与は含まれません。 </br></br>*SQL スクリプトに対するすべてのアクションを実行できる<br/>SQL の `db_datareader`、`db_datawriter`、`connect`、`grant` アクセス許可を使用して SQL サーバーレス エンドポイントに接続できる*|ワークスペース|
|Synapse 共同作成者|Apache Spark プールと統合ランタイムへの Synapse のフル アクセス。 公開されているすべてのコード成果物とその出力 (資格情報やリンクされたサービスを含む) への作成、読み取り、更新、削除のアクセスが含まれます。  コンピューティング オペレーターのアクセス許可が含まれます。 資格情報を使用したり、パイプラインを実行したりするためのアクセス許可は含まれません。 アクセス権の付与は含まれません。 </br></br>_成果物の読み取りと書き込みができる</br>保存されたノートブックとパイプライン出力を表示できる</br>Spark アクティビティに対するすべてのアクションを実行できる</br>Spark プールのログを表示できる_|ワークスペース </br> Spark プール<br/> 統合ランタイム|
|Synapse 成果物発行元|公開されているコード成果物とその出力への作成、読み取り、更新、削除のアクセス。 コードまたはパイプラインを実行したり、アクセス権を付与したりするためのアクセス許可は含まれません。 </br></br>_公開されている成果物を読み取ったり、成果物を公開したりできる</br>保存されたノートブック、Spark ジョブ、パイプライン出力を表示できる_|ワークスペース
|Synapse 成果物ユーザー|公開されているコード成果物とその出力への読み取りアクセス。 新しい成果物を作成できますが、追加のアクセス許可なしに変更を公開したり、コードを実行したりすることはできません。|ワークスペース
|Synapse コンピューティング オペレーター |Spark ジョブおよびノートブックを送信したり、ログを表示したりします。  任意のユーザーから送信された Spark ジョブの取り消しが含まれます。 パイプラインを実行し、パイプライン実行および出力を表示するには、ワークスペース システム ID に対する資格情報使用の追加のアクセス許可が必要です。 </br></br>_ジョブ (他のユーザーから送信されたジョブを含む) を送信したり、取り消したりできる</br>Spark プールのログを表示できる_|ワークスペース</br>Spark プール</br>統合ランタイム|
|Synapse 資格情報ユーザー|パイプライン実行などのアクティビティでの資格情報やリンクされたサービス内での、シークレットのランタイムと構成時の使用。 パイプラインを実行するには、ワークスペース システム ID にスコープ指定されたこのロールが必要です。 </br></br>_資格情報にスコープ指定されているため、資格情報によって保護されているリンクされたサービス経由でデータにアクセスできる (コンピューティング使用のアクセス許可も必要) </br>ワークスペース システム ID の資格情報によって保護されているパイプラインを実行できる (コンピューティング使用の追加のアクセス許可を使用)_|ワークスペース </br>リンクされたサービス</br>資格情報
|Synapse のリンクされた Data Manager|マネージド プライベート エンドポイント、リンクされたサービス、資格情報の作成と管理。 資格情報によって保護されているリンクされたサービスを使用するマネージド プライベート エンドポイントを作成できる|ワークスペース|
|Synapse ユーザー|SQL プール、Apache Spark プール、統合ランタイム、公開されているリンクされたサービスと資格情報の詳細を一覧表示および表示します。 その他の公開されているコード成果物は含まれません。  新しい成果物を作成できますが、追加のアクセス許可なしに実行したり公開したりすることはできません。</br></br>_Spark プール、統合ランタイムを一覧表示したり、読み取ったりできる_|ワークスペース、Spark プール</br>リンクされたサービス </br>資格情報|

## <a name="synapse-rbac-roles-and-the-actions-they-permit"></a>Synapse RBAC ロールとそれによって許可されるアクション

>[!Note]
>- 下の表に一覧表示されているすべてのアクションの前に "Microsoft.Synapse/..." が付加されます。</br>
>- 成果物の読み取り、書き込み、削除のアクションはすべて、ライブ サービスで公開されている成果物に関するものです。  これらのアクセス許可は、接続されている Git リポジトリ内の成果物へのアクセスには影響を与えません。  

次の表は、組み込みロールとそれによってサポートされるアクション/アクセス許可の一覧を示しています。

ロール|アクション
--|--
Synapse 管理者|workspaces/read</br>workspaces/roleAssignments/write、delete</br>workspaces/managedPrivateEndpoint/write、delete</br>workspaces/bigDataPools/useCompute/action</br>workspaces/bigDataPools/viewLogs/action</br>workspaces/integrationRuntimes/useCompute/action</br>workspaces/artifacts/read</br>workspaces/notebooks/write、delete</br>workspaces/sparkJobDefinitions/write、delete</br>workspaces/sqlScripts/write、delete</br>workspaces/dataFlows/write、delete</br>workspaces/pipelines/write、delete</br>workspaces/triggers/write、delete</br>workspaces/datasets/write、delete</br>workspaces/libraries/write、delete</br>workspaces/linkedServices/write、delete</br>workspaces/credentials/write、delete</br>workspaces/notebooks/viewOutputs/action</br>workspaces/pipelines/viewOutputs/action</br>workspaces/linkedServices/useSecret/action</br>workspaces/credentials/useSecret/action|
|Synapse Apache Spark 管理者|workspaces/read</br>workspaces/bigDataPools/useCompute/action</br>workspaces/bigDataPools/viewLogs/action</br>workspaces/notebooks/viewOutputs/action</br>workspaces/artifacts/read</br>workspaces/notebooks/write、delete</br>workspaces/sparkJobDefinitions/write、delete</br>workspaces/libraries/write、delete</br>workspaces/linkedServices/write、delete</br>workspaces/credentials/write、delete|
|Synapse SQL 管理者|workspaces/read</br>workspaces/artifacts/read</br>workspaces/sqlScripts/write、delete</br>workspaces/linkedServices/write、delete</br>workspaces/credentials/write、delete|
|Synapse 共同作成者|workspaces/read</br>workspaces/bigDataPools/useCompute/action</br>workspaces/bigDataPools/viewLogs/action</br>workspaces/integrationRuntimes/useCompute/action</br>workspaces/integrationRuntimes/viewLogs/action</br>workspaces/artifacts/read</br>workspaces/notebooks/write、delete</br>workspaces/sparkJobDefinitions/write、delete</br>workspaces/sqlScripts/write、delete</br>workspaces/dataFlows/write、delete</br>workspaces/pipelines/write、delete</br>workspaces/triggers/write、delete</br>workspaces/datasets/write、delete</br>workspaces/libraries/write、delete</br>workspaces/linkedServices/write、delete</br>workspaces/credentials/write、delete</br>workspaces/notebooks/viewOutputs/action</br>workspaces/pipelines/viewOutputs/action|
|Synapse 成果物発行元|workspaces/read</br>workspaces/artifacts/read</br>workspaces/notebooks/write、delete</br>workspaces/sparkJobDefinitions/write、delete</br>workspaces/sqlScripts/write、delete</br>workspaces/dataFlows/write、delete</br>workspaces/pipelines/write、delete</br>workspaces/triggers/write、delete</br>workspaces/datasets/write、delete</br>workspaces/libraries/write、delete</br>workspaces/linkedServices/write、delete</br>workspaces/credentials/write、delete</br>workspaces/notebooks/viewOutputs/action</br>workspaces/pipelines/viewOutputs/action|
|Synapse 成果物ユーザー|workspaces/read</br>workspaces/artifacts/read</br>workspaces/notebooks/viewOutputs/action</br>workspaces/pipelines/viewOutputs/action|
|Synapse コンピューティング オペレーター |workspaces/read</br>workspaces/bigDataPools/useCompute/action</br>workspaces/bigDataPools/viewLogs/action</br>workspaces/integrationRuntimes/useCompute/action</br>workspaces/integrationRuntimes/viewLogs/action|
|Synapse 資格情報ユーザー|workspaces/read</br>workspaces/linkedServices/useSecret/action</br>workspaces/credentials/useSecret/action|
|Synapse のリンクされた Data Manager|workspaces/read</br>workspaces/managedPrivateEndpoint/write、delete</br>workspaces/linkedServices/write、delete</br>workspaces/credentials/write、delete|
|Synapse ユーザー|workspaces/read|

## <a name="synapse-rbac-actions-and-the-roles-that-permit-them"></a>Synapse RBAC アクションとそれを許可するロール

次の表は、Synapse アクションとこのアクションを許可する組み込みロールの一覧を示しています。

アクション|Role
--|--
workspaces/read|Synapse 管理者</br>Synapse Apache Spark 管理者</br>Synapse SQL 管理者</br>Synapse 共同作成者</br>Synapse 成果物発行元</br>Synapse 成果物ユーザー</br>Synapse コンピューティング オペレーター </br>Synapse 資格情報ユーザー</br>Synapse のリンクされた Data Manager</br>Synapse ユーザー 
workspaces/roleAssignments/write、delete|Synapse 管理者
workspaces/managedPrivateEndpoint/write、delete|Synapse 管理者</br>Synapse のリンクされた Data Manager
workspaces/bigDataPools/useCompute/action|Synapse 管理者</br>Synapse Apache Spark 管理者</br>Synapse 共同作成者</br>Synapse コンピューティング オペレーター 
workspaces/bigDataPools/viewLogs/action|Synapse 管理者</br>Synapse Apache Spark 管理者</br>Synapse 共同作成者</br>Synapse コンピューティング オペレーター 
workspaces/integrationRuntimes/useCompute/action|Synapse 管理者</br>Synapse 共同作成者</br>Synapse コンピューティング オペレーター 
workspaces/artifacts/read|Synapse 管理者</br>Synapse Apache Spark 管理者</br>Synapse SQL 管理者</br>Synapse 共同作成者</br>Synapse 成果物発行元</br>Synapse 成果物ユーザー
workspaces/notebooks/write、delete|Synapse 管理者</br>Synapse Apache Spark 管理者</br>Synapse 共同作成者</br>Synapse 成果物発行元
workspaces/sparkJobDefinitions/write、delete|Synapse 管理者</br>Synapse Apache Spark 管理者</br>Synapse 共同作成者</br>Synapse 成果物発行元
workspaces/sqlScripts/write、delete|Synapse 管理者</br>Synapse SQL 管理者</br>Synapse 共同作成者</br>Synapse 成果物発行元
workspaces/dataFlows/write、delete|Synapse 管理者</br>Synapse 共同作成者</br>Synapse 成果物発行元
workspaces/pipelines/write、delete|Synapse 管理者</br>Synapse 共同作成者</br>Synapse 成果物発行元
workspaces/triggers/write、delete|Synapse 管理者</br>Synapse 共同作成者</br>Synapse 成果物発行元
workspaces/datasets/write、delete|Synapse 管理者</br>Synapse 共同作成者</br>Synapse 成果物発行元
workspaces/libraries/write、delete|Synapse 管理者</br>Synapse Apache Spark 管理者</br>Synapse 共同作成者</br>Synapse 成果物発行元
workspaces/linkedServices/write、delete|Synapse 管理者</br>Synapse 共同作成者</br>Synapse 成果物発行元</br>Synapse のリンクされた Data Manager
workspaces/credentials/write、delete|Synapse 管理者</br>Synapse 共同作成者</br>Synapse 成果物発行元</br>Synapse のリンクされた Data Manager
workspaces/notebooks/viewOutputs/action|Synapse 管理者</br>Synapse Apache Spark 管理者</br>Synapse 共同作成者</br>Synapse 成果物発行元</br>Synapse 成果物ユーザー
workspaces/pipelines/viewOutputs/action|Synapse 管理者</br>Synapse 共同作成者</br>Synapse 成果物発行元</br>Synapse 成果物ユーザー
workspaces/linkedServices/useSecret/action|Synapse 管理者</br>Synapse 資格情報ユーザー
workspaces/credentials/useSecret/action|Synapse 管理者</br>Synapse 資格情報ユーザー

## <a name="synapse-rbac-scopes-and-their-supported-roles"></a>Synapse RBAC スコープとそれによってサポートされるロール

次の表は、Synapse RBAC スコープと各スコープで割り当てることができるロールの一覧を示しています。 

>[!note]
>オブジェクトを作成または削除するには、より高いレベルのスコープのアクセス許可が必要です。

Scope|ロール
--|--
ワークスペース |Synapse 管理者</br>Synapse Apache Spark 管理者</br>Synapse SQL 管理者</br>Synapse 共同作成者</br>Synapse 成果物発行元</br>Synapse 成果物ユーザー</br>Synapse コンピューティング オペレーター </br>Synapse 資格情報ユーザー</br>Synapse のリンクされた Data Manager</br>Synapse ユーザー
Apache Spark プール | Synapse 管理者 </br>Synapse 共同作成者 </br> Synapse コンピューティング オペレーター
統合ランタイム | Synapse 管理者 </br>Synapse 共同作成者 </br> Synapse コンピューティング オペレーター
リンクされたサービス |Synapse 管理者 </br>Synapse 資格情報ユーザー
資格情報 |Synapse 管理者 </br>Synapse 資格情報ユーザー
 
>[!note]
>成果物のロールとアクションはすべて、ワークスペース レベルでスコープ指定されます。 

## <a name="next-steps"></a>次のステップ

ワークスペースの [Synapse RBAC ロールの割り当てを確認する方法](./how-to-review-synapse-rbac-role-assignments.md)について学習します。

[Synapse RBAC ロールの割り当て方法](./how-to-manage-synapse-rbac-role-assignments.md)について学習します
