---
title: 一般的なエラーのトラブルシューティング
description: ポリシー定義、さまざまな SDK、および Kubernetes のアドオンの作成に関する問題をトラブルシューティングする方法について説明します。
ms.date: 08/17/2020
ms.topic: troubleshooting
ms.openlocfilehash: d4ede1703df922196c89a4c1ca4f37cbc95a6297
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545541"
---
# <a name="troubleshoot-errors-using-azure-policy"></a>Azure Policy を使用してエラーを解決する

ポリシー定義を作成したり、SDK を使用したり、[Azure Policy for Kubernetes](../concepts/policy-for-kubernetes.md) を設定したりすると、エラーが発生することがあります。 この記事では、発生する可能性があるさまざまなエラーと、その解決方法について説明します。

## <a name="finding-error-details"></a>エラー詳細の検索

エラーの詳細の場所は、エラーの原因となったアクションによって異なります。

- カスタム ポリシーを使用する場合は、Azure portal で試して、リンティングによるスキーマに関するフィードバックを取得するか、結果の[コンプライアンス データ](../how-to/get-compliance-data.md)を確認して、リソースがどのように評価されたかを確認してください。
- さまざまな SDK を使用する場合、SDK では、関数が失敗した理由に関する詳細が提供されます。
- Kubernetes のアドオンを使用する場合は、クラスター内の[ログ](../concepts/policy-for-kubernetes.md#logging)から開始します。

## <a name="general-errors"></a>一般エラー

### <a name="scenario-alias-not-found"></a>シナリオ:エイリアスが見つからない

#### <a name="issue"></a>問題

Azure Policy が[エイリアス](../concepts/definition-structure.md#aliases)を使用して、Azure Resource Manager のプロパティにマップしています。

#### <a name="cause"></a>原因

無効または存在しないエイリアスがポリシー定義で使用されています。

#### <a name="resolution"></a>解決策

まず、Resource Manager プロパティにエイリアスがあることを確認します。 [Visual Studio Code 用の Azure Policy 拡張機能](../how-to/extension-for-vscode.md)、[Azure Resource Graph](../../resource-graph/samples/starter.md#distinct-alias-values)、または SDK を使用して、使用可能なエイリアスを検索します。 Resource Manager プロパティのエイリアスが存在しない場合は、サポート チケットを作成します。

### <a name="scenario-evaluation-details-not-up-to-date"></a>シナリオ:評価の詳細が最新ではない

#### <a name="issue"></a>問題

リソースが "未開始" 状態であるか、またはコンプライアンスの詳細が最新ではありません。

#### <a name="cause"></a>原因

新しいポリシーまたはイニシアチブの割り当ては、適用されるまでに約 30 分かかります。 既存の割り当てのスコープ内の新規または更新されたリソースは、15 分後に使用できるようになります。 標準のコンプライアンス スキャンは、24 時間ごとに行われます。 詳細については、「[評価のトリガー](../how-to/get-compliance-data.md#evaluation-triggers)」を参照してください。

#### <a name="resolution"></a>解決策

まず、評価が完了して Azure portal または SDK でコンプライアンスの結果を利用できるようになるまで、適切な時間待機します。 Azure PowerShell または REST API を使用して新しい評価スキャンを開始するには「[オンデマンドの評価スキャン](../how-to/get-compliance-data.md#on-demand-evaluation-scan)」を参照してください。

### <a name="scenario-evaluation-not-as-expected"></a>シナリオ:評価が想定どおりではない

#### <a name="issue"></a>問題

リソースの評価状態が、そのリソースに対して想定されている _Compliant_ または_Not-Compliant_ のいずれかではありません。

#### <a name="cause"></a>原因

リソースがポリシー割り当ての正しいスコープに含まれていないか、ポリシー定義が意図したとおりに動作していません。

#### <a name="resolution"></a>解決策

- 対応していることが予期されていた非対応のリソースについては、まず[非準拠である理由を特定](../how-to/determine-non-compliance.md)します。 定義と評価されたプロパティ値を比較することで、リソースが非準拠である理由がわかります。
- 準拠していないことが予期されていた準拠しているリソースについては、ポリシー定義条件を条件で読み取り、リソースのプロパティに対して評価します。 論理演算子によって適切な条件がグループ化されていること、および条件が反転していないことを検証します。

ポリシー割り当ての準拠に `0/0` リソースが表示されている場合は、割り当てスコープ内で適用できると判断されたリソースはありません。 ポリシー定義と割り当てスコープの両方を確認してください。

### <a name="scenario-enforcement-not-as-expected"></a>シナリオ:適用が想定どおりでない

#### <a name="issue"></a>問題

Azure Policy によって処理される予定のリソースが処理されず、[Azure アクティビティ ログ](../../../azure-monitor/platform/platform-logs-overview.md)にエントリがありません。

#### <a name="cause"></a>原因

ポリシー割り当ては _[Disabled]_ の [enforcementMode](../concepts/assignment-structure.md#enforcement-mode) に対して構成されています。 強制モードが無効になっていると、ポリシー効果は適用されず、アクティビティ ログにエントリはありません。

#### <a name="resolution"></a>解決策

**enforcementMode** を _Enabled_ に更新します。 この変更により、Azure Policy はこのポリシー割り当てのリソースを操作し、エントリをアクティビティ ログに送信できます。 **enforcementMode** が既に有効になっている場合は、「[評価が想定どおりではない](#scenario-evaluation-not-as-expected)」を参照してください。

### <a name="scenario-denied-by-azure-policy"></a>シナリオ:Azure Policy による拒否

#### <a name="issue"></a>問題

リソースの作成または更新が拒否されました。

#### <a name="cause"></a>原因

新しいリソースまたは更新されたリソースが存在するスコープへのポリシー割り当てが、[Deny](../concepts/effects.md#deny) 効果が適用されているポリシー定義の条件を満たしています。 これらの定義を満たしているリソースは、作成または更新できません。

#### <a name="resolution"></a>解決策

拒否されたポリシー割り当てのエラー メッセージには、ポリシー定義とポリシー割り当て ID が含まれています。 メッセージ内のエラー情報が欠落している場合は、[アクティビティ ログ](../../../azure-monitor/platform/activity-log.md#view-the-activity-log)でも確認できます。 この情報を使用して、リソースの制限を理解し、許可された値に一致するように要求のリソース プロパティを調整します。

## <a name="template-errors"></a>テンプレート エラー

### <a name="scenario-policy-supported-functions-processed-by-template"></a>シナリオ:ポリシーでサポートされる関数がテンプレートによって処理される

#### <a name="issue"></a>問題

Azure Policy は、Azure Resource Manager テンプレート (ARM テンプレート) の関数とポリシー定義でのみ使用できる関数を多数サポートしています。 Resource Manager は、ポリシー定義の一部としてではなく、デプロイの一部としてこれらの機能を処理します。

#### <a name="cause"></a>原因

`parameter()` や `resourceGroup()` などのサポートされている関数を使用すると、ポリシー定義および Azure Policy エンジンが処理する関数を残す代わりに、デプロイメント時に関数の処理結果が得られます。

#### <a name="resolution"></a>解決策

関数をポリシー定義の一部として渡すには、プロパティが `[[resourceGroup().tags.myTag]` のようになるように `[` で文字列全体をエスケープします。 エスケープ文字により、Resource Manager によってテンプレートが処理されるときに値が文字列として扱われます。 その後 Azure Policy は関数をポリシー定義に配置し、予期したとおりに動的になるようにします。 詳細については、「[Azure Resource Manager テンプレートの構文と式](../../../azure-resource-manager/templates/template-expressions.md)」を参照してください。

## <a name="add-on-installation-errors"></a>アドオンのインストール エラー

### <a name="scenario-install-using-helm-chart-fails-on-password"></a>シナリオ:パスワードによる Helm Chart を使用したインストールの失敗

#### <a name="issue"></a>問題

`helm install azure-policy-addon` コマンドが、次のいずれかのメッセージで失敗します。

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>原因

生成されたパスワードに、Helm Chart では分割されるコンマ (`,`) が含まれています。

#### <a name="resolution"></a>解決策

`helm install azure-policy-addon` を実行する場合は、バックスラッシュ (`\`) を使用してパスワード値のコンマ (`,`) をエスケープします。

### <a name="scenario-install-using-helm-chart-fails-as-name-already-exists"></a>シナリオ:名前が既に存在するため、Helm Chart を使用したインストールが失敗する

#### <a name="issue"></a>問題

`helm install azure-policy-addon` コマンドが失敗し、次のメッセージが表示されます。

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>原因

`azure-policy-addon` という名前の Helm Chart は、既にインストールされているか、部分的にインストールされています。

#### <a name="resolution"></a>解決策

[Kubernetes アドオンの Azure Policy を削除](../concepts/policy-for-kubernetes.md#remove-the-add-on)する指示に従ってから、`helm install azure-policy-addon` コマンドを再実行します。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

- [Microsoft Q&A](/answers/topics/azure-policy.html) を通じて、専門家からの回答を得る。
- [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
- さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。
