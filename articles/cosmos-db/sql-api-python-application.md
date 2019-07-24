---
title: Azure Cosmos DB を使用する Python Flask Web アプリケーションのチュートリアル
description: Azure Cosmos DB を使用してデータを格納し、Azure にホストされた Python Flask Web アプリケーションからそのデータにアクセスする方法をデータベース チュートリアルで確認します。 アプリケーション開発ソリューションを探します。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: tutorial
ms.date: 02/23/2017
ms.author: sngun
ms.openlocfilehash: 24426a4249934af1d85f43552617cda7c1d7e172
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58883889"
---
# <a name="build-a-python-flask-web-application-using-azure-cosmos-db"></a>Azure Cosmos DB を使用した Python Flask Web アプリケーションの作成

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

このチュートリアルでは、Azure Cosmos DB を使用してデータを保存し、Azure App Service でホストされている Python Flask Web アプリケーションからアクセスする方法について説明します。 このチュートリアルでは、Python と Azure Websites の使用経験がある読者を想定しています。

このデータベース チュートリアルの内容:

1. Azure Cosmos DB アカウントを作成してプロビジョニングする。
2. Python Flask アプリケーションを作成する。
3. Web アプリケーションから Azure Cosmos DB に接続して使用する。
4. Web アプリケーションを Azure App Service にデプロイする。

このチュートリアルの手順を実行すると、アンケートに回答する単純な投票アプリケーションを作成できます。

![このデータベース チュートリアルで作成する投票アプリケーションのスクリーンショット](./media/sql-api-python-application/cosmos-db-pythonr-run-application.png)

## <a name="database-tutorial-prerequisites"></a>データベース チュートリアルの前提条件
この記事の手順を実行する前に、次のソフトウェアがインストールされていることを確認してください。

* [Azure サブスクリプション](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* **Azure デプロイメント**および **Python デプロイメント**が有効になっている [Visual Studio 2017](https://www.visualstudio.com/downloads/)。 これらの前提条件がインストールされているかどうかを確認し、ローカルで **Visual Studio インストーラー**を開いてインストールできます。   
* [Microsoft Azure SDK for Python 2.7](https://azure.microsoft.com/downloads/) 
* [Python 2.7](https://www.python.org/downloads/windows/)。 32 ビットまたは 64 ビットのインストールを使用できます。

> [!IMPORTANT]
> 初めて Python 2.7 をインストールする場合は、[Customize Python 2.7.13] 画面で必ず **[Add python.exe to Path]\(python.exe をパスに追加する\)** を選択してください。
> 
> ![[Customize Python 2.7.11] 画面のスクリーンショット。[Add python.exe to Path]\(python.exe をパスに追加する\) の選択が必要](./media/sql-api-python-application/cosmos-db-python-install.png)
> 
> 

* [Python 2.7 用の Microsoft Visual C++ コンパイラ](https://www.microsoft.com/en-us/download/details.aspx?id=44266)。

## <a name="step-1-create-an-azure-cosmos-db-database-account"></a>手順 1: Azure Cosmos DB データベース アカウントを作成する
まず最初に、Azure Cosmos DB アカウントを作成します。 アカウントが既にある場合や、このチュートリアルで Azure Cosmos DB Emulator を使用する場合は、「[手順 2: 新しい Python Flask Web アプリケーションを作成する](#step-2-create-a-new-python-flask-web-application)」をスキップできます。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<br/>
次は、新しい Python Flask Web アプリケーションをゼロから作成する方法について確認しましょう。

## <a name="step-2-create-a-new-python-flask-web-application"></a>手順 2: 新しい Python Flask Web アプリケーションを作成する
1. Visual Studio で、**[ファイル]** メニューの **[新規作成]** をポイントし、**[プロジェクト]** をクリックします。
   
    **[新しいプロジェクト]** ダイアログ ボックスが表示されます。
2. 左側のウィンドウで、**[テンプレート]**、**[Python]**、**[Web]** の順に展開します。 
3. 中央のウィンドウで、**[Flask Web Project]** を選択し、**[名前]** ボックスに「**tutorial**」と入力して、**[OK]** をクリックします。 [Python コードのスタイル ガイド](https://www.python.org/dev/peps/pep-0008/#package-and-module-names)に関するページで説明されているように、Python パッケージの名前はすべて小文字にする必要があることに注意してください。
   
    Python Flask は、Python で Web アプリケーションを短時間で作成するための Web アプリケーション開発フレームワークです。
   
    ![Visual Studio の [新しいプロジェクト] ウィンドウのスクリーンショット。左側で [Python] が強調表示され、中央で [Python Flask Web プロジェクト] が選択され、[名前] ボックスに tutorial という名前が入力されている](./media/sql-api-python-application/image9.png)
4. **[Python Tools for Visual Studio]** ウィンドウで、**[Install into a virtual environment]** をクリックします。 
   
    ![[database tutorial - Python Tools for Visual Studio] ウィンドウのスクリーンショット](./media/sql-api-python-application/python-install-virtual-environment.png)
5. **[仮想環境の追加]** ウィンドウの [Select an interpreter]\(インタープリターの選択\) ボックスで [Python 2.7] または [Python 3.5] を選択し、他の既定値を受け入れて **[作成]** をクリックします。 これで、プロジェクトに必要な Python 仮想環境が設定されます。
   
    ![[database tutorial - Python Tools for Visual Studio] ウィンドウのスクリーンショット](./media/sql-api-python-application/image10_A.png)
   
    環境が正常にインストールされると、出力ウィンドウに " `Successfully installed Flask-0.10.1 Jinja2-2.8 MarkupSafe-0.23 Werkzeug-0.11.5 itsdangerous-0.24 'requirements.txt' was installed successfully.` " と表示されます。

## <a name="step-3-modify-the-python-flask-web-application"></a>手順 3: Python Flask Web アプリケーションを変更する
### <a name="add-the-python-flask-packages-to-your-project"></a>プロジェクトへの Python Flask パッケージの追加
プロジェクトを設定した後は、プロジェクトに必要な Flask パッケージ (Azure Cosmos DB の SQL API 用の Python パッケージ pydocumentdb など) を追加する必要があります。

1. ソリューション エクスプローラーで、 **requirements.txt** という名前のファイルを開き、既存のコードを次のコードに置き換えます。
   
        flask==0.9
        flask-mail==0.7.6
        sqlalchemy==0.7.9
        flask-sqlalchemy==0.16
        sqlalchemy-migrate==0.7.2
        flask-whooshalchemy==0.55a
        flask-wtf==0.8.4
        pytz==2013b
        flask-babel==0.8
        flup
        pydocumentdb>=1.0.0
2. **requirements.txt** ファイルを保存します。 
3. ソリューション エクスプローラーで、**[env]** を右クリックし、**[Install from requirements.txt]** をクリックします。
   
    ![[env (Python 2.7)] を示すスクリーンショット。リストで [requirements.txt からインストール] が選択されている](./media/sql-api-python-application/cosmos-db-python-install-from-requirements.png)
   
    正常にインストールされると、出力ウィンドウに次のメッセージが表示されます。
   
        Successfully installed Babel-2.3.2 Tempita-0.5.2 WTForms-2.1 Whoosh-2.7.4 blinker-1.4 decorator-4.0.9 flask-0.9 flask-babel-0.8 flask-mail-0.7.6 flask-sqlalchemy-0.16 flask-whooshalchemy-0.55a0 flask-wtf-0.8.4 flup-1.0.2 pydocumentdb-1.6.1 pytz-2013b0 speaklater-1.3 sqlalchemy-0.7.9 sqlalchemy-migrate-0.7.2
   
   > [!NOTE]
   > まれに、出力ウィンドウにエラーが表示されることがあります。 その場合は、エラーがクリーンアップに関連しているかどうか確認してください。 クリーンアップには失敗しても、インストールは正常に行われる場合があります (出力ウィンドウを上方向へスクロールして確認してください)。 [仮想環境を確認する](#verify-the-virtual-environment)ことで、インストールを確認できます。 インストールに失敗しても、確認に成功した場合は、続行してかまいません。
   > 
   > 

### <a name="verify-the-virtual-environment"></a>仮想環境の確認
すべてが正しくインストールされているかどうかを確認してみましょう。

1. **Ctrl** + **Shift** + **B** キーを押して、ソリューションをビルドします。
2. ビルドが成功したら、 **F5**キーを押して、Web サイトを開きます。 Flask 開発サーバーが起動し、Web ブラウザーが開きます。 次のページが表示されます。
   
    ![空の Python Flask Web 開発プロジェクトがブラウザーで表示されます](./media/sql-api-python-application/image12.png)
3. Visual Studio で **Shift** + **F5** キーを押して、Web サイトのデバッグを停止します。

### <a name="create-database-collection-and-document-definitions"></a>データベース、コレクション、およびドキュメント定義の作成
これで、新しいファイルを追加し、既存のファイルを更新して、投票アプリケーションを作成できます。

1. ソリューション エクスプローラーで、**tutorial** プロジェクトを右クリックし、**[追加]**、**[新しい項目]** の順にクリックします。 **[Empty Python File]** を選択し、ファイルに **forms.py** という名前を付けます。  
2. forms.py ファイルに次のコードを追加し、ファイルを保存します。

```python
from flask_wtf import Form
from wtforms import RadioField

class VoteForm(Form):
    deploy_preference  = RadioField('Deployment Preference', choices=[
        ('Web Site', 'Web Site'),
        ('Cloud Service', 'Cloud Service'),
        ('Virtual Machine', 'Virtual Machine')], default='Web Site')
```


### <a name="add-the-required-imports-to-viewspy"></a>views.py への必要な import ステートメントの追加
1. ソリューション エクスプローラーで、**tutorial** フォルダーを展開し、**views.py** ファイルを開きます。 
2. 次の import ステートメントを **views.py** ファイルの先頭に追加し、ファイルを保存します。 これらのステートメントにより、Azure Cosmos DB の PythonSDK および Flask パッケージがインポートされます。
   
    ```python
    from forms import VoteForm
    import config_cosmos
    import pydocumentdb.document_client as document_client
    ```

### <a name="create-database-collection-and-document"></a>データベース、コレクション、およびドキュメントの作成
* 引き続き **views.py**で、次のコードをファイルの末尾に追加します。 これにより、フォームで使用されるデータベースの作成が処理されます。 **views.py**内の既存のコードを削除しないように注意してください。 単にこのコードを末尾に追加してください。

```python
@app.route('/create')
def create():
    """Renders the contact page."""
    client = document_client.DocumentClient(config_cosmos.COSMOSDB_HOST, {'masterKey': config_cosmos.COSMOSDB_KEY})

    # Attempt to delete the database.  This allows this to be used to recreate as well as create
    try:
        db = next((data for data in client.ReadDatabases() if data['id'] == config_cosmos.COSMOSDB_DATABASE))
        client.DeleteDatabase(db['_self'])
    except:
        pass

    # Create database
    db = client.CreateDatabase({ 'id': config_cosmos.COSMOSDB_DATABASE })

    # Create collection
    collection = client.CreateCollection(db['_self'],{ 'id': config_cosmos.COSMOSDB_COLLECTION })

    # Create document
    document = client.CreateDocument(collection['_self'],
        { 'id': config_cosmos.COSMOSDB_DOCUMENT,
          'Web Site': 0,
          'Cloud Service': 0,
          'Virtual Machine': 0,
          'name': config_cosmos.COSMOSDB_DOCUMENT 
        })

    return render_template(
       'create.html',
        title='Create Page',
        year=datetime.now().year,
        message='You just created a new database, collection, and document.  Your old votes have been deleted')
```


### <a name="read-database-collection-document-and-submit-form"></a>データベース、コレクション、およびドキュメントの読み取りとフォームの送信
* 引き続き **views.py**で、次のコードをファイルの末尾に追加します。 このコードでは、フォームの設定と、データベース、コレクション、およびドキュメントの読み取りを行っています。 **views.py**内の既存のコードを削除しないように注意してください。 単にこのコードを末尾に追加してください。

```python
@app.route('/vote', methods=['GET', 'POST'])
def vote(): 
    form = VoteForm()
    replaced_document ={}
    if form.validate_on_submit(): # is user submitted vote  
        client = document_client.DocumentClient(config_cosmos.COSMOSDB_HOST, {'masterKey': config_cosmos.COSMOSDB_KEY})

        # Read databases and take first since id should not be duplicated.
        db = next((data for data in client.ReadDatabases() if data['id'] == config_cosmos.COSMOSDB_DATABASE))

        # Read collections and take first since id should not be duplicated.
        coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config_cosmos.COSMOSDB_COLLECTION))

        # Read documents and take first since id should not be duplicated.
        doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config_cosmos.COSMOSDB_DOCUMENT))

        # Take the data from the deploy_preference and increment our database
        doc[form.deploy_preference.data] = doc[form.deploy_preference.data] + 1
        replaced_document = client.ReplaceDocument(doc['_self'], doc)

        # Create a model to pass to results.html
        class VoteObject:
            choices = dict()
            total_votes = 0

        vote_object = VoteObject()
        vote_object.choices = {
            "Web Site" : doc['Web Site'],
            "Cloud Service" : doc['Cloud Service'],
            "Virtual Machine" : doc['Virtual Machine']
        }
        vote_object.total_votes = sum(vote_object.choices.values())

        return render_template(
            'results.html', 
            year=datetime.now().year, 
            vote_object = vote_object)

    else :
        return render_template(
            'vote.html', 
            title = 'Vote',
            year=datetime.now().year,
            form = form)
```


### <a name="create-the-html-files"></a>HTML ファイルの作成
1. ソリューション エクスプローラーで、**tutorial** フォルダー内の **templates** フォルダーを右クリックし、**[追加]**、**[新しい項目]** の順にクリックします。 
2. **[HTML ページ]** を選択し、[名前] ボックスに「**create.html**」と入力します。 
3. 手順 1. と 2. を繰り返して、results.html と vote.html という 2 つの追加の HTML ファイルを作成します。
4. 次のコードを **create.html** in the `<body>` 要素に追加します。 このコードは、新しいデータベース、コレクション、およびドキュメントを作成したことを示すメッセージを表示します。
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```
5. 次のコードを **results.html** の `<body`> 要素に追加します。 このコードは、投票の結果を表示します。
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Results of the vote</h2>
        <br />
   
    {% for choice in vote_object.choices %}
    <div class="row">
        <div class="col-sm-5">{{choice}}</div>
            <div class="col-sm-5">
                <div class="progress">
                    <div class="progress-bar" role="progressbar" aria-valuenow="{{vote_object.choices[choice]}}" aria-valuemin="0" aria-valuemax="{{vote_object.total_votes}}" style="width: {{(vote_object.choices[choice]/vote_object.total_votes)*100}}%;">
                                {{vote_object.choices[choice]}}
                </div>
            </div>
            </div>
    </div>
    {% endfor %}
   
    <br />
    <a class="btn btn-primary" href="{{ url_for('vote') }}">Vote again?</a>
    {% endblock %}
    ```
6. 次のコードを **vote.html** の `<body`> 要素に追加します。 このコードは、アンケートを表示し、投票を受け付けます。 投票が登録されると、制御が views.py に渡されます。これにより、Azure Cosmos DB は投票が行われたことを確認して、ドキュメントを適宜追加できます。
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>What is your favorite way to host an application on Azure?</h2>
    <form action="" method="post" name="vote">
        {{form.hidden_tag()}}
            {{form.deploy_preference}}
            <button class="btn btn-primary" type="submit">Vote</button>
    </form>
    {% endblock %}
    ```
7. **templates** フォルダーの **index.html** の内容を次の内容に置き換えます。 これが、アプリケーションのランディング ページとなります。
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Python + Azure Cosmos DB Voting Application.</h2>
    <h3>This is a sample Cosmos DB voting application using PyDocumentDB</h3>
    <p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```

### <a name="add-a-configuration-file-and-change-the-initpy"></a>構成ファイルの追加と、\_\_init\_\_.py の変更
1. ソリューション エクスプローラーで、**tutorial** プロジェクトを右クリックし、**[追加]**、**[新しい項目]** の順にクリックして、**[Empty Python File]** を選択し、ファイルに **config_cosmos.py** という名前を付けます。 この構成ファイルは、Flask のフォームで必要になります。 これを使用して秘密キーを指定することもできます。 このチュートリアルではこのキーは不要です。
2. 次のコードを config_cosmos.py に追加します。次の手順で **COSMOSDB\_HOST** と **COSMOSDB\_KEY** の値を変更する必要があります。
   
    ```python
    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
   
    COSMOSDB_HOST = 'https://YOUR_COSMOSDB_NAME.documents.azure.com:443/'
    COSMOSDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
   
    COSMOSDB_DATABASE = 'voting database'
    COSMOSDB_COLLECTION = 'voting collection'
    COSMOSDB_DOCUMENT = 'voting document'
    ```
3. [Azure Portal](https://portal.azure.com/) で、**[参照]**、**[Azure Cosmos DB Accounts]\(Azure Cosmos DB アカウント\)** の順にクリックして、**[キー]** ページに移動します。使用するアカウントの名前をダブルクリックし、**[Essentials]** 領域で **[キー]** ボタンをクリックします。 **[キー]** ページで、**[URI]** の値をコピーし、**config.py** ファイルに **COSMOSDB\_HOST** プロパティの値として貼り付けます。 
4. Azure Portal に戻り、**[キー]** ページで、**[プライマリ キー]** または **[セカンダリ キー]** の値をコピーし、**config_cosmos.py** ファイルに **COSMOSDB\_KEY** プロパティの値として貼り付けます。
5. 構成ファイルの読み取りと基本的なログ記録を含めるために、**\_\_init\_\_.py** ファイルに次の行を追加します。 
   
        app.config.from_object('config_cosmos')
        logging.basicConfig(level=logging.INFO,format='%(asctime)s - %(name)s - %(levelname)s - %(message)s')
        logger = logging.getLogger(__name__)
   
    ファイルの内容は次のようになります。
   
    ```python
    import logging
    from flask import Flask
    app = Flask(__name__)
    app.config.from_pyfile('config_cosmos')
    logging.basicConfig(level=logging.INFO,format='%(asctime)s - %(name)s - %(levelname)s - %(message)s')
    logger = logging.getLogger(__name__)

    import tutorial.views
    ```
6. すべてのファイルを追加すると、ソリューション エクスプローラーは次のようになります。
   
    ![Visual Studio ソリューション エクスプローラーのウィンドウのスクリーンショット](./media/sql-api-python-application/cosmos-db-python-solution-explorer.png)

## <a name="step-4-run-your-web-application-locally"></a>手順 4:ローカルで Web アプリケーションを実行する
1. **Ctrl** + **Shift** + **B** キーを押して、ソリューションをビルドします。
2. ビルドが成功したら、 **F5**キーを押して、Web サイトを開きます。 次の画面が表示されます。
   
    ![Python と Azure Cosmos DB を使った投票アプリケーションが Web ブラウザーに表示された状態を示すスクリーンショット](./media/sql-api-python-application/cosmos-db-pythonr-run-application.png)
3. **[投票データベースの作成/クリア]** をクリックして、データベースを生成します。
   
    ![Web アプリケーションの [Create Page] ページのスクリーンショット - 開発詳細](./media/sql-api-python-application/cosmos-db-python-run-create-page.png)
4. 次に、 **[投票]** をクリックし、回答を選択します。
   
    ![投稿用の質問を表示する Web アプリケーションのスクリーンショット](./media/sql-api-python-application/cosmos-db-vote.png)
5. 回答を投票するごとに、該当するカウンターの値が増加します。
   
    ![投票結果のページが表示されているスクリーンショット](./media/sql-api-python-application/cosmos-db-voting-results.png)
6. Shift + F5 キーを押して、プロジェクトのデバッグを停止します。

## <a name="step-5-deploy-the-web-application-to-azure"></a>ステップ 5: Web アプリケーションを Azure にデプロイする
Azure Cosmos DB に対してローカルで適切に動作する完全なアプリケーションを入手できたので、web.config ファイルを作成して、ローカル環境と一致するようにサーバーのファイルを更新し、Azure 上に完成したアプリを表示します。 これは、Visual Studio 2017 固有の手順です。 異なるバージョンの Visual Studio を使用している場合は、[Azure App Service への公開](/visualstudio/python/publishing-to-azure)に関する記事をご覧ください。

1. Visual Studio の**ソリューション エクスプローラー**で、プロジェクトを右クリックし、**[追加]、[新しい項目]** の順に選択します。表示されたダイアログ ボックスで、**[Azure web.config (Fast CGI)]** テンプレートを選択し、**[OK]** をクリックします。 これにより、お使いのプロジェクトのルートに、`web.config` ファイルが作成されます。 

2. パスが Python のインストールと一致するように、`web.config` の `<system.webServer>` セクションを変更します。 たとえば、Python 2.7 x64 の場合、エントリは次のように表示されます。
    
    ```xml
    <system.webServer>
        <handlers>
            <add name="PythonHandler" path="*" verb="*" modules="FastCgiModule" scriptProcessor="D:\home\Python27\python.exe|D:\home\Python27\wfastcgi.py" resourceType="Unspecified" requireAccess="Script"/>
        </handlers>
    </system.webServer>
    ```

3. プロジェクト名と一致するように、`web.config` の `WSGI_HANDLER` エントリを `tutorial.app` に設定します。 

    ```xml
    <!-- Flask apps only: change the project name to match your app -->
    <add key="WSGI_HANDLER" value="tutorial.app"/>
    ```

4. Visual Studio の **ソリューション エクスプローラー**で、**[チュートリアル]** フォルダーを展開し、[`static`] フォルダーを右クリックして、**[追加] > [新しい項目]** の順に選択し、[Azure 静的ファイルの web.config] テンプレートを選んで **[OK]** をクリックします。 この操作により、そのフォルダーの Python の処理を無効にするもう 1 つの `web.config` が、`static` フォルダーに作成されます。 この構成では、Python アプリケーションを使用せずに、静的ファイルに対する要求を既定の Web サーバーに送信します。

5. ファイルを保存し、ソリューション エクスプローラーでプロジェクトを右クリックし (ローカル実行したままになっていないことを確認してください)、**[発行]** を選択します。  
   
     ![tutorial が選択されたソリューション エクスプローラーのスクリーンショット。[発行] オプションが強調表示されている](./media/sql-api-python-application/image20.png)
6. **[発行]** ダイアログ ボックスで、**[Microsoft Azure App Service]** を選択します。**[新規作成]** を選択し、**[発行]** をクリックします。
   
    ![Microsoft Azure App Service が強調表示されている [Web の発行] ウィンドウのスクリーンショット](./media/sql-api-python-application/cosmos-db-python-publish.png)
7. **[App Service の作成]** ダイアログ ボックスで、Web アプリ名と共に、**サブスクリプション**、**リソース グループ**、および **App Service プラン**を入力し、**[作成]** をクリックします。
   
    ![[Microsoft Azure Web Apps] ウィンドウのスクリーンショット](./media/sql-api-python-application/cosmos-db-python-create-app-service.png)
8. 数秒後に、Visual Studio によるサーバーへのファイル コピーが完了し、"内部サーバー エラーが発生したため、ページを表示できません。" というメッセージが  `http://<your app service>.azurewebsites.net/` ページに表示されます。

9. Azure Portal で新しい App Service アカウントを開き、ナビゲーション メニューを下にスクロールして、**[開発ツール]** セクションにある **[拡張機能]** を選択して、**[+ Add]\(+ (追加)\)** をクリックします。

10. **[拡張機能の選択]** ページで、最近インストールされた Python 2.7 まで下にスクロールして、x86 または x64 ビットのオプションを選択し、**[OK]** をクリックして法律条項に同意します。  
   
11. `https://<your app service name>.scm.azurewebsites.net/DebugConsole` で参照できる Kudu コンソールを使用して、お使いのアプリの `requirements.txt` ファイルに一覧表示されたパッケージをインストールします。 これを行うには、Kudu 診断コンソールで、Python フォルダー (`D:\home\Python27`) に移動して、[Kudu コンソール](/visualstudio/python/managing-python-on-azure-app-service#azure-app-service-kudu-console)のセクションに記載されている次のコマンドを実行します。

    ```
    D:\home\Python27>python -m pip install --upgrade -r /home/site/wwwroot/requirements.txt
    ```          

12. 新しいパッケージをインストールした後、**[再起動]** ボタンを押して、Azure Portal で App Service を再起動します。 

    > [!Tip] 
    > アプリの `requirements.txt` ファイルに変更を加えた場合は、もう一度 Kudu コンソールを使用して、その時点で該当のファイルに記載されているパッケージを必ずインストールしてください。 

13. サーバー環境の構成をすべて完了した後、ブラウザーのページを更新すると Web アプリが表示されます。

    ![Bottle、Flask、および Django アプリを App Service に公開した結果](./media/sql-api-python-application/python-published-app-services.png)

    > [!Tip] 
    > Web ページが表示されない場合、または "内部サーバー エラーが発生したため、ページを表示できません。" というメッセージがまだ表示される場合、 Kudo で web.config ファイルを開き、`<httpErrors errorMode="Detailed"></httpErrors>` を system.webServer セクションに追加してから、ページを更新します。 これにより、ブラウザーに詳細なエラー出力が表示されます。 

## <a name="troubleshooting"></a>トラブルシューティング
これがコンピューターで実行した最初の Python アプリの場合は、PATH 変数に次のフォルダー (または同等のインストールの場所) が含まれていることを確認します。

    C:\Python27\site-packages;C:\Python27\;C:\Python27\Scripts;

プロジェクトに **tutorial** 以外の名前を付けた場合に、投票ページでエラーが発生したときは、**\_\_init\_\_.py** の `import tutorial.view` 行で正しいプロジェクト名を参照していることを確認してください。

## <a name="next-steps"></a>次の手順
お疲れさまでした。 ここでは初めての方を対象に、Azure Cosmos DB を使用して Python Web アプリケーションを作成し、Azure に発行する方法を説明しました。

Web アプリケーションに機能を追加する場合は、[Azure Cosmos DB Python SDK](sql-api-sdk-python.md) で利用できる API を見直してください。

Azure、Visual Studio、Python の詳細については、「 [Python デベロッパー センター](https://azure.microsoft.com/develop/python/)」を参照してください。 

Python Flask の追加のチュートリアルについては、「[The Flask Mega-Tutorial, Part I: Hello, World!](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world)」(Flask メガ チュートリアルのパート 1: Hello, World!) を参照してください。 
