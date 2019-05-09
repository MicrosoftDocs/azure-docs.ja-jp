---
title: Azure Service Fabric CLI の概要
description: Azure Service Fabric CLI の使用方法について説明します。 クラスターに接続して、アプリケーションを管理する方法についても説明します。
services: service-fabric
author: Christina-Kang
manager: chackdan
ms.service: service-fabric
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: d5b6f183a59e3f47aa5867b5e09e06541a6a67db
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662654"
---
# <a name="azure-service-fabric-cli"></a>Azure Service Fabric CLI

Azure Service Fabric コマンド ライン インターフェイス (CLI) は、Service Fabric のエンティティを対話的に操作したり管理したりするためのコマンド ライン ユーティリティです。 Service Fabric CLI は、Windows クラスターと Linux クラスターのどちらでも使用できます。 Service Fabric CLI は、Python がサポートされるすべてのプラットフォームで動作します。

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="prerequisites"></a>前提条件

インストール前に、ご使用の環境に Python と pip の両方がインストールされていることを確認してください。 詳細については、[pip のクイックスタート ドキュメント](https://pip.pypa.io/en/latest/quickstart/)と公式の [Python インストール ドキュメント](https://wiki.python.org/moin/BeginnersGuide/Download)を参照してください。

この CLI では、Python のバージョン 2.7、3.5、3.6、3.7 をサポートしています。 Python 2.7 は間もなくサポート終了となるため、Python 3.x の利用をお勧めします。

### <a name="service-fabric-target-runtime"></a>Service Fabric のターゲット ランタイム

Service Fabric CLI は、Service Fabric SDK の最新のランタイム バージョンをサポートすることを意図したものです。 インストールする CLI のバージョンの判断には、以下の表をご利用ください。

| CLI のバージョン   | サポートされているランタイムのバージョン |
|---------------|---------------------------|
| 最新 (7 以前)  | 最新 (6.4 以前)            |
| 6.0.0         | 6.3                       |
| 5.0.0         | 6.2                       |
| 4.0.0         | 6.1                       |
| 3.0.0         | 6.0                       |
| 1.1.0         | 5.6、5.7                  |

`pip install` コマンドの末尾にオプションとして `==<version>` を付けると、インストールする CLI のターゲット バージョンを指定できます。 たとえば、バージョン 1.1.0 であれば、次のような構文になります。

```
pip install -I sfctl==1.1.0
```

必要があれば、以下の `pip install` コマンドを上に挙げたコマンドで置換してください。

Service Fabric CLI リリースの詳細については、[GitHub のドキュメント](https://github.com/Azure/service-fabric-cli/releases)を参照してください。

## <a name="install-pip-python-and-the-service-fabric-cli"></a>pip、Python、Service Fabric CLI のインストール

ご利用のプラットフォームには、さまざまな方法で pip と Python をインストールできます。 ここでは主なオペレーティング システムを対象に、Python 3 と pip をすばやく設定する手順を説明します。

### <a name="windows"></a> Windows

Windows 10、Windows Server 2016、Windows Server 2012 R2 については、標準的な公式インストール手順を使用してください。 Python インストーラーを実行すると、既定で pip もインストールされます。

1. [Python の公式ダウンロード ページ](https://www.python.org/downloads/)に移動し、最新バージョンの Python 3.7 をダウンロードします。

2. インストーラーを起動します。

3. プロンプトの一番下の **[Add Python 3.7 to PATH]\(Python 3.7 を PATH に追加\)** を選択します。

4. **[Install Now]\(今すぐインストール\)** を選択してインストールを完了します。

この時点で新しいコマンド ウィンドウを開くと、対応する Python と pip のバージョンが得られます。

```bat
python --version
pip --version
```

その後、次のコマンドを実行して Azure Service Fabric CLI (sfctl) をインストールし、CLI ヘルプ ページを表示します。

```bat
pip install sfctl
sfctl -h
```

### <a name="ubuntu-and-windows-subsystem-for-linux"></a>Ubuntu と Windows Subsystem for Linux

Service Fabric CLI をインストールするには、以下のコマンドを実行します。

```bash
sudo apt-get install python3
sudo apt-get install python3-pip
pip3 install sfctl
```

インストールが終わったら、以下を使ってテストできます。

```bash
sfctl -h
```

以下に示したようなコマンドが見つからないという趣旨のエラーが表示された場合には、

`sfctl: command not found`

`$PATH` から `~/.local/bin` にアクセスできることを確認してください。

```bash
export PATH=$PATH:~/.local/bin
echo "export PATH=$PATH:~/.local/bin" >> .bashrc
```

フォルダーに対するアクセス許可が正しくないとの理由で Windows Subsystem for Linux へのインストールに失敗した場合には、管理者特権を使ってやり直すことが必要になる場合があります。

```bash
sudo pip3 install sfctl
```

### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (Service Fabric プレビュー サポート)

Service Fabric CLI を Red Hat にインストールするには、次のコマンドを実行します。

```bash
sudo yum install -y python34
sudo yum install python34-setuptools
sudo easy_install-3.4 pip
sudo pip3 install sfctl
```

インストールのテスト方法については、「**Ubuntu と Windows Subsystem for Linux**」セクションに記載の手順を参照してください。

<a name = "cli-mac"></a>
### <a name="macos"></a>MacOS

MacOS では、[HomeBrew パッケージ マネージャー](https://brew.sh)の使用をお勧めします。 HomeBrew をまだインストールしていない場合は、次のコマンドを実行してインストールします。

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

次に、ターミナルから次のコマンドを実行して Python 3.7、pip、Service Fabric CLI をインストールします。

```bash
brew install python3
pip3 install sfctl
sfctl -h
```

## <a name="cli-syntax"></a>CLI 構文

コマンドの先頭には必ず `sfctl` が付きます。 `sfctl -h` を使用すると、使用できるすべてのコマンドについての一般的な情報が表示されます。 `sfctl <command> -h` を使用すると、特定のコマンドについてのヘルプ情報が表示されます。

コマンドの構造は、動詞またはアクションの前にコマンドの対象が来るという一定のパターンに従います。

```azurecli
sfctl <object> <action>
```

この例では、`<object>` が `<action>` の対象です。

## <a name="select-a-cluster"></a>クラスターの選択

何らかの操作を実行するには、まず、接続先のクラスターを選択する必要があります。 たとえば、`testcluster.com` という名前のクラスターを選択してそのクラスターに接続するには、次のコマンドを実行します。

> [!WARNING]
> セキュリティ保護されていない Service Fabric クラスターを運用環境で使用することは避けてください。

```azurecli
sfctl cluster select --endpoint http://testcluster.com:19080
```

クラスターのエンドポイントには、プレフィックスとして `http` または `https` を付けたうえで、 HTTP ゲートウェイのポートを含める必要があります。 このポートとアドレスは、Service Fabric Explorer の URL と同じです。

証明書を使って保護されているクラスターの場合、PEM でエンコードされた証明書を指定できます。 証明書は、単一のファイルとしてまたは証明書とキーのペアとして指定できます。 CA の署名入りではない自己署名証明書の場合は、`--no-verify` オプションを渡して CA 検証を省略することができます。

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

詳細については、[セキュリティ保護された Azure Service Fabric クラスターへの接続](service-fabric-connect-to-secure-cluster.md)に関するページを参照してください。

## <a name="basic-operations"></a>基本操作

クラスター接続情報は Service Fabric CLI セッションをまたいで維持されます。 Service Fabric クラスターを選択したら、任意の Service Fabric コマンドをクラスターに対して実行できます。

たとえば Service Fabric クラスターの正常性の状態を取得するには、次のコマンドを使用します。

```azurecli
sfctl cluster health
```

コマンドからは、次の出力が生成されます。

```json
{
  "aggregatedHealthState": "Ok",
  "applicationHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "name": "fabric:/System"
    }
  ],
  "healthEvents": [],
  "nodeHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "id": {
        "id": "66aa824a642124089ee474b398d06a57"
      },
      "name": "_Test_0"
    }
  ],
  "unhealthyEvaluations": []
}
```

## <a name="tips-and-troubleshooting"></a>ヒントとトラブルシューティング

ここでは、一般的な問題を解決するうえでの推奨事項とヒントをいくつか紹介します。

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>PFX 形式から PEM 形式への証明書の変換

Service Fabric CLI は、クライアント側の証明書を PEM (拡張子 .pem) ファイルとしてサポートします。 Windows の PFX ファイルを使用している場合は、これらの証明書を PEM 形式に変換する必要があります。 PFX ファイルを PEM ファイルに変換するには、次のコマンドを使用します。

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

同様に、PEM ファイルから PFX ファイルに変換するには、次のコマンドをご利用いただけます (ここではパスワードを指定していません)。

```bash
openssl  pkcs12 -export -out Certificates.pfx -inkey Certificates.pem -in Certificates.pem -passout pass:'' 
```

詳細については、[ のドキュメント](https://www.openssl.org/docs/)を参照してください。

### <a name="connection-problems"></a>接続の問題

一部の操作において、次のメッセージが表示される場合があります。

`Failed to establish a new connection`

この場合は、指定したクラスター エンドポイントが利用可能で、リッスン状態になっていることを確認してください。 また、そのホストとポートで Service Fabric Explorer の UI を利用できることも確認してください。 エンドポイントを更新するには `sfctl cluster select` を使用します。

### <a name="detailed-logs"></a>詳細ログ

問題のデバッグまたはレポート作成を行う際には、詳細ログがよく役に立ちます。 `--debug` フラグは、出力の詳細レベルを上げます。

### <a name="command-help-and-syntax"></a>コマンドのヘルプと構文

特定のコマンドや、コマンドのグループについてのヘルプ情報を表示するには、次の例のように、`-h` フラグを使用します。

```azurecli
sfctl application -h
```

別名を使用した場合の例:

```azurecli
sfctl application create -h
```

## <a name="updating-the-service-fabric-cli"></a>Service Fabric CLI の更新 

Service Fabric CLI を更新するには、次のコマンドを実行します (最初のインストール時に選択した内容に応じて `pip` を `pip3` で置き換えます)。

```bash
pip uninstall sfctl
pip install sfctl
```

## <a name="next-steps"></a>次の手順

* [Azure Service Fabric CLI でアプリケーションをデプロイする](service-fabric-application-lifecycle-sfctl.md)
* [Linux 上の Service Fabric の概要](service-fabric-get-started-linux.md)
