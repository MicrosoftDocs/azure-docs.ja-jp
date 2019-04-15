---
title: Event Hubs を使用して Azure Key Vault からのログを統合する | Microsoft Docs
description: このチュートリアルには、Azure ログ統合を利用して Key Vault ログを SIEM で使用できるようにするための必要な手順が用意されています。
services: security
author: barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.topic: article
ms.date: 01/14/2019
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 7e70920e806b3d9838d693ff1fc74a3e9371319d
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58883923"
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Azure Log Integration のチュートリアル:Event Hubs を使用して Azure Key Vault のイベントを処理する

>[!IMPORTANT]
> Azure ログの統合機能は、2019 年 6 月 1 日までに廃止される予定です。 AzLog ダウンロードは、2018 年 6 月 27 日に無効になりました。 今後必要な対応のガイダンスについては、[Azure 監視を使って SIEM ツールと統合する](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)方法に関する投稿を確認してください。 

Azure ログ統合を使用すると、ログに記録されたイベントを取得し、Security Information and Event Management (SIEM) システムで使用できます。 このチュートリアルでは、Azure Event Hubs で取得されるログの処理に Azure ログ統合を使用する方法の例を説明しています。

Azure ログを統合する場合は、SIEM ベンダーの Azure Monitor コネクタを使用して、こちらの[手順](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)に従うことをお勧めします。 ただし、SIEM ベンダーが Azure Monitor へのコネクタを提供していない場合は、そのようなコネクタが入手可能になるまで、Azure Log Integration を一時的なソリューションとして使用できます (SIEM が Azure Log Integration でサポートされている場合)。

 
このチュートリアルを使用して、次の手順例に従い、各手順が解決策をどのようにサポートしているかを理解することで、Azure ログ統合と Event Hubs を連携する方法に慣れることをお勧めします。 ここで学習した内容を利用して、自社に固有の要件をサポートする独自の手順を作成することができます。

> [!WARNING]
> このチュートリアルで使用されている手順とコマンドは、コピー アンド ペーストで使用するためのものではありません。 あくまでも例として示しています。 このチュートリアルの PowerShell コマンドを実際の環境で "そのまま" 使用しないでください。 お客様固有の環境に基づいてカスタマイズする必要があります。


このチュートリアルでは、イベント ハブにログが記録された Azure Key Vault アクティビティを取得し、SIEM システムで JSON ファイルとして使用できるようにするプロセスについて説明します。 その後、JSON ファイルを処理するように SIEM システムを構成できます。

>[!NOTE]
>このチュートリアルで説明する手順の大半は、キー コンテナー、ストレージ アカウント、およびイベント ハブの構成に関するものです。 具体的な Azure ログ統合手順は、このチュートリアルの末尾に記載されています。 運用環境ではこれらの手順を実行しないでください。 ラボ環境のみで使用してください。 運用環境で使用する前に、手順をカスタマイズする必要があります。

その過程で提供される情報は、各手順の背景情報を理解するのに役立ちます。 他の記事へのリンクは特定のトピックの詳細を提供します。

このチュートリアルに記載のサービスについて詳しくは、次をご覧ください。 

- [Azure Key Vault](../key-vault/key-vault-whatis.md)
- [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Azure Log Integration](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>初期セットアップ

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

この記事の手順を完了するには、次のものが必要です。

* Azure のサブスクリプションと、管理者権限のあるそのサブスクリプションのアカウント。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成できます。
 
* Azure ログ統合のインストール要件を満たす、インターネットにアクセス可能なシステム。 このシステムは、クラウド サービスでも、オンプレミスでホストされていても構いません。

* Azure Log Integration がインストールされている。 インストールするには:

   a. リモート デスクトップを使用して、手順 2 で説明したシステムに接続します。   
   b. Azure ログ統合のインストーラーをシステムにコピーします。 c. インストーラーを起動し、マイクロソフト ソフトウェア ライセンス条項に同意します。

* 利用統計情報を提供する場合は、チェック ボックスをオンのままにします。 使用状況に関する情報をマイクロソフトに送信しない場合は、チェック ボックスをオフにします。

   Azure ログ統合とインストール方法について詳しくは、「[Azure 診断ログおよび Windows イベント転送による Azure ログ統合](security-azure-log-integration-get-started.md)」をご覧ください。

* 最新の PowerShell バージョン。

   Windows Server 2016 がインストールされている場合は、PowerShell 5.0 以上が必要です。 その他のバージョンの Windows Server を使用している場合は、それ以前のバージョンの PowerShell がインストールされている可能性があります。 PowerShell ウィンドウで ```get-host``` と入力すると、バージョンを確認できます。 PowerShell 5.0 がインストールされていない場合は、こちらから[ダウンロード](https://www.microsoft.com/download/details.aspx?id=50395)できます。

   PowerShell 5.0 以上がインストールされていれば、[Azure PowerShell のインストール](/powershell/azure/install-az-ps)に関するページの手順に従って最新バージョンのインストールに進めます。


## <a name="create-supporting-infrastructure-elements"></a>サポート インフラストラクチャ要素を作成する

1. 管理者特権の PowerShell ウィンドウを開き、**C:\Program Files\Microsoft Azure Log Integration** に移動します。
1. LoadAzLogModule.ps1 スクリプトを実行して AzLog コマンドレットをインポートします。 `.\LoadAzLogModule.ps1` コマンドを入力します。 (このコマンドの ".\" に注意してください。)次のような結果が表示されます。</br>

   ![読み込まれたモジュール一覧](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

1. `Connect-AzAccount` コマンドを入力します。 ログイン ウィンドウで、このチュートリアルで使用するサブスクリプションの資格情報を入力します。

   >[!NOTE]
   >これがこのマシンから Azure への初回ログインの場合、マイクロソフトによる PowerShell 使用状況データの収集を許可するかどうかに関するメッセージが表示されます。 使用状況データの収集は Azure PowerShell の向上に利用されるため、有効にすることをお勧めします。

1. 認証が成功すると、ログインしています。 サブスクリプション ID と サブスクリプション名をメモに取ります。後の手順で使用します。

1. 後で使用される値を格納する変数を作成します。 次の PowerShell の各行を入力します。 使用する環境に合わせて値を調整する必要がある場合があります。
    - ```$subscriptionName = 'Visual Studio Ultimate with MSDN'``` (サブスクリプション名が異なる可能性があります。 前のコマンドの出力の一部として確認できます。)
    - ```$location = 'West US'``` (この変数はリソースが作成される場所を渡すために使用されます。 この変数を選択した場所のものに変更できます。)
    - ```$random = Get-Random```
    - ```$name = 'azlogtest' + $random``` (この名前は何でも構いませんが、英小文字と数字のみにする必要があります。)
    - ```$storageName = $name``` (この変数はストレージ アカウント名に使用されます。)
    - ```$rgname = $name``` (この変数はリソース グループ名に使用されます。)
    - ```$eventHubNameSpaceName = $name``` (イベント ハブの名前空間の名前。)
1. 使用するサブスクリプションを指定します。
    
    ```Select-AzSubscription -SubscriptionName $subscriptionName```
1. リソース グループを作成します。
    
    ```$rg = New-AzResourceGroup -Name $rgname -Location $location```
    
   この時点で「`$rg`」と入力した場合、このスクリーンショットのような出力が表示されます。

   ![リソース グループ作成後の出力](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
1. 状態情報の追跡に使用されるストレージ アカウントを作成します。
    
    ```$storage = New-AzStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
1. イベント ハブの名前空間を作成します。 これは、イベント ハブの作成に必要です。
    
    ```$eventHubNameSpace = New-AzEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
1. Insights プロバイダーで使用されるルール ID を取得します。
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey'```
1. 指定可能なすべての Azure の場所を取得し、後の手順で使用できる変数に名前を追加します。
    
    a. ```$locationObjects = Get-AzLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    この時点で「`$locations`」と入力すると、場所の名前が表示されますが、Get-AzLocation によって返される追加情報は表示されません。
1. Azure Resource Manager ログ プロファイルを作成します。 
    
    ```Add-AzLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Azure ログ プロファイルについて詳しくは、「[Azure アクティビティ ログの概要](../azure-monitor/platform/activity-logs-overview.md)」をご覧ください。

> [!NOTE]
> ログ プロファイルを作成しようとすると、エラー メッセージが表示される場合があります。 その後、Get-AzLogProfile および Remove-AzLogProfile のドキュメントを確認できます。 Get-AzLogProfile を実行すると、ログ プロファイルに関する情報が表示されます。 ```Remove-AzLogProfile -name 'Log Profile Name'``` コマンドを入力すると、既存のログ プロファイルを削除できます。
>
>![リソース マネージャーのプロファイル エラー](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Key Vault を作成します

1. キー コンテナーを作成します。

   ```$kv = New-AzKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location```

1. キー コンテナーのログの記録を構成します。

   ```Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true```

## <a name="generate-log-activity"></a>ログ アクティビティを生成する

Key Vault にログ アクティビティ生成の要求を送信する必要があります。 キー生成や、Key Vault のシークレットの格納または読み取りなどの操作を行うと、ログ エントリが作成されます。

1. 現在のストレージ キーを表示します。
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. 新しい **key2** を生成します。
    
   ```New-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
1. キーをもう一度表示し、**key2** が別の値であることを確認します。
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. シークレットを設定して読み取り、追加のログ エントリを生成します。
    
   a. ```Set-AzKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)```
   b. ```(Get-AzKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![返されるシークレット](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Azure ログ統合を構成する

これで Key Vault によるイベント ハブへのログ記録に必要なすべての要素を構成できました。次は Azure ログ統合を構成する必要があります。

1. ```$storage = Get-AzStorageAccount -ResourceGroupName $rgname -Name $storagename```
1. ```$eventHubKey = Get-AzEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
1. ```$storagekeys = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
1. ```$storagekey = $storagekeys[0].Value```

各イベント ハブで AzLog コマンドを実行します。

1. ```$eventhubs = Get-AzEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
1. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

しばらく (または最後の 2 つのコマンドを実行した後しばらく) すると、生成中の JSON ファイルが表示されます。 ディレクトリ **C:\users\AzLog\EventHubJson** でこれを確認できます。

## <a name="next-steps"></a>次の手順

- [Azure ログ統合 のFAQ](security-azure-log-integration-faq.md)
- [Azure Log Integration の概要](security-azure-log-integration-get-started.md)
- [Azure リソースのログを SIEM システムと統合する](security-azure-log-integration-overview.md)
