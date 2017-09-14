---
title: "Event Hubs を使用して Azure Key Vault からのログを統合する | Microsoft Docs"
description: "このチュートリアルには、Azure ログ統合を利用して Key Vault ログを SIEM で使用できるようにするための必要な手順が用意されています。"
services: security
author: barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.topic: article
ms.date: 08/07/2017
ms.author: Barclayn
ms.custom: AzLog
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 02dde6366a8897a060df451c7f514f79e6733681
ms.contentlocale: ja-jp
ms.lasthandoff: 08/23/2017

---

# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Azure ログ統合チュートリアル: Event Hubs を使用した Azure Key Vault イベントの処理

Azure ログ統合を使用すると、ログに記録されたイベントを取得し、Security Information and Event Management (SIEM) システムで使用できます。 このチュートリアルでは、Azure Event Hubs で取得されるログの処理に Azure ログ統合を使用する方法の例を説明しています。
 
このチュートリアルを使用して、次の手順例に従い、各手順が解決策をどのようにサポートしているかを理解することで、Azure ログ統合と Event Hubs を連携する方法に慣れることをお勧めします。 ここで学習した内容を利用して、自社に固有の要件をサポートする独自の手順を作成することができます。

>[!WARNING]
このチュートリアルで使用されている手順とコマンドは、コピー アンド ペーストで使用するためのものではありません。 あくまでも例として示しています。 このチュートリアルの PowerShell コマンドを実際の環境で "そのまま" 使用しないでください。 お客様固有の環境に基づいてカスタマイズする必要があります。


このチュートリアルでは、イベント ハブにログが記録された Azure Key Vault アクティビティを取得し、SIEM システムで JSON ファイルとして使用できるようにするプロセスについて説明します。 その後、JSON ファイルを処理するように SIEM システムを構成できます。

>[!NOTE]
>このチュートリアルで説明する手順の大半は、キー コンテナー、ストレージ アカウント、およびイベント ハブの構成に関するものです。 具体的な Azure ログ統合手順は、このチュートリアルの末尾に記載されています。 運用環境ではこれらの手順を実行しないでください。 ラボ環境のみで使用してください。 運用環境で使用する前に、手順をカスタマイズする必要があります。

その過程で提供される情報は、各手順の背景情報を理解するのに役立ちます。 他の記事へのリンクは特定のトピックの詳細を提供します。

このチュートリアルに記載のサービスについて詳しくは、次をご覧ください。 

- [Azure Key Vault](../key-vault/key-vault-whatis.md)
- [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Azure ログ統合](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>初期セットアップ

この記事の手順を完了するには、次のものが必要です。

1. Azure のサブスクリプションと、管理者権限のあるそのサブスクリプションのアカウント。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成できます。
 
2. Azure ログ統合のインストール要件を満たす、インターネットにアクセス可能なシステム。 このシステムは、クラウド サービスでも、オンプレミスでホストされていても構いません。

3. [Azure ログ統合](https://www.microsoft.com/download/details.aspx?id=53324)がインストールされている。 インストールするには:

   a. リモート デスクトップを使用して、手順 2 で説明したシステムに接続します。   
   b. Azure ログ統合のインストーラーをシステムにコピーします。 [ここからインストール ファイルをダウンロード](https://www.microsoft.com/download/details.aspx?id=53324)できます。   
   c. インストーラーを起動し、マイクロソフト ソフトウェア ライセンス条項に同意します。   
   d. 利用統計情報を提供する場合は、チェック ボックスをオンのままにします。 使用状況に関する情報をマイクロソフトに送信しない場合は、チェック ボックスをオフにします。
   
   Azure ログ統合とインストール方法について詳しくは、「[Azure 診断ログおよび Windows イベント転送による Azure ログ統合](security-azure-log-integration-get-started.md)」をご覧ください。

4. 最新の PowerShell バージョン。
 
   Windows Server 2016 がインストールされている場合は、PowerShell 5.0 以上が必要です。 その他のバージョンの Windows Server を使用している場合は、それ以前のバージョンの PowerShell がインストールされている可能性があります。 PowerShell ウィンドウで ```get-host``` と入力すると、バージョンを確認できます。 PowerShell 5.0 がインストールされていない場合は、こちらから[ダウンロード](https://www.microsoft.com/download/details.aspx?id=50395)できます。

   PowerShell 5.0 以上がインストールされていれば、最新バージョンのインストールに進むことができます。
   
   a. PowerShell ウィンドウで、```Install-Module Azure``` コマンドを入力します。 インストール手順を完了します。    
   b. ```Install-Module AzureRM``` コマンドを入力します。 インストール手順を完了します。

   詳しくは、[Azure PowerShell のインストール](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0)に関する記事をご覧ください。


## <a name="create-supporting-infrastructure-elements"></a>サポート インフラストラクチャ要素を作成する

1. 管理者特権の PowerShell ウィンドウを開き、**C:\Program Files\Microsoft Azure Log Integration** に移動します。
2. LoadAzLogModule.ps1 スクリプトを実行して AzLog コマンドレットをインポートします。 `.\LoadAzLogModule.ps1` コマンドを入力します。 (このコマンドの ".\" に注意してください)。次のような結果が表示されます。</br>

   ![読み込まれたモジュール一覧](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

3. `Login-AzureRmAccount` コマンドを入力します。 ログイン ウィンドウで、このチュートリアルで使用するサブスクリプションの資格情報を入力します。

   >[!NOTE]
   >これがこのマシンから Azure への初回ログインの場合、マイクロソフトによる PowerShell 使用状況データの収集を許可するかどうかに関するメッセージが表示されます。 使用状況データの収集は Azure PowerShell の向上に利用されるため、有効にすることをお勧めします。

4. 認証およびログインに成功すると、次のスクリーンショットの情報が表示されます。 サブスクリプション ID と サブスクリプション名をメモに取ります。後の手順で使用します。

   ![PowerShell ウィンドウ](./media/security-azure-log-integration-keyvault-eventhub/login-azurermaccount.png)
5. 後で使用される値を格納する変数を作成します。 次の PowerShell の各行を入力します。 使用する環境に合わせて値を調整する必要がある場合があります。
    - ```$subscriptionName = ‘Visual Studio Ultimate with MSDN’``` (サブスクリプション名が異なる可能性があります。 前のコマンドの出力の一部として確認できます。)
    - ```$location = 'West US'``` (この変数はリソースが作成される場所を渡すために使用されます。 この変数を選択した場所のものに変更できます。)
    - ```$random = Get-Random```
    - ``` $name = 'azlogtest' + $random``` (この名前は何でも構いませんが、英小文字と数字のみにする必要があります。)
    - ``` $storageName = $name``` (この変数はストレージ アカウント名に使用されます。)
    - ```$rgname = $name ``` (この変数はリソース グループ名に使用されます。)
    - ``` $eventHubNameSpaceName = $name``` (イベント ハブの名前空間の名前。)
6. 使用するサブスクリプションを指定します。
    
    ```Select-AzureRmSubscription -SubscriptionName $subscriptionName```
7. リソース グループを作成します。
    
    ```$rg = New-AzureRmResourceGroup -Name $rgname -Location $location```
    
   この時点で「`$rg`」と入力した場合、このスクリーンショットのような出力が表示されます。

   ![リソース グループ作成後の出力](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
8. 状態情報の追跡に使用されるストレージ アカウントを作成します。
    
    ```$storage = New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
9. イベント ハブの名前空間を作成します。 これは、イベント ハブの作成に必要です。
    
    ```$eventHubNameSpace = New-AzureRmEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
10. Insights プロバイダーで使用されるルール ID を取得します。
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey' ```
11. 指定可能なすべての Azure の場所を取得し、後の手順で使用できる変数に名前を追加します。
    
    a. ```$locationObjects = Get-AzureRMLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    この時点で「`$locations`」と入力すると、場所の名前が表示されますが、Get-AzureRmLocation によって返される追加情報は表示されません。
12. Azure Resource Manager ログ プロファイルを作成します。 
    
    ```Add-AzureRmLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Azure ログ プロファイルについて詳しくは、「[Azure アクティビティ ログの概要](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)」をご覧ください。

> [!NOTE]
> ログ プロファイルを作成しようとすると、エラー メッセージが表示される場合があります。 その後、Get-AzureRmLogProfile および Remove-AzureRmLogProfile のドキュメントを確認できます。 Get-AzureRmLogProfile を実行すると、ログ プロファイルに関する情報が表示されます。 ```Remove-AzureRmLogProfile -name 'Log Profile Name' ``` コマンドを入力すると、既存のログ プロファイルを削除できます。
>
>![リソース マネージャーのプロファイル エラー](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Key Vault を作成します

1. キー コンテナーを作成します。

   ```$kv = New-AzureRmKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location ```

2. キー コンテナーのログの記録を構成します。

   ```Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true ```

## <a name="generate-log-activity"></a>ログ アクティビティを生成する

Key Vault にログ アクティビティ生成の要求を送信する必要があります。 キー生成や、Key Vault のシークレットの格納または読み取りなどの操作を行うと、ログ エントリが作成されます。

1. 現在のストレージ キーを表示します。
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
2. 新しい **key2** を生成します。
    
   ```New-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
3. キーをもう一度表示し、**key2** が別の値であることを確認します。
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
4. シークレットを設定して読み取り、追加のログ エントリを生成します。
    
   a. ```Set-AzureKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b.  ```(Get-AzureKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![返されるシークレット](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Azure ログ統合を構成する

これで Key Vault によるイベント ハブへのログ記録に必要なすべての要素を構成できました。次は Azure ログ統合を構成する必要があります。

1. ```$storage = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename```
2. ```$eventHubKey = Get-AzureRmEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
3. ```$storagekeys = Get-AzureRmStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
4. ``` $storagekey = $storagekeys[0].Value```

各イベント ハブで AzLog コマンドを実行します。

1. ```$eventhubs = Get-AzureRmEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
2. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

しばらく (または最後の 2 つのコマンドを実行した後しばらく) すると、生成中の JSON ファイルが表示されます。 ディレクトリ **C:\users\AzLog\EventHubJson** でこれを確認できます。

## <a name="next-steps"></a>次のステップ

- [Azure ログ統合 FAQ](security-azure-log-integration-faq.md)
- [Azure ログ統合の使用](security-azure-log-integration-get-started.md)
- [Azure リソース ログの SIEM システムへの統合](security-azure-log-integration-overview.md)

