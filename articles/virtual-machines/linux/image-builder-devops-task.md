---
title: Azure Image Builder サービスの DevOps タスク
description: アプリケーションと OS をインストールして構成できるように、ビルド成果物を VM イメージに挿入する Azure DevOps タスク。
author: danielsollondon
ms.author: danis
ms.date: 01/27/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: d02a5c6bc194009d459647721dab16be0dcade84
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670474"
---
# <a name="azure-image-builder-service-devops-task"></a>Azure Image Builder サービスの DevOps タスク

この記事では、アプリケーションと OS をインストールして構成できるように、Azure DevOps タスクを使用してビルド成果物を VM イメージに挿入する方法について説明します。

## <a name="devops-task-versions"></a>DevOps タスクのバージョン
Azure VM Image Builder (AIB) には、次の 2 つの DevOps タスクがあります。

* ["安定した" AIB タスク](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder)。これはテスト済みの最新の安定したビルドです。テレメトリには問題は示されていません。 


* ["不安定な" AIB タスク](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder-canary)。最新の更新プログラムと機能を取り入れ、お客様にそれらのテストを許可してから、"安定した" タスクに昇格させることができます。 問題が報告されず、テレメトリにも問題がない場合は、約 1 週間後にタスク コードを "安定" に昇格させます。 

## <a name="prerequisites"></a>前提条件

* [Visual Studio Marketplace から安定した DevOps タスク](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder)をインストールします。
* VSTS DevOps アカウントを取得し、ビルド パイプラインを作成しておく必要があります。
* パイプラインで使用されるサブスクリプションに Image Builder 機能の要件を登録して有効にします。
    * [Az PowerShell](../windows/image-builder-powershell.md#register-features)
    * [Az CLI](../windows/image-builder.md#register-the-features)
    
* ソース イメージのリソース グループに Standard Azure Storage アカウントを作成します。これで、他のリソース グループ/ストレージ アカウントを使用できます。 このストレージ アカウントは、ビルド成果物を DevOps タスクからイメージに転送するために使用されます。

    ```powerShell
    # Az PowerShell
    $timeInt=$(get-date -UFormat "%s")
    $storageAccName="aibstorage"+$timeInt
    $location=westus
    # create storage account and blob in resource group
    New-AzStorageAccount -ResourceGroupName $strResourceGroup -Name $storageAccName -Location $location -SkuName Standard_LRS
    ```

    ```bash
    # Az CLI
    location=westus
    scriptStorageAcc=aibstordot$(date +'%s')
    # create storage account and blob in resource group
    az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS
    ```

## <a name="add-task-to-release-pipeline"></a>リリース パイプラインへのタスクの追加

**[リリース パイプライン]**  >  **[編集]** の順に選択します。

ユーザー エージェントで、追加する *[+]* を選択してから、 **[Image Builder]** を検索します。 **[追加]** を選択します。

次のタスク プロパティを設定します。

### <a name="azure-subscription"></a>Azure サブスクリプション

Image Builder が実行されるサブスクリプションをドロップダウン メニューから選択します。 ソース イメージが配置されているサブスクリプションおよびそれらのイメージが配布されるサブスクリプションと同じものを使用します。 Image Builder の共同作成者にサブスクリプションまたはリソース グループへのアクセスを許可する必要があります。

### <a name="resource-group"></a>リソース グループ

一時的なイメージ テンプレート成果物が格納されるリソース グループを使用します。 テンプレート成果物の作成時に、追加の一時的な Image Builder リソース グループ `IT_<DestinationResourceGroup>_<TemplateName>_guid` が作成されます。 この一時的なリソース グループには、スクリプトなどのイメージ メタデータが格納されます。 タスクの最後に、イメージ テンプレート成果物と一時的な Image Builder リソース グループが削除されます。
 
### <a name="location"></a>場所

場所は、Image Builder が実行されるリージョンです。 設定された数の[リージョン](../image-builder-overview.md#regions)のみがサポートされます。 この場所にソース イメージが存在している必要があります。 たとえば、Shared Image Gallery を使用している場合は、そのリージョンにレプリカが存在している必要があります。

### <a name="managed-identity-required"></a>マネージド ID (必須)
Image Builder には マネージド ID が必要です。ソース カスタム イメージの読み取り、Azure Storage への接続、カスタム イメージの作成などにこれが使用されます。 詳細については、[こちら](../image-builder-overview.md#permissions)を参照してください。

### <a name="vnet-support"></a>VNET のサポート

現在、DevOps タスクでは既存のサブネットの指定はサポートされていません (今後サポートされる予定です) が、既存の VNET を利用する場合は、ARM テンプレートとその内部で入れ子になった Image Builder テンプレートを使用できます。この方法については、Windows の Image Builder テンプレートの例を参照してください。あるいは、[AZ AIB PowerShell](../windows/image-builder-powershell.md) を使用してください。

### <a name="source"></a>source

ソース イメージは、サポートされている Image Builder の OS のものである必要があります。 Image Builder が実行されているのと同じリージョン内の既存のカスタム イメージを選択できます。
* マネージド イメージ - resourceId を渡す必要があります。次に例を示します。
    ```json
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```
* Azure Shared Image Gallery - イメージ バージョンの resourceId を渡す必要があります。次に例を示します。
    ```json
    /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/<versionNumber>
    ```

    最新バージョンの Shared Image Gallery を取得する必要がある場合は、最新バージョンを取得する AZ PowerShell または AZ CLI タスクを先に実行してから、DevOps 変数を設定することができます。 その変数を AZ VM Image Builder の DevOps タスクで使用します。 詳細については、[例](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/8_Getting_Latest_SIG_Version_ResID#getting-the-latest-image-version-resourceid-from-shared-image-gallery)を参照してください。

* (Marketplace) 基本イメージ - よく使われるイメージのドロップダウン リストがあります。これらは、サポートされている OS の "最新" バージョンを常に使用します。 

    基本イメージがリストにない場合は、`Publisher:Offer:Sku` を使用して正確なイメージを指定できます。

    基本イメージのバージョン (省略可能) - 使用するイメージのバージョンを指定できます。既定値は `latest` です。

### <a name="customize"></a>カスタマイズ

#### <a name="provisioner"></a>プロビジョナー

最初に、**Shell** と **PowerShell** の 2 つのカスタマイザーがサポートされています。 インラインのみがサポートされています。 スクリプトをダウンロードする場合は、インライン コマンドを渡してこれを行うことができます。

お使いの OS に応じて、PowerShell または Shell を選択します。

#### <a name="windows-update-task"></a>Windows Update タスク

Windows のみの場合、このタスクではカスタマイズの最後に Windows Update が実行されます。 必要な再起動が行われます。

次の Windows Update の構成が実行されます。
```json
    "type": "WindowsUpdate",
    "searchCriteria": "IsInstalled=0",
    "filters": [
        "exclude:$_.Title -like '*Preview*'",
        "include:$true"
```
プレビュー版ではない重要かつ推奨される Windows 更新プログラムがインストールされます。

#### <a name="handling-reboots"></a>再起動の処理
現在、DevOps タスクでは Windows ビルドの再起動がサポートされていないため、PowerShell コードで再起動を試みると、このビルドは失敗します。 ただし、コードを使用して Linux ビルドを再起動することはできます。

#### <a name="build-path"></a>ビルド パス

このタスクは、DevOps ビルド リリースの成果物をイメージに挿入できるように設計されています。 これを機能させるには、ビルド パイプラインを設定する必要があります。 リリース パイプラインの設定で、ビルド成果物のリポジトリを追加する必要があります。

:::image type="content" source="./media/image-builder-devops-task/add-artifact.png" alt-text="リリース パイプラインでの [成果物の追加] の選択。":::

**[ビルドパス]** ボタンをクリックして、イメージに配置されるビルド フォルダーを選択します。 Image Builder タスクによって、その中のすべてのファイルとディレクトリがコピーされます。 イメージが作成されると、Image Builder によって、それらのファイルとディレクトリが OS に応じて異なるパスに配置されます。

> [!IMPORTANT]
> リポジトリの成果物を追加するときに、ディレクトリの先頭にアンダースコア *_* が付いていることに気付くことがあります。 このアンダースコアが原因で、インライン コマンドに関する問題が発生する可能性があります。 このコマンドでは適切な引用符を使用してください。
> 

次の例で、このしくみについて説明します。

:::image type="content" source="./media/image-builder-devops-task/build-artifacts.png" alt-text="階層を示すディレクトリ構造。":::


* Windows - ファイルは `C:\` にあります。 `webapp` ディレクトリが含まれた `buildArtifacts` という名前のディレクトリが作成されます。

* Linux - ファイルは `/tmp` にあります。 すべてのファイルとディレクトリが含まれた `webapp` ディレクトリが作成されます。 このディレクトリからファイルを移動する必要があります。 そうしなければ、一時ディレクトリに入っているため、それらは削除されます。

#### <a name="inline-customization-script"></a>インライン カスタマイズ スクリプト

* Windows - PowerShell のインライン コマンドはコンマで区切って入力できます。 ビルド ディレクトリ内のスクリプトを実行する場合は、次を使用できます。

    ```PowerShell
    & 'c:\buildArtifacts\webapp\webconfig.ps1'
    ```

   複数のスクリプトを参照することも、コマンドをさらに追加することもできます。次に例を示します。

    ```PowerShell
    & 'c:\buildArtifacts\webapp\webconfig.ps1'
    & 'c:\buildArtifacts\webapp\installAgent.ps1'
    ```
* Linux - Linux システムでは、ビルド成果物は `/tmp` ディレクトリに格納されます。 ただし、多くの Linux OS では、再起動時に /tmp ディレクトリの内容が削除されます。 成果物がイメージ内に存在するようにする場合は、別のディレクトリを作成してそれらをコピーする必要があります。  次に例を示します。

    ```bash
    sudo mkdir /lib/buildArtifacts
    sudo cp -r "/tmp/_ImageBuilding/webapp" /lib/buildArtifacts/.
    ```
    
    "/tmp" ディレクトリを使用しても問題ない場合は、以下のコードを使ってスクリプトを実行できます。
    
    ```bash
    # grant execute permissions to execute scripts
    sudo chmod +x "/tmp/_ImageBuilding/webapp/coreConfig.sh"
    echo "running script"
    sudo . "/tmp/AppsAndImageBuilderLinux/_WebApp/coreConfig.sh"
    ```
    
#### <a name="what-happens-to-the-build-artifacts-after-the-image-build"></a>イメージのビルド後にビルド成果物はどうなるか

> [!NOTE]
> Image Builder ではビルド成果物が自動的に削除されないため、ビルド成果物を削除するコードを常に用意しておくことを強くお勧めします。
> 

* Windows - Image builder によってファイルは `c:\buildArtifacts` ディレクトリに配置されます。 ディレクトリが保持されている場合は、削除する必要があります。 実行するスクリプト内でそれを削除できます。 次に例を示します。

    ```PowerShell
    # Clean up buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts\*" -Force -Recurse
    
    # Delete the buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts" -Force 
    ```
    
* Linux - ビルド成果物は `/tmp` ディレクトリに格納されます。 ただし、多くの Linux OS では、再起動時に `/tmp` ディレクトリの内容が削除されます。 内容を削除するコードを用意して、内容の削除を OS に頼らないようにすることをお勧めします。 次に例を示します。

    ```bash
    sudo rm -R "/tmp/AppsAndImageBuilderLinux"
    ```
    
#### <a name="total-length-of-image-build"></a>イメージのビルドの合計時間

DevOps パイプライン タスクでは合計時間をまだ変更できません。 既定値の 240 分を使用します。 [buildTimeoutInMinutes](./image-builder-json.md#properties-buildtimeoutinminutes) を増やす場合は、リリース パイプラインで AZ CLI タスクを使用できます。 テンプレートをコピーして送信するようにタスクを構成します。 例については、こちらの[ソリューション](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/4_Using_ENV_Variables#using-environment-variables-and-parameters-with-image-builder)を参照するか、Az PowerShell を使用してください。


#### <a name="storage-account"></a>ストレージ アカウント

前提条件で作成したストレージ アカウントを選択します。 一覧に表示されない場合は、それに対するアクセス許可が Image Builder にありません。

ビルドが開始されると、Image Builder によって `imagebuilder-vststask` と呼ばれるコンテナーが作成されます。 このコンテナーは、リポジトリのビルド成果物が格納される場所です。

> [!NOTE]
> ビルド後に毎回、ストレージ アカウントまたはコンテナーを手動で削除する必要があります。
>

### <a name="distribute"></a>配布

3 種類の配布がサポートされています。

#### <a name="managed-image"></a>管理イメージ

* ResourceID:
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```

* 場所

#### <a name="azure-shared-image-gallery"></a>Azure Shared Image Gallery

Shared Image Gallery は既に存在している **必要があります**。

* ResourceID: 
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>
    ```

* リージョン: コンマで区切られたリージョンの一覧。 たとえば、westus, eastus, centralus のようになります。

#### <a name="vhd"></a>VHD

これに値を渡すことはできません。Image Builder では、VHD を *vhds* コンテナー内の一時的な Image Builder リソース グループ `IT_<DestinationResourceGroup>_<TemplateName>` に出力します。 リリース ビルドを開始すると、Image Builder によってログが出力されます。 完了すると、VHD の URL が出力されます。

### <a name="optional-settings"></a>オプション設定

* [VM サイズ](image-builder-json.md#vmprofile) - VM サイズは、既定値の *Standard_D1_v2* からオーバーライドできます。 総カスタマイズ時間を短縮するために、または GPU/HPC などの特定の VM サイズに依存するイメージを作成する必要があるという理由で、オーバーライドする場合があります。

## <a name="how-it-works"></a>しくみ

リリースを作成すると、このタスクによってストレージ アカウントに *imagebuilder-vststask* という名前のコンテナーが作成されます。 ビルド成果物が圧縮されてアップロードされ、zip ファイルの SAS トークンが作成されます。

このタスクでは、タスクに渡されたプロパティを使用して、Image Builder テンプレートの成果物を作成します。 このタスクでは次の処理が実行されます。
* ビルド成果物の zip ファイルと関連するその他のスクリプトをダウンロードする。 これらのファイルは、一時的な Image Builder リソース グループ `IT_<DestinationResourceGroup>_<TemplateName>` のストレージ アカウントに保存されます。
* 先頭に *t_* と 10 桁の単調な整数が付いたテンプレートを作成する。 このテンプレートは、選択したリソース グループに保存されます。 テンプレートは、ビルドの期間中リソース グループ内に存在します。 

出力例:

```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
starting put template...
```

イメージのビルドが開始されると、実行状態がリリース ログに報告されます。

```text
starting run template...
```

イメージのビルドが完了すると、次のテキストのような出力が表示されます。

```text
2019-05-06T12:49:52.0558229Z starting run template...
2019-05-06T13:36:33.8863094Z run template:  Succeeded
2019-05-06T13:36:33.8867768Z getting runOutput for  SharedImage_distribute
2019-05-06T13:36:34.6652541Z ==============================================================================
2019-05-06T13:36:34.6652925Z ## task output variables ##
2019-05-06T13:36:34.6658728Z $(imageUri) =  /subscriptions/<subscriptionID>/resourceGroups/aibwinsig/providers/Microsoft.Compute/galleries/my22stSIG/images/winWAppimages/versions/0.23760.13763
2019-05-06T13:36:34.6659989Z ==============================================================================
2019-05-06T13:36:34.6663500Z deleting template t_1557146959485...
2019-05-06T13:36:34.6673713Z deleting storage blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip
2019-05-06T13:36:34.9786039Z blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip is deleted
2019-05-06T13:38:37.4884068Z delete template:  Succeeded
```

イメージ テンプレートと `IT_<DestinationResourceGroup>_<TemplateName>` が削除されます。

"$ (ImageUri)" VSTS 変数を取得して次のタスクで使用することも、単にその値を使用して VM をビルドすることもできます。

## <a name="output-devops-variables"></a>DevOps の出力変数

ソース Marketplace イメージのパブリッシャー/オファー/SKU/バージョン:
* $(pirPublisher)
* $(pirOffer)
* $(pirSku)
* $(pirVersion)

イメージの URI - 配布されたイメージの ResourceID:
* $(imageUri)

## <a name="faq"></a>よく寄せられる質問

### <a name="can-i-use-an-existing-image-template-i-have-already-created-outside-of-devops"></a>DevOps の外部で既に作成した既存のイメージ テンプレートを使用できますか?

現時点ではできません。

### <a name="can-i-specify-the-image-template-name"></a>イメージ テンプレート名を指定できますか?

いいえ。 一意のテンプレート名が使用され、その後削除されます。

### <a name="the-image-builder-failed-how-can-i-troubleshoot"></a>Image Builder でエラーが発生しました。 トラブルシューティングをどのように行えばよいですか?

ビルド エラーが発生した場合、DevOps タスクではステージング リソース グループを削除しません。 ビルド カスタマイズ ログが含まれているステージング リソース グループにアクセスできます。

VM Image Builder タスクの DevOps ログにエラーが表示されるので、customization.log の場所を参照してください。 次に例を示します。

:::image type="content" source="./media/image-builder-devops-task/devops-task-error.png" alt-text="失敗を示す DevOps タスク エラーの例。":::

トラブルシューティングの詳細については、[Azure Image Builder サービスのトラブルシューティング](image-builder-troubleshoot.md)に関する記事をご覧ください。 

失敗を調査した後は、ステージング リソース グループを削除できます。 まず、イメージ テンプレートのリソース成果物を削除します。 この成果物は、先頭に *t_* が付いており、DevOps タスクのビルド ログで確認できます。

```text
...
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
...
template name:  t_1556938436xxx
...

```

イメージ テンプレートのリソース成果物は、タスクで最初に指定されたリソース グループに含まれています。 トラブルシューティングが完了したら、この成果物を削除してください。 Azure portal を使用して削除する場合は、リソース グループ内で **[非表示の種類の表示]** を選択して成果物を表示します。


## <a name="next-steps"></a>次の手順

詳細については、[Azure Image Builder の概要](../image-builder-overview.md)に関する記事を参照してください。
