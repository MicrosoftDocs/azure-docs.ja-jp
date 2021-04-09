---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: f88ca39a378e997bb72300188166192e3383f6f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102620296"
---
## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* Azure Active Directory 認証用に構成されたイマーシブ リーダー リソース。 設定するには、[これらの手順](../../how-to-create-immersive-reader.md)に従ってください。  環境のプロパティを構成するときに、ここで作成した値の一部が必要になります。 後で参照するために、実際のセッションの出力をテキスト ファイルに保存します。
* [Git](https://git-scm.com/).
* [イマーシブ リーダー SDK](https://github.com/microsoft/immersive-reader-sdk)。
* [Python](https://www.python.org/downloads/) と [pip](https://docs.python.org/3/installing/index.html)。 Python 3.4 以降では、pip は Python バイナリ インストーラーに既定で含まれています。
* [Flask](https://flask.palletsprojects.com/en/1.0.x/)。
* [Jinja](http://jinja.pocoo.org/docs/2.10/)。
* [virtualenv](https://virtualenv.pypa.io/en/latest/) と [Windows 用 virtualenvwrapper-win](https://pypi.org/project/virtualenvwrapper-win/) または [OSX 用 virtualenvwrapper](https://virtualenvwrapper.readthedocs.io/en/latest/)。
* [requests モジュール](https://pypi.org/project/requests/2.7.0/)。
* [Visual Studio Code](https://code.visualstudio.com/) などの IDE。

## <a name="configure-authentication-credentials"></a>認証資格情報の構成

**.env** という名前の新しいファイルを作成し、次の名前と値をそれに貼り付けます。 イマーシブ リーダー リソースを作成したときに取得した値を指定します。

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

このファイルには公開してはならないシークレットが含まれているため、ソース管理にコミットしないでください。

**getimmersivereadertoken** API エンドポイントを、[OAuth](https://oauth.net/2/) などの何らかの形式の認証で保護します。 認証を使用すると、未承認のユーザーが、お使いのイマーシブ リーダー サービスと請求に対して使用するトークンを取得することを防ぐことができます。 この作業は、このチュートリアルの範囲を超えています。

## <a name="create-a-python-web-app-on-windows"></a>Windows で Python Web アプリを作成する

Windows 上で `flask` を使用して Python Web アプリを作成します。

[Git](https://git-scm.com/) のインストール。

Git がインストールされたら、コマンド プロンプトを開き、イマーシブ リーダー SDK Git リポジトリを、お使いのコンピューターのフォルダーに複製します。

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

[Python](https://www.python.org/downloads/) をインストールします。

**[Add Python to PATH]\(パスに Python を追加する\)** チェック ボックスをオンにします。

![Python Windows のインストール ダイアログの手順 1](../../media/pythoninstallone.jpg)

チェック ボックスをオンにして **[Optional Features]\(オプション機能\)** を追加し、 **[Next]\(次へ\)** を選択します。

![Python Windows のインストール ダイアログの手順 2](../../media/pythoninstalltwo.jpg)

**[Custom installation]\(カスタム インストール\)** を選択し、インストール パスをルート フォルダーとして設定します (たとえば、`C:\Python37-32\`)。 その後、 **[インストール]** を選択します。

![Python Windows のインストール ダイアログの手順 3](../../media/pythoninstallthree.jpg)

Python のインストールが完了したら、コマンド プロンプトを開き、`cd` を使用して Python Scripts フォルダーに移動します。

```cmd
cd C:\Python37-32\Scripts
```

Flask をインストールします。

```cmd
pip install flask
```

Jinja2 をインストールします。 これは Python 用の完全な機能を備えたテンプレート エンジンです。

```cmd
pip install jinja2
```

virtualenv をインストールします。 このツールは分離された Python 環境を作成します。

```cmd
pip install virtualenv
```

virtualenvwrapper-win をインストールします。 virtualenvwrapper の背後にある考え方は、virtualenv の使用を容易にすることです。

```cmd
pip install virtualenvwrapper-win
```

requests モジュールをインストールします。 requests は、Python で書かれた Apache2 ライセンスの HTTP ライブラリです。

```cmd
pip install requests
```

python-dotenv モジュールをインストールします。 このモジュールは、.env ファイルからキーと値のペアを読み取り、それらを環境変数に追加します。

```cmd
pip install python-dotenv
```

仮想環境を作成します。

```cmd
mkvirtualenv advanced-python
```

`cd` を使用してサンプル プロジェクトのルート フォルダーに移動します。

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

サンプル プロジェクトをこの環境に接続します。 このアクションにより、新しく作成された仮想環境がサンプル プロジェクトのルート フォルダーにマップされます。

```cmd
setprojectdir .
```

仮想環境をアクティブにします。

```cmd
activate
```

これでプロジェクトがアクティブになり、コマンド プロンプトに `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` のように表示されます。

環境を非アクティブ化します。

```cmd
deactivate
```

この環境が非アクティブ化されたため、`(advanced-python)` プレフィックスは削除されます。

この環境を再アクティブ化するには、サンプル プロジェクトのルート フォルダーから `workon advanced-python` を実行します。

```cmd
workon advanced-python
```

### <a name="start-the-immersive-reader-with-sample-content"></a>イマーシブ リーダーを起動してサンプル コンテンツを表示する

この環境がアクティブになったら、サンプル プロジェクトのルート フォルダーから `flask run` を入力してサンプル プロジェクトを実行します。

```cmd
flask run
```

ブラウザーを開き、 http://localhost:5000 に移動します。

## <a name="create-a-python-web-app-on-osx"></a>OSX 上で Python Web アプリを作成する

OSX 上で `flask` を使用して Python Web アプリを作成します。

[Git](https://git-scm.com/) のインストール。

Git がインストールされたら、ターミナルを開き、イマーシブ リーダー SDK Git リポジトリを、お使いのコンピューターのフォルダーに複製します。

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

[Python](https://www.python.org/downloads/) をインストールします。

Python ルート フォルダー (たとえば、`Python37-32`) が、Applications フォルダーに存在するはずです。

Python のインストールが完了したら、ターミナルを開き、`cd` を使用して Python Scripts フォルダーに移動します。

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

pip をインストールします。

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

以下のコードを実行して、アクセス許可の問題を回避するために、現在サインインしているユーザー用に pip をインストールします。

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- パスワードを求められたら、入力します。
- pip インストールのパスを PATH 変数に追加します。
- ファイルの末尾に移動し、リストの最後の項目として追加するパスを入力します (たとえば、`PATH=$PATH:/usr/local/bin`)。
- **CTRL + X** キーを押して終了します。
- 「**Y**」と入力して、変更されたバッファーを保存します。
- これで完了です。 これをテストするには、新しいターミナル ウィンドウで、次のように入力します: `echo $PATH`。

Flask をインストールします。

```bash
pip install flask --user
```

Jinja2 をインストールします。 これは Python 用の完全な機能を備えたテンプレート エンジンです。

```bash
pip install Jinja2 --user
```

virtualenv をインストールします。 このツールは分離された Python 環境を作成します。

```bash
pip install virtualenv --user
```

virtualenvwrapper をインストールします。 virtualenvwrapper の背後にある考え方は、virtualenv の使用を容易にすることです。

```bash
pip install virtualenvwrapper --user
```

requests モジュールをインストールします。 requests は、Python で書かれた Apache2 ライセンスの HTTP ライブラリです。

```bash
pip install requests --user
```

python-dotenv モジュールをインストールします。 このモジュールは、.env ファイルからキーと値のペアを読み取り、それらを環境変数に追加します。

```bash
pip install python-dotenv --user
```

仮想環境を保持するフォルダーを選択し、次のコマンドを実行します。

```bash
mkdir ~/.virtualenvs
```

`cd` を使用して、イマーシブ リーダー SDK の Python サンプル アプリケーション フォルダーに移動します。

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

仮想環境を作成します。

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

サンプル プロジェクトをこの環境に接続します。 このアクションにより、新しく作成された仮想環境がサンプル プロジェクトのルート フォルダーにマップされます。

```bash
setprojectdir .
```

仮想環境をアクティブにします。

```bash
activate
```

これでプロジェクトがアクティブになり、コマンド プロンプトに `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` のように表示されます。

環境を非アクティブ化します。

```bash
deactivate
```

この環境が非アクティブ化されたため、`(advanced-python)` プレフィックスは削除されます。

この環境を再アクティブ化するには、サンプル プロジェクトのルート フォルダーから `workon advanced-python` を実行します。

```bash
workon advanced-python
```

## <a name="start-the-immersive-reader-with-sample-content"></a>イマーシブ リーダーを起動してサンプル コンテンツを表示する

この環境がアクティブになったら、サンプル プロジェクトのルート フォルダーから `flask run` を入力してサンプル プロジェクトを実行します。

```bash
flask run
```

ブラウザーを開き、 http://localhost:5000 に移動します。

## <a name="next-steps"></a>次のステップ

* [イマーシブ リーダー SDK](https://github.com/microsoft/immersive-reader-sdk) と[イマーシブ リーダー SDK リファレンス](../../reference.md)を確認します。
* [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/) でコード サンプルを見る。
