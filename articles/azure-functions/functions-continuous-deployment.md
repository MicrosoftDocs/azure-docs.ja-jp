---
title: "Azure Functions の継続的なデプロイ | Microsoft Docs"
description: "Azure App Service の継続的なデプロイ機能を使用して、Azure Functions を発行します。"
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/25/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 66753c80223e33c3add9d37aa4d81656472391ca
ms.openlocfilehash: e1a869d3c3512f32eab7840f5abc17abd8ce8004


---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions の継続的なデプロイ
Azure Functions を利用すると、関数アプリの継続的なデプロイを簡単に構成することができます。 Functions は Azure App Service と BitBucket、Dropbox、GitHub、Visual Studio Team Services (VSTS) との統合を利用して、継続的なデプロイのワークフローを有効にします。このワークフローでは、関数コードへの更新がこれらのいずれかのサービスに発行されたときに、Azure が更新を取り込みます。 Azure Functions に慣れていない場合は、「 [Azure Functions の概要](functions-overview.md)」から始めてください。

複数の頻繁に発生する投稿を統合する場合、継続的なデプロイはプロジェクトに最適なオプションとなります。 また、関数コードのソース管理を維持することもできます。 現在、次のデプロイ ソースがサポートされています。

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://bitbucket.org/)
* [Git ローカル リポジトリ](../app-service-web/app-service-deploy-local-git.md)
* Git 外部リポジトリ
* [GitHub]
* Mercurial 外部リポジトリ
* [OneDrive](https://onedrive.live.com/)
* [Visual Studio Team Services](https://www.visualstudio.com/team-services/)

デプロイは関数アプリごとに構成されます。 継続的なデプロイが有効になると、 ポータル内の関数コードへのアクセスは、 *読み取り専用*に設定されます。

## <a name="continuous-deployment-requirements"></a>継続的なデプロイの要件

継続的なデプロイを設定する前に、デプロイ ソースを構成し、そのデプロイ ソースに関数コードを追加する必要があります。 特定の関数アプリのデプロイで、各関数は名前付きサブディレクトリに存在します。その場合のディレクトリ名は、関数の名前です。 このフォルダー構造は、本質的にサイト コードです。 

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="set-up-continuous-deployment"></a>Azure App Service での GIT による継続的なデプロイ
既存の関数アプリの継続的なデプロイを構成するには、次の手順に従います。

1. [Azure Functions ポータル](https://functions.azure.com/signin)の関数アプリで、**[Function App Settings (関数アプリの設定)]** > **[Configure continuous integration (継続的インテグレーションの構成)]** > **[セットアップ]** の順にクリックします。
   
    ![継続的なデプロイの設定](./media/functions-continuous-deployment/setup-deployment.png)
   
    ![継続的なデプロイの設定](./media/functions-continuous-deployment/setup-deployment-1.png)
   
    また、 **[Start from source control (ソース管理から開始)]**をクリックして Functions のクイック スタートから [デプロイ] ブレードにアクセスすることもできます。
2. **[展開元]** ブレードで、**[ソースの選択]** をクリックし、選択したデプロイ ソースの情報を入力して、**[OK]** をクリックします。
   
    ![デプロイ ソースの選択](./media/functions-continuous-deployment/choose-deployment-source.png)

継続的なデプロイを構成した後は、デプロイ ソース内のすべての変更ファイルが関数アプリにコピーされ、完全なサイトのデプロイがトリガーされます。 ソース内のファイルが更新されると、サイトは再デプロイされます。

## <a name="deployment-options"></a>デプロイ オプション

一般的なデプロイ シナリオを次に示します。

- [ステージング環境のデプロイの作成](#staging)
- [継続的なデプロイへの既存の関数の移動](#existing)

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>ステージング環境のデプロイの作成

関数アプリはデプロイ スロットをまだサポートしていません。 ただし、継続的インテグレーションを使用して、個別のステージング環境と運用環境のデプロイを管理できます。

ステージング環境のデプロイを構成し、使用するプロセスは、一般的には次のようになります。

1. サブスクリプションで運用環境コードおよびステージング環境用の 2 つの関数アプリを作成します。 

2. 既にお持ちでない場合は、デプロイ ソースを作成します。 この例では、[GitHub] を使用します。

3. 運用環境の関数アプリでは、上記の「**継続的なデプロイの設定**」の手順を完了し、GitHub リポジトリのマスター分岐にデプロイ分岐を設定します。
   
    ![デプロイ分岐の選択](./media/functions-continuous-deployment/choose-deployment-branch.png)

4. ステージング環境の関数アプリでこの手順を繰り返しますが、代わりに GitHub リポジトリ内のステージング環境の分岐を選択します。 デプロイ ソースが分岐をサポートしていない場合は、別のフォルダーを使用します。

5. ステージング環境の分岐またはフォルダーでコードに更新プログラムを追加し、ステージング環境のデプロイでそれらの変更が反映されていることを確認します。

6. テストした後、マージはステージング環境の分岐からマスター分岐に変更されます。 これにより、運用環境の関数アプリへのデプロイがトリガーされます。 デプロイ ソースが分岐をサポートしていない場合は、ステージング環境のフォルダーから運用環境のフォルダー内のファイルを上書きします。

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>継続的なデプロイへの既存の関数の移動
ポータル内で作成および保持した既存の関数がある場合は、FTP またはローカルの Git リポジトリを使用して、既存の関数コードをダウンロードする必要があります。その後、上記で説明したように継続的なデプロイを設定することができます。 これは関数アプリの App Service の設定で行うことができます。 ファイルをダウンロードした後は、選択した継続的なデプロイ ソースにアップロードできます。

> [!NOTE]
> 継続的インテグレーションを構成した後は、Functions のポータルでソース ファイルを編集することはできなくなります。

- [方法: デプロイ資格情報の構成](#credentials)
- [方法: FTP を使用したファイルのダウンロード](#downftp)
- [方法: ローカルの Git リポジトリを使用したファイルのダウンロード](#downgit)

<a name="credentials"></a>
#### <a name="how-to-configure-deployment-credentials"></a>方法: デプロイ資格情報の構成
FTP またはローカル Git リポジトリで関数アプリからファイルをダウンロードする前に、サイトにアクセスするための資格情報を構成する必要があります。その作業は、ポータルから実行することができます。 資格情報は関数アプリ レベルで設定されます。

1. [Azure Functions ポータル](https://functions.azure.com/signin)の関数アプリで、**[Function App Settings (関数アプリの設定)]** > **[Go to App Service Settings (App Service の設定に移動)]** > **[デプロイ資格情報]** の順にクリックします。
   
    ![ローカル デプロイ資格情報の設定](./media/functions-continuous-deployment/setup-deployment-credentials.png)

2. ユーザー名とパスワードを入力し、 **[保存]**をクリックします。 これでこれらの資格情報を使用して、FTP または組み込みの Git リポジトリから関数アプリにアクセスできます。

<a name="downftp"></a>
#### <a name="how-to-download-files-using-ftp"></a>方法: FTP を使用したファイルのダウンロード

1. [Azure Functions ポータル](https://functions.azure.com/signin)の関数アプリで、**[Function App Settings (関数アプリの設定)]** > **[Go to App Service Settings (App Service の設定に移動)]** > **[プロパティ]** の順にクリックし、**[FTP/デプロイ ユーザー]****[FTP ホスト名]**、**[FTPS ホスト名]** の値をコピーします。  

    **[FTP/デプロイ ユーザー]** には、FTP サーバーに適切なコンテキストを提供するために、ポータルに表示されているとおり、アプリ名を含めて入力してください。
   
    ![デプロイ情報の取得](./media/functions-continuous-deployment/get-deployment-credentials.png)

2. FTP クライアントから、収集した接続情報を使用してアプリに接続し、関数のソース ファイルをダウンロードします。

<a name="downgit"></a>
#### <a name="how-to-download-files-using-the-local-git-repository"></a>方法: ローカルの Git リポジトリを使用したファイルのダウンロード

1. [Azure Functions ポータル](https://functions.azure.com/signin)の関数アプリで、**[Function App Settings (関数アプリの設定)]** > **[Configure continuous integration (継続的インテグレーションの構成)]** > **[セットアップ]** の順にクリックします。

2. [デプロイ] ブレードで、**[ソースの選択]**、**[ローカル Git リポジトリ]** の順にクリックし、**[OK]** をクリックします。

3. **[Go to App Service Settings (App Service の設定に移動)]** > **[プロパティ]** の順にクリックし、[Git URL] の値をメモします。 
   
    ![継続的なデプロイの設定](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

4. Git 対応のコマンドラインまたはお気に入りの Git ツールを使用して、ローカル コンピューターにリポジトリを複製します。 Git clone コマンドは、次のようになります。
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

5. 次の例のように、関数アプリからローカル コンピューター上のクローンにファイルをフェッチします。
   
        git pull origin master
   
    要求された場合は、関数アプリ デプロイのユーザー名とパスワードを入力します。  

[GitHub]: https://github.com/



<!--HONumber=Nov16_HO4-->


