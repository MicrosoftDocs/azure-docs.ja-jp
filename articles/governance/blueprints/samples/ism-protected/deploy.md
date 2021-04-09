---
title: Australian Government ISM PROTECTED ブループリント サンプルをデプロイする
description: ブループリント アーティファクト パラメーターの詳細を含む Australian Government ISM PROTECTED ブループリント サンプルのデプロイ手順です。
ms.date: 01/21/2021
ms.topic: sample
ms.openlocfilehash: 97dd983ef3188cad7d36459197e57e4e0f3c4e8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100577338"
---
# <a name="deploy-the-australian-government-ism-protected-blueprint-sample"></a>Australian Government ISM PROTECTED ブループリント サンプルをデプロイする

Azure Blueprints ISM PROTECTED ブループリント サンプルをデプロイするには、以下の手順を実行する必要があります。

> [!div class="checklist"]
> - サンプルから新しいブループリントを作成する
> - サンプルのコピーを **発行済み** としてマークする
> - ブループリントのコピーを既存のサブスクリプションに割り当てる

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free) を作成してください。

## <a name="create-blueprint-from-sample"></a>サンプルからブループリントを作成する

最初に、サンプルをスターターとして使用して環境内に新しいブループリントを作成することにより、ブループリント サンプルを実装します。

1. 左側のウィンドウにある **[すべてのサービス]** を選択します。 **[ブループリント]** を探して選択します。

1. 左側の **[はじめに]** ページで、 _[ブループリントの作成]_ の下にある **[作成]** ボタンを選択します。

1. **ISM PROTECTED** ブループリント サンプルを _[その他のサンプル]_ で検索し、 **[このサンプルを使用する]** を選択します。

1. ブループリント サンプルの _[基本]_ を入力します。

   - **[ブループリントの名前]** : ISM PROTECTED ブループリント サンプルのコピーの名前を指定します。
   - **[定義の場所]** :省略記号を使用して、サンプルのコピーを保存する管理グループを選択します。

1. ページの上部にある _[アーティファクト]_ タブまたはページの下部にある **[次へ: アーティファクト]** を選択します。

1. ブループリント サンプルを構成するアーティファクトの一覧を確認します。 多くのアーティファクトには、後で定義するパラメーターがあります。 ブループリント サンプルの確認が完了したら、 **[下書きの保存]** を選択します。

## <a name="publish-the-sample-copy"></a>サンプルのコピーを発行する

環境でのブループリント サンプルのコピーの作成が完了しました。 これは **下書き** モードで作成されており、割り当ておよびデプロイの前に **発行** する必要があります。 ブループリント サンプルのコピーは、お使いの環境や必要性に応じてカスタマイズできますが、それが原因で ISM PROTECTED コントロールに準拠しなくなる可能性もあります。

1. 左側のウィンドウにある **[すべてのサービス]** を選択します。 **[ブループリント]** を探して選択します。

1. 左側の **[ブループリントの定義]** ページを選択します。 ブループリント サンプルのコピーを、フィルターを使用して検索し、選択します。

1. ページの上部にある **[ブループリントを発行する]** を選択します。 右側の新しいページで、ブループリント サンプルのコピーの **[バージョン]** を指定します。 このプロパティは、後で変更を加える場合に役立ちます。 **[変更に関するメモ]** (例:「ISM PROTECTED ブループリント サンプルから発行する最初のバージョン」) を入力します。 次に、ページの下部にある **[発行]** を選択します。

## <a name="assign-the-sample-copy"></a>サンプルのコピーを割り当てる

正常に **発行** されたブループリント サンプルのコピーは、保存先の管理グループ内のサブスクリプションに割り当てることができます。 この手順では、ブループリント サンプルのコピーの各デプロイを一意にするためのパラメーターを指定します。

1. 左側のウィンドウにある **[すべてのサービス]** を選択します。 **[ブループリント]** を探して選択します。

1. 左側の **[ブループリントの定義]** ページを選択します。 ブループリント サンプルのコピーを、フィルターを使用して検索し、選択します。

1. ブループリント定義ページの上部にある **[ブループリントの割り当て]** を選択します。

1. ブループリント割り当て用のパラメーター値を指定します。

   - 基本

     - **サブスクリプション**:ブループリント サンプルのコピーを保存した管理グループ内の 1 つ以上のサブスクリプションを選択します。 複数のサブスクリプションを選択すると、入力したパラメーターを使用して、それぞれに対して割り当てが作成されます。
     - **割り当て名**:名前は、ブループリントの名前に基づいてあらかじめ設定されています。
       必要に応じて変更することも、そのままにしておくこともできます。
     - **[場所]** :マネージド ID を作成するリージョンを選択します。 Azure Blueprint は、この管理対象 ID を使用して、割り当てられたブループリント内にすべての成果物をデプロイします。 詳細については、[Azure リソースの管理対象 ID の概要](../../../../active-directory/managed-identities-azure-resources/overview.md)に関するページをご覧ください。
     - **ブループリント定義ラベル**:ブループリント サンプルのコピーの **発行済み** バージョンを選択します。

   - ロックの割り当て

     お使いの環境のブループリントのロック設定を選択します。 詳細については、[ブループリント リソースのロック](../../concepts/resource-locking.md)に関するページを参照してください。

   - マネージド ID

     既定の _システム割り当て_ マネージド ID オプションはそのままにします。

   - アーティファクトのパラメーター

     このセクションで定義するパラメーターは、定義対象のアーティファクトに適用されます。 これらのパラメーターはブループリントの割り当て時に定義されるので、[動的パラメーター](../../concepts/parameters.md#dynamic-parameters)です。 アーティファクトのパラメーターとその説明を含む詳しい一覧については、「[アーティファクトのパラメーター表](#artifact-parameters-table)」を参照してください。

1. すべてのパラメーターの入力が完了したら、ページの下部にある **[割り当て]** を選択します。 ブループリントの割り当てが作成され、アーティファクトのデプロイが開始されます。 デプロイに要する時間は、約 1 時間です。 デプロイの状態を確認するには、ブループリントの割り当てを開きます。

> [!WARNING]
> Azure Blueprints サービスと、組み込まれているブループリント サンプルは、**無料** でご利用になれます。 Azure リソースは、[製品ごとに課金](https://azure.microsoft.com/pricing/)されます。 このブループリント サンプルでデプロイされるリソースの実行コストを見積もるには、[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用します。

## <a name="artifact-parameters-table"></a>アーティファクトのパラメーター表

以下の表は、ブループリント アーティファクトのパラメーターの一覧を示しています。

|アーティファクト名|アーティファクトの種類|パラメーター名|説明|
|-|-|-|-|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|VM で構成する必要がある Log Analytics ワークスペース ID|これは、VM で構成する必要がある Log Analytics ワークスペース ID (GUID) です。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|診断ログを有効にする必要のあるリソースの種類の一覧|診断ログ設定が無効になっていないかを監査するリソースの種類の一覧。 使用できる値は [Azure Monitor リソース ログ カテゴリ](../../../../azure-monitor/essentials/resource-logs-categories.md#supported-log-categories-per-resource-type)に関するページにあります。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|Windows VM Administrators グループから除外する必要があるユーザーの一覧|ローカルの Administrators グループで除外する必要があるメンバーのセミコロン区切りリスト。 例:Administrator; myUser1; myUser2|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|Windows VM Administrators グループに含める必要があるユーザーの一覧|ローカルの Administrators グループに含める必要があるメンバーのセミコロン区切りリスト。 例:Administrator; myUser1; myUser2|
|\[プレビュー\]:Linux VM スケール セット (VMSS) 用の Log Analytics エージェントのデプロイ|ポリシー割り当て|Linux VM スケール セット (VMSS) 用の Log Analytics ワークスペース|このワークスペースが割り当てのスコープの外部にある場合は、ポリシー割り当てのプリンシパル ID に "Log Analytics 共同作成者" 権限 (または同等の権限) を手動で付与する必要があります。|
|\[プレビュー\]:Linux VM スケール セット (VMSS) 用の Log Analytics エージェントのデプロイ|ポリシー割り当て|省略可能:スコープに追加するため、サポートされている Linux OS を持つ VM イメージの一覧|空の配列 (\[\]) を使用して、オプションのパラメーターがないことを示すことができます。|
|\[プレビュー\]:Linux VM への Log Analytics エージェントのデプロイ|ポリシー割り当て|Linux VM 用の Log Analytics ワークスペース|このワークスペースが割り当てのスコープの外部にある場合は、ポリシー割り当てのプリンシパル ID に "Log Analytics 共同作成者" 権限 (または同等の権限) を手動で付与する必要があります。|
|\[プレビュー\]:Linux VM への Log Analytics エージェントのデプロイ|ポリシー割り当て|省略可能:スコープに追加するため、サポートされている Linux OS を持つ VM イメージの一覧|空の配列 (\[\]) を使用して、オプションのパラメーターがないことを示すことができます。|
|\[プレビュー\]:Windows VM Scale Sets (VMSS) 用の Log Analytics エージェントのデプロイ|ポリシー割り当て|Windows VM スケール セット (VMSS) 用の Log Analytics ワークスペース|このワークスペースが割り当てのスコープの外部にある場合は、ポリシー割り当てのプリンシパル ID に "Log Analytics 共同作成者" 権限 (または同等の権限) を手動で付与する必要があります。|
|\[プレビュー\]:Windows VM Scale Sets (VMSS) 用の Log Analytics エージェントのデプロイ|ポリシー割り当て|省略可能:スコープに追加するため、サポートされている Windows OS を持つ VM イメージの一覧|空の配列 (\[\]) を使用して、オプションのパラメーターがないことを示すことができます。|
|\[プレビュー\]:Windows VM への Log Analytics エージェントのデプロイ|ポリシー割り当て|Windows VM 用の Log Analytics ワークスペース|このワークスペースが割り当てのスコープの外部にある場合は、ポリシー割り当てのプリンシパル ID に "Log Analytics 共同作成者" 権限 (または同等の権限) を手動で付与する必要があります。|
|\[プレビュー\]:Windows VM への Log Analytics エージェントのデプロイ|ポリシー割り当て|省略可能:スコープに追加するため、サポートされている Windows OS を持つ VM イメージの一覧|空の配列 (\[\]) を使用して、オプションのパラメーターがないことを示すことができます。|
|ストレージ アカウントに対する Advanced Threat Protection のデプロイ|ポリシー割り当て|結果|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|SQL サーバーでの監査のデプロイ|ポリシー割り当て|保存期間の日数を示す値 (0 は無制限の保存期間を示します)|保存日数 (省略可能: 指定しない場合には 180 日間)|
|SQL サーバーでの監査のデプロイ|ポリシー割り当て|SQL サーバー監査のためのストレージ アカウントのリソース グループ名|監査によって、Azure Storage アカウントの監査ログにデータベース イベントが書き込まれます (ストレージ アカウントは、SQL Server が作成された各リージョンに作成され、そのリージョン内のすべてのサーバーによって共有されます)。 重要 - 監査を適切に実施するために、リソース グループまたはストレージ アカウントを削除したり、名前を変更したりしないでください。|
|ネットワーク セキュリティ グループの診断設定のデプロイ|ポリシー割り当て|ネットワーク セキュリティ グループの診断用のストレージ アカウント プレフィックス|このプレフィックスがネットワーク セキュリティ グループの場所と組み合わされて、作成されるストレージ アカウント名となります。|
|ネットワーク セキュリティ グループの診断設定のデプロイ|ポリシー割り当て|ネットワーク セキュリティ グループの診断用のストレージ アカウントのリソース グループ名 (存在する必要があります)|ストレージ アカウントが作成されるリソース グループ。 このリソース グループは、既に存在している必要があります。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|リソースとリソース グループが許可される場所|リソースをデプロイする際に組織が指定できる Azure の場所の一覧。 ここで指定された値は、ポリシーのイニシアティブ内の "許可されている場所" ポリシーでも使用されます。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|脆弱性評価を SQL マネージド インスタンス上で有効にする必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|脆弱性評価を SQL サーバー上で有効にする必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|Virtual Machines で脆弱性評価を有効にする必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|ストレージ アカウントの geo 冗長ストレージを有効にする必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|Azure Database for MariaDB の geo 冗長バックアップを有効にする必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|Azure Database for MySQL の geo 冗長バックアップを有効にする必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|Azure Database for PostgreSQL の geo 冗長バックアップを有効にする必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|インターネットに接続している仮想マシン用のネットワーク セキュリティ グループ ルールを強化する必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|Web アプリケーションには HTTPS を介してのみアクセスできるようにする|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|Function App には HTTPS 経由でのみアクセスできるようにする|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|読み取りアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|非推奨のアカウントをサブスクリプションから削除する必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|すべてのリソースが Web アプリにアクセスすることを CORS で許可しない|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|仮想マシン スケール セットにシステムの更新プログラムをインストールする必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|Azure SQL データベースの長期的な geo 冗長バックアップを有効にする必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|Windows VM の Administrators グループから除外されたユーザーの一覧|ローカルの Administrators グループで除外する必要があるメンバーのセミコロン区切りリスト。 例:Administrator; myUser1; myUser2|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|VM で構成する必要がある Log Analytics ワークスペース ID|これは、VM で構成する必要がある Log Analytics ワークスペース ID (GUID) です。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|SQL サーバーの脆弱性評価の設定には、スキャン レポートを受信するためのメール アドレスが含まれている必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|アダプティブ ネットワーク強化の推奨事項をインターネット接続仮想マシンに適用する必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|複数の所有者がサブスクリプションに割り当てられている必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|仮想マシンでディスク暗号化を適用する必要がある| ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|Function App でリモート デバッグを無効にする|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|SQL データベースで Transparent Data Encryption を有効にする必要がある| ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|脆弱性評価を SQL マネージド インスタンス上で有効にする必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|Redis Cache に対してセキュリティで保護された接続のみを有効にする必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|エンドポイント保護ソリューションを仮想マシン スケール セットにインストールする必要がある |ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|省略可能:スコープに追加するため、サポートされている Windows OS を持つ VM イメージの一覧|空の配列 [] を使用して、オプションのパラメーターがないことを示すことができます。
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|省略可能:スコープに追加するため、サポートされている Linux OS を持つ VM イメージの一覧 |空の配列 [] を使用して、オプションのパラメーターがないことを示すことができます。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|ストレージ アカウントに対する制限のないネットワーク アクセスの監査|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|ストレージ アカウントへの安全な転送を有効にする必要がある  |ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|適応型アプリケーション制御を仮想マシンで有効にする必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|最大 3 人の所有者をサブスクリプションに対して指定する必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|[プレビュー] Virtual Machines で脆弱性評価を有効にする必要がある  |ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|CORS で、すべてのリソースが Web アプリケーションにアクセスすることを許可しない|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある |  ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|非推奨のアカウントをサブスクリプションから削除する必要がある |ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|関数アプリには HTTPS v2 を介してのみアクセスできるようにする  |ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|脆弱性評価ソリューションによって脆弱性を修復する必要がある  |ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|Azure サブスクリプションにはアクティビティ ログのログ プロファイルが必要  |ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|診断ログを有効にする必要のあるリソースの種類の一覧|診断ログ設定が無効になっていないかを監査するリソースの種類の一覧。 使用できる値は [Azure Monitor リソース ログ カテゴリ](../../../../azure-monitor/essentials/resource-logs-categories.md#supported-log-categories-per-resource-type)に関するページにあります。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|システム更新プログラムをマシンにインストールする必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|App Service では最新の TLS バージョンを使用する必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|Microsoft IaaSAntimalware 拡張機能は Windows Server 上にデプロイする必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。 |
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|Web アプリケーションには HTTPS v2 を介してのみアクセスできるようにする  |ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。 |
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|DDoS Protection Standard を有効にする必要がある  |ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。 |
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある |ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|Advanced Data Security を、SQL サーバー上で有効にする必要がある |ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|Advanced Data Security を SQL マネージド インスタンス上で有効にする必要がある |ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|エンドポイント保護の不足を Azure Security Center で監視する  |ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|仮想マシンで Just-In-Time ネットワーク アクセス制御を適用する必要がある |ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|Service Fabric クラスターは、クライアント認証に Azure Active Directory だけを使用する必要がある |ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|App Service には HTTPS v2 を介してのみアクセスできるようにする |ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|仮想マシン スケール セットにシステムの更新プログラムをインストールする必要がある  |ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|Web アプリケーションのリモート デバッグを無効にする|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある  |ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。|
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある  |ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。 |
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|［パスワード履歴を強制する］  |  パスワードの再利用制限、つまり同じパスワードを再利用する前にユーザー アカウントに新しいパスワードを何回作成する必要があるかを指定します。 |
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|パスワードの有効期間  |  ユーザー アカウントのパスワードの変更が必要になるまでに経過してもよい最大日数を指定します。 値の形式はコンマで区切られた 2 つの整数で、両端を含む範囲を示します。 |
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|パスワードの変更禁止期間 |   ユーザー アカウントのパスワードを変更できるようになるまでに経過する必要がある最小日数を指定します。 |
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|パスワードの最小文字数  |   ユーザー アカウントのパスワードに含める必要がある最小文字数を指定します。 |
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|パスワードでは、複雑さの要件を満たす必要がある|ユーザー アカウントのパスワードを複雑にする必要があるかどうかを指定します。 その必要がある場合、複雑なパスワードには、ユーザーのアカウント名またはフルネームの一部を含めることができません。また、6 文字以上でなければならず、大文字、小文字、数字、非アルファベットの文字を組み合わせて使用する必要があります。 |
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|コンテナーのセキュリティ構成の脆弱性を修復する必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。 |
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|App Service でリモート デバッグを無効にする|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。 |
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。 |
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|脆弱性評価を SQL サーバー上で有効にする必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。 |
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|Web アプリでは最新の TLS バージョンを使用する必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。 |
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|インターネットに接続する仮想マシンは、ネットワーク セキュリティ グループを使用して保護する必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。 |
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。 |
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|関数アプリでは最新の TLS バージョンを使用する必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。 |
|\[プレビュー\]:監査要件をサポートするため、Australian Government ISM PROTECTED コントロールを監査し、特定の VM 拡張機能をデプロイする|ポリシー割り当て|SQL データベースの脆弱性を修復する必要がある|ポリシーの効果の詳細については、「[Azure Policy の効果について](../../../policy/concepts/effects.md)」を参照してください。 |

## <a name="next-steps"></a>次のステップ

Australian Government ISM PROTECTED のブループリント サンプルのデプロイの手順を確認しました。次に、以下の記事でブループリントおよびコントロール マッピングについて確認します。

> [!div class="nextstepaction"]
> [ISM PROTECTED ブループリント - 概要](./index.md)
> [ISM PROTECTED ブループリント - コントロール マッピング](./control-mapping.md)

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../../how-to/update-existing-assignments.md)方法を参照する。