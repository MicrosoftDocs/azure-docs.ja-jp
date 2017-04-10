---
title: "CLI を使用した Service Fabric クラスターの対話操作 | Microsoft Docs"
description: "Azure CLI を使用して Service Fabric クラスターの対話操作を実行する方法"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: c3ec8ff3-3b78-420c-a7ea-0c5e443fb10e
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/02/2017
ms.author: subramar
translationtype: Human Translation
ms.sourcegitcommit: 615e7ea84aae45f384edb671a28e4ff98b4ade3a
ms.openlocfilehash: d61b7a9c8199b15c8bb24e7146ea93a2f67fb0a7
ms.lasthandoff: 01/05/2017


---
# <a name="using-the-azure-cli-to-interact-with-a-service-fabric-cluster"></a>Azure CLI を使用した Service Fabric クラスターの対話操作
Linux 上で Azure CLI を使用して、Linux マシンから Service Fabric クラスターの対話操作を実行できます。

まず、次のコマンドを使用して、Git リポジトリから CLI の最新バージョンを入手し、自分のパスでセットアップします。

```sh
 git clone https://github.com/Azure/azure-xplat-cli.git
 cd azure-xplat-cli
 npm install
 sudo ln -s \$(pwd)/bin/azure /usr/bin/azure
 azure servicefabric
```

サポートされている各コマンドで、コマンドの名前を入力して、そのコマンドのヘルプを取得できます。 コマンドでは、オート コンプリートがサポートされています。 たとえば、次のコマンドを使用すると、すべてのアプリケーション コマンドのヘルプが表示されます。 

```sh
 azure servicefabric application 
```

次の例に示すように、ヘルプをさらにフィルター処理して特定のコマンドに限定することができます。

```sh
 azure servicefabric application  create
```

CLI でオート コンプリートを有効にするには、次のコマンドを実行します。

```sh
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.sh\_profile
source ~/azure.completion.sh
```

次のコマンドでは、クラスターに接続し、クラスター内のノードを示します。

```sh
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric node show
```

名前付きパラメーターを使用し、内容を確認するには、コマンドの後に「--help」と入力します。 次に例を示します。

```sh
 azure servicefabric node show --help
 azure servicefabric application create --help
```

**クラスターに属していない**コンピューターから、複数のコンピューターから成るクラスターに接続する場合は、次のコマンドを使用します。

```sh
 azure servicefabric cluster connect http://PublicIPorFQDN:19080
```

PublicIPorFQDN タグは、必要に応じて、実際の IP または FQDN に置き換えてください。 **クラスターに属している**コンピューターから、複数のコンピューターから成るクラスターに接続する場合は、次のコマンドを使用します。

```sh
 azure servicefabric cluster connect --connection-endpoint http://localhost:19080 --client-connection-endpoint PublicIPorFQDN:19000
```

Azure Portal を使用して作成した Linux Service Fabric クラスターの対話操作には、PowerShell または CLI を使用できます。 

> [!WARNING]
> これらのクラスターはセキュリティで保護されていないため、クラスター マニフェストにパブリック IP アドレスを追加すると、ワンボックスを開いている可能性があります。

## <a name="using-the-azure-cli-to-connect-to-a-service-fabric-cluster"></a>Azure CLI を使用した Service Fabric クラスターへの接続
セキュリティで保護されたクラスターに接続する方法を以下の Azure CLI コマンドで説明します。 証明書の詳細は、クラスター ノード上の証明書と一致する必要があります。

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert
```

ご使用の証明書に証明機関 (CA) が含まれている場合は、次の例のように --ca-cert-path パラメーターを追加する必要があります。 

```
 azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
複数の CA がある場合は、区切り記号としてコンマを使用します。

証明書の共通名が接続エンドポイントと一致しない場合は、次のコマンドに示すように、 `--strict-ssl-false` パラメーターを使用して検証を回避することができます。 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl-false 
```

CA 検証を省略したい場合は、次のコマンドに示すように、--reject-unauthorized-false パラメーターを追加します。 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized-false 
```

接続後、他の CLI コマンドを実行してクラスターの対話操作を実行することができます。 

## <a name="deploying-your-service-fabric-application"></a>Service Fabric アプリケーションのデプロイ
Service Fabric アプリケーションをコピー、登録、起動するには、次のコマンドを実行します。

```
azure servicefabric application package copy [applicationPackagePath] [imageStoreConnectionString] [applicationPathInImageStore]
azure servicefabric application type register [applicationPathinImageStore]
azure servicefabric application create [applicationName] [applicationTypeName] [applicationTypeVersion]
```


## <a name="upgrading-your-application"></a>アプリケーションのアップグレード
このプロセスは、 [Windows のプロセス](service-fabric-application-upgrade-tutorial-powershell.md)に似ています。

プロジェクトのルート ディレクトリから、アプリケーションをビルド、コピー、登録、作成します。 アプリケーション インスタンスが fabric:/MySFApp という名前で、種類が MySFApp の場合、コマンドは次のようになります。

```
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
 azure servicefabric application create fabric:/MySFApp MySFApp 1.0
```

アプリケーションに変更を加え、変更されたサービスをリビルドします。  変更されたサービスのマニフェスト ファイル (ServiceManifest.xml) を、サービスの更新されたバージョン (および必要に応じて Code、Config、Data) で更新します。 さらに、アプリケーションのマニフェスト (ApplicationManifest.xml) を、アプリケーションの更新されたバージョン番号と変更されたサービスで変更します。  ここで、次のコマンドを使用して、更新されたアプリケーションをコピーして登録します。

```
 azure servicefabric cluster connect http://localhost:19080>
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
```

これで、次のコマンドを使用して、アプリケーションのアップグレードを開始できます。

```
 azure servicefabric application upgrade start -–application-name fabric:/MySFApp -–target-application-type-version 2.0  --rolling-upgrade-mode UnmonitoredAuto
```

SFX を使用してアプリケーションのアップグレードを監視できるようになりました。 数分で、アプリケーションが更新されました。  更新されたアプリケーションをエラーで試し、Service Fabric の自動ロールバック機能を確認することもできます。

## <a name="converting-from-pfx-to-pem-and-vice-versa"></a>PFX から PEM または PEM から PFX への変換

別の環境に存在する可能性のあるセキュリティで保護されたクラスターにアクセスするために、ローカル コンピューター (Windows または Linux) に証明書をインストールすることが必要な場合があります。 たとえば、Windows コンピューターからセキュリティで保護された Linux クラスターへのアクセスまたはその逆方向のアクセスを行うときに、PFX から PEM または PEM から PFX に証明書を変換することが必要な場合があります。 

PEM ファイルから PFX ファイルに変換するには、次のコマンドを使用します。

```bash
openssl pkcs12 -export -out certificate.pfx -inkey mycert.pem -in mycert.pem -certfile mycert.pem
```

PFX ファイルから PEM ファイルに変換するには、次のコマンドを使用します。

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

詳細については、[OpenSSL のドキュメント](https://www.openssl.org/docs/man1.0.1/apps/pkcs12.html)を参照してください。

<a id="troubleshooting"></a>
## <a name="troubleshooting"></a>トラブルシューティング
### <a name="copying-of-the-application-package-does-not-succeed"></a>アプリケーション パッケージのコピーが失敗する
`openssh` がインストールされているかどうかを確認してください。 既定では、Ubuntu Desktop にこれがインストールされていません。 次のコマンドを使用してインストールします。

```
 sudo apt-get install openssh-server openssh-client**
```

問題が解決しない場合は、次のコマンドを使用して **sshd_config** ファイルを変更し、SSH の PAM を無効にしてみてください。

```sh
 sudo vi /etc/ssh/sshd_config
#Change the line with UsePAM to the following: UsePAM no
 sudo service sshd reload
```

それでも問題が解決しない場合は、次のコマンドを実行して SSH セッションの数を増やしてみてください。

```sh
 sudo vi /etc/ssh/sshd\_config
# Add the following to lines:
# MaxSessions 500
# MaxStartups 300:30:500
 sudo service sshd reload
```
(パスワードとは対照的に) SSH 認証のキーの使用はまだサポートされていません (プラットフォームではパッケージのコピーに SSH を使用しているため)。そのため、代わりにパスワード認証を使用してください。



## <a name="next-steps"></a>次のステップ
開発環境を設定し、Linux クラスターに Service Fabric アプリケーションをデプロイします。


