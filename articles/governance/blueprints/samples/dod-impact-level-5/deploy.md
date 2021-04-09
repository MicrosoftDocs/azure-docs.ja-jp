---
title: DoD 影響レベル 5 ブループリント サンプル
description: ブループリント アーティファクト パラメーターの詳細を含む DoD 影響レベル 5 ブループリント サンプルのデプロイ手順です。
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: 6136136eef5d405ae0849e5ce8c8faede138fb00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98034902"
---
# <a name="deploy-the-dod-impact-level-5-blueprint-sample"></a>DoD 影響レベル 5 ブループリント サンプルをデプロイする

Azure Blueprints 国防総省影響レベル 5 (DoD IL5) のブループリント サンプルをデプロイするには、以下の手順を実行します。

> [!div class="checklist"]
> - サンプルから新しいブループリントを作成する
> - サンプルのコピーを **発行済み** としてマークする
> - ブループリントのコピーを既存のサブスクリプションに割り当てる

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free) を作成してください。

## <a name="create-blueprint-from-sample"></a>サンプルからブループリントを作成する

最初に、サンプルをスターターとして使用して環境内に新しいブループリントを作成することにより、ブループリント サンプルを実装します。

1. 左側のウィンドウにある **[すべてのサービス]** を選択します。 **[ブループリント]** を探して選択します。

1. 左側の **[はじめに]** ページで、 _[ブループリントの作成]_ の下にある **[作成]** ボタンを選択します。

1. **DoD 影響レベル 5** ブループリント サンプルを _[その他のサンプル]_ で検索し、 **[このサンプルを使用する]** を選択します。

1. ブループリント サンプルの _[基本]_ を入力します。

   - **[ブループリントの名前]** : DoD 影響レベル 5 ブループリント サンプルのコピーの名前を指定します。
   - **[定義の場所]** :省略記号を使用して、サンプルのコピーを保存する管理グループを選択します。

1. ページの上部にある _[アーティファクト]_ タブまたはページの下部にある **[次へ: アーティファクト]** を選択します。

1. ブループリント サンプルを構成するアーティファクトの一覧を確認します。 多くのアーティファクトには、後で定義するパラメーターがあります。 ブループリント サンプルの確認が完了したら、 **[下書きの保存]** を選択します。

## <a name="publish-the-sample-copy"></a>サンプルのコピーを発行する

環境でのブループリント サンプルのコピーの作成が完了しました。 これは **下書き** モードで作成されており、割り当ておよびデプロイの前に **発行** する必要があります。 ブループリント サンプルのコピーは、お使いの環境や必要性に応じてカスタマイズできますが、その変更が原因で DoD 影響レベル 5 コントロールに準拠しなくなる可能性もあります。

1. 左側のウィンドウにある **[すべてのサービス]** を選択します。 **[ブループリント]** を探して選択します。

1. 左側の **[ブループリントの定義]** ページを選択します。 ブループリント サンプルのコピーを、フィルターを使用して検索し、選択します。

1. ページの上部にある **[ブループリントを発行する]** を選択します。 右側の新しいページで、ブループリント サンプルのコピーの **[バージョン]** を指定します。 このプロパティは、後で変更を加える場合に役立ちます。 **[変更に関するメモ]** に入力します (例: 「DoD IL5 ブループリント サンプルから発行する最初のバージョン」)。 次に、ページの下部にある **[発行]** を選択します。

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
|DoD 影響レベル 5|ポリシーの割り当て|Windows VM の Administrators グループに含める必要があるユーザーの一覧|ローカルの Administrators グループに含める必要があるユーザーのセミコロン区切りリスト。例: Administrator; myUser1; myUser2|
|DoD 影響レベル 5|ポリシーの割り当て|Windows VM の Administrators グループから除外されたユーザーの一覧|ローカルの Administrators グループで除外する必要があるユーザーのセミコロン区切りリスト。例: Administrator; myUser1; myUser2|
|DoD 影響レベル 5|ポリシーの割り当て|診断ログを有効にする必要のあるリソースの種類の一覧||
|DoD 影響レベル 5|ポリシーの割り当て|VM エージェント レポート用の Log Analytics ワークスペース ID|VM エージェントからレポートする必要のある Log Analytics ワークスペースの ID (GUID)|
|DoD 影響レベル 5|ポリシーの割り当て|Network Watcher を有効にする必要があるリージョンの一覧|リージョンの完全な一覧を確認するには、Get-AzLocation を使用します。|
|DoD 影響レベル 5|ポリシーの割り当て|Windows Web サーバーの TLS の最小バージョン|Windows Web サーバーで有効にする必要がある TLS プロトコルの最小バージョン|
|DoD 影響レベル 5|ポリシーの割り当て|最新の PHP バージョン|App Services に対してサポートされる最新の PHP バージョン|
|DoD 影響レベル 5|ポリシーの割り当て|最新の Java バージョン|App Services に対してサポートされる最新の Java バージョン|
|DoD 影響レベル 5|ポリシーの割り当て|最新の Windows Python バージョン|App Services に対してサポートされる最新の Python バージョン|
|DoD 影響レベル 5|ポリシーの割り当て|最新の Linux Python バージョン|App Services に対してサポートされる最新の Python バージョン|
|DoD 影響レベル 5|ポリシーの割り当て|省略可能:監査スコープに追加する Log Analytics エージェントをサポートする Windows VM イメージの一覧|イメージのセミコロン区切りのリスト。例: /subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage|
|DoD 影響レベル 5|ポリシーの割り当て|省略可能:監査スコープに追加する Log Analytics エージェントをサポートする Linux VM イメージの一覧|イメージのセミコロン区切りのリスト。例: /subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 複数の所有者がサブスクリプションに割り当てられている必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 仮想マシンでディスク暗号化を適用する必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: サブスクリプション所有者に対する重要度 - 高のアラートのメール通知を有効にする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 関数アプリでリモート デバッグを無効にする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 関数アプリの一部として使用された ".NET Framework" のバージョンが最新であることを確認する|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: SQL データベースで Transparent Data Encryption を有効にする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 脆弱性評価を SQL Managed Instance 上で有効にする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: API アプリの一部として使用された "PHP のバージョン" が最新であることを確認する|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: Redis Cache に対してセキュリティで保護された接続のみを有効にする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: エンドポイント保護ソリューションを仮想マシン スケール セットにインストールする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: ストレージ アカウントに対する制限のないネットワーク アクセスの監査|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: SQL Managed Instance の高度なデータ セキュリティ設定に、セキュリティ アラートを受信するためのメール アドレスが含まれている必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: ストレージ アカウントへの安全な転送を有効にする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 適応型アプリケーション制御を仮想マシンで有効にする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: Azure Database for PostgreSQL の geo 冗長バックアップを有効にする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: Web アプリの一部として使用された "Java のバージョン" が最新であることを確認する|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 最大 3 人の所有者をサブスクリプションに対して指定する必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 自分のサブスクリプションに対し、セキュリティ連絡先のメール アドレスを指定する必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: CORS で、Web アプリケーションへのアクセスをすべてのリソースには許可しない|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 読み取りアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 非推奨のアカウントをサブスクリプションから削除する必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: Function App には HTTPS 経由でのみアクセスできるようにする|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: Web アプリの一部として使用された "Python のバージョン" が最新であることを確認する|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 関数アプリの一部として使用された "Python のバージョン" が最新であることを確認する|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: Web アプリの一部として使用された "PHP のバージョン" が最新であることを確認する|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: API アプリの一部として使用された "Python のバージョン" が最新であることを確認する|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 脆弱性評価ソリューションによって脆弱性を修復する必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: Azure Database for MySQL の geo 冗長バックアップを有効にする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: Web アプリの一部として使用された ".NET Framework" のバージョンが最新であることを確認する|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: システム更新プログラムをマシンにインストールする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: API アプリの一部として使用された "Java のバージョン" が最新であることを確認する|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: Web アプリの実行に使用された "HTTP のバージョン" が最新であることを確認する|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: API アプリでは最新の TLS バージョンを使用する必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: SQL Server の高度なデータ セキュリティ設定に、セキュリティ アラートを受信するためのメール アドレスが含まれている必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: API アプリの実行に使用された "HTTP のバージョン" が最新であることを確認する|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: Microsoft IaaSAntimalware 拡張機能は Windows Server 上にデプロイする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 関数アプリの一部として使用された "Java のバージョン" が最新であることを確認する|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: インターネットに接続するエンドポイント経由のアクセスを制限する必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: Security Center の Standard 価格レベルを選択する必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: カスタム RBAC 規則の使用監査|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: Web アプリケーションには HTTPS を介してのみアクセスできるようにする|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: SQL Server の監査を有効にする必要があります|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 仮想マシンに Log Analytics エージェントをインストールする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: DDoS Protection Standard を有効にする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 関数アプリの一部として使用された "PHP のバージョン" が最新であることを確認する|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: Advanced Data Security を、SQL Server 上で有効にする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: Advanced Data Security を SQL Managed Instance 上で有効にする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: SQL Managed Instance の Advanced Data Security 設定で管理者とサブスクリプションの所有者に対するメール通知を有効にする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: Endpoint Protection の欠落の Azure Security Center での監視|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 仮想マシンで Just-In-Time ネットワーク アクセス制御を適用する必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 自分のサブスクリプションに対し、セキュリティ連絡先の電話番号を指定する必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: Service Fabric クラスターは、クライアント認証に Azure Active Directory だけを使用する必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: API アプリには HTTPS を介してのみアクセスできるようにする|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: SQL Managed Instance の Advanced Data Security 設定で、[Advanced Threat Protection の種類] を [すべて] に設定する必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: ストレージ アカウントの geo 冗長ストレージを有効にする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: API アプリの一部として使用された ".NET Framework" のバージョンが最新であることを確認する|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 仮想マシン スケール セットにシステムの更新プログラムをインストールする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: SQL Server の Advanced Data Security 設定で、管理者とサブスクリプションの所有者に対するメール通知を有効にする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: Web アプリケーションのリモート デバッグを無効にする|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: Azure SQL データベースの長期的な geo 冗長バックアップを有効にする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 関数アプリの実行に使用された "HTTP のバージョン" が最新であることを確認する|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: SQL Server の Advanced Data Security 設定で、[Advanced Threat Protection の種類] を [すべて] に設定する必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: コンテナーのセキュリティ構成の脆弱性を修復する必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: API アプリでリモート デバッグを無効にする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 脆弱性評価を SQL Server 上で有効にする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 仮想マシン スケール セットに Log Analytics エージェントをインストールする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: Web アプリでは最新の TLS バージョンを使用する必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: 関数アプリでは最新の TLS バージョンを使用する必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: [プレビュー]: Kubernetes Service を脆弱性のない Kubernetes バージョンにアップグレードする必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|
|DoD 影響レベル 5|ポリシーの割り当て|ポリシーの効果: SQL データベースの脆弱性を修復する必要がある|このポリシーに対する Azure Policy の効果。効果の詳細については、 https://aka.ms/policyeffects をご覧ください|

## <a name="next-steps"></a>次のステップ

DoD 影響レベル 5 ブループリント サンプルのデプロイ手順を確認したので、次に、以下の記事でブループリントおよびコントロール マッピングについて確認します。

> [!div class="nextstepaction"]
> [DoD 影響レベル 5 のブループリント - 概要](./index.md)
> [DoD 影響レベル 5 のブループリント - コントロール マッピング](./control-mapping.md)

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../../how-to/update-existing-assignments.md)方法を参照する。