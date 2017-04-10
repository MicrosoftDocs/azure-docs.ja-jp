---
title: "Chef による Azure 仮想マシンのデプロイ | Microsoft Docs"
description: "Chef を使用して自動化された仮想マシンのデプロイと構成を Microsoft Azure で実行する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: diegoviso
manager: timlt
tags: azure-service-management,azure-resource-manager
editor: 
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 05/19/2015
ms.author: diviso
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 38f58c0f025b51e3764c978d28acdf5abe21f255


---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Chef で Azure 仮想マシンのデプロイメントを自動化する
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Chef は自動化と必要な状態の構成を提供する優れたツールです。

Chef は最新の cloud-api リリースで Azure とのシームレスな統合を提供しており、1 つのコマンドで構成状態をプロビジョニングしてデプロイできます。

この記事では、Chef 環境を設定し、Azure 仮想マシンをプロビジョニングする方法と、ポリシーまたは「CookBook」を作成し、それを Azure 仮想マシンに導入する方法について説明します。

早速始めましょう。

## <a name="chef-basics"></a>Chef の基礎
開始する前に Chef の基本的な概念を確認しておくことをお勧めします。  <a href="http://www.chef.io/chef" target="_blank">ここ</a> に優れた資料があるので、このチュートリアルを開始する前に一読することをお勧めします。 基礎については開始する前に要約します。

次の図は、大まかな Chef アーキテクチャを示しています。

![][2]

Chef には、Chef サーバー、Chef クライアント (ノード)、および Chef ワークステーションという 3 つの主要なアーキテクチャ コンポーネントがあります。

Chef サーバーは管理ポイントとなります。Chef サーバーにはホスト型ソリューションとオンプレミスのソリューションの 2 つのオプションがあります。 ここではホスト型ソリューションを使用します。

Chef クライアント (ノード) は管理対象のサーバーに置かれるエージェントです。

Chef ワークステーションは管理者用のワークステーションで、ポリシーを作成したり、管理コマンドを実行します。 Chef ワークステーションから **knife** コマンドを実行してインフラストラクチャを管理します。

また「Cookbook」と「Recipe」の概念もあります。 これらの概念が、実際に定義して、サーバーに適用するポリシーになります。

## <a name="preparing-the-workstation"></a>ワークステーションを準備する
まずはワークステーションを準備します。 ここでは、標準の Windows ワークステーションを使用します。 構成ファイルと Cookbook を保存するディレクトリを作成する必要があります。

最初に C:\chef というディレクトリを作成します。

次に 2 個目のディレクトリ c:\chef\cookbooks を作成します。

Azure 設定ファイルをダウンロードして Chef が Azure のサブスクリプションと通信できるようにする必要があります。

パブリッシュ設定は、 [ここ](https://manage.windowsazure.com/publishsettings/)からダウンロードしてください。

発行設定ファイルを C:\chef に保存します。

## <a name="creating-a-managed-chef-account"></a>管理された Chef アカウントの作成
ホストされる Chef アカウントに [ここ](https://manage.chef.io/signup)からサインアップします。

サインアップ プロセス中、新しい組織を作成するように尋ねられます。

![][3]

組織を作成したら、スターター キットをダウンロードします。

![][4]

> [!NOTE]
> キーがリセットされるという警告が表示されても、構成済みのインフラストラクチャがまだないため、そのまま進んでかまいません。
> 
> 

このスターター キットの zip ファイルには、組織の構成ファイルとキーが含まれています。

## <a name="configuring-the-chef-workstation"></a>Chef ワークステーションの構成
chef-starter.zip を C:\chef に展開します。

chef-starter\chef-repo\.chef にあるすべてのファイルを c:\chef ディレクトリにコピーします。

これでディレクトリは次の例のようになります。

![][5]

c:\chef のルートには Azure パブリッシュ ファイルを含む 4 つのファイルがあります。

PEM ファイルには組織と管理者の通信用秘密キーが含まれ、knife.rb ファイルには knife 構成が含まれています。 knife.rb ファイルを編集する必要があります。

任意のエディターでファイルを開き、パスから「/../」を削除して「cookbook_path」を変更し、次のようにします。

    cookbook_path  ["#{current_dir}/cookbooks"]

また、Azure パブリッシュ設定ファイルの名前を反映する次の行を追加します。

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

これで、knife.rb ファイルは次の例のようになります。

![][6]

これらの行によって、Knife が c:\chef\cookbooks の cookbook ディレクトリを参照し、Azure 操作中に Azure パブリッシュ設定を使用するようになります。

## <a name="installing-the-chef-development-kit"></a>Chef 開発キットのインストール
次に ChefDK (Chef 開発キット) を [ダウンロードしてインストール](http://downloads.getchef.com/chef-dk/windows) し、Chef Workstation を設定します。

![][7]

既定の場所である c:\opscode にインストールします。 このインストールは約 10 分かかります。

PATH 変数に C:\opscode\chefdk\bin;C:\opscode\chefdk\embedded\bin;c:\users\yourusername\.chefdk\gem\ruby\2.0.0\bin のエントリが含まれていることを確認します。

ない場合は必ずこのパスを追加してください。

*パスの順序が重要ですのでご注意ください。*  opscode パスの順序が正しくない場合は、問題が生じます。

続行する前にワークステーションを再起動します。

次に Knife Azure 拡張機能をインストールします。 これで Knife に「Azure プラグイン」が提供されます。

次のコマンドを実行します。

    chef gem install knife-azure ––pre

> [!NOTE]
> –pre 引数は、最新の API セットへのアクセスを提供する Knife Azure プラグインの最新 RC バージョンを、確実に受け取るようにします。
> 
> 

さまざまな依存関係も同時にインストールされる場合があります。

![][8]

すべてが正しく構成されたことを確認するには、次のコマンドを実行します。

    knife azure image list

すべてが正しく構成されていると、利用できる Azure イメージの一覧がスクロール表示されます。

おめでとうございます。 ワークステーションがセットアップされました。

## <a name="creating-a-cookbook"></a>Cookbook の作成
Cookbookは管理するクライアント上で実行する一連のコマンドを定義するために Chef で使用します。 Cookbook は簡単に作成でき、 **chef generate cookbook** コマンドを使用して Cookbook のテンプレートを生成します。 ポリシーで IIS を自動的にデプロイさせるため、Cookbook webserver を呼び出します。

C:\Chef ディレクトリで次のコマンドを実行します。

    chef generate cookbook webserver

これで、ディレクトリ C:\Chef\cookbooks\webserver に一連のファイルが生成されます。 次に、管理する仮想マシンで Chef クライアントに実行させる一連のコマンドを定義する必要があります。

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
既に説明したように、default.html ページとして使用するテンプレート ファイルを生成する必要があります。

次のコマンドを実行してテンプレートを生成します。

    chef generate template webserver Default.htm

C:\chef\cookbooks\webserver\templates\default\Default.htm.erb ファイルに移動します。 シンプルな「Hello World」 HTML コードを追加して、ファイルを編集した後、ファイルを保存します。

## <a name="upload-the-cookbook-to-the-chef-server"></a>Chef サーバーに Cookbook をアップロードする
このステップでは、ローカル コンピューターに作成した Cookbook のコピーを、Chef がホストするサーバーにアップロードします。 アップロードが完了すると、Cookbook が **[ポリシー]** タブに表示されます。

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Knife Azure で仮想マシンをデプロイする
Azure 仮想マシンをデプロイして、IIS Web サービスと既定のWeb ページをインストールする "Webserver" Cookbook を適用します。

これを実行するには **knife azure server create** コマンドを使用します。

コマンドの例を次に示します。

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

パラメーターを見ればすぐにわかります。 特定の変数に置き換えて実行してください。

> [!NOTE]
> このコマンド ラインでは –tcp-endpoints パラメーターを使用し、エンドポイント ネットワーク フィルター ルールも自動化しています。 ここではポート 80 と 3389 を開き、Web ページと RDP セッションにアクセスできるようにしています。
> 
> 

コマンドを実行したら、Azure ポータルに移動し、コンピューターでプロビジョニングが開始することを確認します。

![][13]

次にコマンド プロンプトが表示されます。

![][10]

デプロイメントが完了したら、ポート 80 で Web サービスに接続できるようになります。このポートは knife azure コマンドで仮想マシンをプロビジョニングした際に開きました。 この仮想マシンはクラウド サービスで唯一の仮想マシンであるため、クラウド サービスの URL で接続します。

![][11]

ご覧のように、クリエイティブな html コードにしました。

また、ポート 3389 を経由して Azure クラシック ポータルから RDP セッションを通じて接続できます。

お役に立てれば幸いです。 今すぐ Azure でコードを工夫したインフラストラクチャを始めてください。

<!--Image references-->
[2]: ./media/virtual-machines-windows-chef-automation/2.png
[3]: ./media/virtual-machines-windows-chef-automation/3.png
[4]: ./media/virtual-machines-windows-chef-automation/4.png
[5]: ./media/virtual-machines-windows-chef-automation/5.png
[6]: ./media/virtual-machines-windows-chef-automation/6.png
[7]: ./media/virtual-machines-windows-chef-automation/7.png
[8]: ./media/virtual-machines-windows-chef-automation/8.png
[9]: ./media/virtual-machines-windows-chef-automation/9.png
[10]: ./media/virtual-machines-windows-chef-automation/10.png
[11]: ./media/virtual-machines-windows-chef-automation/11.png
[13]: ./media/virtual-machines-windows-chef-automation/13.png


<!--Link references-->



<!--HONumber=Dec16_HO2-->


