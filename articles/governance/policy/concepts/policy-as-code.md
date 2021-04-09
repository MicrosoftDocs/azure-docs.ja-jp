---
title: コードとしての Azure Policy ワークフローを設計する
description: Azure Policy 定義をコードとしてデプロイし、リソースを自動的に検証するようにワークフローを設計する方法について説明します。
ms.date: 10/20/2020
ms.topic: conceptual
ms.openlocfilehash: 74d2097e4db4442e6e65f30541864fb554f7379d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94359682"
---
# <a name="design-azure-policy-as-code-workflows"></a>コードとしての Azure Policy ワークフローを設計する

クラウド ガバナンスの取り組みを進めるにつれて、各ポリシー定義の管理を、Azure portal 内あるいはさまざまな SDK を通じて手動で行うことから、エンタープライズ規模でより管理しやすい反復可能なものに移行したくなる場合もあります。 クラウドで大規模なシステムを管理するには、次の 2 つの方法があります。

- コードとしてのインフラストラクチャ:環境を定義するコンテンツ (Azure Resource Manager テンプレート (ARM テンプレート) から Azure Policy 定義、Azure Blueprints に至るすべてのもの) をソース コードとして処理する方法。
- DevOps:エンド ユーザーに価値を継続的に提供できる人材、プロセス、製品の集まりです。

コードとしての Azure Policy は、これらのアイデアを組み合わせたものです。 基本的には、ソース管理でポリシー定義を保持し、変更が行われるたびにその変更をテストして検証します。 ただし、これはコードとしてのインフラストラクチャまたは DevOps に対するポリシー関与の範囲とすべきではありません。

この検証手順も、他の継続的インテグレーションまたは継続的デプロイのワークフローのコンポーネントである必要があります。 例として、アプリケーション環境や仮想インフラストラクチャのデプロイなどがあります。 Azure Policy 検証をビルドおよびデプロイ プロセスの初期コンポーネントとすることにより、アプリケーションと運用チームは、運用環境へのデプロイを試行するよりもずっと前に、変更が準拠していないかどうかを遅滞なく見つけることができます。

## <a name="definitions-and-foundational-information"></a>定義と基本情報

コードとしての Azure Policy ワークフローの詳細に入る前に、次の定義と例を確認してください。

- [ポリシー定義](./definition-structure.md)
- [イニシアチブ定義](./initiative-definition-structure.md)

ファイル名は、ポリシーまたはイニシアチブのいずれかの定義の一部に対応しています。
- `policy(set).json` - 定義全体
- `policy(set).parameters.json` - 定義の `properties.parameters` の部分
- `policy.rules.json` - 定義の `properties.policyRule` の部分
- `policyset.definitions.json` - 定義の `properties.policyDefinitions` の部分

これらのファイル形式の例は、[Azure Policy GitHub リポジトリ](https://github.com/Azure/azure-policy/)で入手できます。

- ポリシー定義:[リソースにタグを追加する](https://github.com/Azure/azure-policy/tree/master/samples/Tags/add-tag)
- イニシアチブ定義:[課金タグ](https://github.com/Azure/azure-policy/tree/master/samples/PolicyInitiatives/multiple-billing-tags)

## <a name="workflow-overview"></a>ワークフローの概要

コードとしての Azure Policy の推奨される一般的なワークフローは、次の図のようになります。

:::image type="complex" source="../media/policy-as-code/policy-as-code-workflow.png" alt-text="作成からテスト、デプロイまでの、コードとしての Azure Policy のワークフロー ボックスを示している図。" border="false":::
   図は、コードとしての Azure Policy ワークフロー ボックスを示しています。 作成には、ポリシーとイニシアチブ定義の作成が含まれます。 テストには、強制モードが無効な割り当てが含まれます。 コンプライアンス対応状態のゲートウェイ チェックの後に、M S I アクセス許可の割り当ての付与およびリソースの修復が行われます。  デプロイには、強制モードが有効な割り当ての更新が含まれます。
:::image-end:::

### <a name="create-and-update-policy-definitions"></a>ポリシー定義を作成および更新する

ポリシー定義は JSON を使用して作成され、ソース管理に格納されます。 各ポリシーには、パラメーター、規則、環境パラメーターなどの独自のファイル セットがあり、これらは同じフォルダーに格納する必要があります。 次の構造は、ソース管理でポリシー定義を維持するための推奨される方法です。

```text
.
|
|- policies/  ________________________ # Root folder for policy resources
|  |- policy1/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- assign.<name1>.json _________ # Assignment 1 for this policy definition
|     |- assign.<name2>.json _________ # Assignment 2 for this policy definition
|  |- policy2/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- assign.<name1>.json _________ # Assignment 1 for this policy definition
|     |- assign.<name2>.json _________ # Assignment 2 for this policy definition
|
```

新しいポリシーを追加した場合、または既存のポリシーを更新した場合は、ワークフローによって Azure のポリシー定義が自動的に更新されます。 新しいポリシー定義または更新されたポリシー定義のテストについては、後の手順で説明します。

また、既存の定義と割り当てを [GitHub](https://www.github.com) のソース コード管理環境に取り込むには、「[Azure Policy リソースをエクスポートする](../how-to/export-resources.md)」を確認してください。

### <a name="create-and-update-initiative-definitions"></a>イニシアチブ定義を作成および更新する

同様に、イニシアチブには独自の JSON ファイルと、同じフォルダーに格納する必要がある関連ファイルがあります。 イニシアチブ定義では、ポリシー定義が既に存在している必要があるため、ポリシーのソースがソース管理で更新されて Azure で更新されるまで、イニシアチブ定義の作成および更新を行うことはできません。 次の構造は、ソース管理でイニシアチブ定義を維持するための推奨される方法です。

```text
.
|
|- initiatives/ ______________________ # Root folder for initiatives
|  |- init1/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- assign.<name1>.json _________ # Assignment 1 for this policy initiative
|     |- assign.<name2>.json _________ # Assignment 2 for this policy initiative
|
|  |- init2/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- assign.<name1>.json _________ # Assignment 1 for this policy initiative
|     |- assign.<name2>.json _________ # Assignment 2 for this policy initiative
|
```

ポリシー定義と同様に、既存のイニシアチブを追加または更新した場合、ワークフローは Azure のイニシアチブ定義を自動的に更新するはずです。 新しいイニシアチブ定義または更新されたイニシアチブ定義のテストについては、後の手順で説明します。

### <a name="test-and-validate-the-updated-definition"></a>更新された定義をテストおよび検証する

自動化によって、新たに作成または更新されたポリシーまたはイニシアチブの定義が取得され、Azure 内のオブジェクトが更新された後で、行った変更をテストします。 ポリシーまたはその一部であるイニシアチブは、運用環境から最も遠い環境のリソースに割り当てる必要があります。 通常、この環境は _開発_ 環境です。

割り当てでは、[enforcementMode](./assignment-structure.md#enforcement-mode) を _disabled_ にして、リソースの作成と更新がブロックされず、既存のリソースが、更新されたポリシー定義に準拠していることを引き続き監査されるようにする必要があります。 enforcementMode を使用する場合でも、割り当てスコープは、ポリシーの検証用のリソース グループまたはサブスクリプションのいずれかにすることをお勧めします。

> [!NOTE]
> 強制モードは便利ですが、これはさまざまな条件下でポリシー定義を完全にテストすることの代替手段ではありません。 ポリシー定義は、`PUT` および `PATCH` REST API 呼び出し、準拠リソースおよび非準拠リソース、およびリソースに不足しているプロパティなどのエッジ ケースを使用してテストする必要があります。

割り当てがデプロイされたら、Azure Policy SDK、[Azure Policy コンプライアンス スキャン GitHub アクション](https://github.com/marketplace/actions/azure-policy-compliance-scan)、または [Azure Pipelines のセキュリティとコンプライアンス評価タスク](/azure/devops/pipelines/tasks/deploy/azure-policy)を使用して、新しい割り当ての[コンプライアンス データを取得](../how-to/get-compliance-data.md)します。 ポリシーおよび割り当てのテストに使用する環境には、準拠リソースと非準拠リソースの両方が必要です。
コードの適切な単体テストと同様に、リソースが想定どおりであること、および偽陽性や偽陰性がないことをテストする必要があります。 期待したもののみにテストおよび検証を行った場合、予期しないまたは未確認の影響をポリシーから受ける可能性があります。 詳細については、「[新しい Azure ポリシー定義の影響を評価する](./evaluate-impact.md)」を参照してください。

### <a name="enable-remediation-tasks"></a>修復タスクを有効にする

割り当ての検証が期待どおりである場合、次の手順では修復を検証します。
[deployIfNotExists](./effects.md#deployifnotexists) または [modify](./effects.md#modify) のいずれかを使用するポリシーは、非準拠状態からの修復タスクと正しいリソースに変化する可能性があります。

リソースを修復するための最初の手順は、ポリシー割り当てに、ポリシー定義に定義されたロール割り当てを付与することです。 このロール割り当てによって、ポリシー割り当ての管理対象 ID に、リソースの準拠性を保つために必要な変更を行う十分な権限が与えられます。

ポリシー割り当てに適切な権限が付与されたら、ポリシー SDK を使用して、非準拠であることがわかっている一連のリソースに対して修復タスクをトリガーします。 続行する前に、これらの修復されたタスクに対して以下の 3 つのテストを完了する必要があります。

- 修復タスクが正常に完了したことを検証します
- ポリシーの評価を実行して、ポリシーのコンプライアンス結果が期待どおりに更新されていることを確認します
- リソースに対して環境単体テストを直接実行し、プロパティが変更されたことを検証します

更新されたポリシー評価結果と環境の両方をテストすると、修復タスクによって予期された内容が変更されたこと、およびポリシー定義によってコンプライアンスが想定どおりに変更されたことの確認が直接行われます。

### <a name="update-to-enforced-assignments"></a>強制割り当てを更新する

すべての検証ゲートが完了したら、**enforcementMode** を _enabled_ にするように割り当てを更新します。 この変更は、まずは運用環境から遠く離れた同じ環境内で行うことをお勧めします。 その環境が想定どおりに動作することが確認されたら、その次の環境を含むように変更のスコープを設定してゆき、ポリシーが運用リソースにデプロイされるまでこの操作を行う必要があります。

## <a name="process-integrated-evaluations"></a>プロセス統合評価

コードとしての Azure Policy の一般的なワークフローでは、ポリシーとイニシアチブを開発して大規模な環境にデプロイします。 しかし、アプリケーションのデプロイや、インフラストラクチャを作成するための ARM テンプレートの実行など、Azure でリソースをデプロイまたは作成するワークフローのデプロイ プロセスには、ポリシーの評価が含まれている必要があります。

このような場合、テスト サブスクリプションまたはリソース グループに対するアプリケーションまたはインフラストラクチャのデプロイが完了した後で、既存のすべてのポリシーとイニシアチブのスコープ検査の検証のために、ポリシーの評価を行う必要があります。 このような環境では **enforcementMode** が _disabled_ として構成されている場合もありますが、アプリケーションまたはインフラストラクチャのデプロイがポリシー定義に違反しているかどうかを早期に把握しておくと便利です。 したがって、このポリシーの評価はこれらのワークフローの 1 つの手順であり、非準拠のリソースを作成するデプロイメントを不合格とする必要があります。

## <a name="review"></a>確認

この記事では、コードとしての Azure Policy の一般的なワークフローについて、またポリシーの評価が他のデプロイ ワークフローの一部であるべき場合について説明しました。 このワークフローは、スクリプト化した手順と、トリガーに基づく自動化をサポートするすべての環境で使用できます。 GitHub でこのワークフローを使用するためのチュートリアルについては、[チュートリアル:GitHub を使用してコードとしての Azure Policy を実装する](../tutorials/policy-as-code-github.md)方法に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [ポリシー定義の構造](./definition-structure.md)についてさらに学習します。
- [ポリシー割り当ての構造](./assignment-structure.md)についてさらに学習します。
- [プログラムによってポリシーを作成する](../how-to/programmatically-create.md)方法を理解します。
- [コンプライアンス データを取得する](../how-to/get-compliance-data.md)方法を学習します。
- [準拠していないリソースを修復する](../how-to/remediate-resources.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。
