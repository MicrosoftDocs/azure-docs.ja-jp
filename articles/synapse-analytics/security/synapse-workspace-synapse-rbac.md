---
title: Synapse ロールベースのアクセス制御
description: Azure Synapse Analytics のロールベースのアクセス制御について説明する記事
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9e96d6decba679c7a4764a77f1e9720000faf78c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100105149"
---
# <a name="what-is-synapse-role-based-access-control-rbac"></a>Synapse ロールベースのアクセス制御 (RBAC) とは

Synapse RBAC では、Synapse ワークスペースとそのコンテンツに合わせて [Azure RBAC](../../role-based-access-control/overview.md) の機能が拡張されています。 

Azure RBAC は、Synapse ワークスペースとその SQL プール、Apache Spark プール、統合ランタイムを作成、更新、または削除できるユーザーを管理するために使用されます。

Synapse RBAC は、以下を実行できるユーザーを管理するために使用されます。
- コード成果物を発行し、発行されたコード成果物を一覧表示したり、それにアクセスしたりする 
- Apaches Spark プールや統合ランタイムに対してコードを実行する
- 資格情報で保護されたリンク (データ) サービスにアクセスする 
- ジョブの実行を監視またはキャンセルし、ジョブの出力や実行ログを確認する  

>[!Note]
>Synapse RBAC は、発行された SQL スクリプトへのアクセスを管理するために使用されますが、サーバーレス SQL プールへの制限付きアクセス制御のみを提供し、専用 SQL プールへのアクセス制御には使用 _されません_。  SQL プールへのアクセスは、主に SQL のセキュリティを使用して制御されます。

## <a name="what-can-i-do-with-synapse-rbac"></a>Synapse RBAC でできること

Synapse RBAC でできることの例を次に示します。
  - Apache Spark のノートブックやジョブに加えられた変更をユーザーがライブ サービスに発行できるようにします。
  - ユーザーが特定の Apache Spark プールでノートブックや Spark ジョブを実行したり取り消したりできるようにします。
  - ユーザーが特定の資格情報を使用できるようにします。これにより、ワークスペースのシステム ID で保護されたパイプラインを実行したり、資格情報で保護されたリンク サービス内のデータにアクセスしたりできます。 
  - 管理者が特定の Spark プールでのジョブの実行を管理、監視、キャンセルできるようにします。    

## <a name="how-synapse-rbac-works"></a>Synapse RBAC のしくみ
Azure RBAC と同様に、Synapse RBAC もロールの割り当てを作成することによって機能します。 ロールの割り当ては、セキュリティ プリンシパル、ロールの定義、スコープの 3 つの要素で構成されます。  

### <a name="security-principals"></a>セキュリティ プリンシパル

"_セキュリティ プリンシパル_" は、ユーザー、グループ、サービス プリンシパル、またはマネージド ID です。

### <a name="roles"></a>ロール
 
"_ロール_" は、特定のリソースの種類または成果物の種類に対して実行できるアクセス許可またはアクションのコレクションです。

Synapse には、さまざまなペルソナのニーズに適合するアクションのコレクションを定義する複数の組み込みロールがあります。
- 管理者は、ワークスペースを作成および構成するためのフルアクセスを取得できます 
- 開発者は、SQL スクリプト、ノートブック、パイプライン、データフローなどを作成、更新、デバッグできますが、このコードを運用環境のコンピューティング リソースまたはデータに対して発行したり、実行したりすることはできません
- オペレーターは、コードまたは実行からの出力にアクセスせずに、システムの状態やアプリケーションの実行を監視および管理したり、ログを確認したりできます。
- セキュリティ スタッフは、コード、コンピューティング リソースまたはデータにアクセスしなくても、エンドポイントを管理および構成できます。

Synapse の組み込みロールについて詳しくは、[こちら](./synapse-workspace-synapse-rbac-roles.md)をご覧ください。 

### <a name="scopes"></a>スコープ

"_スコープ_" は、アクセスが適用されるリソースまたは成果物を定義します。  Synapse では、階層型スコープがサポートされています。  上位のスコープで付与されたアクセス許可は、下位のオブジェクトによって継承されます。  Synapse RBAC では、最上位のスコープはワークスペースです。  ワークスペース スコープでロールを割り当てると、ワークスペース内の該当するすべてのオブジェクトにアクセス許可が付与されます。  

ワークスペース内で現在サポートされているスコープは、Apache Spark プール、統合ランタイム、リンク サービス、資格情報です。 

コード成果物へのアクセス権は、ワークスペース スコープで付与されます。  ワークスペース内の成果物コレクションへのアクセス権の付与は、今後のリリースでサポートされる予定です。

## <a name="resolving-role-assignments-to-determine-permissions"></a>アクセス許可を決定するためのロールの割り当ての解決

ロールの割り当てによって、指定されたスコープでロールによって定義されたアクセス許可がプリンシパルに付与されます。

Synapse RBAC は、Azure RBAC と同じように加算方式のモデルです。 1 つのプリンシパルにさまざまなスコープで複数のロールを割り当てることができます。 セキュリティ プリンシパルのアクセス許可を計算する場合、システムでは、そのプリンシパルと、そのプリンシパルが直接または間接的に含まれるグループの両方に割り当てられたすべてのロールが考慮されます。  また、適用されるアクセス許可を決定する際に各割り当てのスコープも考慮されます。  

## <a name="enforcing-assigned-permissions"></a>割り当てられたアクセス許可の適用

Synapse Studio では、必要なアクセス許可をお持ちでない場合にアクションを試みると、特定のボタンやオプションが淡色表示されたり、アクセス許可エラーが返されたりすることがあります。 

ボタンやオプションが無効になっている場合は、そのボタンまたはオプションの上にカーソルを合わせると、必要なアクセス許可を示すツールヒントが表示されます。  Synapse 管理者に問い合わせて、必要なアクセス許可を付与するロールを割り当ててください。 特定のアクションを提供するロールについては、[こちら](./synapse-workspace-synapse-rbac-roles.md)をご覧ください。

## <a name="who-can-assign-synapse-rbac-roles"></a>Synapse RBAC ロールを割り当てることができるユーザー

Synapse RBAC ロールを割り当てることができるのは、Synapse 管理者だけです。  ワークスペース レベルの Synapse 管理者は、任意のスコープでアクセス権を付与できます。  下位レベルのスコープの Synapse 管理者は、そのスコープでのアクセス権のみを付与できます。 

新しいワークスペースが作成されると、その作成者には、ワークスペース スコープの Synapse 管理者ロールが自動的に付与されます。   

## <a name="where-do-i-manage-synapse-rbac"></a>Synapse RBAC を管理する場所

Synapse RBAC は、管理ハブのアクセス制御ツールを使用して Synapse Studio 内から管理されます。 

## <a name="next-steps"></a>次のステップ

組み込みの [Synapse RBAC ロール](./synapse-workspace-synapse-rbac-roles.md)について理解します。

ワークスペースの [Synapse RBAC ロールの割り当てを確認する方法](./how-to-review-synapse-rbac-role-assignments.md)について学習します。

[Synapse RBAC ロールの割り当て方法](./how-to-manage-synapse-rbac-role-assignments.md)について学習します