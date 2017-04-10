---
title: "CLI から Azure へのログイン | Microsoft Docs"
description: "Mac、Linux、および Windows 用の Azure コマンドライン インターフェイス (Azure CLI) からの Azure サブスクリプションへの接続"
editor: tysonn
manager: timlt
documentationcenter: 
author: squillace
services: virtual-machines-linux,virtual-network,storage,azure-resource-manager
tags: azure-resource-manager,azure-service-management
ms.assetid: ed856527-d75e-4e16-93fb-253dafad209d
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: rasquill
"\"/": 
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 31efab60690b54faf7992251fcd01e307c4464f2
ms.lasthandoff: 03/21/2017


---
# <a name="log-in-to-azure-from-the-azure-cli"></a>Azure CLI から Azure へのログイン
Azure CLI は、Azure リソースで使用できるオープン ソース、クロスプラットフォームのコマンド群です。 この記事では、Azure サブスクリプションに Azure CLI を接続するための Azure アカウント資格情報を入力するさまざまな方法について説明します。

* Azure Active Directory によって認証するには、`azure login` CLI コマンドを実行します。 この方法では、両方の[コマンド モード](#cli-command-modes)で CLI コマンドにアクセスできます。 追加のオプションを指定せずにコマンドを実行すると、`azure login` では、Web ポータル経由の対話形式でログインを続行するように求められます。 追加の `azure login` コマンド オプションについては、この記事のシナリオを参照するか、または「`azure login --help`」と入力します。
* Azure サービス管理モード CLI コマンドのみを使用する (ほとんどの新しいデプロイメントに推奨されません) 必要がある場合は、コンピューターに発行設定ファイルをダウンロードしてインストールできます。

CLI をまだインストールしていない場合は、「 [Azure CLI のインストール](cli-install-nodejs.md)」を参照してください。 Azure サブスクリプションがない場合は、 [無料アカウント](http://azure.microsoft.com/free/) を数分で作成することができます。

各種アカウント ID と Azure サブスクリプションに関する背景については、「 [Azure サブスクリプションを Azure Active Directory に関連付ける方法](active-directory/active-directory-how-subscriptions-associated-directory.md)」を参照してください。

## <a name="scenario-1-azure-login-with-interactive-login"></a>シナリオ 1: 対話型ログインによる Azure ログイン
特定のアカウントでは、CLI によって、`azure login` を実行し、次に Web ブラウザーで Web ポータルから*対話型ログイン*と呼ばれるログイン プロセスを続行するように求められます。 一般的な理由は、多要素認証を必要とするように設定された職場または学校のアカウント (*組織アカウント*とも呼ばれる) を持つ場合です。 さらに、リソース マネージャー モードのコマンドを使用する場合に、Microsoft アカウントで対話型ログインを使用します。

対話型ログインは簡単です。次の例に示すように、オプションを指定しないで「`azure login`」と入力します。

```
azure login
```                                                                                             

出力は次のようになります。

```         
info:    Executing command login
info:    To sign in, use a web browser to open the page http://aka.ms/devicelogin. Enter the code XXXXXXXXX to authenticate.
```
コマンド出力に示されたコードをコピーし、http://aka.ms/devicelogin または指定されている場合はその他のページにブラウザーを開きます (同じコンピューター、または別のコンピューターやデバイスでブラウザーを開くことができます)。コードを入力すると、使用する ID のユーザー名とパスワードを入力するように求められます。 このプロセスが完了すると、コマンド シェルによってログインが完了します。 次のような内容が表示されます。

    info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Added subscription Azure Free Trial
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK

> [!NOTE]
> 対話型ログインでは、認証と承認は Azure Active Directory を使用して行われます。 Microsoft アカウントの ID を使用する場合、ログイン プロセスは Azure Active Directory の既定のドメインにアクセスします (無料 Azure アカウントにサインアップした場合は、Azure Active Directory によってアカウントの既定のドメインが自動的に作成されています)。
>
>

## <a name="scenario-2-azure-login-with-a-username-and-password"></a>シナリオ 2: ユーザー名とパスワードによる Azure ログイン
多要素認証を必要としない職場または学校のアカウントを使用する場合は、ユーザー名 (`-u`) パラメーターを指定して `azure login` コマンドを実行します。 コマンド ラインにパスワードを入力するように求められます (または必要に応じて、`azure login` コマンドの追加のパラメーターとしてパスワードを渡すことができます)。 次の例では、組織アカウントのユーザー名が渡されます。

    azure login -u myUserName@contoso.onmicrosoft.com

パスワードを入力するように求められます。

    info:    Executing command login
    Password: *********

これでログイン プロセスは完了です。

    info:    Added subscription Visual Studio Ultimate with MSDN
    +
    info:    login command OK

初めてこれらの資格情報を使用してログインする場合は、認証トークンをキャッシュするかどうかの確認を求められます。 このプロンプトは、 `azure logout` コマンド (この記事の後半で説明) を以前に使用した場合にも表示されます。 自動化のシナリオでこのプロンプトが表示されないようにするには、`azure login` に `-q` パラメーターを付けて実行します。

## <a name="scenario-3-azure-login-with-a-service-principal"></a>シナリオ 3: サービス プリンシパルによる Azure ログイン
Active Directory アプリケーションのサービス プリンシパルを作成し、サービス プリンシパルにサブスクリプションに対するアクセス許可がある場合は、`azure login` コマンドを実行してサービス プリンシパルを認証できます。 シナリオによっては、`azure login` コマンドの明示的なパラメーターとしてサービス プリンシパルの資格情報を入力できます。 たとえば、次のコマンドは、サービス プリンシパル名と Active Directory テナント ID を渡します。

    azure login -u https://www.contoso.org/example --service-principal --tenant myTenantID

パスワードの入力が求められます。 CLI スクリプトまたはアプリケーション コードによって資格情報を提供するか、または自動化のシナリオために、証明書を使用して、非対話的にサービス プリンシパルを認証することもできます。 詳細と例については、「[リソースにアクセスするためのサービス プリンシパルを Azure CLI で作成する](resource-group-authenticate-service-principal-cli.md)」を参照してください。

## <a name="scenario-4-use-a-publish-settings-file"></a>シナリオ 4: 発行設定ファイルの使用
Azure Service Management モードの CLI コマンドを使用するだけで十分である場合は (クラシック デプロイ モデルで Azure VM をデプロイする場合など)、発行設定ファイルを使用して接続できます。 この方法では、証明書をローカル コンピューターにインストールして、サブスクリプションと証明書が有効である限り、管理タスクを実行できるようにします。

* アカウントの**発行設定ファイルをダウンロード**するには、「`azure config mode asm`」と入力して、CLI が Service Management モードであることを確認します。 次に、次のコマンドを実行します。

        azure account download

これにより、既定のブラウザーが開き、 [Azure クラシック ポータル](https://manage.windowsazure.com)にサインインするよう求められます。 サインインした後、 `.publishsettings` ファイルがダウンロードされます。 ファイルを保存した場所をメモしておきます。

> [!NOTE]
> アカウントが複数の Azure Active Directory テナントに関連付けられている場合は、発行の設定ファイルのダウンロード対象となる Active Directory を選択するよう促されることがあります。
>
>

ダウンロード ページを使用して選択するか、Azure クラシック ポータルにアクセスして選択すると、選択した Active Directory が、クラシック ポータルおよびダウンロード ページで既定として使用されようになります。 既定を確定した後、'**click here to return to the selection page (選択ページに戻るにはここをクリックしてください)**' というテキストが、ダウンロード ページの上部に表示されます。 選択されたページに戻るには、用意されているリンクを使用します。

* **発行設定ファイルをインポートするには**、次のコマンドを実行します。

        azure account import <path to your .publishsettings file>

> [!IMPORTANT]
> 発行設定をインポートした後は、`.publishsettings` ファイルを削除する必要があります。 このファイルは Azure CLI では不要であり、第三者によってサブスクリプションヘのアクセスに使用されるセキュリティ上のリスクがあるためです。
>
>

## <a name="cli-command-modes"></a>CLI コマンド モード
Azure CLI には、異なるコマンド セットを使用して、Azure リソースを操作するための次の&2; つのコマンド モードが用意されています。

* **Resource Manager モード** - Resource Manager デプロイ モデルでの Azure リソースの操作用です。 このモードを設定するには、 `azure config mode arm`を実行します。
* **サービス管理モード** - クラシック デプロイ モデルでの Azure リソースの操作用です。 このモードを設定するには、 `azure config mode asm`を実行します。

最初にインストールされたときの CLI の現在のリリースは Resource Manager モードになっています。

> [!NOTE]
> Resource Manager モードとサービス管理モードは相互に排他的です。 つまり、どちらか一方のモードで作成されたリソースは、他方のモードは管理できません。
>
>

## <a name="multiple-subscriptions"></a>複数のサブスクリプション
複数の Azure サブスクリプションがある場合は、Azure に接続すると、資格情報に関連付けられているすべてのサブスクリプションへのアクセスが許可されます。 1 つのサブスクリプションが既定として選択され、操作の実行時に Azure CLI によって使用されます。 現在の既定のサブスクリプションを含むサブスクリプションを表示するには、`azure account list` コマンドを使用します。 このコマンドでは、次のような情報が返されます。

    info:    Executing command account list
    data:    Name              Id                                    Current
    data:    ----------------  ------------------------------------  -------
    data:    Azure-sub-1       ####################################  true
    data:    Azure-sub-2       ####################################  false

上記のリストでは、**Current** 列に現在の既定のサブスクリプションとして Azure-sub-1 が表示されています。 既定のサブスクリプションを変更するには、 `azure account set` コマンドを使用して、既定にするサブスクリプションを指定します。 次に例を示します。

    azure account set Azure-sub-2

この結果、既定のサブスクリプションは Azure-sub-2 に変更されます。

> [!NOTE]
> 既定のサブスクリプションの変更はすぐに有効になり、グローバルな変更です。新しい Azure CLI コマンドの実行には、同じコマンドライン インスタンスからでも別のインスタンスからでも、この新しい既定のサブスクリプションが使用されます。
>
>

Azure CLI で既定以外のサブスクリプションを使用して、既定のサブスクリプションを変更しないようにするには、コマンドで `--subscription` オプションを使用して、その操作に使用するサブスクリプションの名前を指定します。

Azure サブスクリプションに接続すると、その Azure CLI コマンドの使用を開始し、Azure リソースを操作することができます。

## <a name="storage-of-cli-settings"></a>CLI 設定の格納
`azure login` コマンドを使用してログインするか、発行設定をインポートすると、CLI プロファイルとログが、`user` ディレクトリの `.azure` ディレクトリに格納されます。 `user` ディレクトリはオペレーティング システムによって保護されていますが、 追加の作業を行って `user` ディレクトリを暗号化することをお勧めします。 そのためには、次の操作を行います。

* Windows の場合、ディレクトリ プロパティを変更するか、または BitLocker を使用します。
* Mac の場合、ディレクトリに対して FileVault を有効にします。
* Ubuntu の場合、Encrypted Home ディレクトリ機能を使用します。 その他の Linux ディストリビューションにも同様の機能が用意されています。

## <a name="logging-out"></a>ログアウト
ログアウトするには、次のコマンドを使用します。

    azure logout -u <username>

アカウントに関連付けられたサブスクリプションが Active Directory のみを使用して認証された場合は、ログアウトするとローカル プロファイルからサブスクリプション情報が削除されます。 ただし、サブスクリプション用に発行設定ファイルがインポートされている場合は、ログアウトするとローカル プロファイルから Active Directory 関連の情報のみが削除されます。

## <a name="next-steps"></a>次のステップ
* Azure CLI コマンドを使用するには、「[Resource Manager モードでの Azure CLI コマンド](virtual-machines/azure-cli-arm-commands.md)」および「[Azure サービス管理 (asm) モードでの Azure CLI コマンド](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)」をご覧ください。
* Azure CLI の詳細、ソース コードのダウンロード、問題のレポート、プロジェクトへの協力については、 [GitHub リポジトリの Azure CLI](https://github.com/azure/azure-xplat-cli)のページを参照してください。
* Azure CLI、または Azure を利用していて問題が発生した場合は、 [Azure のフォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting)をご覧ください。

