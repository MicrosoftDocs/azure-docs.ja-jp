---
title: CIS Microsoft Azure Foundations Benchmark v1.3.0 ブループリント サンプル
description: CIS Microsoft Azure Foundations Benchmark v1.3.0 ブループリント サンプルの概要。 このブループリント サンプルは、お客様が特定のコントロールを評価するのに役立ちます。
ms.date: 03/11/2021
ms.topic: sample
ms.openlocfilehash: d6b3a84ab426c5003233958b77f5c480f28cd704
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103202394"
---
# <a name="cis-microsoft-azure-foundations-benchmark-v130-blueprint-sample"></a>CIS Microsoft Azure Foundations Benchmark v1.3.0 ブループリント サンプル

CIS Microsoft Azure Foundations Benchmark v1.3.0 ブループリント サンプルには、特定の CIS Microsoft Azure Foundations Benchmark v1.3.0 の推奨事項を評価する際に役立つ、[Azure Policy](../../policy/overview.md) を使用したガバナンス ガードレールが用意されています。 このブループリントは、CIS Microsoft Azure Foundations Benchmark v1.3.0 の推奨事項の実施が必要とされる、Azure でデプロイされたアーキテクチャのために、一連の主要なポリシーをデプロイするのに役立ちます。

## <a name="recommendation-mapping"></a>推奨マッピング

[Azure Policy の推奨マッピング](../../policy/samples/cis-azure-1-3-0.md)では、このブループリントに含まれるポリシーの定義について詳しく取り上げると共に、それらの定義が CIS Microsoft Azure Foundations Benchmark v1.3.0 の **推奨事項** にどのようにマッピングされるかについて詳しく説明します。 リソースはアーキテクチャに割り当てられると、割り当て済みのポリシー定義に違反していないかどうかを Azure Policy によって評価されます。 詳細については、[Azure Policy](../../policy/overview.md) に関するページをご覧ください。

## <a name="deploy"></a>配置

Azure Blueprints CIS Microsoft Azure Foundations Benchmark v1.3.0 ブループリント サンプルをデプロイするには、次の手順を実行する必要があります。

> [!div class="checklist"]
> - サンプルから新しいブループリントを作成する
> - サンプルのコピーを **発行済み** としてマークする
> - ブループリントのコピーを既存のサブスクリプションに割り当てる

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free) を作成してください。

### <a name="create-blueprint-from-sample"></a>サンプルからブループリントを作成する

最初に、サンプルをスターターとして使用して環境内に新しいブループリントを作成することにより、ブループリント サンプルを実装します。

1. 左側のウィンドウにある **[すべてのサービス]** を選択します。 **[ブループリント]** を探して選択します。

1. 左側の **[はじめに]** ページで、 _[ブループリントの作成]_ の下にある **[作成]** ボタンを選択します。

1. _[その他のサンプル]_ から **CIS Microsoft Azure Foundations Benchmark v1.3.0** ブループリント サンプルを見つけて **[このサンプルを使用する]** を選択します。

1. ブループリント サンプルの _[基本]_ を入力します。

   - **[ブループリントの名前]** : CIS Microsoft Azure Foundations Benchmark ブループリント サンプルのコピーの名前を指定します。
   - **[定義の場所]** :省略記号を使用して、サンプルのコピーを保存する管理グループを選択します。

1. ページの上部にある _[アーティファクト]_ タブまたはページの下部にある **[次へ: アーティファクト]** を選択します。

1. ブループリント サンプルに含まれるアーティファクトの一覧を確認します。 多くのアーティファクトには、後で定義するパラメーターがあります。 ブループリント サンプルの確認が完了したら、 **[下書きの保存]** を選択します。

### <a name="publish-the-sample-copy"></a>サンプルのコピーを発行する

環境でのブループリント サンプルのコピーの作成が完了しました。 これは **下書き** モードで作成されており、割り当ておよびデプロイの前に **発行** する必要があります。 ブループリント サンプルのコピーは環境やニーズに合わせてカスタマイズできますが、その変更によって CIS Microsoft Azure Foundations Benchmark v1.3.0 の推奨事項との整合性が失われる可能性があります。

1. 左側のウィンドウにある **[すべてのサービス]** を選択します。 **[ブループリント]** を探して選択します。

1. 左側の **[ブループリントの定義]** ページを選択します。 ブループリント サンプルのコピーを、フィルターを使用して検索し、選択します。

1. ページの上部にある **[ブループリントを発行する]** を選択します。 右側の新しいページで、ブループリント サンプルのコピーの **[バージョン]** を指定します。 このプロパティは、後で変更を加える場合に役立ちます。 "CIS Microsoft Azure Foundations Benchmark ブループリント サンプルから発行された最初のバージョン" などの **[変更に関するメモ]** を指定します。 次に、ページの下部にある **[発行]** を選択します。

### <a name="assign-the-sample-copy"></a>サンプルのコピーを割り当てる

正常に **発行** されたブループリント サンプルのコピーは、保存先の管理グループ内のサブスクリプションに割り当てることができます。 この手順では、ブループリント サンプルのコピーの各デプロイを一意にするためのパラメーターを指定します。

1. 左側のウィンドウにある **[すべてのサービス]** を選択します。 **[ブループリント]** を探して選択します。

1. 左側の **[ブループリントの定義]** ページを選択します。 ブループリント サンプルのコピーを、フィルターを使用して検索し、選択します。

1. ブループリント定義ページの上部にある **[ブループリントの割り当て]** を選択します。

1. ブループリント割り当て用のパラメーター値を指定します。

   - 基本

     - **サブスクリプション**:ブループリント サンプルのコピーを保存した管理グループ内の 1 つ以上のサブスクリプションを選択します。 複数のサブスクリプションを選択すると、入力したパラメーターを使用して、それぞれに対して割り当てが作成されます。
     - **割り当て名**:名前は、ブループリントの名前に基づいてあらかじめ設定されています。
       必要に応じて変更することも、そのままにしておくこともできます。
     - **[場所]** :マネージド ID を作成するリージョンを選択します。 Azure Blueprint は、この管理対象 ID を使用して、割り当てられたブループリント内にすべての成果物をデプロイします。 詳細については、[Azure リソースの管理対象 ID の概要](../../../active-directory/managed-identities-azure-resources/overview.md)に関するページをご覧ください。
     - **ブループリント定義ラベル**:ブループリント サンプルのコピーの **発行済み** バージョンを選択します。

   - ロックの割り当て

     お使いの環境のブループリントのロック設定を選択します。 詳細については、[ブループリント リソースのロック](../concepts/resource-locking.md)に関するページを参照してください。

   - マネージド ID

     既定の _システム割り当て_ マネージド ID オプションはそのままにします。

   - アーティファクトのパラメーター

     このセクションで定義するパラメーターは、定義対象のアーティファクトに適用されます。 これらのパラメーターはブループリントの割り当て時に定義されるので、[動的パラメーター](../concepts/parameters.md#dynamic-parameters)です。 アーティファクトのパラメーターとその説明を含む詳しい一覧については、「[アーティファクトのパラメーター表](#artifact-parameters-table)」を参照してください。

1. すべてのパラメーターの入力が完了したら、ページの下部にある **[割り当て]** を選択します。 ブループリントの割り当てが作成され、アーティファクトのデプロイが開始されます。 デプロイに要する時間は、約 1 時間です。 デプロイの状態を確認するには、ブループリントの割り当てを開きます。

> [!WARNING]
> Azure Blueprints サービスと、組み込まれているブループリント サンプルは、**無料** でご利用になれます。 Azure リソースは、[製品ごとに課金](https://azure.microsoft.com/pricing/)されます。 このブループリント サンプルでデプロイされるリソースの実行コストを見積もるには、[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用します。

### <a name="artifact-parameters-table"></a>アーティファクトのパラメーター表

以下の表は、ブループリント アーティファクトのパラメーターの一覧を示しています。

|アーティファクト名|アーティファクトの種類|パラメーター名|説明|
|-|-|-|-|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|使用が承認されている仮想マシン拡張機能の一覧|仮想マシン拡張機能のセミコロン区切りの一覧です。拡張機能の完全な一覧を確認するには、Azure PowerShell コマンド Get-AzVMExtensionImage を使用してください|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: SQL マネージド インスタンスでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: SQL サーバーの脆弱性評価の設定には、スキャン レポートを受信するためのメール アドレスが含まれている必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Azure Data Lake Store の診断ログを有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 仮想マシンでディスク暗号化を適用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: キー コンテナーでは消去保護を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: API アプリで "クライアント証明書 (着信クライアント証明書)" が "オン" に設定されていることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: SQL サーバーでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 関数アプリではマネージド ID を使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Azure Defender for Key Vault を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: カスタム サブスクリプションの所有者ロールは存在してはならない|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: キーには有効期限が設定されている必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: SQL データベースで Transparent Data Encryption を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: SQL Managed Instance で脆弱性評価を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: API アプリの一部として使用された "PHP のバージョン" が最新であることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Azure Defender for App Service を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: ストレージ アカウントでは仮想ネットワーク規則を使用してネットワーク アクセスを制限する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Web アプリではマネージド ID を使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: インターネットからの SSH アクセスをブロックする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: アタッチされていないディスクを暗号化する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Azure Defender for Storage を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: ストレージ アカウントではネットワーク アクセスを制限する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Logic Apps の診断ログを有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: IoT Hub の診断ログを有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 関数アプリでは FTPS のみを必須にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 特定のセキュリティ操作のアクティビティ ログ アラートが存在する必要がある (Microsoft.Security/securitySolutions/delete)|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 特定のセキュリティ操作のアクティビティ ログ アラートが存在する必要がある (Microsoft.Security/securitySolutions/write)|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: ストレージ アカウントへの安全な転送を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Batch アカウントの診断ログを有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: サブスクリプションで Log Analytics エージェントの自動プロビジョニングを有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Web アプリの一部として使用された "Java のバージョン" が最新であることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Web アプリでは FTPS を必須にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Azure Defender for servers を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: サブスクリプションには、セキュリティの問題に備えて連絡先メール アドレスが用意されている必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: ストレージ アカウントのパブリック アクセスを禁止する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Azure Defender for Kubernetes を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: PostgreSQL データベース サーバーに対する接続の調整を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Web アプリで "クライアント証明書 (着信クライアント証明書)" が "オン" に設定されていることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 読み取りアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Azure Defender for SQL servers on machines を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 重要度が高いアラートのメール通知を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: ストレージ アカウントでは暗号化にカスタマー マネージド キーを使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Web アプリの一部として使用された "Python のバージョン" が最新であることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 関数アプリの一部として使用された "Python のバージョン" が最新であることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Web アプリの一部として使用された "PHP のバージョン" が最新であることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: API アプリの一部として使用された "Python のバージョン" が最新であることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Virtual Machine Scale Sets の診断ログを有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Azure Defender for Azure SQL Database servers を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: イベント ハブの診断ログを有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: システム更新プログラムをマシンにインストールする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: API アプリの一部として使用された "Java のバージョン" が最新であることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: SQL Server は 90 日以上の監査保持期間で構成されている必要がある。|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Web アプリの実行に使用された "HTTP のバージョン" が最新であることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: API アプリでは最新の TLS バージョンを使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Web アプリで認証を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: シークレットには有効期限が設定されている必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: API アプリの実行に使用された "HTTP のバージョン" が最新であることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: API アプリでは FTPS のみを必須にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 関数アプリの一部として使用された "Java のバージョン" が最新であることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Web アプリケーションには HTTPS を介してのみアクセスできるようにする|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: SQL Server の監査を有効にする必要があります|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Advanced Data Security を、SQL Server 上で有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: SQL Managed Instance で Advanced Data Security を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Kubernetes サービスではロールベースのアクセス制御 (RBAC) を使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Endpoint Protection の欠落の Azure Security Center での監視|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Search サービスの診断ログを有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: App Services の診断ログを有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 特定の管理操作のアクティビティ ログ アラートが存在する必要がある (Microsoft.Network/networkSecurityGroups/delete)|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 特定の管理操作のアクティビティ ログ アラートが存在する必要がある (Microsoft.Network/networkSecurityGroups/securityRules/delete)|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 特定の管理操作のアクティビティ ログ アラートが存在する必要がある (Microsoft.Network/networkSecurityGroups/securityRules/write)|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 特定の管理操作のアクティビティ ログ アラートが存在する必要がある (Microsoft.Network/networkSecurityGroups/write)|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 特定の管理操作のアクティビティ ログ アラートが存在する必要がある (Microsoft.Sql/servers/firewallRules/delete)|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 特定の管理操作のアクティビティ ログ アラートが存在する必要がある (Microsoft.Sql/servers/firewallRules/write)|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 承認された VM 拡張機能のみをインストールする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Azure Defender for container registries を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: API アプリではマネージド ID を使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: API アプリで認証を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 特定のポリシー操作のアクティビティ ログ アラートが存在する必要がある (Microsoft.Authorization/policyAssignments/delete)|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 特定のポリシー操作のアクティビティ ログ アラートが存在する必要がある (Microsoft.Authorization/policyAssignments/write)|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 関数アプリで認証を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Data Lake Analytics の診断ログを有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: ストレージ アカウントでは、信頼された Microsoft サービスからのアクセスを許可する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Key Vault の診断ログを有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: PostgreSQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 関数アプリの実行に使用された "HTTP のバージョン" が最新であることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: インターネットからの RDP アクセスをブロックする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: MySQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 関数アプリで "クライアント証明書 (着信クライアント証明書)" が "オン" に設定されていることを確認する|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: PostgreSQL データベース サーバーではログ チェックポイントが有効でなければならない|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: PostgreSQL データベース サーバーではログ接続が有効でなければならない|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: PostgreSQL データベース サーバーでは切断をログに記録する必要がある。|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 脆弱性評価を SQL Server 上で有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Web アプリでは最新の TLS バージョンを使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Service Bus の診断ログを有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: Azure Stream Analytics の診断ログを有効にする必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: 関数アプリでは最新の TLS バージョンを使用する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|ポリシーの効果: アクティビティ ログがあるコンテナーを含むストレージ アカウントは、BYOK を使用して暗号化する必要がある|効果の詳細については、[https://aka.ms/policyeffects](https://aka.ms/policyeffects) を参照してください。 |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|仮想マシン スケール セットの診断ログが有効になっている場合は、監査するときに AKS クラスターを含める||
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|App Services に対する最新の Java バージョン|App Services に対してサポートされる最新の Java バージョン|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|App Services に対する Linux 用の最新の Python バージョン|App Services に対してサポートされる最新の Python バージョン|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|Network Watcher を有効にする必要があるリージョンの一覧|リージョンの完全な一覧を確認するには、PowerShell コマンド Get-AzLocation を実行します|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|App Services に対する最新の PHP バージョン|App Services に対してサポートされる最新の PHP バージョン|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|リソース ログの必須保持期間 (日数)|リソース ログの詳細については、「[https://aka.ms/resourcelogs](https://aka.ms/resourcelogs)」を参照してください |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|Network Watcher 用のリソース グループの名前|Network Watcher が配置されているリソース グループの名前|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|ポリシーの割り当て|SQL サーバーに必要な監査設定||

## <a name="next-steps"></a>次のステップ

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../how-to/update-existing-assignments.md)方法を参照する。