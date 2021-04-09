---
title: Azure Monitor アラートのアクション ルール
description: Azure Monitor におけるアクション ルールとはどのようなものか、およびそれを構成して管理する方法を説明します。
ms.topic: conceptual
ms.date: 03/15/2021
ms.openlocfilehash: f70d798270ad82193f7ae5935d34f8f418d35e05
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471678"
---
# <a name="action-rules-preview"></a>アクション ルール (プレビュー)

アクション ルールを使用すると、Azure Resource Manager の任意のスコープ (Azure サブスクリプション、リソース グループ、ターゲット リソース) で、アクションを定義または抑制できます。 さまざまなフィルターを使用して、操作するアラート インスタンスの特定のサブセットを絞り込むことができます。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rBZ2]

## <a name="why-and-when-should-you-use-action-rules"></a>アクション ルールを使用する理由とタイミング

### <a name="suppression-of-alerts"></a>アラートの抑制

アラートによって生成される通知を抑制すると便利なシナリオは多数あります。 これらのシナリオは、計画メンテナンス期間中の抑制から、業務時間外の抑制まで多岐にわたります。 たとえば、**ContosoVM** が計画メンテナンス中のため、**ContosoVM** の担当チームは、次の週末はアラート通知を抑制したいと考えています。

チームは **ContosoVM** 上で構成されている各警告ルールを手動で無効に (そしてメンテナンス後に再び有効に) できますが、これは単純なプロセスではありません。 アクション ルールを使用すると、抑制の期間を柔軟に構成できるため、アラートの抑制を大規模に定義できます。 前の例では、週末のアラート通知をすべて抑制する 1 つのアクション ルールを、チームで **ContosoVM** に対して定義できるようになります。

### <a name="actions-at-scale"></a>大規模なアクション

警告ルールを使用すると、アラート生成時にトリガーされるアクション グループを定義できますが、顧客は多くの場合、業務スコープ全体にわたる共通のアクション グループを持ちます。 たとえば、リソース グループ **ContosoRG** の担当チームはおそらく、**ContosoRG** 内で定義されるすべての警告ルールに対して同じアクション グループを定義します。

アクション ルールを使うと、このプロセスを簡略化できます。 アクションをまとめて定義することで、構成されたスコープで生成されるすべてのアラートに対してアクション グループをトリガーできます。 前の例では、チームは **ContosoRG** に、その中で生成されたすべてのアラートに対して同じアクション グループをトリガーする 1 つのアクション ルールを定義できるようになります。

> [!NOTE]
> アクション ルールは、現在、Azure Service Health アラートには適用されません。

## <a name="configuring-an-action-rule"></a>アクション ルールの構成

### <a name="portal"></a>[ポータル](#tab/portal)

Azure Monitor の **アラート** ランディング ページから **[アクションの管理]** を選択して、機能にアクセスできます。 次に、 **[アクション ルール (プレビュー)]** を選択します。 アラートのランディング ページのダッシュボードから **[アクション ルール (プレビュー)]** を選択して、ルールにアクセスできます。

![Azure Monitor ランディング ページからのアクション ルール](media/alerts-action-rules/action-rules-landing-page.png)

**[+ New Action Rule]\(+ 新しいアクション ルール\)** を選択します。

![スクリーンショットには、[アクションの管理] ページが表示され、[New Action Rule]\(新しいアクション ルール\) ボタンが強調表示されています。](media/alerts-action-rules/action-rules-new-rule.png)

または、警告ルールを構成するときにアクション ルールを作成できます。

![スクリーンショットには、[ルールの作成] ページが表示され、[アクション ルールを作成] ボタンが強調表示されています。](media/alerts-action-rules/action-rules-alert-rule.png)

アクション ルールを作成するためのフロー ページは次のとおりです。 次の要素を構成します。

![新しいアクション ルールの作成フロー](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Scope

最初にスコープ (Azure サブスクリプション、リソース グループ、またはターゲット リソース) を選択します。 また、1 つのサブスクリプション内のスコープの組み合わせを複数選択することもできます。

![アクション ルールのスコープ](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>フィルター条件

オプションでフィルターを定義して、ルールがアラートの特定のサブセット、または各アラートの特定のイベントに適用されるようにすることができます (たとえば、「発生」のみや「解決済み」のみなど)。

使用可能なフィルターは次のとおりです。

* **Severity**  
このルールは、選択した重大度のアラートにのみ適用されます。  
たとえば、**severity = Sev1** は、ルールが Sev1 の重大度のアラートにのみ適用されることを意味します。
* **サービスの監視**  
このルールは、選択した監視サービスから通知されたアラートにのみ適用されます。  
たとえば、**monitor service = "Azure Backup"** は、ルールがバックアップ アラート (Azure Backup から通知される) のみに適用されることを意味します。
* **リソースの種類**  
このルールは、選択したリソースの種類に関するアラートにのみ適用されます。  
たとえば、**resource type = "Virtual Machines"** は、ルールが仮想マシンのアラートにのみ適用されることを意味します。
* **アラート ルール ID**  
このルールは、特定のアラート ルールから通知されたアラートにのみ適用されます。 値は、アラート ルールの Resource Manager ID である必要があります。  
たとえば、**alert rule ID = "/subscriptions/SubId1/resourceGroups/RG1/providers/microsoft.insights/metricalerts/API-Latency"** は、このルールが "API-Latency" メトリック アラート ルールから通知されたアラートにのみ適用されることを意味します。  
_注意 - 適切なアラート ルール ID を取得するには、CLI からアラート ルールを一覧表示するか、ポータルで特定のアラート ルールを開き、[プロパティ] をクリックして、[リソース ID] の値をコピーします。_
* **監視条件**  
このルールは、指定した監視条件 (**発生** または **解決済み**) のアラート イベントにのみ適用されます。
* **説明**  
このルールは、[アラートの説明] フィールドに特定の文字列が含まれているアラートにのみ適用されます。 そのフィールドには、アラート ルールの説明が含まれています。  
たとえば、**説明に 'prod' が含まれている** 場合、ルールが、その説明に "prod" という文字列を含むアラートのみに一致することを意味します。
* **[アラートのコンテキスト (ペイロード)]**  
このルールは、[アラートのコンテキスト] フィールドに 1 つ以上の特定の値が含まれているアラートにのみ適用されます。  
たとえば、 **[アラートのコンテキスト (ペイロード)] に 'Computer-01' が含まれている** 場合、ルールが、ペイロードに文字列 "Computer-01" が含まれているアラートにのみ適用されることを意味します。

ルールに複数のフィルターを設定すると、そのすべてが適用されます。 たとえば、**resource type' = Virtual Machines** と **severity' = Sev0** を設定した場合、ルールは、仮想マシン上の Sev0 アラートに対してのみ適用されます。

![アクション ルール フィルター](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>抑制またはアクション グループの構成

次に、アラートの抑制またはアクション グループのサポートに対するアクション ルールを構成します。 両方を選択することはできません。 構成は、前に定義したスコープとフィルターに一致するすべてのアラートのインスタンスに対して機能します。

#### <a name="suppression"></a>抑制

**抑制** を選択した場合、アクションと通知の抑制の期間を構成します。 次のいずれかのオプションを選択します。
* **[今すぐ (常に)]** : すべての通知を無期限に抑制します。
* **[At a scheduled time]\(スケジュールされた時間\)** : 指定された期間だけ、通知を抑制します。
* **[With a recurrence]\(繰り返し\)** : 日単位、週単位、または月単位の定期的なスケジュールで通知を抑制します。

![アクション ルールの抑制](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>アクション グループ

トグルで **[Action group]\(アクション グループ\)** を選択した場合、既存のアクション グループを追加するか、新しいアクション グループを作成します。

> [!NOTE]
> アクション ルールと関連付けることができるアクション グループは 1 つだけです。

![アクション グループを選択して新しいアクション ルールを追加または作成](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>アクション ルールの詳細

最後に、アクション ルールに対して次の詳細を構成します。
* 名前
* 保存先のリソース グループ
* 説明

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI でアクション ルールを作成するには、[az monitor action-rule create](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create) コマンドを使用します。  `az monitor action-rule` リファレンスは、[Azure Monitor の数ある Azure CLI リファレンス](/cli/azure/azure-cli-reference-for-monitor)の 1 つにすぎません。

### <a name="prepare-your-environment"></a>環境を準備する

1. [Azure CLI のインストール](/cli/azure/install-azure-cli)

   必要に応じて、Azure Cloud Shell を使用してこの記事の手順を完了することもできます。  Azure Cloud Shell は、ブラウザーを介して使用する対話型のシェル環境です。  次のいずれかの方法で Cloud Shell を起動します。

   - [https://shell.azure.com](https://shell.azure.com) に移動して Cloud Shell を開きます

   - [Azure portal](https://portal.azure.com) の右上にあるメニュー バーの **[Cloud Shell]** ボタンを選択します

1. サインインします。

   CLI のローカル インストールを使用する場合は、[az login](/cli/azure/reference-index#az-login) コマンドを使用してサインインします。  ターミナルに表示される手順に従って、認証プロセスを完了します。

    ```azurecli
    az login
    ```

1. `alertsmanagement` 拡張機能をインストールする

   `az monitor action-rule` コマンドは、コア Azure CLI の試験段階の拡張機能です。 拡張機能のリファレンスの詳細については、「[Azure CLI で拡張機能を使用する](/cli/azure/azure-cli-extensions-overview?)」を参照してください。

   ```azurecli
   az extension add --name alertsmanagement
   ```

   次の警告が生成されます。

   ```output
   The installed extension `alertsmanagement` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="create-action-rules-with-the-azure-cli"></a>Azure CLI を使用してアクション ルールを作成する

必須と省略可能なパラメーターについては、[az monitor action-rule create](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create) の Azure CLI リファレンス コンテンツを参照してください。

リソース グループ内の通知を抑制するアクション ルールを作成します。

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --scope-type ResourceGroup \
                              --scope /subscriptions/0b1f6471-1bf0-4dda-aec3-cb9272f09590/resourceGroups/MyResourceGroupName \
                              --suppression-recurrence-type Always \
                              --alert-context Contains Computer-01 \
                               --monitor-service Equals "Log Analytics"
```

毎週末、サブスクリプション内のすべての VM で、すべての Sev4 アラートの通知を抑制するアラート ルールを作成します。

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --severity Equals Sev4 \
                              --target-resource-type Equals Microsoft.Compute/VirtualMachines \
                              --suppression-recurrence-type Weekly \
                              --suppression-recurrence 0 6 \
                              --suppression-start-date 12/09/2018 \
                              --suppression-end-date 12/18/2018 \
                              --suppression-start-time 06:00:00 \
                              --suppression-end-time 14:00:00

```

* * *

## <a name="example-scenarios"></a>シナリオ例

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>シナリオ 1:重大度に基づくアラートの抑制

Contoso では、毎週末、サブスクリプション **ContosoSub** 内のすべての VM で、すべての Sev4 アラートの通知を抑制します。

**解決方法:** 次のアクション ルールを作成します。
* スコープ = **ContosoSub**
* フィルター
    * 重大度 = **Sev4**
    * リソースの種類 = **仮想マシン**
* 繰り返しの抑制、毎週、**土曜日** と **日曜日** をオン

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>シナリオ 2: アラート コンテキスト (ペイロード) に基づくアラートの抑制

Contoso では、メンテナンスのため、**ContosoSub** 内の **Computer-01** に対して生成されるすべてのログ アラートに対する通知を無期限に抑制します。

**解決方法:** 次のアクション ルールを作成します。
* スコープ = **ContosoSub**
* フィルター
    * サービスの監視 = **Log Analytics**
    * アラート コンテキスト (ペイロード) に **Computer-01** が含まれる
* 抑制を **[今すぐ (常に)]** に設定

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>シナリオ 3: リソース グループで定義されているアクション グループ

Contoso は、[1 つのサブスクリプション レベルで 1 つのメトリック アラート](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)を定義しました。 ただし、リソース グループ **ContosoRG** から生成されるアラート専用にトリガーするアクションを定義したいと考えています。

**解決方法:** 次のアクション ルールを作成します。
* スコープ = **ContosoRG**
* フィルターなし
* アクション グループを **ContosoActionGroup** に設定

> [!NOTE]
> "*アクション ルールと警告ルールで定義されるアクション グループは独立して動作し、重複は除去されません。* " 上記のシナリオでは、警告ルールに定義されたアクション グループがある場合、これはアクション ルールに定義されたアクション グループと連動してトリガーされます。

## <a name="managing-your-action-rules"></a>アクション ルールの管理

### <a name="portal"></a>[ポータル](#tab/portal)

リスト ビューから、アクション ルールを表示および管理できます。

![アクション ルールのリスト ビュー](media/alerts-action-rules/action-rules-list-view.png)

ここでは、横にあるチェック ボックスをオンにすることで、アクション ルールをまとめて有効化/無効化/削除できます。 アクション ルールを選択すると、その構成ページが開きます。 このページを使用して、アクション ルールの定義を更新し、それを有効または無効にすることができます。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI から [az monitor action-rule](/cli/azure/ext/alertsmanagement/monitor) コマンドを使用して、アクション ルールを表示および管理することができます。

Azure CLI でアクション ルールを管理する前に、「[アクション ルールの構成](#configuring-an-action-rule)」に記載されている手順を使用して環境を準備します。

```azurecli
# List all action rules for a subscription
az monitor action-rule list

# Get details of an action rule
az monitor action-rule show --resource-group MyResourceGroupName --name MyActionRuleName

# Update an action rule.
az monitor action-rule update --resource-group MyResourceGroupName --name MyActionRuleName --status Disabled

# Delete an action rule.
az monitor action-rule delete --resource-group MyResourceGroupName --name MyActionRuleName
```

* * *

## <a name="best-practices"></a>ベスト プラクティス

[結果の数](./alerts-unified-log.md)オプションで作成したログ アラートでは、検索結果全体を使用して 1 つのアラート インスタンスが生成されます (たとえば、複数のコンピューターが含まれる可能性があります)。 このシナリオでは、アクション ルールで **アラート コンテキスト (ペイロード)** フィルターが使用される場合、一致する限りアラート インスタンスに対して作用します。 前に説明したシナリオ 2 では、生成されたログ アラートの検索結果に、**Computer-01** と **Computer-02** の両方が含まれる場合は、通知全体が抑制されます。 つまり、**Computer-02** に対しても通知はまったく生成されません。

![アクション ルールとログ アラートの図。アラート インスタンスが 1 つ強調表示されています。](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

アクション ルールでログ アラートを最適に利用するには、[メトリック測定](./alerts-unified-log.md)オプションを使ってログ アラートを作成します。 このオプションを使用すると、その定義されたグループ フィールドに基づいて個別のアラート インスタンスが生成されます。 シナリオ 2 では、**Computer-01** と **Computer-02** に対して個別のアラート インスタンスが生成されます。 シナリオで説明されているアクション ルールに従って、**Computer-01** の通知のみが抑制されます。 **Computer-02** の通知は、引き続き通常どおり起動されます。

![アクション ルールとログ アラート (結果の数)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>よく寄せられる質問

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>アクション ルールを構成するとき、通知の重複を回避するため、重複する可能性のあるすべてのアクション ルールを確認したいのですが、 これを行うことはできますか?

アクション ルールを構成するときにスコープを定義した後、同じスコープで重複するアクション ルール (ある場合) の一覧を確認できます。 この重複は、次のいずれかのオプションの可能性があります。

* 完全一致: たとえば、定義しているアクション ルールと重複するアクション ルールが、同じサブスクリプションに対するものです。
* サブセット: たとえば、定義しているアクション ルールはサブスクリプションに対するもので、重複するアクション ルールはそのサブスクリプション内のリソース グループに対するものです。
* スーパーセット: たとえば、定義しているアクション ルールはリソース グループに対するもので、重複するアクション ルールはそのリソース グループを含むサブスクリプションに対するものです。
* 交差: たとえば、定義しているアクション ルールは **VM1** と **VM2** に対するもので、重複するアクション ルールは **VM2** と **VM3** に対するものです。

![[新しいアクション ルール] ページのスクリーンショット。同じスコープ ウィンドウで定義されているアクション ルールに重複するアクション ルールが表示されています。](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>警告ルールを構成するとき、定義している警告ルールに作用する可能性のあるアクション ルールが既に定義されているかどうかを確認することはできますか?

警告ルールのターゲット リソースを定義した後、 **[アクション]** セクションの **[構成されたアクションを表示する]** をクリックすることで、同じスコープで作用するアクション ルールの一覧を確認できます (ある場合)。 この一覧は、スコープの次のシナリオに基づいて設定されます。

* 完全一致: たとえば、定義しているアラート ルールとアクション ルールが、同じサブスクリプションに対するものです。
* サブセット: たとえば、定義しているアラート ルールはサブスクリプションに対するもので、アクション ルールはそのサブスクリプション内のリソース グループに対するものです。
* スーパーセット: たとえば、定義しているアラート ルールはリソース グループに対するもので、アクション ルールはそのリソース グループを含むサブスクリプションに対するものです。
* 交差: たとえば、定義しているアラート ルールは **VM1** と **VM2** に対するもので、アクション ルールは **VM2** と **VM3** に対するものです。

![アクション ルールの重複](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>アクション ルールによって抑制されているアラートを表示できますか?

[アラート一覧ページ](./alerts-managing-alert-instances.md)で、 **[抑制の状態]** という名前の追加列を選択できます。 アラート インスタンスの通知が抑制されている場合、一覧にその状態が表示されます。

![抑制されたアラート インスタンス](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>アクション グループを含むアクション ルールと、同じスコープに対する抑制がアクティブな別のアクション ルールがある場合は、どうなりますか?

同じスコープに対しては抑制が常に優先されます。

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>2 つの個別のアクション ルールで 1 つのリソースが監視されていると、どうなりますか? 受け取る通知は 1 つですか、2 つですか? ここでは、次のシナリオの **VM2** を例に説明します。

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with action group AG1`

VM1 と VM3 のすべてのアラートについては、アクション グループ AG1 が 1 回トリガーされます。 **VM2** のすべてのアラートについては、アクション グループ AG1 が 2 回トリガーされます。これは、アクション ルールではアクションは重複除去されないためです。

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>2 つの個別のアクション ルールで監視されているリソースがあり、一方でアクションを呼び出し、もう一方で抑制すると、どうなりますか? ここでは、次のシナリオの **VM2** を例に説明します。

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with suppression`

VM1 のすべてのアラートについては、アクション グループ AG1 が 1 回トリガーされます。 VM2 と VM3 に対するすべてのアラートのアクションと通知は抑制されます。

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>同じリソースに対して、異なるアクション グループを呼び出すアラート ルールとアクション ルールが定義されていると、どうなりますか? ここでは、次のシナリオの **VM1** を例に説明します。

   `alert rule rule1 on VM1 with action group AG2`

   `action rule AR1 defined for VM1 with action group AG1`

VM1 のすべてのアラートについては、アクション グループ AG1 が 1 回トリガーされます。 警告ルール "rule1" がトリガーされるときは常に、AG2 もさらにトリガーされます。 アクション ルールと警告ルール内で定義されるアクション グループは独立して動作し、重複は除去されません。

## <a name="next-steps"></a>次のステップ

- [Azure でのアラートについてさらに詳しく学習する](./alerts-overview.md)
