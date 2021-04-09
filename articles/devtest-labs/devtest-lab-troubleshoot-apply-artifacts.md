---
title: Azure DevTest Labs の成果物に関する問題のトラブルシューティング | Microsoft Docs
description: Azure DevTest Labs の仮想マシンで、成果物を適用するときに発生する問題をトラブルシューティングする方法について学びます。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a89b675a1b3bf134b98e09c7278f0eccb594c325
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483195"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>Azure DevTest Labs 仮想マシンに、成果物を適用する場合に生じる問題のトラブルシューティング
仮想マシンへの成果物の適用は、さまざまな理由で失敗する可能性があります。 この記事では、考えられる原因を特定するのに役立ついくつかの方法について説明します。

この記事に関連してさらにヘルプが必要な場合は、[MSDN AzureおよびStack Overflowフォーラム](https://azure.microsoft.com/support/forums/) で、Azure DevTest Labs（DTL）の専門家に問い合わせできます。 または、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 [サポートを受ける] をクリックします。   

> [!NOTE]
> この記事は、Windows と Windows 以外の仮想マシンの両方に適用されます。 いくつかの違いがありますが、この記事では、それらを明示的に示しています。

## <a name="quick-troubleshooting-steps"></a>簡単なトラブルシューティング手順
VM が実行されていることを確認します。 DevTest Labs では、VM が実行されていること、[Microsoft Azure 仮想マシンエージェント (VM エージェント)](../virtual-machines/extensions/agent-windows.md) がインストールされ、準備ができている必要があります。

> [!TIP]
> **Azure portal** で、VM の  **[成果物の管理]** ページに移動して、VM が成果物を適用する準備ができているかどうかを確認します。 そのページの一番上にメッセージが表示されます。 
> 
> **Azure PowerShell** を使用して、**canApplyArtifacts** フラグを調べます。これは、GET 操作で展開した場合にのみ返されます。 次のコマンド例を参照してください。

```powershell
Select-AzSubscription -SubscriptionId $SubscriptionId | Out-Null
$vm = Get-AzResource `
        -Name "$LabName/$VmName" `
        -ResourceGroupName $LabRgName `
        -ResourceType 'microsoft.devtestlab/labs/virtualmachines' `
        -ApiVersion '2018-10-15-preview' `
        -ODataQuery '$expand=Properties($expand=ComputeVm)'
$vm.Properties.canApplyArtifacts
```

## <a name="ways-to-troubleshoot"></a>トラブルシューティング方法 
次のメソッドのいずれかを使用して、DevTest Labs および Resource Manager デプロイ モデルを使用して作成された VM のトラブルシューティングを行うことができます。

- **Azure portal** - 問題の原因を視覚的にすばやく確認する必要がある場合に最適です。
- **Azure PowerShell** - PowerShell プロンプトに慣れている場合は、Azure PowerShell コマンドレットを使用して DevTest Labs リソースをすばやくクエリします。

> [!TIP]
> VM 内の成果物の実行を確認する方法の詳細については、「[ラボでの成果物失敗の診断](devtest-lab-troubleshoot-artifact-failure.md)」を参照してください。

## <a name="symptoms-causes-and-potential-resolutions"></a>症状、原因、および考えられる解決策 

### <a name="artifact-appears-to-stop-responding"></a>成果物が応答を停止しているように見える

成果物は、事前定義されたタイムアウト期間が経過するまで応答を停止しているように見え、成果物は、**失敗** としてマークされます。

成果物がハングしているように見える場合は、まず、スタックしている場所を確認します。 成果物は、実行中の次の手順のいずれかでブロックできます。

- **最初の要求中**。 DevTest Labs は、カスタムスクリプト拡張機能 (CSE) の使用を要求する Azure Resource Manager テンプレートを作成します。 そのため、バックグラウンドでリソースグループのデプロイがトリガーされます。 このレベルでエラーが発生すると、問題の VM のリソースグループの **アクティビティログ** の詳細が表示されます。  
    - アクティビティログには、ラボ VM ページのナビゲーションバーからアクセスできます。 それを選択すると、**仮想マシンへの成果物の適用**(アーティファクトの適用操作が直接トリガーされた場合) または **仮想マシンの追加または変更**（成果物の適用操作が VM 作成プロセスの一部であった場合）のいずれかのエントリが表示されます。
    - これらのエントリでエラーを検索します。 エラーには、それに応じてタグ付けされない場合があります。また、各エントリを調査する必要があります。
    - 各エントリの詳細を調査するときは、必ず JSON ペイロードの内容を確認してください。 ドキュメントの下部にエラーが表示されることがあります。
- **成果物を実行しようとしていル場合**。 ネットワークまたはストレージの問題が原因である可能性があります。 詳細については、この記事で後述する各セクションを参照してください。 スクリプトの作成方法によっても発生する可能性があります。 次に例を示します。
    - PowerShell スクリプトには、**必須のパラメーター** がありますが、ユーザーが空白のままにすることを許可しているか、artifactfile. json 定義ファイルのプロパティに既定値がないために、1つのパラメーターに値を渡すことはできません。 ユーザーの入力を待機しているため、スクリプトが応答を停止します。
    - PowerShell スクリプトでは、実行の一部として **ユーザー入力** が必要です。 スクリプトは、ユーザー介入を必要とせずに、警告なしで動作するように記述する必要があります。
- **VM エージェントは、準備が完了するまでに時間がかかります**。 VM が最初に起動されたとき、または成果物を適用するための要求を処理するためにカスタムスクリプト拡張機能が最初にインストールされたときに、VM は VM エージェントをアップグレードするか、VM エージェントが初期化されるのを待機する必要があります。 VM エージェントが依存しているサービスの初期化に長い時間がかかっている可能性があります。 そのような場合のトラブルシューティングの詳細については、「[Azure 仮想マシンエージェントの概要](../virtual-machines/extensions/agent-windows.md)」を参照してください。

### <a name="to-verify-if-the-artifact-appears-to-stop-responding-because-of-the-script"></a>スクリプトが原因で成果物が応答を停止しているように見えるかどうかを確認するには

1. 問題の仮想マシンにログインします。
2. バーチャルマシンでスクリプトをローカルにコピーするか、`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>`の下のバーチャルマシンでスクリプトを検索してください。 それは成果物のスクリプトがダウンロードされる場所です。
3. 管理者特権でコマンドプロンプトを使用して、スクリプトをローカルで実行し、問題の原因と同じパラメーター値を指定します。
4. スクリプトに望ましくない動作があるかどうかを判断します。 その場合は、成果物の更新を要求します (パブリックリポジトリからのものである場合)。または、自分で修正してください (プライベートリポジトリからのものである場合)。

> [!TIP]
> [パブリックリポジトリ](https://github.com/Azure/azure-devtestlab) でホストされている成果物の問題を修正し、レビューと承認のために変更を送信することができます。 [README.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md) ドキュメントの「**コントリビューション**」のセクションを参照してください。
> 
> 独自の成果物の作成の詳細については、[AUTHORING.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md) ドキュメントを参照してください。

### <a name="to-verify-if-the-artifact-appears-to-stop-responding-because-of-the-vm-agent"></a>VM エージェントが原因で成果物が応答を停止したように見えるかどうかを確認するには
1. 問題の仮想マシンにログインします。
2. エクスプローラーを使用して **C:\WindowsAzure\logs** に移動します。
3. ファイル **Waappagent.exe** を検索して開きます。
4. VM エージェントがいつ開始され、初期化が完了したかを示すエントリを探します (つまり、最初のハートビートが送信されます)。 新しいエントリを優先するか、または問題が発生した期間の周囲を優先します。

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    この例では、ハートビートが送信されたため、VM エージェントの開始時間が10分20秒かかったことがわかります。 この場合の原因は、OOBE サービスの開始に時間がかかることでした。

> [!TIP]
> Azure 拡張機能の一般情報については、「[Azure 仮想マシンの拡張機能と特徴](../virtual-machines/extensions/overview.md)」をご覧ください。

## <a name="storage-errors"></a>ストレージエラー
DevTest Labs では、成果物をキャッシュするために作成されたラボのストレージアカウントにアクセスする必要があります。 DevTest Labs が成果物を適用すると、構成されたリポジトリから成果物の構成とそのファイルが読み取られます。 既定では、DevTest Labs は、**パブリックアーティファクトリポジトリ** へのアクセスを構成します。

VM の構成方法によっては、このリポジトリに直接アクセスできない場合があります。 したがって、仕様上、DevTest Labs では、ラボが最初に初期化されたときに作成されたストレージアカウントに成果物がキャッシュされます。

このストレージアカウントへのアクセスが何らかの方法でブロックされている場合に、VM から Azure Storage サービスへのトラフィックがブロックされていると、次のようなエラーが表示されることがあります。

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

上記のエラーは、 **[成果物の管理]** の下にある  **[成果物の結果]** ページの **[ディプロイ** メッセージ] セクションに表示されます。 また、問題の仮想マシンのリソースグループの下にある **アクティビティ ログ** にも表示されます。

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>Azure Storage サービスとの通信がブロックされていないことを確認するには:

- **追加されたネットワークセキュリティグループ (NSG)** を確認します。 サブスクリプション ポリシーが追加されているために、そこでは、NSG が、すべての仮想ネットワークで、自動的に構成される可能性があります。 また、ラボの既定の仮想ネットワーク (使用されている場合) または ラボで構成され、VM の作成に使用される他の仮想ネットワークにも影響します。
- **既定のラボのストレージアカウント** (つまり、ラボの作成時に作成された最初のストレージアカウント。名前は、通常、英文字 "a" で始まり、2 桁の数字で終わる \<labname\>#) を確認します。
    1. ラボのリソースグループに移動します。
    2. 名前が規則に一致する **ストレージアカウント** の種類のリソースを検索します。
    3. **ファイア ウォールと仮想ネットワーク** と呼ばれる [ストレージアカウント] ページに移動します。
    4. **[すべてのネットワーク]** に設定されていることを確認します。 **[選択したネットワーク]**  オプションが選択されている場合は、VM の作成に使用されたラボの仮想ネットワークが一覧に追加されていることを確認します。

詳細なトラブルシューティングについては、[Azure Storage ファイアウォールおよび仮想ネットワークの構成](../storage/common/storage-network-security.md)に関する記事を参照してください。

> [!TIP]
> **ネットワーク セキュリティ グループの規則を確認する**。 [IP フロー検証](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify)を使用して、ネットワークセキュリティグループの規則が仮想マシンとの間のトラフィックをブロックしていることを確認します。 有効なセキュリティ グループ規則を確認して、受信 「**許可**」NSG 規則が存在していることを確認することもできます。 詳細については、「[有効なセキュリティ規則を使用した VM トラフィック フローのトラブルシューティング](../virtual-network/diagnose-network-traffic-filter-problem.md)」を参照してください。

## <a name="other-sources-of-error"></a>その他のエラー原因
他にも頻度の低いエラーの原因があります。 それぞれのケースに該当するかどうかを評価してください。 その一例を次に示します。 

- **プライベートリポジトリの有効期限が切れた個人用アクセストークン**。 有効期限が切れた場合、成果物は表示されず、有効期限が切れたプライベート アクセス トークンを持つリポジトリから成果物を参照するスクリプトは、それに応じて失敗します。

## <a name="next-steps"></a>次のステップ
これらのエラーが発生していないのに、成果物を適用できない場合は、Azure サポートインシデントを申請できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。
