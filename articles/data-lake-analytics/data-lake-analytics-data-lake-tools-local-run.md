---
title: ローカル コンピューター上で Azure Data Lake U-SQL スクリプトを実行する
description: Azure Data Lake Tools for Visual Studio を使用してローカル コンピューター上で U-SQL ジョブを実行する方法について説明します。
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/03/2018
ms.openlocfilehash: 5f9410cc91174420662bb5efc67c8904b5d5e647
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018989"
---
# <a name="run-u-sql-scripts-on-your-local-machine"></a>ローカル コンピューターで U-SQL スクリプトを実行する

U-SQL スクリプトを開発すると、スクリプトをローカルで実行できるので時間と費用を節約できます。 Azure Data Lake Tools for Visual Studio は、ローカル コンピューター上での U-SQL スクリプトの実行をサポートしています。 

## <a name="basic-concepts-for-local-runs"></a>ローカル実行の基本的な概念

次の表は、ローカル実行のためのコンポーネント、およびこれらのコンポーネントとクラウド実行との対応を示しています。

|コンポーネント|ローカルの実行|クラウド実行|
|---------|---------|---------|
|ストレージ|ローカル データ ルート フォルダー|既定の Azure Data Lake Store アカウント|
|Compute|U-SQL ローカル実行エンジン|Azure Data Lake Analytics サービス|
|実行環境|ローカル コンピューター上の作業ディレクトリ|Azure Data Lake Analytics クラスター|

以下のセクションでは、ローカル実行コンポーネントの詳細について説明します。

### <a name="local-data-root-folders"></a>ローカル データ ルート フォルダー

ローカル データ ルート フォルダーは、ローカル コンピューティング アカウント用の **ローカル ストア** です。 ローカル コンピューター上のローカル ファイル システム内のすべてのフォルダーをローカル データ ルート フォルダーにすることができます。 これは、Data Lake Analytics アカウントの既定の Azure Data Lake Store アカウントと同じです。 別のデータ ルート フォルダーへの切り替えは、別の既定のストア アカウントへの切り替えと同じです。 

データ ルート フォルダーは、次のように使用されます。
- メタデータを格納します。 たとえば、データベース、テーブル、テーブル値関数、アセンブリなどです。
- U-SQL スクリプトで相対パスとして定義されている入力および出力パスを検索します。 相対パスを使用すると、U-SQL スクリプトを Azure にデプロイする処理が容易になります。

### <a name="u-sql-local-run-engines"></a>U-SQL ローカル実行エンジン

U-SQL ローカル実行エンジンは、U-SQL ジョブ用の **ローカル コンピューティング アカウント** です。 ユーザーは、Azure Data Lake Tools for Visual Studio を使用して U-SQL ジョブをローカルで実行できます。 また、ローカル実行は Azure Data Lake U-SQL SDK コマンド ラインやプログラミング インターフェイスでもサポートされています。 詳細については、[Azure Data Lake U-SQL SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) に関するページを参照してください。

### <a name="working-directories"></a>作業ディレクトリ

U-SQL スクリプトを実行する場合、コンパイル結果のキャッシュ、ログの実行などの機能を実行するための作業ディレクトリ フォルダーが必要です。 Azure Data Lake Tools for Visual Studio では、作業ディレクトリは U-SQL プロジェクトの作業ディレクトリです。 このディレクトリは `<U-SQL project root path>/bin/debug>` 以下にあります。 作業ディレクトリは、新しい実行がトリガーされるたびにクリーニングされます。

## <a name="local-runs-in-microsoft-visual-studio"></a>Microsoft Visual Studio のローカル実行

Azure Data Lake Tools for Visual Studio には、ローカル実行エンジンが組み込まれています。 これらのツールはエンジンをローカル コンピューティング アカウントとして扱います。 U-SQL スクリプトをローカルで実行するには、スクリプトのエディターの端にあるドロップダウンで **Local-machine** または **Local-project** アカウントを選択します。 次に **[送信]** を選択します。

![U-SQL スクリプトをローカル アカウントに送信する](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-runs-with-a-local-machine-account"></a>Local-machine アカウントを使用したローカル実行

**Local-machine** アカウントは、ローカル ストア アカウントとして 1 つのローカル データ ルート フォルダーを持つ共有ローカル コンピューティング アカウントです。 既定で、データ ルート フォルダーは **C:\Users\<username>\AppData\Local\USQLDataRoot** です。 **[ツール]**  >  **[Data Lake]**  >  **[オプションと設定]** で構成することもできます。

![ローカル データ ルート フォルダーを構成する](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
ローカル実行には U-SQL プロジェクトが必要です。 U-SQL プロジェクトの作業ディレクトリは、U-SQL ローカル実行の作業ディレクトリに使用されます。 ローカル実行中にコンパイル結果、実行ログ、その他のジョブの実行に関連したファイルが生成され、作業ディレクトリ フォルダーの下に格納されます。 スクリプトを再実行するたびに、作業ディレクトリ内のこれらのファイルはすべてクリーニングされ、再生成されます。

## <a name="local-runs-with-a-local-project-account"></a>Local-project アカウントを使用したローカル実行

**Local-project** アカウントは、分離されたローカル データ ルート フォルダーを持つ、各プロジェクトのプロジェクトごとに分離されたローカル コンピューティング アカウントです。 Visual Studio のソリューション エクスプローラーで開いているすべてのアクティブな U-SQL プロジェクトには、対応する `(Local-project: <project name>)` アカウントがあります。 アカウントは、Visual Studio のサーバー エクスプローラーと U-SQL スクリプト エディターの余白の両方に表示されます。  

**Local-project** アカウントを使用すると、クリーンな分離開発環境を実現できます。 **Local-machine** アカウントには、すべてのローカル ジョブのメタデータと入出力データを格納する共有のローカル データ ルート フォルダーがあります。 ただし、**Local-project** アカウントの場合、U-SQL スクリプトが実行されるたびに、U-SQL プロジェクトの作業ディレクトリ以下に一時的なローカル データ ルート フォルダーが作成されます。 この一時データ ルート フォルダーは、リビルドまたは再実行が発生するとクリーニングされます。 

U-SQL プロジェクトは、プロジェクトの参照とプロパティを使用して分離ローカル実行環境を管理します。 プロジェクト内の U-SQL スクリプトの入力データ ソースと、参照されるデータベース環境の両方を構成できます。

### <a name="manage-the-input-data-source-for-a-local-project-account"></a>Local-project アカウントの入力データ ソースを管理する 

U-SQL プロジェクトはローカル データ ルート フォルダーを作成し、**Local-project** アカウントのデータを設定します。 一時データ ルート フォルダーは、リビルドおよびローカル実行が発生するたびにクリーニングされ、U-SQL プロジェクトの作業ディレクトリの下に再作成されます。 ローカル ジョブの実行の前に、U-SQL プロジェクトによって構成されたすべてのデータ ソースがこの一時ローカル データ ルート フォルダーにコピーされます。 

データ ソースのルート フォルダーを構成できます。 **[U-SQL プロジェクト] を右クリック** >  **[プロパティ]**  >  **[Test Data Source]** の順に選択します。 **Local-project** アカウントで U-SQL スクリプトを実行する場合は、**Test Data Source** フォルダー内のすべてのファイルおよびサブフォルダーが一時ローカル データ ルート フォルダーにコピーされます。 サブフォルダー以下のファイルも含まれます。 ローカル ジョブの実行後、その出力結果も、プロジェクトの作業ディレクトリ内の一時ローカル データ ルート フォルダーの下に見つけることができます。 プロジェクトがリビルドされ、クリーニングされると、このすべての出力が削除され、クリーニングされます。 

![プロジェクトのテスト データ ソースを構成する](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-a-referenced-database-environment-for-a-local-project-account"></a>**Local-project** アカウントの参照されるデータベース環境を管理する 

U-SQL クエリで U-SQL データベース オブジェクトを使用するか、またはそれに対してクエリを実行する場合は、この U-SQL スクリプトをローカルで実行する前に、データベース環境をローカルで使用できるように準備する必要があります。 **Local-project** アカウントの場合は、U-SQL データベースの依存関係を U-SQL プロジェクト参照によって管理できます。 U-SQL プロジェクトに U-SQL データベース プロジェクト参照を追加できます。 **Local-project** アカウントで U-SQL スクリプトを実行する前に、参照されるすべてのデータベースが一時ローカル データ ルート フォルダーにデプロイされます。 さらに、実行のたびに、一時データ ルート フォルダーが新しい分離された環境としてクリーニングされます。

以下の関連する記事を参照してください。
* U-SQL データベースの定義と参照を管理する方法については、[U-SQL データベース プロジェクト](data-lake-analytics-data-lake-tools-develop-usql-database.md)に関するページを参照してください。

## <a name="the-difference-between-local-machine-and-local-project-accounts"></a>**Local-machine** アカウントと **Local-project** アカウントの違い

**Local-machine** アカウントは、ユーザーのローカル コンピューター上で Azure Data Lake Analytics アカウントをシミュレートします。 ここでは、Azure Data Lake Analytics アカウントと同じエクスペリエンスが共有されます。 **Local-project** アカウントは、ユーザー フレンドリなローカル開発環境を提供します。 この環境を利用すると、スクリプトをローカルで実行する前にデータベースの参照と入力データをデプロイできます。 **Local-machine** アカウントは、すべてのプロジェクトからアクセスできる、共有された永続的な環境を提供します。 **Local-project** アカウントは、プロジェクトごとの分離開発環境を提供します。 これは実行のたびに更新されます。 **Local-project** アカウントは、新しい変更をすばやく適用することによって、より高速な開発エクスペリエンスを提供します。

**Local-machine** アカウントと **Local-project** アカウントのその他の違いについては、次の表を参照してください。

|違いの観点|Local-machine|Local-project|
|----------------|---------------|---------------|
|ローカル アクセス|すべてのプロジェクトからアクセスできます。|このアカウントにアクセスできるのは対応するプロジェクトだけです。|
|ローカル データ ルート フォルダー|永続的なローカル フォルダー。 **[ツール]**  >  **[Data Lake]**  >  **[オプションと設定]** で構成されます。|U-SQL プロジェクトの作業ディレクトリの下の、ローカル実行のたびに作成される一時フォルダー。 このフォルダーは、リビルドまたは再実行が発生するとクリーニングされます。|
|U-SQL スクリプトの入力データ|永続的なローカル データ ルート フォルダーの下の相対パス。|**U-SQL プロジェクトの [プロパティ]**  >  **[Test Data Source]** で設定されます。 ローカル実行の前に、すべてのファイルおよびサブフォルダーが一時データ ルート フォルダーにコピーされます。|
|U-SQL スクリプトの出力データ|永続的なローカル データ ルート フォルダーの下の相対パス。|一時データ ルート フォルダーに出力されます。 これらの結果は、リビルドまたは再実行が発生するとクリーニングされます。|
|参照されるデータベースのデプロイ|**Local-machine** アカウントに対して実行する場合、参照されるデータベースは自動的にはデプロイされません。 Azure Data Lake Analytics アカウントへの送信の場合も同じです。|参照されるデータベースは、ローカル実行の前に **Local-project** アカウントに自動的にデプロイされます。 リビルドまたは再実行が発生すると、すべてのデータベース環境がクリーニングされ、再デプロイされます。|

## <a name="a-local-run-with-the-u-sql-sdk"></a>U-SQL SDK でのローカル実行

ローカルの Visual Studio で U-SQL スクリプトを実行できます。また、Azure Data Lake U-SQL SDK を使用して、コマンド ラインやプログラミング インターフェイスで U-SQL スクリプトをローカルで実行することもできます。 これらのインターフェイス経由で、U-SQL のローカル実行やテストを自動化できます。

詳細については、[Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md) に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Data Lake Analytics の CI/CD パイプラインをセットアップする方法](data-lake-analytics-cicd-overview.md)。
- [Azure Data Lake Analytics コードをテストする方法](data-lake-analytics-cicd-test.md)。
