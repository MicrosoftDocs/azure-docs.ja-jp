---
title: Azure Functions の継続的なデプロイ | Microsoft Docs
description: Azure App Service の継続的なデプロイ機能を使用して、Azure Functions を発行します。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: fd8fa690c508b8bf748490668c1e9aaa811ac247
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2019
ms.locfileid: "56300280"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions の継続的なデプロイ
Azure Functions を利用すると、App Service の継続的インテグレーションを使用して、関数アプリを簡単にデプロイできます。 Functions は、BitBucket、Dropbox、GitHub、および Azure DevOps と統合されています。 これにより、関数コードの更新がこれらの統合されたサービスのいずれかを使用して行われるワークフローで、Azure へのデプロイをトリガーできるようになります。 Azure Functions に慣れていない場合は、「 [Azure Functions の概要](functions-overview.md)」から始めてください。

複数の頻繁に発生する投稿を統合する場合、継続的なデプロイはプロジェクトに最適なオプションとなります。 また、関数コードのソース管理を維持することもできます。 現在、次のデプロイ ソースがサポートされています。

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* 外部リポジトリ (Git または Mercurial)
* [Git ローカル リポジトリ](../app-service/deploy-local-git.md)
* [GitHub](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [Azure DevOps](https://azure.microsoft.com/services/devops/)

デプロイは関数アプリごとに構成されます。 継続的なデプロイが有効になると、 ポータル内の関数コードへのアクセスは、 *読み取り専用*に設定されます。

## <a name="continuous-deployment-requirements"></a>継続的なデプロイの要件

継続的なデプロイを設定する前に、デプロイ ソースを構成し、そのデプロイ ソースに関数コードを追加する必要があります。 特定の関数アプリのデプロイで、各関数は名前付きサブディレクトリに存在します。その場合のディレクトリ名は、関数の名前です。  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Azure DevOps からデプロイできるようにするには、まず、Azure サブスクリプションに Azure DevOps 組織をリンクする必要があります。 詳しくは、「[Set up billing for your Azure DevOps organization](https://docs.microsoft.com/azure/devops/organizations/billing/set-up-billing-for-your-organization-vs?view=vsts#set-up-billing-via-the-azure-portal)」\(Azure DevOps 組織の課金の設定\) をご覧ください。

## <a name="set-up-continuous-deployment"></a>Azure App Service での GIT による継続的なデプロイ
既存の関数アプリの継続的なデプロイを構成するには、次の手順に従います。 この手順は、GitHub リポジトリとの統合を示しますが、Azure DevOps またはその他のデプロイ サービスに対しても同様の手順が適用されます。

1. [Azure Portal](https://portal.azure.com) の関数アプリで、**[プラットフォーム機能]** と **[展開オプション]** をクリックします。 
   
    ![継続的なデプロイの設定](./media/functions-continuous-deployment/setup-deployment.png)
 
2. 次に、**[デプロイ]** ブレードで **[セットアップ]** をクリックします。
 
    ![継続的なデプロイの設定](./media/functions-continuous-deployment/setup-deployment-1.png)
   
3. **[展開元]** ブレードで、**[ソースの選択]** をクリックし、選択したデプロイ ソースの情報を入力して、**[OK]** をクリックします。
   
    ![デプロイ ソースの選択](./media/functions-continuous-deployment/choose-deployment-source.png)

継続的なデプロイを構成した後は、デプロイ ソース内のすべてのファイル変更が関数アプリにコピーされ、完全なサイトのデプロイがトリガーされます。 ソース内のファイルが更新されると、サイトは再デプロイされます。

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

3. 運用環境の関数アプリでは、前述の「**継続的なデプロイの設定**」の手順を完了し、GitHub リポジトリのマスター分岐にデプロイ分岐を設定します。
   
    ![デプロイ分岐の選択](./media/functions-continuous-deployment/choose-deployment-branch.png)

4. ステージング環境の関数アプリでこの手順を繰り返しますが、代わりに GitHub リポジトリ内のステージング環境の分岐を選択します。 デプロイ ソースが分岐をサポートしていない場合は、別のフォルダーを使用します。
    
5. ステージング環境の分岐またはフォルダーでコードに更新プログラムを追加し、ステージング環境のデプロイでそれらの変更が反映されていることを確認します。

6. テストした後、マージはステージング環境の分岐からマスター分岐に変更されます。 このマージにより、運用環境の関数アプリへのデプロイがトリガーされます。 デプロイ ソースが分岐をサポートしていない場合は、ステージング環境のフォルダーから運用環境のフォルダー内のファイルを上書きします。

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>継続的なデプロイへの既存の関数の移動
ポータル内で作成および保持した既存の関数がある場合は、FTP またはローカルの Git リポジトリを使用して、既存の関数コードをダウンロードする必要があります。その後、上記で説明したように継続的なデプロイを設定することができます。 これは関数アプリの App Service の設定で行うことができます。 ファイルをダウンロードした後は、選択した継続的なデプロイ ソースにアップロードできます。

> [!NOTE]
> 継続的インテグレーションを構成した後は、Functions のポータルでソース ファイルを編集することはできなくなります。

- [方法:デプロイ資格情報の構成](#credentials)
- [方法:FTP を使用したファイルのダウンロード](#downftp)
- [方法:ローカルの Git リポジトリを使用したファイルのダウンロード](#downgit)

<a name="credentials"></a>
#### <a name="how-to-configure-deployment-credentials"></a>方法:デプロイ資格情報の構成
FTP またはローカル Git リポジトリで関数アプリからファイルをダウンロードする前に、サイトにアクセスするための資格情報を構成する必要があります。 資格情報は関数アプリ レベルで設定されます。 以下の手順に従って、Azure Portal でデプロイ資格情報を設定します。

1. [Azure Portal](https://portal.azure.com) の関数アプリで、**[プラットフォーム機能]** と **[デプロイ資格情報]** をクリックします。
   
    ![ローカル デプロイ資格情報の設定](./media/functions-continuous-deployment/setup-deployment-credentials.png)

2. ユーザー名とパスワードを入力し、 **[保存]** をクリックします。 これでこれらの資格情報を使用して、FTP または組み込みの Git リポジトリから関数アプリにアクセスできます。

<a name="downftp"></a>
#### <a name="how-to-download-files-using-ftp"></a>方法:FTP を使用したファイルのダウンロード

1. [Azure Portal](https://portal.azure.com) の関数アプリで、**[プラットフォーム機能]** と **[プロパティ]** をクリックし、**FTP/デプロイ ユーザー**、**FTP ホスト名**、および **FTPS ホスト名**の値をコピーします。  

    **[FTP/デプロイ ユーザー]** には、FTP サーバーに適切なコンテキストを提供するために、ポータルに表示されているとおり、アプリ名を含めて入力してください。
   
    ![デプロイ情報の取得](./media/functions-continuous-deployment/get-deployment-credentials.png)

2. FTP クライアントから、収集した接続情報を使用してアプリに接続し、関数のソース ファイルをダウンロードします。

<a name="downgit"></a>
#### <a name="how-to-download-files-using-a-local-git-repository"></a>方法:ローカルの Git リポジトリを使用したファイルのダウンロード

1. [Azure Portal](https://portal.azure.com) の関数アプリで、**[プラットフォーム機能]** と **[展開オプション]** をクリックします。 
   
    ![継続的なデプロイの設定](./media/functions-continuous-deployment/setup-deployment.png)
 
2. 次に、**[デプロイ]** ブレードで **[セットアップ]** をクリックします。
 
    ![継続的なデプロイの設定](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. **[展開元]** ブレードで **[ローカル Git リポジトリ]**、**[OK]** の順にクリックします。

3. **[プラットフォーム機能]** で、**[プロパティ]** をクリックし、Git URL の値をメモします。 
   
    ![継続的なデプロイの設定](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

4. Git 対応のコマンド プロンプトまたはお気に入りの Git ツールを使用して、ローカル コンピューターにリポジトリを複製します。 Git clone コマンドは、次のようになります。
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

5. 次の例のように、関数アプリからローカル コンピューター上のクローンにファイルをフェッチします。
   
        git pull origin master
   
    要求された場合は、[構成されたデプロイ資格情報](#credentials)を入力します。  

[GitHub]: https://github.com/

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Functions のベスト プラクティス](functions-best-practices.md)
