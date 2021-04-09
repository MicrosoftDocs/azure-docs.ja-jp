---
title: コンテナー インスタンスに readiness probe を設定する
description: probe を構成して、Azure Container Instances のコンテナーの準備ができたときのみ要求を受信するようにする方法について説明します。
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: 3e89086d66f284df35e36dc8f1d68bb09264843f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86169665"
---
# <a name="configure-readiness-probes"></a>readiness probe の構成

トラフィックを処理するコンテナー化されたアプリケーションの場合、コンテナーが受信要求を処理する準備ができているかどうか確認したほうがいいです。 Azure Container Instances は、特定の条件下でコンテナーにアクセスできないようにする構成を組み込む、readiness probe をサポートしています。 readiness probe は、[Kubernetes readiness probe](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/) のように動作します。 たとえば、コンテナー アプリが起動時に大規模なデータセットを読み込む必要があり、この間は要求を受信したくない場合があります。

この記事では、readiness probe を含むコンテナー グループをデプロイして、probe が成功したときにのみコンテナーがトラフィックを受信するようにする方法について説明します。

Azure Container Instances は、[liveness probe](container-instances-liveness-probe.md) もサポートしています。これを構成すると、異常なコンテナーが自動的に再起動されます。

> [!NOTE]
> 現時点では、仮想ネットワークにデプロイされたコンテナー グループ内で readiness probe を使用することはできません。

## <a name="yaml-configuration"></a>YAML の構成

例として、readiness probe を含む次のスニペットを使用して `readiness-probe.yaml` ファイルを作成します。 このファイルは、小規模な Web アプリを実行するコンテナーで構成される、コンテナー グループを定義します。 アプリは、パブリックの `mcr.microsoft.com/azuredocs/aci-helloworld` イメージからデプロイされます。 このコンテナー アプリは、「[Azure CLI を使用してコンテナー インスタンスを Azure にデプロイする](container-instances-quickstart.md)」やその他のクイックスタートにも示されています。

```yaml
apiVersion: 2019-12-01
location: eastus
name: readinesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      command:
        - "/bin/sh"
        - "-c"
        - "node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait"
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      readinessProbe:
        exec:
          command:
          - "cat"
          - "/tmp/ready"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="start-command"></a>開始コマンド

デプロイには、コンテナーの初回の実行開始時に実行される開始コマンドを定義する `command` プロパティが含まれています。 このプロパティは、文字列の配列を受け入れます。 このコマンドは、Web アプリが実行されているがコンテナーの準備ができていない時間をシミュレートします。 

まず、シェル セッションを開始し、`node` コマンドを実行して Web アプリを起動します。 また、240 秒間スリープするコマンドを開始します。その後、`/tmp` ディレクトリ内に `ready` という名前のファイルを作成します。

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>readiness コマンド

この YAML ファイルは、readiness チェックとして機能する `exec` readiness コマンドをサポートする `readinessProbe` を定義します。 この例の readiness コマンドでは、`/tmp` ディレクトリに `ready` ファイルがあるかどうかをテストします。

`ready` ファイルが存在しない場合、readiness コマンドは 0 以外の値で終了します。コンテナーは実行を続行しますが、アクセスすることはできません。 コマンドが終了コード 0 で正常に終了すると、コンテナーにアクセスできるようになります。 

`periodSeconds` プロパティは、readiness コマンドを 5 秒ごとに実行するように指定します。 readiness probe は、コンテナー グループの有効期間にわたって実行されます。

## <a name="example-deployment"></a>デプロイ例

次のコマンドを実行して、上記の YAML 構成を含むコンテナー グループをデプロイします。

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>readiness チェックの表示

この例では、最初の 240 秒間、`ready` ファイルが存在するかどうかチェックしたときに readiness コマンドが失敗します。 返された状態コードは、コンテナーの準備ができていないことを通知します。

これらのイベントは、Azure Portal または Azure CLI から表示できます。 たとえば、ポータルには、readiness コマンドが失敗したときにトリガーされる種類 `Unhealthy` のイベントが表示されます。 

![Portal の異常なイベント][portal-unhealthy]

## <a name="verify-container-readiness"></a>コンテナーの readiness の確認

コンテナーを開始した後、最初はアクセスできないことを確認できます。 プロビジョニングの後、コンテナー グループの IP アドレスを取得します。

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

readiness probe が失敗したときにサイトにアクセスしてみます。

```bash
wget <ipAddress>
```

出力は、最初はサイトにアクセスできないことを示しています。
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

240 秒後、readiness コマンドが成功し、コンテナーの準備ができていることが通知されます。 今度は、`wget` コマンドを実行すると成功します。

```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response...200 OK
Length: 1663 (1.6K) [text/html]
Saving to: ‘index.html.1’

index.html.1                       100%[===============================================================>]   1.62K  --.-KB/s    in 0s      

2019-10-15 16:49:38 (113 MB/s) - ‘index.html.1’ saved [1663/1663] 
```

コンテナーの準備ができたら、Web ブラウザーを使用して IP アドレスを参照することにより、Web アプリにアクセスすることもできます。

> [!NOTE]
> readiness probe は、コンテナー グループの有効期間中は継続して実行されます。 readiness コマンドが後になって失敗した場合、コンテナーに再度アクセスできなくなります。 
> 

## <a name="next-steps"></a>次のステップ

readiness probe は、依存するコンテナーで構成される複数コンテナー グループを必要とするシナリオで役に立ちます。 複数コンテナーのシナリオの詳細については、「[Azure Container Instances でのコンテナー グループ](container-instances-container-groups.md)」を参照してください。

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
