---
title: Azure Container Instances で liveness probe を構成する
description: Azure Container Instances で liveness probe を構成して、異常なコンテナーを再起動する方法について説明します
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ms.openlocfilehash: 28205d6db85d7a5051f283445d95dd2375e174c8
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325866"
---
# <a name="configure-liveness-probes"></a>liveness probe の構成

コンテナー化されたアプリケーションは、実行に時間がかかり、分断状態になり、コンテナーを再起動して修復する必要が生じる場合があります。 Azure Container Instances では、構成を含む liveness probe がサポートされていて、重要な機能が動作しなくなった場合にお使いのコンテナーを再起動できます。

この記事では、liveness probe を含むコンテナー グループをデプロイする方法について説明し、シミュレーションした異常なコンテナーの自動再起動方法を示します。

## <a name="yaml-deployment"></a>YAML のデプロイ

次のコードを使用して `liveness-probe.yaml` ファイルを作成します。 このファイルは、最終的に異常状態になる NGNIX コンテナーから構成されるコンテナー グループを定義します。

```yaml
apiVersion: 2018-06-01
location: eastus
name: livenesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: nginx
      command:
        - "/bin/sh"
        - "-c"
        - "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      livenessProbe:
        exec:
            command:
                - "cat"
                - "/tmp/healthy"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

次のコマンドを実行し、上記の YAML 構成を使用してこのコンテナー グループをデプロイします。

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>コマンドを開始する

このデプロイによって、コンテナーが初めて実行されるときに実行される開始コマンドが定義されます。これは文字列の配列を受け入れる `command` プロパティによって定義されます。 この例では、これは Bash セッションを開始し、このコマンドを渡すことによって `healthy` というファイルを `/tmp` ディレクトリ内に作成します。

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 その後 30 秒間スリープ状態になり、ファイルを削除してから 10 分間スリープ状態になります。

### <a name="liveness-command"></a>Liveness コマンド

このデプロイは、liveness 検査として動作する `exec` liveness コマンドをサポートする `livenessProbe` を定義します。 このコマンドがゼロ以外の値で終了すると、コンテナーが強制終了して再起動し、`healthy` ファイルが見つからなかったことを通知します。 このコマンドが終了コード 0 で正常終了した場合、アクションは行われません。

`periodSeconds` プロパティは、liveness コマンドを 5 秒ごとに実行することを指定します。

## <a name="verify-liveness-output"></a>liveness の出力を確認する

最初の 30 秒以内には、start コマンドによって作成された `healthy` ファイルが存在します。 liveness コマンドが `healthy` ファイルの存在を検査するとき、状態コードがゼロを返し、成功を通知するため、再起動は行われません。

30 秒後、`cat /tmp/healthy` で異常が発生し始め、異常状態および強制終了イベントが発生します。

これらのイベントは、Azure Portal または Azure CLI から表示できます。

![Portal の異常なイベント][portal-unhealthy]

Azure Portal でイベントを表示することによって、liveness コマンド失敗時に `Unhealthy` タイプのイベントがトリガーされます。 後続のイベントは `Killing` タイプで、再起動を開始できるようにコンテナーの削除を示します。 コンテナーの再起動カウントは、これが発生するたびに増分されます。

パブリック IP アドレスやノード固有のコンテンツなどのリソースが保持されるように再起動が適切に完了します。

![Portal の再起動カウンター][portal-restart]

liveness probe が継続的に失敗し、過剰な回数の再起動がトリガーされる場合、お使いのコンテナーは指数バック オフ遅延になります。

## <a name="liveness-probes-and-restart-policies"></a>Liveness probe および再起動のポリシー

再起動ポリシーは、liveness probe によってトリガーされる再起動動作よりも優先されます。 たとえば、`restartPolicy = Never` *および* liveness probe を設定した場合、コンテナー グループは、liveness 検査失敗イベントでは再起動しません。 コンテナー グループはコンテナー グループの再起動ポリシーである `Never` に従います。

## <a name="next-steps"></a>次の手順

タスク ベースのシナリオでは、前提となる機能が正しく動作しない場合に自動再起動を有効にする liveness probe が必要になる場合があります。 タスク ベースのコンテナーの実行に関する詳細については、「[Azure Container Instances でコンテナー化タスクを実行する](container-instances-restart-policy.md)」を参照してください。

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
