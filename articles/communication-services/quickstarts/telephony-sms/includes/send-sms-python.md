---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: lakshmans
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: lakshmans
ms.openlocfilehash: e8424f6b5b7617b00de6dedbece3325f3c5513c8
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622154"
---
Communication Services Python SMS クライアント ライブラリを使用して SMS メッセージを送信することによって、Azure Communication Services の使用を開始します。

このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](#todo-sdk-repo) | [Package (PiPy)](#todo-nuget) | [Samples](#todo-samples)--> 

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- [Python](https://www.python.org/downloads/) 2.7、3.5 以降
- アクティブな Communication Services リソースと接続文字列。 [Communication Services リソースを作成します](../../create-communication-resource.md)。
- SMS が有効になっている電話番号。 [電話番号を取得します](../get-phone-number.md)。

### <a name="prerequisite-check"></a>前提条件のチェック

- ターミナルまたはコマンド ウィンドウで `python --version` コマンドを実行して、Python がインストールされていることを確認します。
- Communication Services リソースに関連付けられている電話番号を表示するには、[Azure portal](https://portal.azure.com/) にサインインし、Communication Services リソースを見つけて、左側のナビゲーション ペインから **[電話番号]** タブを開きます。

## <a name="setting-up"></a>設定

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

ターミナルまたはコマンド ウィンドウを開き、アプリ用の新しいディレクトリを作成して、そこに移動します。

```console
mkdir sms-quickstart && cd sms-quickstart
```

テキスト エディターを使用して、**send-sms.py** という名前のファイルをプロジェクトのルート ディレクトリに作成し、基本的な例外処理を含むプログラムの構造を追加します。 このクイックスタートのソース コードはすべて、以降のセクションでこのファイルに追加することになります。

```python
import os
from azure.communication.sms import SmsClient

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-the-package"></a>パッケージをインストールする

まだアプリケーション ディレクトリにいる間に、`pip install` コマンドを使用して、Python 用の Azure Communication Services SMS クライアント ライブラリ パッケージをインストールします。

```console
pip install azure-communication-sms --pre
```

## <a name="object-model"></a>オブジェクト モデル

Python 用 Azure Communication Services SMS クライアント ライブラリが備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| 名前                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | このクラスは、すべての SMS 機能に必要となります。 サブスクリプション情報を使用してこれをインスタンス化し、そのインスタンスを使用して SMS を送信します。                                                                                                                 |
| SmsSendResult               | このクラスには、SMS サービスからの結果が含まれます。                                          |

## <a name="authenticate-the-client"></a>クライアントを認証する

接続文字列を使用して **SmsClient** をインスタンス化します。 次のコードは、`COMMUNICATION_SERVICES_CONNECTION_STRING` という名前の環境変数からリソースの接続文字列を取得します。 [リソースの接続文字列を管理する](../../create-communication-resource.md#store-your-connection-string)方法について確認してください。

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.getenv('COMMUNICATION_SERVICES_CONNECTION_STRING')

# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(connection_string)
```

## <a name="send-a-11-sms-message"></a>1:1 の SMS メッセージを送信する

1 人の受信者に SMS メッセージを送信するには、1 人の受信者の電話番号を使用して **SmsClient** から ```send``` メソッドを呼び出します。 また、オプションのパラメーターを渡して、配信レポートを有効にするかどうか、およびカスタム タグを設定するかどうかを指定することもできます。 **send-sms.py** の `try` ブロックの末尾に、次のコードを追加します。

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to="<to-phone-number>,
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

`<from-phone-number>` は通信サービス リソースに関連付けられている、SMS が有効になっている電話番号で置き換え、`<to-phone-number>` はメッセージの送信先の電話番号で置き換える必要があります。 

## <a name="send-a-1n-sms-message"></a>1:N の SMS メッセージを送信する

受信者の一覧に SMS メッセージを送信するには、受信者の電話番号の一覧を使用して **SmsClient** から ```send``` メソッドを呼び出します。 また、オプションのパラメーターを渡して、配信レポートを有効にするかどうか、およびカスタム タグを設定するかどうかを指定することもできます。 **send-sms.py** の `try` ブロックの末尾に、次のコードを追加します。

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to=["<to-phone-number-1>", "<to-phone-number-2>"],
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

`<from-phone-number>` は通信サービスに関連付けられている、SMS が有効になっている電話番号で置き換え、`<to-phone-number-1>` および `<to-phone-number-2>` はメッセージの送信先の電話番号で置き換える必要があります。 

## <a name="optional-parameters"></a>省略可能のパラメーター

`enable_delivery_report` パラメーターは、配信レポートを構成するために使用できる省略可能なパラメーターです。 これは、SMS メッセージが配信されたときにイベントを生成する場合に便利です。 SMS メッセージの配信レポートを構成するには、[SMS イベントの処理](../handle-sms-events.md)に関するクイックスタートを参照してください。

`tag` パラメーターは、カスタム タグを構成するために使用できる省略可能なパラメーターです。

## <a name="run-the-code"></a>コードの実行

アプリケーション ディレクトリから `python` コマンドを使用してアプリケーションを実行します。

```console
python send-sms.py
```
