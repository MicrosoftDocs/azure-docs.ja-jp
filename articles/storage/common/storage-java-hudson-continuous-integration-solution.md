---
title: Hudson と Blob Storage を使用する方法 | Microsoft Docs
description: Hudson で Azure BLOB ストレージをビルド アーティファクトのリポジトリとして使用する方法について説明します。
services: storage
author: seguler
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 02/28/2017
ms.author: tarcher
ms.subservice: common
ms.openlocfilehash: 54e91d4df1109b9ece1150f8b44665789e4dfce1
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875881"
---
# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Hudson 継続的インテグレーション ソリューションでの Azure Storage の使用
## <a name="overview"></a>概要
ここでは、Hudson 継続的インテグレーション (CI) ソリューションで作成されるビルド アーティファクトのリポジトリとして、またはビルド プロセスで使用されるダウンロード可能なファイルのソースとして BLOB ストレージを使用する方法について説明します。 この方法が有用になるシナリオの 1 つが、アジャイル開発環境で (Java などの言語を使って) コーディングをしており、継続的インテグレーションに基づいてビルドを実行するとき、ビルド アーティファクト用のリポジトリが必要な場合です。このリポジトリがあれば、ビルド アーティファクトを他の組織のメンバーや顧客と共有したり、そのアーカイブを保存したりできます。  もう 1 つのシナリオとしては、ビルド ジョブ自体にその他のファイルが必要になる場合、たとえば、ビルドの入力で依存関係のダウンロードが必要になる場合などが考えられます。

このチュートリアルでは、Microsoft が公開している Hudson CI 用の Azure Storage プラグインを使用します。

## <a name="introduction-to-hudson"></a>Hudson の概要
Hudson では、開発者がコードの変更を簡単に統合し、ビルドを自動的に頻繁に生成できるようになり、ソフトウェア プロジェクトの継続的な統合が実現されます。これにより、開発者の生産性が向上します。 ビルドはバージョン管理され、ビルド アーティファクトをさまざまなリポジトリにアップロードできます。 この記事では、Azure BLOB ストレージをビルド アーティファクトのリポジトリとして使用する方法について説明します。 また、Azure BLOB ストレージから依存関係をダウンロードする方法も紹介します。

Hudson の詳細については、「 [Meet Hudson (Hudson について)](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson)」を参照してください。

## <a name="benefits-of-using-the-blob-service"></a>Blob service を使用するメリット
Blob service を使用してアジャイル開発のビルド アーティファクトをホストするメリットには、次の点が挙げられます。

* ビルド アーティファクトやダウンロード可能な依存関係に高可用性を実現。
* Hudson CI ソリューションでビルド アーティファクトをアップロードする際のパフォーマンスを改善。
* 顧客およびパートナーがビルド アーティファクトをダウンロードする際のパフォーマンスを改善。
* 匿名アクセス、有効期限ベースの Shared Access Signature によるアクセス、プライベート アクセスなどから 1 つを選んでユーザー アクセス ポリシーを制御。

## <a name="prerequisites"></a>前提条件
Hudson CI ソリューションで Blob service を使用するには、次のものが必要です。

* Hudson 継続的インテグレーション ソリューション。
  
    Hudson CI ソリューションがない場合には、次の方法によって Hudson CI ソリューションを実行できます。
  
  1. Java が有効なコンピューターで、<http://hudson-ci.org/> から Hudson WAR をダウンロードします。
  2. コマンド プロンプトを開いて Hudson WAR が格納されているフォルダーに移動し、Hudson WAR を実行します。 たとえば、version 3.1.2 をダウンロードした場合は、次のコマンドを実行します。
     
      `java -jar hudson-3.1.2.war`

  3. ブラウザーで `http://localhost:8080/`を開きます。 これで Hudson ダッシュボードが開かれます。
  4. Hudson を初めて使用するときに、 `http://localhost:8080/`で初期セットアップを完了します。
  5. 初期セットアップの完了後、Hudson WAR の実行中のインスタンスを取り消し、Hudson WAR を再度起動します。次に、Azure Storage プラグインのインストールと構成を行うために、Hudson ダッシュボード (`http://localhost:8080/`) を再度開きます。
     
      通常の Hudson CI ソリューションであればサービスとして実行されるように設定しますが、このチュートリアルではコマンド ラインで Hudson war を実行するだけで十分です。
* Azure アカウント。 Azure アカウントには、<https://www.azure.com> でサインアップできます。
* Azure ストレージ アカウント。 まだストレージ アカウントを取得していない場合には、「 [ストレージ アカウントを作成する](../common/storage-quickstart-create-account.md)」に記載の手順に従って作成できます。
* 以降では、Hudson CI のビルド アーティファクトで Blob service をリポジトリとして使用するうえで必要な手順を、基本的な例を使って説明しています。Hudson CI ソリューションにある程度習熟していることが望ましいものの、必須ではありません。

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>Hudson CI で Blob service を使用する方法
Hudson で Blob service を使用するには、Azure Storage プラグインをインストールし、そのプラグインを構成してストレージ アカウントを使用するようにしたうえで、ビルド後にビルド アーティファクトをストレージ アカウントにアップロードするアクションを作成する必要があります。 以降のセクションでは、ここに挙げた手順について説明します。

## <a name="how-to-install-the-azure-storage-plugin"></a>Azure Storage プラグインのインストール方法
1. Hudson ダッシュボードで、 **[Manage Hudson]** をクリックします。
2. **[Manage Hudson]** ページで **[Manage Plugins]** をクリックします。
3. **[Available]** タブをクリックします。
4. **[Others]** をクリックします。
5. **[Artifact Uploaders]** セクションで、 **[Microsoft Azure Storage plugin]** を選択します。
6. **[インストール]** をクリックします。
7. インストール完了後、Hudson を再起動します。

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Azure Storage プラグインを構成してストレージ アカウントを使用する方法
1. Hudson ダッシュボードで、 **[Manage Hudson]** をクリックします。
2. **[Manage Hudson]** ページで **[Configure System]** をクリックします。
3. **[Microsoft Azure Storage Account Configuration]** セクションで、次の操作を行います。
   
    a. [Azure Portal](https://portal.azure.com) で取得したストレージ アカウント名を入力します。
   
    b. 同様に、[Azure Portal](https://portal.azure.com) で取得したストレージ アカウント キーを入力します。
   
    c. パブリック Azure クラウドを使用している場合、 **[Blob Service Endpoint URL]** には既定値を使用します。 これとは異なる Azure クラウドを使用している場合には、[Azure Portal](https://portal.azure.com) でストレージ アカウント用に指定されたエンドポイントを使用します。
   
    d. **[Validate storage credentials]** をクリックしてストレージ アカウントを検証します。
   
    e. [省略可能] Hudson CI で利用できるストレージ アカウントを追加する場合は、 **[Add more storage accounts]** をクリックします。
   
    f. **[Save]** をクリックして設定を保存します。

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>ビルド後にビルド アーティファクトをストレージ アカウントにアップロードするアクションの作成方法
説明のため、ストレージ アカウントにファイルをアップロードするためのビルド後のアクションを追加する前に、複数のファイルを作成するジョブを作成する必要があります。

1. Hudson ダッシュボードで、 **[New Job]** をクリックします。
2. ジョブの名前を **MyJob** に設定し、 **[Build a free-style software job]** 、 **[OK]** の順にクリックします。
3. ジョブ構成の **[Build]** セクションで **[Add build step]** をクリックした後、 **[Execute Windows batch command]** を選択します。
4. **[Command]** で次のコマンドを使用します。

    ```   
        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
    ```

5. ジョブ構成の **[Post-build Actions]** セクションで **[Upload artifacts to Microsoft Azure Blob storage]** をクリックします。
6. **[Storage Account Name]** では、使用するストレージ アカウントを選択します。
7. **[Container Name]** では、コンテナー名を指定します(コンテナーは、ビルド アーティファクトをアップロードする時点で存在していなければ、自動で作成されます)。 (コンテナーは、ビルド アーティファクトをアップロードする時点で存在していなければ、自動で作成されます)。環境変数を使用することもできます。この例では、コンテナー名に「 **${JOB_NAME}** 」と入力します。
   
    **ヒント**
   
    **[Execute Windows batch command]** にスクリプトを入力した **[Command]** セクションの下には、Hudson が認識できる環境変数へのリンクがあります。 環境変数の名前および説明を確認するには、リンクをクリックします。 **BUILD_URL** など、特殊文字が含まれる環境変数は、コンテナー名および共通仮想パスに使用できません。
8. この例では **[Make new container public by default]** をクリックします。 (プライベート コンテナーを使用する場合には、Shared Access Signature を作成してアクセスを許可する必要があります。 ただし、この点についてはこの記事では取り扱いません。 Shared Access Signature の詳細については、「[Shared Access Signatures (SAS) の使用](../storage-dotnet-shared-access-signature-part-1.md)」を参照してください。)
9. [省略可能] ビルド アーティファクトをアップロードする前にコンテナーの内容をクリアする場合、 **[Clean container before uploading]** をクリックします (コンテナーの内容をクリアしない場合は、チェック ボックスをオフにします)。
10. **[List of Artifacts to upload]\(アップロードするアーティファクトのリスト\)** では、「**text/*.txt**」と入力します。
11. **[Common virtual path for uploaded artifacts]** では、「 **${BUILD\_ID}/${BUILD\_NUMBER}** 」と入力します。
12. **[Save]** をクリックして設定を保存します。
13. Hudson ダッシュボードで、 **[Build Now]** をクリックして **MyJob** を実行します。 コンソール出力でステータスを確認します。 ビルド後のアクションによってビルド アーティファクトのアップロードが開始されると、コンソール出力に Azure Storage に関するステータス メッセージが表示されます。
14. ジョブが正常に完了すると、パブリック BLOB を開いてビルド アーティファクトを確認できます。
    
    a. [Azure Portal](https://portal.azure.com) にサインインします。
    
    b. **[Storage]** をクリックします。
    
    c. Hudson に使用したストレージ アカウント名をクリックします。
    
    d. **[コンテナー]** をクリックします。
    
    e. **myjob** という名前のコンテナーをクリックします。これは、Hudson ジョブを作成したときに割り当てたジョブ名を小文字にしたものです。 Azure Storage では、コンテナー名と BLOB 名は小文字です (大文字と小文字は区別されます)。 **myjob** という名前のコンテナーの BLOB の一覧に、**hello.txt** と **date.txt** の 2 つがあります。 そのどちらかの URL をコピーして、ブラウザーで開きます。 このテキスト ファイルがビルド アーティファクトとしてアップロードされていることがわかります。

アーティファクトを Azure BLOB ストレージにアップロードするビルド後のアクションは、ジョブごとに 1 つのみ作成できます。 **[List of Artifacts to upload]** でセミコロンを区切り記号として使用することで、アーティファクトを Azure BLOB ストレージにアップロードするビルド後のアクション 1 つに、(ワイルドカードを含む) 複数のファイルとファイル パスを指定できます。 たとえば、Hudson ビルドによってワークスペースの **build** フォルダーに JAR ファイルと TXT ファイルが生成され、これら両方のファイルを Azure Blob Storage にアップロードする場合は、 **[List of Artifacts to upload]** の値に「**build/\*.jar;build/\*.txt**」を使用します。 また、2 重コロンの構文を使用すると、BLOB 名で使用するパスを指定できます。 たとえば、JAR ファイルのアップロードに BLOB パス内の **binaries** を使用し、TXT ファイルのアップロードに BLOB パス内の **notices** を使用する場合、 **[List of Artifacts to upload]** の値として **build/\*.jar::binaries;build/\*.txt::notices** を使用します。

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Azure BLOB ストレージからのダウンロードを実行するビルド手順の作成方法
次の手順では、Azure BLOB ストレージから項目をダウンロードするビルド手順を構成する方法を示します。 この手順は、Azure BLOB ストレージに保持している JAR ファイルなどの項目をビルドに含める場合に便利です。

1. ジョブ構成の **[Build]** セクションで **[Add build step]** をクリックし、 **[Download from Azure Blob storage]** を選択します。
2. **[Storage Account Name]** では、使用するストレージ アカウントを選択します。
3. **[Container name]** では、ダウンロードする BLOB が格納されているコンテナーの名前を指定します。 環境変数を使用できます。
4. **[Blob name]** には BLOB 名を指定します。 環境変数を使用できます。 また、アスタリスクを使用して、BLOB 名の先頭文字 (複数可) の後にワイルドカードを指定できます。 たとえば「**project\\** *」と入力すると、名前が "**project**" で始まる BLOB がすべて指定されます。
5. [省略可能] **[Download path]** では、Azure Blob Storage のファイルのダウンロード先となる、Hudson を実行しているコンピューター上のパスを指定します。 Azure BLOB ストレージのファイルはこのパスにダウンロードされます。 環境変数も使用できます( **[Download path]** に値を入力しない場合、Azure Blob Storage のファイルは、ジョブのワークスペースにダウンロードされます)。

Azure BLOB ストレージからダウンロードする項目が他にもある場合は、追加のビルド手順を作成できます。

目的の BLOB が正常にダウンロードされていることを確かめるには、ビルドを実行した後に、ビルド履歴のコンソール出力またはダウンロード先を確認します。

## <a name="components-used-by-the-blob-service"></a>Blob service が使用するコンポーネント
以下では、Blob service コンポーネントの概要を説明します。

* **ストレージ アカウント**: Azure のストレージにアクセスする場合には必ず、ストレージ アカウントを使用します。 これは、アクセスする BLOB の名前空間の中でも最高レベルに位置するものです。 アカウントに格納できるコンテナーの数は、コンテナーの合計サイズが 100 TB (テラバイト) 未満である限り無制限です。
* **コンテナー**:コンテナーは、BLOB のセットをグループ化します。 すべての BLOB はコンテナーに格納されている必要があります。 1 つのアカウントに格納できるコンテナーの数は無制限です。 また、1 つのコンテナーに保存できる BLOB の数も無制限です。
* **BLOB**:任意の種類およびサイズのファイルです。 Azure Storage に格納できる BLOB には、ブロック BLOB とページ BLOB の 2 種類があります。 ほとんどのファイルはブロック BLOB です。 1 つのブロック BLOB には、最大で 200 GB までのデータを格納できます。 このチュートリアルでは、 ブロック BLOB を使用します。 もう 1 つの種類の BLOB であるページ BLOB には、最大 1 TB までのデータを格納できます。ファイルのバイト数の範囲が頻繁に変更される場合には、こちらの方が効率的です。 BLOB の詳細については、「[ブロック BLOB、追加 BLOB、ページ BLOB について](https://msdn.microsoft.com/library/azure/ee691964.aspx)」をご覧ください。
* **URL 形式**:BLOB は、次の URL 形式を使用してアドレスを指定し、アクセスできます。
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (ここに挙げた形式は、パブリック Azure クラウドに適用されるものです。 これとは異なる Azure クラウドを使用している場合は、[Azure Portal](https://portal.azure.com) 内のエンドポイントを使用して URL エンドポイントを指定します。)
  
    この形式では、`storageaccount` はストレージ アカウントの名前、`container_name` はコンテナーの名前、`blob_name` は BLOB の名前をそれぞれ表します。 コンテナー名にはパスを複数使用することができます。その場合には、スラッシュ **/** で区切ります。 このチュートリアルで例に使用したコンテナー名は **MyJob**、共通仮想パスは **${BUILD\_ID}/${BUILD\_NUMBER}** でした。このため、BLOB の URL は次のようになります。
  
    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>次の手順
* [Meet Hudson (Hudson について)](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
* [Azure Storage SDK for Java](https://github.com/azure/azure-storage-java)
* [Azure ストレージ クライアント SDK リファレンス](http://dl.windowsazure.com/storage/javadoc/)
* [Azure Storage Services REST API (Azure Storage サービスの REST API)](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure のストレージ チーム ブログ](https://blogs.msdn.com/b/windowsazurestorage/)

詳細については、「[Azure for Java developers (Java 開発者向けの Azure)](/java/azure)」を参照してください。
