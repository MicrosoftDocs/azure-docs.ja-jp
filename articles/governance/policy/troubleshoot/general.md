---
title: 一般的なエラーのトラブルシューティング
description: ポリシー定義の作成、さまざまな SDK、および Kubernetes のアドオンに関する問題をトラブルシューティングする方法について説明します。
ms.date: 01/26/2021
ms.topic: troubleshooting
ms.openlocfilehash: 6e0e4067f07266bae9c87fd4443d27314cc28c0b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100592608"
---
# <a name="troubleshoot-errors-with-using-azure-policy"></a>Azure Policy の使用に関するエラーをトラブルシューティングする

ポリシー定義を作成したり、SDK を操作したり、[Kubernetes 用の Azure Policy](../concepts/policy-for-kubernetes.md) アドオンを設定したりするときに、エラーが発生することがあります。 この記事では、発生する可能性があるさまざまな一般的エラーについて説明し、その解決方法を示します。

## <a name="find-error-details"></a>エラーの詳細を見つける

エラーの詳細の場所は、操作している Azure Policy の側面によって異なります。

- カスタム ポリシーを操作している場合は、Azure portal に移動し、リンティングによるスキーマに関するフィードバックを取得するか、結果の[コンプライアンス データ](../how-to/get-compliance-data.md)を確認して、リソースがどのように評価されたかを確認してください。
- さまざまな SDK を操作している場合は、SDK によって、関数が失敗した理由に関する詳細が提供されます。
- Kubernetes のアドオンを操作している場合は、クラスター内の[ログ](../concepts/policy-for-kubernetes.md#logging)から開始します。

## <a name="general-errors"></a>一般エラー

### <a name="scenario-alias-not-found"></a>シナリオ:エイリアスが見つからない

#### <a name="issue"></a>問題

正しくないまたは存在しないエイリアスがポリシー定義で使用されています。 Azure Policy が[エイリアス](../concepts/definition-structure.md#aliases)を使用して、Azure Resource Manager のプロパティにマップしています。

#### <a name="cause"></a>原因

正しくないまたは存在しないエイリアスがポリシー定義で使用されています。

#### <a name="resolution"></a>解像度

まず、Resource Manager プロパティにエイリアスがあることを確認します。 使用可能なエイリアスを検索するには、[Visual Studio Code 用の Azure Policy 拡張機能](../how-to/extension-for-vscode.md)、または SDK にアクセスします。
Resource Manager プロパティのエイリアスが存在しない場合は、サポート チケットを作成します。

### <a name="scenario-evaluation-details-arent-up-to-date"></a>シナリオ:評価の詳細が最新ではない

#### <a name="issue"></a>問題

リソースが "_Not Started (未開始)_ " 状態であるか、またはコンプライアンスの詳細が最新ではありません。

#### <a name="cause"></a>原因

新しいポリシーまたはイニシアチブの割り当ては、適用されるまでに約 30 分かかります。 既存の割り当てのスコープ内の新規または更新されたリソースは、約 15 分で使用できるようになります。 標準のコンプライアンス スキャンは、24 時間ごとに行われます。 詳細については、「[評価のトリガー](../how-to/get-compliance-data.md#evaluation-triggers)」を参照してください。

#### <a name="resolution"></a>解像度

まず、評価が完了して Azure portal または SDK でコンプライアンスの結果を利用できるようになるまで、適切な時間待機します。 Azure PowerShell または REST API を使用して新しい評価スキャンを開始するには、「[オンデマンドの評価スキャン](../how-to/get-compliance-data.md#on-demand-evaluation-scan)」を参照してください。

### <a name="scenario-compliance-isnt-as-expected"></a>シナリオ:コンプライアンスが想定どおりでない

#### <a name="issue"></a>問題

リソースが、そのリソースに対して想定されている _Compliant_ または _Not-Compliant_ のいずれの評価状態でもありません。

#### <a name="cause"></a>原因

リソースがポリシー割り当ての正しいスコープに含まれていないか、ポリシー定義が意図したとおりに動作していません。

#### <a name="resolution"></a>解決方法

ポリシー定義をトラブルシューティングするには、次のことを実行します。

1. まず、評価が完了して Azure portal または SDK でコンプライアンスの結果を利用できるようになるまで、適切な時間待機します。 

1. Azure PowerShell または REST API を使用して新しい評価スキャンを開始するには、「[オンデマンドの評価スキャン](../how-to/get-compliance-data.md#on-demand-evaluation-scan)」を参照してください。
1. 割り当てパラメーターと割り当てスコープが正しく設定されていることを確認します。
1. [ポリシー定義モード](../concepts/definition-structure.md#mode)を確認します。
   - モードは、すべてのリソースの種類に対して `all` である必要があります。
   - ポリシー定義によってタグや場所が確認される場合は、モードは `indexed` である必要があります。
1. リソースのスコープが[除外対象](../concepts/assignment-structure.md#excluded-scopes)または[適用除外対象](../concepts/exemption-structure.md)でないことを確認します。
1. ポリシー割り当ての準拠に `0/0` リソースが表示されている場合は、割り当てスコープ内で適用できると判断されたリソースはありません。 ポリシー定義と割り当てスコープの両方を確認してください。
1. 準拠していると予期されていた非準拠のリソースについては、[非準拠である理由の特定](../how-to/determine-non-compliance.md)に関するページを参照してください。 定義と、評価されたプロパティ値を比較することで、リソースが非準拠であった理由がわかります。
   - **対象の値** が間違っている場合は、ポリシー定義を修正します。
   - **現在の値** が間違っている場合は、`resources.azure.com` を介してリソース ペイロードを検証します。
1. その他の一般的な問題と解決策については、[トラブルシューティング: 適用が想定どおりでない](#scenario-enforcement-not-as-expected)に関する記事を参照してください。

複製してカスタマイズした組み込みのポリシー定義またはカスタム定義で引き続き問題が発生する場合は、問題が適切に転送されるように、"**ポリシーの作成**" についてサポート チケットを作成してください。

### <a name="scenario-enforcement-not-as-expected"></a>シナリオ:適用が想定どおりでない

#### <a name="issue"></a>問題

Azure Policy によって処理されると予想されているリソースが処理されておらず、[Azure アクティビティ ログ](../../../azure-monitor/essentials/platform-logs-overview.md)にエントリがありません。

#### <a name="cause"></a>原因

ポリシー割り当ては、_Disabled_ の [**enforcementMode**](../concepts/assignment-structure.md#enforcement-mode) 設定に対して構成されています。
**enforcementMode** が無効になっていると、ポリシー効果は適用されず、アクティビティ ログにエントリはありません。

#### <a name="resolution"></a>解決方法

ポリシー割り当ての適用をトラブルシューティングするには、次のことを実行します。

1. まず、評価が完了して Azure portal または SDK でコンプライアンスの結果を利用できるようになるまで、適切な時間待機します。 

1. Azure PowerShell または REST API を使用して新しい評価スキャンを開始するには、「[オンデマンドの評価スキャン](../how-to/get-compliance-data.md#on-demand-evaluation-scan)」を参照してください。
1. 割り当てパラメーターと割り当てスコープが正しく設定されていること、および **enforcementMode** が "_有効_" になっていることを確認します。
1. [ポリシー定義モード](../concepts/definition-structure.md#mode)を確認します。
   - モードは、すべてのリソースの種類に対して `all` である必要があります。
   - ポリシー定義によってタグや場所が確認される場合は、モードは `indexed` である必要があります。
1. リソースのスコープが[除外対象](../concepts/assignment-structure.md#excluded-scopes)または[適用除外対象](../concepts/exemption-structure.md)でないことを確認します。
1. リソースのペイロードがポリシー ロジックと一致することを確認します。 これは、[HTTP Archive (HAR) トレースをキャプチャ](../../../azure-portal/capture-browser-trace.md)するか、Azure Resource Manager テンプレート (ARM テンプレート) のプロパティを確認することによって実行できます。
1. その他の一般的な問題と解決策については、[トラブルシューティング: コンプライアンスが想定どおりでない](#scenario-compliance-isnt-as-expected)に関する記事を参照してください。

複製してカスタマイズした組み込みのポリシー定義またはカスタム定義で引き続き問題が発生する場合は、問題が適切に転送されるように、"**ポリシーの作成**" についてサポート チケットを作成してください。

### <a name="scenario-denied-by-azure-policy"></a>シナリオ:Azure Policy による拒否

#### <a name="issue"></a>問題

リソースの作成または更新が拒否されました。

#### <a name="cause"></a>原因

新しいまたは更新されたリソースのスコープへのポリシー割り当てが、[Deny](../concepts/effects.md#deny) 効果が適用されているポリシー定義の条件を満たしています。 これらの定義を満たしているリソースは、作成または更新できません。

#### <a name="resolution"></a>解像度

拒否されたポリシー割り当てのエラー メッセージには、ポリシー定義とポリシー割り当て ID が含まれています。 メッセージ内のエラー情報が欠落している場合は、[アクティビティ ログ](../../../azure-monitor/essentials/activity-log.md#view-the-activity-log)でも確認できます。 この情報を使用して、リソースの制限を理解し、許可された値に一致するように要求のリソース プロパティを調整します。

## <a name="template-errors"></a>テンプレート エラー

### <a name="scenario-policy-supported-functions-processed-by-template"></a>シナリオ:ポリシーでサポートされる関数がテンプレートによって処理される

#### <a name="issue"></a>問題

Azure Policy では、ARM テンプレートの関数と、ポリシー定義でのみ使用できる関数が多数サポートされています。 Resource Manager は、ポリシー定義の一部としてではなく、デプロイの一部としてこれらの機能を処理します。

#### <a name="cause"></a>原因

`parameter()` や `resourceGroup()` などのサポートされている関数を使用すると、ポリシー定義や Azure Policy エンジンの関数に処理させる代わりに、デプロイ時に関数の処理結果が得られます。

#### <a name="resolution"></a>解決方法

関数をポリシー定義の一部として渡すには、プロパティが `[[resourceGroup().tags.myTag]` のようになるように `[` で文字列全体をエスケープします。 このエスケープ文字により、Resource Manager でテンプレートが処理されるときに値は文字列として扱われます。 その後、関数は Azure Policy によってポリシー定義に配置され、それにより、予期したとおりに動的になります。 詳細については、「[Azure Resource Manager テンプレートの構文と式](../../../azure-resource-manager/templates/template-expressions.md)」を参照してください。

## <a name="add-on-for-kubernetes-installation-errors"></a>Kubernetes インストール エラー用アドオン

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-of-a-password-error"></a>シナリオ:パスワード エラーが原因で、Helm Chart を使用したインストールが失敗する

#### <a name="issue"></a>問題

`helm install azure-policy-addon` コマンドが失敗し、次のいずれかのエラーが返されます。

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>原因

生成されたパスワードにコンマ (`,`) が含まれており、ここで Helm Chart が分割されています。

#### <a name="resolution"></a>解決方法

`helm install azure-policy-addon` を実行するときに、パスワード値のコンマ (`,`) を円記号 (`\`) でエスケープします。

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-the-name-already-exists"></a>シナリオ:名前が既に存在するため、Helm Chart を使用したインストールが失敗する

#### <a name="issue"></a>問題

`helm install azure-policy-addon` コマンドが失敗し、次のエラーが返されます。

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>原因

`azure-policy-addon` という名前の Helm Chart は、既にインストールされているか、部分的にインストールされています。

#### <a name="resolution"></a>解像度

[Kubernetes 用の Azure Policy アドオンを削除](../concepts/policy-for-kubernetes.md#remove-the-add-on)する指示に従ってから、`helm install azure-policy-addon` コマンドを再実行します。

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>シナリオ:Azure 仮想マシンのユーザー割り当て ID がシステム割り当てマネージド ID に置き換えられる

#### <a name="issue"></a>問題

マシン内の監査設定にゲスト構成ポリシーのイニシアチブを割り当てると、マシンに割り当てられていたユーザー割り当てマネージド ID は割り当てられなくなります。 システム割り当てマネージド ID のみが割り当てられます。

#### <a name="cause"></a>原因

以前にゲスト構成の DeployIfNotExists 定義で使用されていたポリシー定義により、システム割り当て ID がマシンに割り当てられましたが、またユーザー割り当て ID の割り当ても削除されました。

#### <a name="resolution"></a>解決方法

以前にこの問題の原因となった定義は、" _\[非推奨\]_ " と表示され、ユーザー割り当てマネージド ID を削除せずに前提条件を管理するポリシー定義に置き換えられます。 手動操作が必要です。 " _\[非推奨\]_ " とマークされている既存のポリシー割り当てをすべて削除し、元のものと同じ名前の更新された前提条件ポリシー イニシアチブとポリシー定義に置き換えてください。

詳細な説明については、[ゲスト構成の監査ポリシーに関してリリースされた重要な変更](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)に関するブログ記事を参照してください。

## <a name="add-on-for-kubernetes-general-errors"></a>Kubernetes 一般的エラー用アドオン

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-egress-restrictions"></a>シナリオ:エグレス制限のため、アドオンは Azure Policy サービス エンドポイントに接続できません

#### <a name="issue"></a>問題

アドオンは Azure Policy サービス エンドポイントに接続できず、次のいずれかのエラーが返されます。

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>原因

この問題は、クラスター エグレスがロック ダウンされている場合に発生します。

#### <a name="resolution"></a>解決方法

次の記事に記載されているドメインとポートが開いていることを確認します。

- [AKS クラスターに必要な送信ネットワーク規則と完全修飾ドメイン名 (FQDN)](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [Azure Arc 対応 Kubernetes 用の Azure Policy アドオン (プレビュー) をインストールする](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-the-aad-pod-identity-configuration"></a>シナリオ:aad-pod-identity の構成のため、アドオンは Azure Policy サービス エンドポイントに接続できない

#### <a name="issue"></a>問題

アドオンは Azure Policy サービス エンドポイントに接続できず、次のいずれかのエラーが返されます。

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>原因

このエラーは _add-pod-identity_ がクラスターにインストールされていて、_kube-system_ ポッドが _aad-pod-identity_ で除外されていない場合に発生します。

Azure Instance Metadata エンドポイントへの呼び出しをインターセプトするように、_aad-pod-identity_ コンポーネントの Node Managed Identity (NMI) ポッドによりノードの iptables が変更されます。 この設定の場合、Metadata エンドポイントに要求が行われると、ポッドで _aad-pod-identity_ が使用されていない場合でも、NMI によって要求がインターセプトされます。
CustomResourceDefinition (CRD) に定義されているラベルに一致するポッドから発信された Metadata エンドポイントに対する要求は、NMI で処理せずにプロキシ処理する必要があることを _aad-pod-identity_ に通知するように、_AzurePodIdentityException_ CRD を構成できます。

#### <a name="resolution"></a>解決方法

_AzurePodIdentityException_ CRD を構成することによって、_kube-system_ 名前空間内の `kubernetes.azure.com/managedby: aks` ラベルを持つシステム ポッドを _aad-pod-identity_ で除外します。

詳細については、[特定のポッドまたはアプリケーションでの Azure Active Directory (Azure AD) ポッド ID の無効化](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)に関する記事を参照してください。

例外を構成するには、この例に従ってください。

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: mic-exception
  namespace: default
spec:
  podLabels:
    app: mic
    component: mic
---
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: aks-addon-exception
  namespace: kube-system
spec:
  podLabels:
    kubernetes.azure.com/managedby: aks
```

### <a name="scenario-the-resource-provider-isnt-registered"></a>シナリオ:リソース プロバイダーが登録されていない

#### <a name="issue"></a>問題

アドオンは Azure Policy サービス エンドポイントに接続できますが、アドオン ログに次のいずれかのエラーが表示されます。

- `The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
  https://aka.ms/policy-register-subscription for how to register subscriptions.`

- `policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
  StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
  Code="InternalServerError" Message="Encountered an internal server error.`

#### <a name="cause"></a>原因

"Microsoft.PolicyInsights" リソース プロバイダーが登録されていません。 アドオンによってポリシー定義が取得され、コンプライアンス データが返されるためには、登録されている必要があります。

#### <a name="resolution"></a>解決方法

"Microsoft.PolicyInsights"リソース プロバイダーをクラスター サブスクリプションに登録します。 手順については、[リソース プロバイダーの登録](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)に関するページを参照してください。

### <a name="scenario-the-subscription-is-disabled"></a>シナリオ:サブスクリプションが無効

#### <a name="issue"></a>問題

アドオンは Azure Policy サービス エンドポイントに接続できますが、次のエラーが表示されます。

`The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.`

#### <a name="cause"></a>原因

このエラーは、サブスクリプションに問題があると判断され、このサブスクリプションをブロックするために機能フラグ `Microsoft.PolicyInsights/DataPlaneBlocked` が追加されたことを示しています。

#### <a name="resolution"></a>解決方法

この問題を調査して解決するには、[機能チームにお問い合わせください](mailto:azuredg@microsoft.com)。

## <a name="next-steps"></a>次のステップ

お客様の問題がこの記事に掲載されていない場合、または解決できない場合は、次のいずれかのチャネルにアクセスしてサポートを受けてください。

- [Microsoft Q&A](/answers/topics/azure-policy.html) を通じて、専門家からの回答を得る。
- [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる。 この Twitter 上の Microsoft Azure 公式リソースは、Azure コミュニティを適切な回答、サポート、エキスパートに結び付けることによって、カスタマー エクスペリエンスを向上させます。
- それでもヘルプが必要な場合は、[Azure サポート サイト](https://azure.microsoft.com/support/options/)にアクセスし、 **[サポート リクエストの送信]** を選択してください。
