---
title: Azure PowerShell のインストールおよび構成方法
description: Azure PowerShell のインストールと構成の方法について説明します。
editor: tysonn
manager: dongill
documentationcenter: ''
services: ''
author: coreyp-at-msft

ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 04/22/2016
ms.author: coreyp

---
# Azure PowerShell のインストールおよび構成方法
<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/xplat-cli-install/" title="Azure CLI">Azure CLI</a></div>

## Azure PowerShell とは
Azure PowerShell は、Windows PowerShell を使用して Azure を管理するためのコマンドレットを提供するモジュールです。このコマンドレットを使用して、Azure Platform から配信されるソリューションやサービスを、作成、テスト、デプロイ、管理できます。ほとんどの場合、コマンドレットを使用して Microsoft Azure 管理ポータルと同じタスク (クラウド サービス、仮想マシン、仮想ネットワーク、Web アプリの作成や構成など) を実行できます。

<a id="Install"></a>

## 手順 1: インストール
Azure PowerShell は、以下に示す 2 つの方法でインストールできます。WebPI からインストールする方法と PowerShell ギャラリーからインストールする方法です。

### WebPI から Azure PowerShell をインストールする
Azure PowerShell 1.0 以降は、0.9.x までと同じ方法で WebPI からインストールすることができます。[Azure Powershell](http://aka.ms/webpi-azps) をダウンロードして、インストールを開始します。Azure PowerShell 0.9.x がインストールされている場合は、アップグレードの一環としてバージョン 0.9.x がアンインストールされます。Azure PowerShell モジュールが PowerShell ギャラリーからインストールされた場合は、Azure PowerShell 環境の整合性を確保するために、インストール前にモジュールが自動的に削除されます。

> [!NOTE]
> PowerShell ギャラリーから以前にインストールされた Azure モジュールは、自動的に削除されます。これは、インストールしたモジュールとその場所に関して混乱を防ぐための措置です。PowerShell ギャラリーのモジュールは通常では **%ProgramFiles%\WindowsPowerShell\Modules** にインストールされます。これに対し、WebPI インストーラーを使用した場合、Azure モジュールは **%ProgramFiles(x86)%\Microsoft SDKs\Azure\PowerShell** にインストールされます。インストール中にエラーが発生した場合は、**%ProgramFiles%\WindowsPowerShell\Modules** フォルダーから Azure* フォルダーを手動で削除してから、インストールをもう一度試みます。
> 
> 

インストールが完了したら、Azure PowerShell コマンドレットを含むディレクトリが ```$env:PSModulePath``` の設定に含まれている必要があります。

> [!NOTE]
> PowerShell **$env:PSModulePath** には、WebPI からのインストール時に生じる可能性がある既知の問題があります。システムの更新やその他のインストールでコンピューターの再起動が必要になった場合、Azure PowerShell のインストール パスが **$env:PSModulePath** の更新に含まれない場合があります。このような場合は、インストールまたはアップグレード後に Azure PowerShell コマンドレットを使用しようとすると、「コマンドレットは認識されません」というメッセージが表示されることがあります。このような場合は、コンピューターを再起動すると問題が解決します。
> 
> 

コマンドレットを読み込もうとしたとき、または実行しようとしたときに、次のようなメッセージが表示される場合:

```
    PS C:\> Get-AzureRmResource
    Get-AzureRmResource : The term 'Get-AzureRmResource' is not recognized as the name of a cmdlet, function,
    script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is
    correct and try again.
    At line:1 char:1
    + Get-AzureRmResource
    + ~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ObjectNotFound: (get-azurermresourcefork:String) [], CommandNotFoundException
        + FullyQualifiedErrorId : CommandNotFoundException
```

この問題は、コンピューターを再起動するか、次のように C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\ (XXXX はインストールされている PowerShell のバージョン) からコマンドレットをインポートすることによって修正できます。

```
import-module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\azure.psd1"
import-module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\expressroute\expressroute.psd1"
```

### PowerShell ギャラリーから Azure PowerShell をインストールする
PowerShell ギャラリーから、管理者特権の Windows PowerShell または PowerShell Integrated Scripting Environment (ISE) プロンプトを使用して、次のコマンドで Azure PowerShell 1.3.0 以上をインストールします。

    # Install the Azure Resource Manager modules from the PowerShell Gallery
    Install-Module AzureRM

    # Install the Azure Service Management module from the PowerShell Gallery
    Install-Module Azure

#### 前述のコマンドの詳細
* **Install-Module AzureRM** は、Azure Resource Manager のコマンドレットのロールアップ モジュールをインストールします。AzureRM モジュールは、Azure Resource Manager の各モジュールの特定のバージョン範囲に依存します。バージョン範囲が含まれていることにより、同じメジャー バージョンの AzureRM モジュールをインストールするときに、互換性を破るモジュール変更は含まれません。AzureRM モジュールをインストールするときには、以前にインストールしていない Azure Resource Manager モジュールをダウンロードして、PowerShell ギャラリーからインストールします。Azure PowerShell モジュールで使用されるセマンティック バージョニングの詳細については、[semver.org](http://semver.org) を参照してください。 
* **Install-Module Azure** は、Azure モジュールをインストールします。これは、Azure PowerShell 0.9.x のサービス管理モジュールとなります。大きな変更はなく、以前のバージョンの Azure モジュールと互換性があります。

## 手順 2: 起動
コマンドレットは、標準の Windows PowerShell コンソールまたは PowerShell Integrated Scripting Environment (ISE) から実行できます。どちらのコンソールでも、開く方法は、実行している Windows のバージョンによって異なります。

* Windows 8 または Windows Server 2012 以降を実行しているコンピューターでは、組み込みの検索機能を使用できます。**スタート**画面で「power」と入力します。Windows PowerShell が名前に含まれているすべてのアプリケーションが一覧表示されます。コンソールを開くには、いずれかのアプリケーションをクリックします。(アプリを**スタート**画面にピン留めするには、そのアイコンを右クリックします。)
* Windows 8 または Windows Server 2012 より前のバージョンを実行しているコンピューターでは、**[スタート]** メニューを使用します。**[スタート]** メニューから、**[すべてのプログラム]**、**[アクセサリ]**、**[Windows PowerShell]** フォルダー、**[Windows PowerShell]** の順にクリックします。

Windows PowerShell コンソールで行う作業の多くは、**Windows PowerShell ISE** からメニュー項目とキーボード ショートカットを使用して実行することもできます。ISE を使用するには、Windows PowerShell コンソール、Cmd.exe、または **[ファイル名を指定して実行]** ボックスに「**powershell\_ise.exe**」と入力します。

### 基本的なコマンド
    # To make sure the Azure PowerShell module is available after you install
    Get-Module –ListAvailable 

    # To login to Azure Resource Manager
    Login-AzureRmAccount

    # You can also use a specific Tenant if you would like a faster login experience
    # Login-AzureRmAccount -TenantId xxxx

    # To view all subscriptions for your account
    Get-AzureRmSubscription

    # To select a default subscription for your current session
    Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

    # View your current Azure PowerShell session context
    # This session state is only applicable to the current session and will not affect other sessions
    Get-AzureRmContext

    # To select the default storage context for your current session
    Set-AzureRmCurrentStorageAccount –ResourceGroupName “your resource group” –StorageAccountName “your storage account name”

    # View your current Azure PowerShell session context
    # Note: the CurrentStorageAccount is now set in your session context
    Get-AzureRmContext

    # To list all of the blobs in all of your containers in all of your accounts
    Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob


## 手順 3: 接続
コマンドレットでサービスを管理するには、サブスクリプションが必要です。サブスクリプションがまだない場合は、Azure サブスクリプションを購入できます。手順については、「[Azure の購入方法](http://go.microsoft.com/fwlink/p/?LinkId=320795)」を参照してください。

1. 「**Login-AzureRmAccount**」と入力します。
2. アカウントに関連付けられている電子メール アドレスとパスワードを入力します。Azure により資格情報が認証および保存され、ウィンドウが閉じます。

または

職場または学校のアカウントにサインインします。

    $cred = Get-Credential
    Login-AzureRmAccount -Credential $cred
> [!NOTE]
> 組織アカウントに複数のテナントが関連付けられている場合、TenantId パラメーターを指定します。
> 
> 

    $loadersubscription = Get-AzureRmSubscription -SubscriptionName $YourSubscriptionName -TenantId $YourAssociatedSubscriptionTenantId


> [!NOTE]
> この非対話型のログイン方法は、職場または学校のアカウントでのみ機能します。職場または学校のアカウントは、職場または学校によって管理されているユーザーで、職場または学校の Azure Active Directory インスタンスで定義されています。現在、職場または学校のアカウントがなく、Microsoft アカウントを使用して Azure サブスクリプションにログインしている場合は、次の手順を使用して簡単に職場または学校のアカウントを作成できます。
> 
> 1. [Azure クラシック ポータル](https://manage.windowsazure.com)にログインし、**[Active Directory]** をクリックします。
> 2. ディレクトリがない場合は、**[ディレクトリの作成]** を選択し、求められる情報を入力します。
> 3. ディレクトリを選択し、新しいユーザーを追加します。この新しいユーザーは、職場または学校のアカウントを使用してサインインできます。ユーザーの作成時、ユーザーの電子メール アドレスと仮パスワードの両方が通知されます。この情報は、以降の手順 5. で使用するため保管しておいてください。
> 4. Azure クラシック ポータルで **[設定]**、**[管理者]** の順に選択します。**[追加]** を選択し、共同管理者として新しいユーザーを追加します。これにより、職場または学校のアカウントで Azure サブスクリプションを管理できるようになります。
> 5. 最後に、Azure クラシック ポータルからログアウトし、職場または学校のアカウントを使用してログインし直します。初めてこのアカウントを使用してログインする場合は、パスワードを変更するように求められます。
> 
> 職場または学校のアカウントを使用して Microsoft Azure にサインアップする方法の詳細については、[組織としての Microsoft Azure へのサインアップに関するページ](active-directory/sign-up-organization.md)を参照してください。
> 
> Azure の認証とサブスクリプション管理の詳細については、「[アカウント、サブスクリプション、管理者ロールを管理する](http://go.microsoft.com/fwlink/?LinkId=324796)」を参照してください。
> 
> 

### アカウントおよびサブスクリプションの詳細を表示する
Azure PowerShell で使用するためのアカウントとサブスクリプションは、複数所有することができます。**Add-AzureRmAccount** を繰り返し実行することで、複数のアカウントを追加できます。

使用可能な Azure アカウントを表示するには、「**Get-AzureAccount**」と入力します。

Azure サブスクリプションを表示するには、「**Get-AzureRmSubscription**」と入力します。

## <a id="Help"></a>ヘルプの表示
これらのリソースは、特定のコマンドレットに関するヘルプ情報を提供します。

* コンソールから、組み込みのヘルプ システムを使用できます。**Get-Help** コマンドレットを実行すると、ヘルプ システムにアクセスできます。 
* コミュニティの情報を参照する場合は、以下の人気フォーラムもご覧ください。
  
  * [MSDN 上の Azure フォーラム](http://go.microsoft.com/fwlink/p/?LinkId=320212)
  * [StackOverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

## 詳細情報
コマンドレットの使用方法の詳細については、次のリソースを参照してください。

Windows PowerShell の基本的な使用手順については、「[Windows PowerShell を使用する](http://go.microsoft.com/fwlink/p/?LinkId=321939)」を参照してください。

コマンドレットのリファレンスについては、「[Azure コマンドレット リファレンス](https://msdn.microsoft.com/library/windowsazure/jj554330.aspx)」を参照してください。

スクリプトを使用した Azure の管理に役立つサンプル スクリプトと手順については、「[スクリプト センター](http://go.microsoft.com/fwlink/p/?LinkId=321940)」を参照してください。

<!---HONumber=AcomDC_0518_2016-->
