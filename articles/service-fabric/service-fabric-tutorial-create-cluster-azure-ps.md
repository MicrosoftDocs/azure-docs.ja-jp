---
title: "Azure に Service Fabric クラスターを作成する | Microsoft Docs"
description: "PowerShell を使用して Azure に Windows または Linux の Service Fabric クラスターを作成する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/13/2017
ms.author: ryanwi
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 80c5a2a43302e1cc8ec3b4298eb393a1861252d3
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---

# <a name="create-a-secure-cluster-on-azure-using-powershell"></a>PowerShell を使用して Azure にセキュリティで保護されたクラスターを作成する
このチュートリアルでは、Azure で実行される Service Fabric クラスター (Window または Linux) を作成する方法について説明します。 完了すると、クラウドで実行されているクラスターにアプリケーションをデプロイできるようになります。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * PowerShell を使用してセキュリティで保護された Service Fabric クラスターを Azure に作成する
> * X.509 証明書を持つクラスターを保護する
> * PowerShell を使用してクラスターに接続する
> * クラスターの削除

## <a name="prerequisites"></a>前提条件
このチュートリアルを開始する前に
- Azure サブスクリプションを持っていない場合は[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成する
- [Service Fabric SDK と PowerShell モジュール](service-fabric-get-started.md)をインストールする
- [Azure PowerShell モジュール Version 4.1 以上](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)をインストールする

次の手順では、単一ノード (単一の仮想マシン) プレビュー Service Fabric クラスターを作成します。 このクラスターは、クラスターと共に作成される自己署名証明書によって保護され、キー コンテナーに配置されます。 単一ノード クラスターは 1 台の仮想マシンを超えて拡張することはできず、Preview クラスターを新しいバージョンにアップグレードすることはできません。

Azure で Service Fabric クラスターを実行することによって発生するコストを計算するには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用します。
Service Fabric クラスターを作成する方法について詳しくは、「[Azure Resource Manager を使用して Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-arm.md)」をご覧ください。

## <a name="create-the-cluster-using-azure-powershell"></a>Azure PowerShell を使用してクラスターを作成する
1. [Service Fabric 用の Azure Resource Manager](https://aka.ms/securepreviewonelineclustertemplate) GitHub リポジトリから Azure Resource Manager テンプレートとパラメーター ファイルのローカル コピーをダウンロードします。  *azuredeploy.json* は、Service Fabric クラスターを定義する Azure Resource Manager テンプレートです。 *azuredeploy.parameters.json* はクラスター デプロイをカスタマイズするためのパラメーター ファイルです。

2. *azuredeploy.parameters.json* パラメーター ファイルで次のパラメーターをカスタマイズします。

   | パラメーター       | Description | 推奨値 |
   | --------------- | ----------- | --------------- |
   | clusterLocation | クラスターのデプロイ先の Azure リージョン。 | *westeurope、eastasia、eastus など* |
   | clusterName     | 作成するクラスターの名前。 | *bobs-sfpreviewcluster など* |
   | adminUserName   | クラスター仮想マシンのローカル管理者アカウントです。 | *任意の有効な Windows Server ユーザー名* |
   | adminPassword   | クラスター仮想マシンのローカル管理者アカウントのパスワードです。 | *任意の有効な Windows Server パスワード* |
   | clusterCodeVersion | 実行する Service Fabric バージョン (255.255.X.255 はプレビュー バージョンです)。 | **255.255.5718.255** |
   | vmInstanceCount | クラスター内の仮想マシンの数 (1 または 3 ～ 99 で指定できます)。 | **1** | *プレビュー クラスターの場合、1 つの仮想マシンのみを指定します* |

3. PowerShell コンソールを開き、Azure にログインし、クラスターをデプロイするサブスクリプションを選択します。

   ```powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```
4. Service Fabric によって使用される証明書のパスワードを作成し、暗号化します。

   ```powershell
   $pwd = "<your password>" | ConvertTo-SecureString -AsPlainText -Force
   ```
5. 次のコマンドを実行して、クラスターとその証明書を作成します。

   ```powershell
      New-AzureRmServiceFabricCluster `
          -TemplateFile C:\Users\me\Desktop\azuredeploy.json `
          -ParameterFile C:\Users\me\Desktop\azuredeploy.parameters.json `
          -CertificateOutputFolder C:\Users\me\Desktop\ `
          -CertificatePassword $pwd `
          -CertificateSubjectName "mycluster.westeurope.cloudapp.azure.com" `
          -ResourceGroupName myclusterRG
   ```

   >[!NOTE]
   >`-CertificateSubjectName` パラメーターは、パラメーター ファイルで指定される clusterName パラメーターおよび、選択した Azure リージョンに紐付けられているドメインと一致する必要があります (例: `clustername.eastus.cloudapp.azure.com`)。

構成が完了すると、Azure に作成されたクラスターに関する情報が出力されます。 また、このパラメーターに指定されているパスの -CertificateOutp ディレクトリにもクラスター証明書がコピーされます。 Service Fabric Explorer にアクセスし、クラスターの正常性を表示するには、この証明書が必要です。

クラスターの URL をメモします。これは *https://mycluster.westeurope.cloudapp.azure.com:19080* のような URL です。

## <a name="modify-the-certificate--access-service-fabric-explorer"></a>証明書の変更と Service Fabric Explorer へのアクセス ##

1. 証明書をダブルクリックして証明書インポート ウィザードを開きます。

2. 既定の設定を使用しますが、**[このキーをエクスポート可能にする]** チェック ボックス (**[秘密キーの保護]** 手順で) を必ずオンにしてください。 Visual Studio では、このチュートリアルの後半で、Azure Container Registry の構成時に、Service Fabric クラスター認証のために証明書をエクスポートする必要があります。

3. ブラウザーで Service Fabric Explorer を開けるようになりました。 Service Fabric Explorer を開くには、Web ブラウザーを使用してクラスターの **ManagementEndpoint** URL にアクセスし、コンピューターに保存した証明書を選択します。

>[!NOTE]
>自己署名証明書を使用しているため、Service Fabric Explorer を開くときに、証明書エラーが表示されます。 Edge では、*[詳細]* をクリックし、*[Web ページへ移動]* リンクをクリックする必要があります。 Chrome では、*[詳細設定]* をクリックし、*[続行]* リンクをクリックする必要があります。

>[!NOTE]
>クラスターの作成に失敗した場合、既にデプロイされているリソースを更新するコマンドをいつでも再実行できます。 証明書が失敗したデプロイの一部として作成された場合、新しい証明書が生成されます。 クラスターの作成のトラブルシューティングを行うには、「[Azure Resource Manager を使用して Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-arm.md)」をご覧ください。

## <a name="connect-to-the-secure-cluster"></a>セキュリティで保護されたクラスターに接続する
Service Fabric SDK とともにインストールされた Service Fabric PowerShell モジュールを使用してクラスターに接続します。  まず、この証明書を現在のユーザーのコンピューターの個人用 (マイ) ストアにインストールする必要があります。  次の PowerShell コマンドを実行します。

```powershell
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

これで、セキュリティで保護されたクラスターに接続する準備ができました。

**Service Fabric** PowerShell モジュールには、Service Fabric のクラスター、アプリケーション、およびサービスを管理するための多くのコマンドレットが用意されています。  セキュリティで保護されたクラスターに接続するには、[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) コマンドレットを使用します。 証明書の拇印と接続エンドポイントの詳細は、前の手順の出力で確認できます。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

接続とクラスターの正常性を確認するには、[Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) コマンドレットを使用します。

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

クラスターは、クラスター リソース自体に加え、その他の Azure リソースで構成されます。 クラスターと、そのクラスターによって使用されるすべてのリソースを削除するための最も簡単な方法は、リソース グループを削除することです。

Azure にログインして、クラスターを削除するサブスクリプション ID を選択します。  サブスクリプション ID は、[Azure Portal](http://portal.azure.com) にログインして確認できます。 リソース グループとそのグループのクラスター リソースすべてを削除するには、[Remove-AzureRMResourceGroup cmdlet](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup) コマンドレットを使用します。

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId "Subcription ID"

$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="next-steps"></a>次のステップ
このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure に Service Fabric クラスターを作成する
> * X.509 証明書を持つクラスターを保護する
> * PowerShell を使用してクラスターに接続する
> * クラスターの削除

次のチュートリアルでは、既存のアプリケーションをデプロイする方法について説明します。
> [!div class="nextstepaction"]
> [Docker Compose を使用して既存の .NET アプリケーションをデプロイする](service-fabric-host-app-in-a-container.md)

