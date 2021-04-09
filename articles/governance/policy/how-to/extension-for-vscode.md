---
title: Visual Studio Code 用の Azure Policy 拡張機能
description: Visual Studio Code 用の Azure Policy 拡張機能を使用して Azure Resource Manager エイリアスを検索する方法について説明します。
ms.date: 01/11/2021
ms.topic: how-to
ms.openlocfilehash: 4c4ba0eeb0506179ff92ead0ee86f048600d157e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98107941"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>Visual Studio Code 用の Azure Policy 拡張機能を使用する

> Azure Policy 拡張機能のバージョン **0.1.1** 以降に適用されます

Visual Studio Code 用の Azure Policy 拡張機能を使用して[エイリアス](../concepts/definition-structure.md#aliases)を検索し、リソースとポリシーを確認し、オブジェクトをエクスポートし、ポリシー定義を評価する方法について説明します。 最初に、Visual Studio Code で Azure Policy 拡張機能をインストールする方法を説明します。 次に、エイリアスを検索する方法について説明します。

Visual Studio Code 用の Azure Policy 拡張機能は Windows にインストールできます。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次の項目が必要です。

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
- [Visual Studio Code](https://code.visualstudio.com)。

## <a name="install-and-configure-the-azure-policy-extension"></a>Azure Policy 拡張機能のインストールと構成

前提条件を満たしたら、次の手順に従って、Visual Studio Code 用 Azure Policy 拡張機能をインストールできます。

1. Visual Studio Code を開きます。
1. メニュー バーから、 **[表示]**  >  **[拡張機能]** の順に移動します。
1. 検索ボックスに「**Azure Policy**」と入力します。
1. 検索結果から **Azure Policy** を選択し、 **[インストール]** を選択します。
1. 必要に応じて **[再読み込み]** を選択します。

国内のクラウド ユーザーの場合は、まず次の手順に従って Azure 環境を設定します。

1. **File\Preferences\Settings** を選択します。
1. 次の文字列を検索します: _Azure:Cloud_ を検索します
1. 一覧から国内のクラウドを選択します。

   :::image type="content" source="../media/extension-for-vscode/set-default-azure-cloud-sign-in.png" alt-text="Visual Studio Code 用の国内の Azure クラウド サインインを選択するスクリーンショット。" border="false":::

## <a name="using-the-policy-extension"></a>Azure Policy 拡張機能の使用

> [!NOTE]
> Visual Studio Code 用の Azure Policy 拡張機能で表示されたポリシーに対してローカルで行われた変更は、Azure に同期されません。

### <a name="connect-to-an-azure-account"></a>Azure アカウントに接続する

リソースを評価してエイリアスを検索するには、まず Azure アカウントに接続する必要があります。 Visual Studio Code から Azure に接続するには、次の手順に従います。

1. Azure Policy 拡張機能またはコマンド パレットから Azure にサインインします。

   - Azure Policy 拡張機能

     Azure Policy 拡張機能から、 **[Azure にサインイン]** を選択します。

     :::image type="content" source="../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png" alt-text="Visual Studio Code と Azure Policy 拡張機能のアイコンのスクリーンショット。" border="false":::

   - コマンド パレット

     メニュー バーから、 **[表示]**  >  **[コマンド パレット]** の順に移動し、「**Azure: Sign In**」と入力します。

     :::image type="content" source="../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png" alt-text="コマンド パレットからの Visual Studio Code 用の Azure クラウド サインイン オプションのスクリーンショット。" border="false":::

1. サインインの手順に従って Azure にサインインします。 接続すると、Visual Studio Code ウィンドウの下部にあるステータス バーに Azure アカウント名が表示されます。

### <a name="select-subscriptions"></a>[サブスクリプション] を選択する

初めてサインインすると、Azure Policy 拡張機能によって既定のサブスクリプション リソースとポリシーのみが読み込まれます。 リソースやポリシーの表示に対してサブスクリプションを追加または削除するには、次の手順を行います。

1. コマンド パレットまたはウィンドウのフッターからサブスクリプション コマンドを開始します。

   - コマンド パレット:

     メニュー バーから、 **[表示]** > **[コマンド パレット]** の順に移動し、「**Azure: Select Subscriptions**」と入力します。

   - ウィンドウのフッター

     画面の下部にあるウィンドウのフッターで、**Azure: \<your account\>** に一致するセグメントを選択します。

1. フィルター ボックスを使用して、名前でサブスクリプションをすばやく検索します。 次に、各サブスクリプションのチェックをオンまたはオフにして、Azure Policy 拡張機能によって表示されるサブスクリプションを設定します。 表示するサブスクリプションの追加または削除が完了したら、 **[OK]** を選択します。

### <a name="search-for-and-view-resources"></a>リソースを検索して表示する

Azure Policy 拡張機能では、 **[リソース]** ウィンドウに、選択したサブスクリプションのリソースがリソース プロバイダー別およびリソース グループ別に表示されます。 ツリービューには、選択したサブスクリプション内またはサブスクリプション レベルで、次のリソースのグループが含まれています。

- **リソース プロバイダー**
  - リソースおよびポリシー エイリアスを持つ関連子リソースを含む、登録済みの各リソース プロバイダー
- **リソース グループ**
  - 属するリソース グループごとのすべてのリソース

既定では、この拡張機能により "リソース プロバイダー" の部分が、既存のリソースおよびポリシー エイリアスを持つリソースによってフィルター処理されます。 すべてのリソース グループをフィルター処理せずに表示するには、 **[設定]**  >  **[拡張機能]**  >  **[Azure Policy]** でこの動作を変更します。

1 つのサブスクリプションに数百または数千のリソースがあるお客様は、リソースを見つけやすい検索方法を好む場合があります。 Azure Policy 拡張機能を使用すると、次の手順で特定のリソースを検索できるようになります。

1. Azure Policy 拡張機能またはコマンド パレットから検索インターフェイスを開始します。

   - Azure Policy 拡張機能

     Azure Policy 拡張機能から、 **[リソース]** パネルにカーソルを合わせ、省略記号を選択してから **[リソースの検索]** を選択します。

   - コマンド パレット:

     メニュー バーから、 **[表示]** > **[コマンド パレット]** の順に移動し、「**Resources: Search Resources**」と入力します。

1. 表示するサブスクリプションが複数選択されている場合は、フィルターを使用して検索するサブスクリプションを選択します。

1. フィルターを使用して、検索対象である、前に選択したサブスクリプションの一部であるリソース グループを選択します。

1. フィルターを使用して、表示するリソースを選択します。 フィルターは、リソース名とリソースの種類の両方に対して機能します。

### <a name="discover-aliases-for-resource-properties"></a>リソース プロパティのエイリアスを検出する

リソースを選択すると、検索インターフェイスを使用したか、ツリー ビューで選択したかに関係なく、リソースとその Azure Resource Manager のプロパティ値のすべてを表す JSON ファイルが Azure Policy 拡張機能によって開かれます。

リソースが開いたら、リソース マネージャーのプロパティ名または値の上にカーソルを合わせると、Azure Policy エイリアスが表示されます (存在する場合)。 この例では、リソースの種類は `Microsoft.Compute/virtualMachines` リソースであり、**properties.storageProfile.imageReference.offer** プロパティの上にカーソルが合わせられています。 カーソルを合わせると、一致するエイリアスが表示されます。

:::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="プロパティにカーソルが合わされてエイリアス名が表示されている、Visual Studio Code 用の Azure Policy 拡張機能のスクリーンショット。" border="false":::

> [!NOTE]
> この VS Code 拡張機能では、Resource Manager モードのプロパティの評価のみがサポートされています。 これらのモードの詳細については、[モードの定義](../concepts/definition-structure.md#mode)を参照してください。

### <a name="search-for-and-view-policies-and-assignments"></a>ポリシーと割り当てを検索して表示する

Azure Policy 拡張機能では、ポリシーの種類とポリシーの割り当ての一覧が、 **[ポリシー]** ウィンドウでの表示対象として選択されたサブスクリプションのツリービューの形で表示されます。 1 つのサブスクリプションに数百または数千のポリシーまたは割り当てがあるお客様は、ポリシーまたは割り当てを見つけやすい検索方法を好む場合があります。 Azure Policy 拡張機能を使用すると、次の手順で特定のポリシーまたは割り当てを検索できるようになります。

1. Azure Policy 拡張機能またはコマンド パレットから検索インターフェイスを開始します。

   - Azure Policy 拡張機能

     Azure Policy 拡張機能から、 **[ポリシー]** パネルにカーソルを合わせ、省略記号を選択してから **[ポリシーの検索]** を選択します。

   - コマンド パレット:

     メニュー バーから、 **[表示]** > **[コマンド パレット]** の順に移動し、「**Policies: Search Policies**」と入力します。

1. 表示するサブスクリプションが複数選択されている場合は、フィルターを使用して検索するサブスクリプションを選択します。

1. フィルターを使用して、検索対象である、前に選択したサブスクリプションの一部であるポリシーの種類または割り当てを選択します。

1. フィルターを使用して、表示するポリシーを選択します。 このフィルターは、ポリシー定義またはポリシー割り当ての _displayName_ に対して機能します。

ポリシーまたは割り当てを選択すると、検索インターフェイスを使用するか、ツリービューで選択することによって、ポリシーまたは割り当てとそのリソース マネージャーのすべてのプロパティ値を表す JSON が Azure Policy 拡張機能によって開かれます。 拡張機能により、開いている Azure Policy JSON スキーマの検証が可能です。

### <a name="export-objects"></a>オブジェクトをエクスポートする

サブスクリプションからのオブジェクトは、ローカルの JSON ファイルにエクスポートできます。 **[リソース]** または **[ポリシー]** ペインで、エクスポート可能なオブジェクトにマウス ポインターを合わせるか選択します。 強調表示された行の末尾で、保存アイコンを選択し、そのリソース JSON を保存するフォルダーを選択します。

次のオブジェクトはローカルにエクスポートできます。

- [リソース] ペイン
  - リソース グループ
  - 個々のリソース (リソース グループ内またはリソース プロバイダーの下)
- [ポリシー] ペイン
  - ポリシーの割り当て
  - 組み込みのポリシー定義
  - カスタム ポリシー定義
  - イニシアティブ

### <a name="on-demand-evaluation-scan"></a>オンデマンドの評価スキャン

評価スキャンは、Visual Studio Code 用の Azure Policy 拡張機能を使用して開始できます。 評価を開始するには、リソース、ポリシー定義、ポリシー割り当ての各オブジェクトを選択し、ピン留めします。

1. 各オブジェクトをピン留めするには、 **[リソース]** ペインまたは **[ポリシー]** ペインでそれを見つけ、編集タブへのピン留めアイコンを選択します。 オブジェクトをピン留めすると、それが拡張機能の **[評価]** ペインに追加されます。
1. **[評価]** ペインで、各オブジェクトの 1 つを選択し、評価のために選択するアイコンを使用して、評価に含まれるものとしてマークします。
1. **[評価]** ペインの上部で、評価の実行アイコンを選択します。 Visual Studio Code の新しいペインが開き、結果の評価の詳細が JSON 形式で表示されます。

> [!NOTE]
> [AuditIfNotExists](../concepts/effects.md#auditifnotexists) または [DeployIfNotExists](../concepts/effects.md#deployifnotexists) ポリシー定義の場合は、 **[評価]** ペインでプラス アイコンを使用して、存在を確認するために "_関連する_" リソースを選択します。

評価結果には、ポリシー定義とポリシー割り当てに関する情報が、**policyEvaluations.evaluationResult** プロパティと共に表示されます。 出力は次の例のようになります。

```json
{
    "policyEvaluations": [
        {
            "policyInfo": {
                ...
            },
            "evaluationResult": "Compliant",
            "effectDetails": {
                "policyEffect": "Audit",
                "existenceScope": "None"
            }
        }
    ]
}
```

> [!NOTE]
> この VS Code 拡張機能では、Resource Manager モードのプロパティの評価のみがサポートされています。 これらのモードの詳細については、[モードの定義](../concepts/definition-structure.md#mode)を参照してください。
>
> 評価機能は、拡張機能の macOS および Linux のインストールでは機能しません。

### <a name="sign-out"></a>サインアウトする

メニュー バーから、 **[ビュー]**  >  **[コマンド パレット]** の順に移動し、「**Azure: Sign Out**」と入力します。

## <a name="next-steps"></a>次のステップ

- [Azure Policy のサンプル](../samples/index.md)を確認します。
- 「[Azure Policy の定義の構造](../concepts/definition-structure.md)」を確認します。
- 「[Policy の効果について](../concepts/effects.md)」を確認します。
- [プログラムによってポリシーを作成する](programmatically-create.md)方法を理解します。
- [準拠していないリソースを修復する](remediate-resources.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。
