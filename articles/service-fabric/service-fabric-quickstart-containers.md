---
title: Azure で Service Fabric 上に Windows コンテナー アプリを作成する | Microsoft Docs
description: このチュートリアルでは、Azure Service Fabric で初めての Windows コンテナー アプリケーションを作成します。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: jpconnock
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2019
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: ebc3d988afd9257bbdf045814877ab70012836aa
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68225114"
---
# <a name="quickstart-deploy-windows-containers-to-service-fabric"></a>クイック スタート:Service Fabric に Windows コンテナーをデプロイする

Azure Service Fabric は、スケーラブルで信頼性に優れたマイクロサービスとコンテナーのデプロイと管理を行うための分散システム プラットフォームです。

既存のアプリケーションを Service Fabric クラスター上の Windows コンテナー内で実行する場合は、アプリケーションに変更を加える必要はありません。 このクイックスタートでは、あらかじめ用意されている Docker コンテナー イメージを Service Fabric アプリケーションにデプロイする方法を紹介します。 最後まで読み進めていけば、Windows Server Core 2016 Server と IIS コンテナーを稼働状態にすることができます。 このクイックスタートでは、Windows コンテナーのデプロイについて説明します。 Linux コンテナーをデプロイするには、[こちらのクイックスタート](service-fabric-quickstart-containers-linux.md)を確認してください。

![IIS の既定の Web ページ][iis-default]

このクイック スタートでは、次の方法について説明します。

* Docker イメージ コンテナーをパッケージ化する
* 通信を構成する
* Service Fabric アプリケーションのビルドとパッケージ化
* コンテナー アプリケーションを Azure にデプロイする


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション ([無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成できます)。
* 次のものを実行している開発コンピューター。
  * Visual Studio 2019 または Windows 2019。
  * [Service Fabric SDK およびツール](service-fabric-get-started.md)。

## <a name="package-a-docker-image-container-with-visual-studio"></a>Visual Studio で Docker イメージ コンテナーをパッケージ化する

Service Fabric SDK およびツールには、コンテナーを Service Fabric クラスターにデプロイするときに役立つサービスのテンプレートが用意されています。

"管理者" として Visual Studio を起動します。  **[ファイル]**  >  **[新規作成]**  >  **[プロジェクト]** の順に選択します。

**[Service Fabric アプリケーション]** を選択し、"MyFirstContainer" という名前を付けて、 **[作成]** をクリックします。

**[ホスト コンテナーとアプリケーション]** テンプレートから **[コンテナー]** を選択します。

**[イメージ名]** に「mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016」と入力します。これが [Windows Server Core Server と IIS の基本イメージ](https://hub.docker.com/_/microsoft-windows-servercore-iis)になります。

ポート 80 で受信するサービスへの要求が、コンテナーのポート 80 にマッピングされるように、コンテナーとホストとの間のポート マッピングを構成します。  **[Container Port] (コンテナー ポート)** を "80" に設定し、 **[ホスト ポート]** を "80" に設定します。  

サービスに "MyContainerService" という名前を付けて **[OK]** をクリックします。

![[New service] (新しいサービス) ダイアログ][new-service]

## <a name="specify-the-os-build-for-your-container-image"></a>コンテナー イメージの OS ビルドを指定する

特定のバージョンの Windows Server で構築されたコンテナーは、異なるバージョンの Windows Server を実行しているホスト上では動作しない可能性があります。 たとえば、Windows Server バージョン 1709 を使用して構築されたコンテナーは、Windows Server 2016 を実行しているホスト上では動作しません。 詳細については、[Windows Server コンテナーの OS とホスト OS の互換性](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility)に関するページを参照してください。 

Service Fabric ランタイム バージョン 6.1 以降では、コンテナーごとに複数の OS イメージを指定し、それぞれに展開する必要がある OS のビルド バージョンでタグ付けすることができます。 これにより、異なるバージョンの Windows OS を実行しているホスト全体でアプリを実行できるようになります。 詳細については、「[OS ビルド固有のコンテナー イメージを指定する](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)」を参照してください。 

Microsoft は、異なるバージョンの Windows Server 上に構築された IIS のバージョンごとに異なるイメージを公開しています。 Service Fabric で、アプリを展開するクラスター ノード上で実行されている Windows Server のバージョンと互換性のあるコンテナーが展開されるようにするには、*ApplicationManifest.xml* ファイルに以下の行を追加します。 Windows Server 2016 のビルド バージョンは 14393 であり、Windows Server バージョン 1709 のビルド バージョンは 16299 です。

```xml
    <ContainerHostPolicies CodePackageRef="Code"> 
      <ImageOverrides> 
        ...
          <Image Name="mcr.microsoft.com/windows/servercore/iis:windowsservercore-1803" /> 
          <Image Name= "mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016" Os="14393" /> 
          <Image Name="mcr.microsoft.com/windows/servercore/iis:windowsservercore-1709" Os="16299" /> 
      </ImageOverrides> 
    </ContainerHostPolicies> 
```

サービス マニフェストは、引き続きナノサーバーの 1 つのイメージ `mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016` のみを指定します。

また、*ApplicationManifest.xml* ファイルで、**PasswordEncrypted** を **false** に変更します。 Docker Hub にあるパブリック コンテナー イメージのアカウントとパスワードは空白です。空白のパスワードを暗号化するとビルド エラーが発生するため、暗号化を無効にします。

```xml
<RepositoryCredentials AccountName="" Password="" PasswordEncrypted="false" />
```

## <a name="create-a-cluster"></a>クラスターの作成

次のサンプル スクリプトでは、X.509 証明書で保護される 5 つのノードの Service Fabric クラスターを作成します。 コマンドで自己署名証明書を作成し、それを新しい Key Vault にアップロードします。 証明書はローカル ディレクトリにもコピーされます。 このスクリプトを使用したクラスターの作成の詳細については、「[Service Fabric クラスターの作成](scripts/service-fabric-powershell-create-secure-cluster-cert.md)」を参照してください。

必要に応じて、[Azure PowerShell ガイド](/powershell/azure/overview)の手順に従って Azure PowerShell をインストールします。

次のスクリプトを実行する前に、PowerShell で `Connect-AzAccount` を実行して Azure との接続を作成します。

次のスクリプトをクリップボードにコピーして、**Windows PowerShell ISE** を開きます。  内容を空の Untitled1.ps1 ウィンドウに貼り付けます。 次に、スクリプト内の変数 `subscriptionId`、`certpwd`、`certfolder`、`adminuser`、`adminpwd` などに値を指定します。  スクリプトを実行する前に、`certfolder` に指定したディレクトリが存在している必要があります。

[!code-powershell[main](../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

変数に実際の値を指定したら、**F5** キーを押してスクリプトを実行します。

スクリプトを実行してクラスターが作成されたら、出力で `ClusterEndpoint` を検索します。 例:

```powershell
...
ClusterEndpoint : https://southcentralus.servicefabric.azure.com/runtime/clusters/b76e757d-0b97-4037-a184-9046a7c818c0
```

### <a name="install-the-certificate-for-the-cluster"></a>クラスターの証明書をインストールする

ここで、*CurrentUser\My* 証明書ストアに PFX をインストールします。 PFX ファイルは、上記の PowerShell スクリプトで `certfolder` 環境変数を使用して指定したディレクトリにあります。

そのディレクトリに移動してから、次の PowerShell コマンドを実行します。`certfolder` ディレクトリにある PFX ファイルの名前と、`certpwd` 変数に指定したパスワードは置き換えます。 この例では、現在のディレクトリは、PowerShell スクリプトの `certfolder` 変数で指定されたディレクトリに設定されています。 そこから、`Import-PfxCertificate`コマンドが実行されます。

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\mysfclustergroup20190130193456.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString Password#1234 -AsPlainText -Force)
```

このコマンドからは、拇印が返されます。

```powershell
  ...
  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
0AC30A2FA770BEF566226CFCF75A6515D73FC686  CN=mysfcluster.SouthCentralUS.cloudapp.azure.com
```

後の手順のために、拇印の値を覚えておいてください。

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>Visual Studio を使用してアプリケーションを Azure にデプロイする

これでアプリケーションの準備ができたので、Visual Studio から直接クラスターにデプロイできます。

ソリューション エクスプローラーで **[MyFirstContainer]** を右クリックして、 **[発行]** を選択します。 [発行] ダイアログが表示されます。

前に `Import-PfxCertificate` コマンドを実行したときの PowerShell ウィンドウで **CN=** に続く内容をコピーし、それにポート `19000` を追加します。 たとえば、「 `mysfcluster.SouthCentralUS.cloudapp.azure.com:19000` 」のように入力します。 それを **[接続のエンドポイント]** フィールドにコピーします。 この値は、後の手順で必要になるため、覚えておいてください。

**[詳細な接続パラメーター]** をクリックし、接続パラメーターの情報を確認します。  *FindValue* と *ServerCertThumbprint* の値は、前の手順で `Import-PfxCertificate` を実行したときにインストールされた証明書の拇印に一致する必要があります。

![[発行] ダイアログ](./media/service-fabric-quickstart-containers/publish-app.png)

**[発行]** をクリックします。

クラスター内の各アプリケーションには、一意の名前が必要です。 名前の競合が発生している場合は、Visual Studio プロジェクトの名前を変更し、もう一度デプロイします。

ブラウザーを開き、前の手順で **[接続のエンドポイント]** フィールドに入力したアドレスに移動します。 URL には、必要に応じて先頭にスキーム ID (`http://`) を、末尾にポート (`:80`) を追加してください。 たとえば、http:\//mysfcluster.SouthCentralUS.cloudapp.azure.com:80 のようになります。

 IIS の既定の Web ページが表示されます。![IIS の既定の Web ページ][iis-default]

## <a name="clean-up"></a>クリーンアップ

クラスターの実行中は、料金が継続的に発生します。 [クラスターの削除](service-fabric-cluster-delete.md)を検討してください。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、次の方法について説明しました。

* Docker イメージ コンテナーをパッケージ化する
* 通信を構成する
* Service Fabric アプリケーションのビルドとパッケージ化
* コンテナー アプリケーションを Azure にデプロイする

Service Fabric で Windows コンテナーを操作する方法の詳細については、Windows コンテナー アプリのチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Windows コンテナー アプリの作成](./service-fabric-host-app-in-a-container.md)

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
[new-service]: ./media/service-fabric-quickstart-containers/NewService.png
