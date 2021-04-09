---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: c0a0b44b3de088539a070f1182248c4e6db7303f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97510279"
---
## <a name="create-a-self-hosted-integration-runtime"></a>自己ホスト型統合ランタイムを作成する

このセクションでは、セルフホステッド統合ランタイムを作成し、SQL Server データベースがあるオンプレミスのマシンに関連付けます。 セルフホステッド統合ランタイムは、マシンの SQL Server から Azure SQL Database にデータをコピーするコンポーネントです。 

1. 統合ランタイムの名前に使用する変数を作成します。 一意の名前を使用し、その名前をメモしておきます。 このチュートリアルの後の方で、それを使用します。 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
2. セルフホステッド統合ランタイムを作成します。 

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   出力例を次に示します。

   ```console
    Name              : <Integration Runtime name>
    Type              : SelfHosted
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Description       : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DataFactory/factories/<DataFactoryName>/integrationruntimes/ADFTutorialIR
    ```
  
3. 作成された統合ランタイムの状態を取得するために、次のコマンドを実行します。 **[状態]** プロパティの値が **[NeedRegistration]** に設定されていることを確認します。 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   出力例を次に示します。

   ```console  
   State                     : NeedRegistration
   Version                   : 
   CreateTime                : 9/24/2019 6:00:00 AM
   AutoUpdate                : On
   ScheduledUpdateDate       : 
   UpdateDelayOffset         : 
   LocalTimeZoneOffset       : 
   InternalChannelEncryption : 
   Capabilities              : {}
   ServiceUrls               : {eu.frontend.clouddatahub.net}
   Nodes                     : {}
   Links                     : {}
   Name                      : ADFTutorialIR
   Type                      : SelfHosted
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Description               : 
   Id                        : /subscriptions/<subscription ID>/resourceGroups/<ResourceGroup name>/providers/Microsoft.DataFactory/factories/<DataFactory name>/integrationruntimes/<Integration Runtime name>
   ```

4. 次のコマンドを実行して、クラウドの Azure Data Factory サービスにセルフホステッド統合ランタイムを登録するために使用される認証キーを取得します。 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   出力例を次に示します。

   ```json
   {
    "AuthKey1": "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
    "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    

5. 次の手順でマシンにインストールするセルフホステッド統合ランタイムを登録するために使用するいずれかのキーをコピーします (二重引用符は除外します)。  

## <a name="install-the-integration-runtime-tool"></a>統合ランタイム ツールをインストールする

1. 既にマシンに統合ランタイムがインストールされている場合は、 **[プログラムの追加と削除]** を使用してそれをアンインストールします。 

2. セルフホステッド統合ランタイムをローカルの Windows マシンに[ダウンロード](https://www.microsoft.com/download/details.aspx?id=39717)します。 インストールを実行します。

3. **[Welcome to Microsoft Integration Runtime Setup]\(Microsoft Integration Runtime セットアップへようこそ\)** ページで **[次へ]** を選択します。

4. **[使用許諾契約書]** ページで使用条件とライセンス契約に同意し、 **[次へ]** を選択します。

5. **[インストール先フォルダー]** ページで **[次へ]** を選択します。

6. **[Ready to install Microsoft Integration Runtime]\(Microsoft Integration Runtime のインストール準備完了\)** ページで **[インストール]** を選択します。

7. **[Completed the Microsoft Integration Runtime Setup]\(Microsoft Integration Runtime セットアップの完了\)** ページで **[完了]** を選択します。

8. **[統合ランタイム (セルフホステッド) の登録]** ページで、前のセクションで保存したキーを貼り付け、 **[登録]** を選択します。 

    ![統合ランタイムの登録](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)

9. **[新しい統合ランタイム (セルフホステッド) ノード]** ページで **[完了]** を選択します。 

10. セルフホステッド統合ランタイムが正常に登録されると、次のメッセージが表示されます。

    ![正常に登録](media/data-factory-create-install-integration-runtime/registered-successfully.png)

14. **[統合ランタイム (セルフホステッド) の登録]** ページで **[構成マネージャーの起動]** を選択します。

15. ノードがクラウド サービスに接続されると、次のページが表示されます。

    ![ノード接続済みページ](media/data-factory-create-install-integration-runtime/node-is-connected.png)

16. ここで、SQL Server データベースへの接続をテストします。

    ![[診断] タブ](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    a. **[構成マネージャー]** ページで、 **[診断]** タブに移動します。

    b. [データ ソースの種類] として **[SqlServer]** を選択します。

    c. サーバー名を入力します。

    d. データベース名を入力します。

    e. 認証モードを選択します。

    f. ユーザー名を入力します。

    g. ユーザー名に関連付けられているパスワードを入力します。

    h. 統合ランタイムから SQL Server に接続できることを確認するために、 **[テスト]** を選択します。 接続が成功すると、緑色のチェック マークが表示されます。 接続が失敗した場合は、エラー メッセージが表示されます。 問題を修正し、統合ランタイムから SQL Server に接続できるようにします。    

    > [!NOTE]
    > 認証の種類、サーバー、データベース、ユーザー、およびパスワードの値をメモしておきます。 このチュートリアルの後の方で使用します。
