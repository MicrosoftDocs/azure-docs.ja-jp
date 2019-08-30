---
title: チュートリアル:Python を使用してイマーシブ リーダーを起動する
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、イマーシブ リーダーを起動する Python アプリケーションを作成します。
services: cognitive-services
author: dylankil
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 08/02/2019
ms.author: dylankil
ms.openlocfilehash: 5e33108c9fc674abaf980a1272cca31aa21cffff
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991130"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-python-sample-project"></a>チュートリアル:Python サンプル プロジェクトを使用してイマーシブ リーダーを起動する

[概要](./overview.md)に関するページでは、イマーシブ リーダーの機能とそのしくみ (どのようにして言語学習者、新しい読者、学習障碍者の読解力向上のために実証済みの手法を実装するか) について説明しました。 このチュートリアルでは、イマーシブ リーダーを起動する Python Web アプリケーションの作成方法について説明します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * サンプル プロジェクトを使用して、Pip、Flask、Jinja、および virtualenv を使用する Python Web アプリを作成する
> * アクセス トークンの取得
> * イマーシブ リーダーを起動してサンプル コンテンツを表示する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

* Azure Active Directory (Azure AD) 認証用に構成されたイマーシブ リーダー リソース。 設定するには、[これらの手順](./azure-active-directory-authentication.md)に従ってください。 環境のプロパティを構成するときに、ここで作成した値の一部が必要になります。 後で参照するために、実際のセッションの出力をテキスト ファイルに保存します。
* [Git](https://git-scm.com/)
* [イマーシブ リーダー SDK](https://github.com/microsoft/immersive-reader-sdk)
* [Python](https://www.python.org/downloads/) と [pip](https://docs.python.org/3/installing/index.html)。 Python 3.4 以降では、pip は Python バイナリ インストーラーに既定で含まれています。
* [Flask](https://flask.palletsprojects.com/en/1.0.x/)
* [Jinja](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) と [Windows 用 virtualenvwrapper-win](https://pypi.org/project/virtualenvwrapper-win/) または [OSX 用 virtualenvwrapper](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [requests モジュール](https://pypi.org/project/requests/2.7.0/)
* [Visual Studio Code](https://code.visualstudio.com/) などの IDE

## <a name="acquire-an-azure-ad-authentication-token"></a>Azure AD 認証トークンを取得する

Azure AD 認証トークンを取得するバックエンド API を記述します。

この部分については、上の Azure AD 認証構成の前提条件の手順におけるいくつかの値が必要です。 そのセッションで保存したテキスト ファイルを参照します。

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

これらの値が用意できたら、 _.env_ という名前の新しいファイルを作成し、そこに次のコードを貼り付けて、上記のカスタム プロパティ値を指定します。 サンプル アプリ内の _.env._ ファイルを、新しく作成したファイルに置き換えます。

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

このファイルは、公開するべきでない機密情報を含んでいるため、ソース管理にはコミットしないでください。

**getimmersivereadertoken** API エンドポイントはなんらかの形式の認証 ([OAuth](https://oauth.net/2/) など) の背後で保護して、未承認のユーザーがトークンを取得し、ご使用のイマーシブ リーダー サービスと請求に対して使用できないようにする必要があります。この作業は、このチュートリアルの範囲を超えています。

## <a name="create-a-python-web-app-on-windows"></a>Windows で Python Web アプリを作成する

Windows 上で `flask` を使用して Python Web アプリを作成します。

[Git](https://git-scm.com/) のインストール。

Git がインストールされたら、コマンド プロンプトを開き、イマーシブ リーダー SDK Git リポジトリを、お使いのコンピューターのフォルダーに "複製" します

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

[Python](https://www.python.org/downloads/) をインストールします。

[Add Python to PATH]\(パスに Python を追加する\) チェック ボックスをオンにします。

![Python Windows のインストール ダイアログの手順 1](./media/pythoninstallone.jpg)

オプションの機能を追加するには、該当するチェック ボックスをオンにし、[次へ] をクリックします。

![Python Windows のインストール ダイアログの手順 2](./media/pythoninstalltwo.jpg)

[Custom installation]\(カスタム インストール\) を選択します。インストール パスをルート フォルダーとして設定し (たとえば、`C:\Python37-32\`)、[Install]\(インストール\) をクリックします。

![Python Windows のインストール ダイアログの手順 3](./media/pythoninstallthree.jpg)

Python のインストールが完了したら、コマンド プロンプトを開き、`cd` を使用して Python Scripts フォルダーに移動します。

```cmd
cd C:\Python37-32\Scripts
```

Flask をインストールします。

```cmd
pip install flask
```

Jinja2 をインストールします。 Python 用の完全な機能を備えたテンプレート エンジン。

```cmd
pip install jinja2
```

virtualenv をインストールします。 分離された Python 環境を作成するためのツール。

```cmd
pip install pip install virtualenv
```

virtualenvwrapper-win をインストールします。 virtualenvwrapper の背後にある考え方は、virtualenv の使用を容易にすることです。

```cmd
pip install virtualenvwrapper-win
```

requests モジュールをインストールします。 requests は、Python で書かれた Apache2 ライセンスの HTTP ライブラリです。

```cmd
pip install requests
```

仮想環境を作成する

```cmd
mkvirtualenv advanced-python
```

`cd` を使用してサンプル プロジェクトのルート フォルダーに移動します。

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

サンプル プロジェクトを環境に接続します。 これにより、新しく作成された仮想環境がサンプル プロジェクトのルート フォルダーにマップされます。

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

環境が非アクティブ化されたため、`(advanced-python)` プレフィックスは削除されます。

環境を再アクティブ化するには、サンプル プロジェクトのルート フォルダーから `workon advanced-python` を実行します。

```cmd
workon advanced-python
```

### <a name="launch-the-immersive-reader-with-sample-content"></a>イマーシブ リーダーを起動してサンプル コンテンツを表示する

環境がアクティブになったら、サンプル プロジェクトのルート フォルダーから `flask run` を入力してサンプル プロジェクトを実行します。

```cmd
flask run
```

ブラウザーを開き、 _http://localhost:5000_ に移動します。

## <a name="create-a-python-web-app-on-osx"></a>OSX 上で Python Web アプリを作成する

OSX 上で `flask` を使用して Python Web アプリを作成します。

[Git](https://git-scm.com/) のインストール。

Git がインストールされたら、ターミナルを開き、イマーシブ リーダー SDK Git リポジトリを、お使いのコンピューターのフォルダーに "複製" します

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

次に、以下を実行して、アクセス許可の問題を回避するために、現在サインインしているユーザー用に pip をインストールします。

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- パスワードを求められたら、入力します。
- pip インストールのパスを PATH 変数に追加します。
- ファイルの末尾に移動し、リストの最後の項目として追加するパスを入力します (たとえば、`PATH=$PATH:/usr/local/bin`)。
- control-x を押して終了します。
- 「`Y`」と入力して、変更されたバッファーを保存します。
- これで完了です。 これをテストするには、新しいターミナル ウィンドウで、次のように入力します: `echo $PATH`。

Flask をインストールします。

```bash
pip install flask --user
```

Jinja2 をインストールします。 Python 用の完全な機能を備えたテンプレート エンジン。

```bash
pip install Jinja2 --user
```

virtualenv をインストールします。 分離された Python 環境を作成するためのツール。

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

仮想環境を保持するフォルダーを選択し、次のコマンドを実行します

```bash
mkdir ~/.virtualenvs
```

`cd` を使用して、イマーシブ リーダー SDK の Python サンプル アプリケーション フォルダーに移動します。

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

仮想環境を作成する

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

サンプル プロジェクトを環境に接続します。 これにより、新しく作成された仮想環境がサンプル プロジェクトのルート フォルダーにマップされます。

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

環境が非アクティブ化されたため、`(advanced-python)` プレフィックスは削除されます。

環境を再アクティブ化するには、サンプル プロジェクトのルート フォルダーから `workon advanced-python` を実行します。

```bash
workon advanced-python
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>イマーシブ リーダーを起動してサンプル コンテンツを表示する

環境がアクティブになったら、サンプル プロジェクトのルート フォルダーから `flask run` を入力してサンプル プロジェクトを実行します。

```bash
flask run
```

ブラウザーを開き、 _http://localhost:5000_ に移動します。

## <a name="next-steps"></a>次の手順

* [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) と [Immersive Reader SDK リファレンス](./reference.md)を探索する
* [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/) でコード サンプルを見る
