---
title: Azure Container Instances での SSL の有効化
description: Azure Container Instances 内で実行されるコンテナー グループに対する、SSL または TLS エンドポイントを作成します
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: f11fb209f8d2ace51081fd81f453faf9505af27c
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326064"
---
# <a name="enable-an-ssl-endpoint-in-a-container-group"></a>コンテナー グループで SSL エンドポイントを有効にする

この記事では、SSL プロバイダーを実行しているアプリケーション コンテナーとサイドカー コンテナーを使用して[コンテナー グループ](container-instances-container-groups.md)を作成する方法について説明します。 コンテナー グループを別の SSL エンドポイントで設定することにより、アプリケーション コードを変更せずにアプリケーションの SSL 接続を有効にすることができます。

2 つのコンテナーで構成されるコンテナー グループを設定します。
* パブリック Microsoft [aci-helloworld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) イメージを使用してシンプルな Web アプリを実行する、アプリケーション コンテナー。 
* SSL を使用するよう構成されたパブリック [Nginx](https://hub.docker.com/_/nginx) イメージを実行するサイドカー コンテナー。 

この例ではコンテナー グループにより、Nginx 用のポート 443 のみがそのパブリック IP アドレスで公開されます。 Nginx により HTTPS 要求がコンパニオン Web アプリにルーティングされます。コンパニオン Web アプリはポート 80 で内部的にリッスンします。 他のポートをリッスンするコンテナー アプリの例を適応させることもできます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure Cloud Shell または Azure CLI のローカル インストールを使用し、この記事の作業を完了できます。 Azure CLI をローカルで使用する場合は、バージョン 2.0.55 以降をお勧めします。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-a-self-signed-certificate"></a>自己署名証明書の作成

SSL プロバイダーとして Nginx を設定するには、SSL 証明書が必要です。 この記事では、自己署名 SSL 証明書の作成および設定方法について説明します。 運用環境のシナリオでは、証明機関から証明書を取得する必要があります。

自己署名 SSL 証明書を作成するには、Azure Cloud Shell と多くの Linux ディストリビューションで入手可能な [OpenSSL](https://www.openssl.org/) ツールを使用するか、ご使用のオペレーティング システムにある同等のクライアント ツールを使用します。

まず、ローカルの作業ディレクトリに証明書の要求 (.csr ファイル) を作成します。

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

画面の指示に従って、ID 情報を追加します。 Common Name には、証明書に関連付けられているホスト名を入力します。 パスワードの入力を求めるメッセージが表示されたら、何も入力せず Enter キーを押して、パスワードの追加をスキップします。

次のコマンドを実行して、証明書の要求から自己署名証明書 (.crt file) を作成します。 例:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

これでディレクトリ内には、証明書の要求 (`ssl.csr`)、秘密キー (`ssl.key`)、自己署名証明書 (`ssl.crt`) の 3 つのファイルが表示されます。 `ssl.key` と `ssl.crt` は後の手順で使用します。

## <a name="configure-nginx-to-use-ssl"></a>SSL を使用するよう Nginx を構成する

### <a name="create-nginx-configuration-file"></a>Nginx 構成ファイルを作成する

このセクションでは、Nginx で SSL を使用するための構成ファイルを作成します。 まず、次のテキストを `nginx.conf` という名前の新しいファイルにコピーします。 Azure Cloud Shell では、Visual Studio Code を使用して作業ディレクトリにファイルを作成できます。

```console
code nginx.conf
```

`location` で、`proxy_pass` にアプリ用の正しいポートを必ず設定してください。 この例では、`aci-helloworld` コンテナー用にポート 80 を設定します。

```console
# nginx Configuration File
# https://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1 TLSv1.1 TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;


        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80
            
            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>シークレットと構成ファイルを Base64 でエンコードする

Nginx 構成ファイル、SSL 証明書、SSL キーを Base64 でエンコードします。 次のセクションで、エンコードされたコンテンツを、コンテナー グループのデプロイに使用される YAML ファイルに入力します。

```console
cat nginx.conf | base64 -w 0 > base64-nginx.conf
cat ssl.crt | base64 -w 0 > base64-ssl.crt
cat ssl.key | base64 -w 0 > base64-ssl.key
```

## <a name="deploy-container-group"></a>コンテナー グループをデプロイする

次に、[YAML ファイル](container-instances-multi-container-yaml.md)にコンテナー構成を指定してコンテナー グループをデプロイします。

### <a name="create-yaml-file"></a>YAML ファイルを作成する

次の YAML を、`deploy-aci.yaml` という名前の新しいファイルにコピーします。 Azure Cloud Shell では、Visual Studio Code を使用して作業ディレクトリにファイルを作成できます。

```console
code deploy-aci.yaml
```

Base64 でエンコードされたファイルのコンテンツを、`secret` の下に示された場所に入力します。 たとえば、Base64 でエンコードされた各ファイルを `cat` して、そのコンテンツを表示します。 デプロイ中に、これらのファイルはコンテナー グループ内の[シークレット ボリューム](container-instances-volume-secret.md)に追加されます。 この例では、シークレット ボリュームは Nginx コンテナーにマウントされます。

```YAML
api-version: 2018-10-01
location: westus
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: nginx
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <Enter contents of base64-ssl.crt here>
      ssl.key: <Enter contents of base64-ssl.key here>
      nginx.conf: <Enter contents of base64-nginx.conf here>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>コンテナー グループをデプロイする

[az group create](/cli/azure/group#az-group-create) コマンドでリソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az container create](/cli/azure/container#az-container-create) コマンドでコンテナー グループをデプロイし、YAML ファイルを引数として渡します。

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>デプロイ状態の表示

デプロイの状態を表示するには、次の [az container show](/cli/azure/container#az-container-show) コマンドを使用します。

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

デプロイが成功した場合は、次のような出力が表示されます。

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   mcr.microsoft.com/azuredocs/nginx, aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-ssl-connection"></a>SSL 接続を確認する

実行中のアプリケーションを表示するには、ご利用のブラウザーでその IP アドレスにアクセスします。 たとえば、この例の IP アドレスは `52.157.22.76` です。 Nginx サーバーの構成により、実行中のアプリケーションを表示するには `https://<IP-ADDRESS>` を使用する必要があります。 `http://<IP-ADDRESS>` で接続しようとすると、失敗します。

![Azure コンテナー インスタンスで実行されているアプリケーションを示すブラウザー スクリーンショット](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> この例では証明機関の証明書ではなく、自己署名証明書を使用するため、HTTPS 経由でサイトに接続しようとするとブラウザにセキュリティの警告が表示されます。 これは正しい動作です。
>

## <a name="next-steps"></a>次の手順

この記事では、コンテナー グループ内で実行している Web アプリに SSL 接続できるよう Nginx コンテナーを設定する方法について説明しました。 ポート 80 以外のポートをリッスンするアプリの例を適応させることもできます。 ポート 80 (HTTP) 上のサーバー接続を自動的にリダイレクトして HTTPS を使用するよう、Nginx 構成ファイルを更新することもできます。

この記事ではサイドカー内の Nginx を使用しましたが、[Caddy](https://caddyserver.com/) などの別の SSL プロバイダーを使用することもできます。

コンテナー グループ内で SSL を有効にする別の方法として、[Azure Application Gateway](../application-gateway/overview.md) を使用した [Azure Virtual Network](container-instances-vnet.md) にグループをデプロイするというものもあります。 ゲートウェイは、SSL エンドポイントとして設定できます。 ゲートウェイ上で SSL ターミネーションを有効にするために適応可能な、サンプルの[デプロイ テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet)を参照してください。
