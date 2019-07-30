---
title: Chef による Azure 仮想マシンの展開 | Microsoft Docs
description: Chef を使用して自動化された仮想マシンの展開と構成を Microsoft Azure で実行する方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: diegoviso
manager: gwallace
tags: azure-service-management,azure-resource-manager
editor: ''
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 07/09/2019
ms.author: diviso
ms.openlocfilehash: 74b92c277b1d6eaa0984e55a70459bad59c2bf84
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719280"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Chef で Azure 仮想マシンの展開を自動化する

Chef は自動化と必要な状態の構成を提供する優れたツールです。

Chef は最新のクラウド API リリースで Azure とのシームレスな統合を提供しており、1 つのコマンドで構成状態をプロビジョニングしてデプロイできます。

この記事では、Chef 環境を設定し、Azure 仮想マシンをプロビジョニングした後、ポリシーまたは "Cookbook" を作成して、それを Azure 仮想マシンに展開します。

## <a name="chef-basics"></a>Chef の基礎
開始する前に、[Chef の基本的な概念を確認します](https://www.chef.io/chef)。

次の図は、大まかな Chef アーキテクチャを示しています。

![][2]

Chef には、Chef サーバー、Chef クライアント (ノード)、および Chef ワークステーションという 3 つの主要なアーキテクチャ コンポーネントがあります。

Chef サーバーは管理ポイントとなります。Chef サーバーにはホスト型ソリューションとオンプレミスのソリューションの 2 つのオプションがあります。

Chef クライアント (ノード) は管理対象のサーバーに置かれるエージェントです。

Chef ワークステーションは、ポリシーを作成したり、管理コマンドを実行したりする管理者用のワークステーションと Chef ツールのソフトウェア パッケージの両方を指す名前です。

通常、_ワークステーション_ はアクションを実行する場所、_Chef ワークステーション_ はソフトウェア パッケージ用と見なされます。
たとえば、_Chef ワークステーション_ の一部として knife コマンドをダウンロードしますが、インフラストラクチャを管理するため、_ワークステーション_ から knife コマンドを実行します。

また Chef は、実際に定義して、サーバーに適用するポリシーとなる "Cookbooks" と "Recipes" の概念を使用します。

## <a name="preparing-your-workstation"></a>ワークステーションを準備する

まず、Chef 構成ファイルと Cookbook を保存するディレクトリを作成して、ワークステーションを準備します。

C:\Chef というディレクトリを作成します。

お使いのワークステーション上に、最新バージョンの [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) をダウンロードしてインストールします。

## <a name="configure-azure-service-principal"></a>Azure サービス プリンシパルを構成する

最も簡単に言えば、Azure サービス プリンシパルとは 1 つのサービス アカウントです。   ここでは、サービス プリンシパルを使用して、Chef Workstation から Azure リソースを作成します。  必要なアクセス許可を利用して関連するサービス プリンシパルを作成するには、PowerShell 内で次のコマンドを実行する必要があります。
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

SubscriptionID、TenantID、ClientID、およびクライアント シークレット (上記で設定したパスワード) をメモしておいてください。後で必要になります。 

## <a name="setup-chef-server"></a>Chef サーバーの設定

このガイドでは、Hosted Chef にサインアップしていることを前提としています。

Chef サーバーを使用していない場合は、次の手順を実行できます。

* [Hosted Chef](https://manage.chef.io/signup) をサインアップします。これは、Chef を開始する最も簡単な方法です。
* [Chef ドキュメント](https://docs.chef.io/)の[インストール手順](https://docs.chef.io/install_server.html)に従って、Linux ベースのマシンにスタンドアロン Chef サーバーをインストールします。

### <a name="creating-a-hosted-chef-account"></a>Hosted Chef アカウントの作成

ホストされる Chef アカウントに[ここ](https://manage.chef.io/signup)からサインアップします。

サインアップ プロセス中、新しい組織を作成するように尋ねられます。

![][3]

組織を作成したら、スターター キットをダウンロードします。

![][4]

> [!NOTE]
> キーがリセットされるという警告が表示されても、構成済みのインフラストラクチャがまだないため、そのまま進んでかまいません。
>

このスターター キットの zip ファイルでは、`.chef` ディレクトリに組織の構成ファイルとユーザー キーが含まれています。

`organization-validator.pem` は秘密キーであり、秘密キーは Chef サーバーに保存するべきではないため、個別にダウンロードする必要があります。 [Chef Manage](https://manage.chef.io/) から、[管理] セクションに移動して、[Reset Validation Key]\(検証キーのリセット\) を選択します。これで、個別にダウンロードするためのファイルが提供されます。 ファイルを c:\chef に保存します。

### <a name="configuring-your-chef-workstation"></a>Chef ワークステーションの構成

chef-starter.zip を c:\chef に展開します。

chef-starter\chef-repo\.chef にあるすべてのファイルを c:\chef ディレクトリにコピーします。

c:\Downloads に保存されている場合、`organization-validator.pem` ファイルを c:\chef にコピーします。

これでディレクトリは次の例のようになります。

```powershell
    Directory: C:\Users\username\chef

Mode           LastWriteTime    Length Name
----           -------------    ------ ----
d-----    12/6/2018   6:41 PM           .chef
d-----    12/6/2018   5:40 PM           chef-repo
d-----    12/6/2018   5:38 PM           cookbooks
d-----    12/6/2018   5:38 PM           roles
-a----    12/6/2018   5:38 PM       495 .gitignore
-a----    12/6/2018   5:37 PM      1678 azuredocs-validator.pem
-a----    12/6/2018   5:38 PM      1674 user.pem
-a----    12/6/2018   5:53 PM       414 knife.rb
-a----    12/6/2018   5:38 PM      2341 README.md
```

c:\chef のルートには、5 つのファイルと 4 つのディレクトリがあるはずです (空の chef-repo ディレクトリを含む)。

### <a name="edit-kniferb"></a>knife.rb の編集

PEM ファイルには組織と、通信用の管理者の秘密キーが含まれ、knife.rb ファイルには knife 構成が含まれています。 knife.rb ファイルを編集する必要があります。

適当なエディターで knife.rb ファイルを開きます。 変更されていないファイルは次のようになります。

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

knife.rb に以下の情報を追加します。

validation_client_name   "myorg-validator"

validation_key           "#{current_dir}/myorg.pem"

knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"

knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"

knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"

knife[:azure_client_secret] =     "#1234p$wdchef19"


これらの行によって、Knife では c:\chef\cookbooks 下の cookbook ディレクトリを参照し、また、Azure 操作の中で作成した Azure サービス プリンシパルが使用されることが保証されます。

これで、knife.rb ファイルは次の例のようになります。

![][14]

<!--- Giant problem with this section: Chef 12 uses a config.rb instead of knife.rb
// However, the starter kit hasn't been updated
// So, I don't think this will even work on the modern Chef -->

<!--- update image [6] knife.rb -->

```rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "myorg"
client_key               "#{current_dir}/myorg.pem"
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg-validator.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
cookbook_path            ["#{current_dir}/../cookbooks"]
knife[:azure_tenant_id] = "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] = "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] = "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] = "#1234p$wdchef19"
```

## <a name="install-chef-workstation"></a>Chef ワークステーションのインストール

次に、Chef ワークステーションを[ダウンロードして、インストール](https://downloads.chef.io/chef-workstation/)します。
既定の場所に Chef ワークステーションをインストールします。 このインストールには数分かかる場合があります。

デスクトップに "CW PowerShell" が表示されます。これは、Chef 製品とやり取りするのに必要となるツールと一緒に読み込まれる環境です。 CW PowerShell により、`chef` などの従来の Chef CLI コマンドと共に、`chef-run` などの新しいアドホック コマンドが使用できるようになります。 `chef -v` を使用して、Chef ワークステーションと Chef ツールのインストールされているバージョンを確認します。 Chef ワークステーション アプリで [About Chef Workstation]\(Chef ワークステーションについて\) を選択して、ワークステーションのバージョンを確認することもできます。

`chef --version` は、次のように返します。

```
Chef Workstation: 0.4.2
  chef-run: 0.3.0
  chef-client: 15.0.300
  delivery-cli: 0.0.52 (9d07501a3b347cc687c902319d23dc32dd5fa621)
  berks: 7.0.8
  test-kitchen: 2.2.5
  inspec: 4.3.2
```

> [!NOTE]
> パスの順序が重要です。 opscode パスの順序が正しくない場合は、問題が生じます。
>

続行する前にワークステーションを再起動します。

### <a name="install-knife-azure"></a>Knife Azure のインストール

このチュートリアルでは、Azure Resource Manager を使用して、仮想マシンとやり取りすることを想定しています。

Knife Azure 拡張機能をインストールします。 これで Knife に「Azure プラグイン」が提供されます。

次のコマンドを実行します。

    chef gem install knife-azure ––pre

> [!NOTE]
> –pre 引数は、最新の API セットへのアクセスを提供する Knife Azure プラグインの最新 RC バージョンを、確実に受け取るようにします。
>
>

さまざまな依存関係も同時にインストールされる場合があります。

![][8]

すべてが正しく構成されたことを確認するには、次のコマンドを実行します。

    knife azurerm server list

すべてが正しく構成されていると、利用できる Azure イメージの一覧がスクロール表示されます。

おめでとうございます。 これで、ワークステーションがセットアップされました。

## <a name="creating-a-cookbook"></a>Cookbook の作成

Cookbookは管理するクライアント上で実行する一連のコマンドを定義するために Chef で使用します。 Cookbook は簡単に作成でき、**chef generate cookbook** コマンドを使用して Cookbook のテンプレートを生成します。 この cookbook は、IIS を自動的にデプロイさせる Web サーバー用です。

C:\Chef ディレクトリで次のコマンドを実行します。

    chef generate cookbook webserver

このコマンドによって、ディレクトリ C:\Chef\cookbooks\webserver に一連のファイルが生成されます。 次に、管理対象の仮想マシンで Chef クライアントに実行させる一連のコマンドを定義します。

コマンドはファイル default.rb に保存されています。 このファイルでは、IIS をインストールして、起動し、wwwroot フォルダーにテンプレートをコピーする一連のコマンドを定義します。

C:\chef\cookbooks\webserver\recipes\default.rb ファイルを変更し、次の行を追加します。

    powershell_script 'Install IIS' do
         action :run
         code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
         action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
         source 'Default.htm.erb'
         rights :read, 'Everyone'
    end

完了したらファイルを保存します。

## <a name="creating-a-template"></a>テンプレートの作成
この手順では、default.html ページとして使用するテンプレート ファイルを生成します。

次のコマンドを実行してテンプレートを生成します。

    chef generate template webserver Default.htm

`C:\chef\cookbooks\webserver\templates\default\Default.htm.erb` ファイルに移動します。 シンプルな「Hello World」 HTML コードを追加して、ファイルを編集した後、ファイルを保存します。

## <a name="upload-the-cookbook-to-the-chef-server"></a>Chef サーバーに Cookbook をアップロードする
このステップでは、ローカル コンピューターに作成した Cookbook のコピーを、Chef がホストするサーバーにアップロードします。 アップロードが完了すると、Cookbook が **[ポリシー]** タブに表示されます。

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Knife Azure で仮想マシンをデプロイする
Azure 仮想マシンをデプロイして、IIS Web サービスと既定のWeb ページをインストールする "Webserver" Cookbook を適用します。

これを実行するには **knife azurerm server create** コマンドを使用します。

コマンドの例を次に示します。

    knife azurerm server create `
    --azure-resource-group-name rg-chefdeployment `
    --azure-storage-account store `
    --azure-vm-name chefvm `
    --azure-vm-size 'Standard_DS2_v2' `
    --azure-service-location 'westus' `
    --azure-image-reference-offer 'WindowsServer' `
    --azure-image-reference-publisher 'MicrosoftWindowsServer' `
    --azure-image-reference-sku '2016-Datacenter' `
    --azure-image-reference-version 'latest' `
    -x myuser -P myPassword123 `
    --tcp-endpoints '80,3389' `
    --chef-daemon-interval 1 `
    -r "recipe[webserver]"


上記の例では、米国西部リージョン内に Windows Server 2016 がインストールされている Standard_DS2_v2 仮想マシンを作成します。 特定の変数に置き換えて実行してください。

> [!NOTE]
> このコマンド ラインでは –tcp-endpoints パラメーターを使用し、エンドポイント ネットワーク フィルター ルールも自動化しています。 ここでは、ポート 80 と 3389 を開き、Web ページと RDP セッションにアクセスできるようにしました。
>
>

コマンドを実行したら、Azure portal に移動し、ご使用のマシンでプロビジョニングが開始されることを確認します。

![][15]

次にコマンド プロンプトが表示されます。

![][16]

デプロイが完了すると、新しい仮想マシンのパブリック IP アドレスが完了したデプロイに表示されます。これをコピーして Web ブラウザーに貼り付けて、デプロイした Web サイトを閲覧できます。 仮想マシンをデプロイしたときに、ポート 80 を開いたので、外部で利用可能になっているはずです。   

![][11]

この例では、クリエイティブ HTML コードを使用します。

また、[Chef Manage](https://manage.chef.io/) 上でノードのステータスも閲覧できます。 

![][17]

また、ポート 3389 を経由して Azure portal から RDP セッションを通じて接続できます。

よろしくお願いいたします。 今すぐ Azure でコードを工夫したインフラストラクチャを始めてください。

<!--Image references-->
[2]: media/chef-automation/2.png
[3]: media/chef-automation/3.png
[4]: media/chef-automation/4.png
[5]: media/chef-automation/5.png
[6]: media/chef-automation/6.png
[7]: media/chef-automation/7.png
[8]: media/chef-automation/8.png
[9]: media/chef-automation/9.png
[10]: media/chef-automation/10.png
[11]: media/chef-automation/11.png
[13]: media/chef-automation/13.png
[14]: media/chef-automation/14.png
[15]: media/chef-automation/15.png
[16]: media/chef-automation/16.png
[17]: media/chef-automation/17.png


<!--Link references-->
