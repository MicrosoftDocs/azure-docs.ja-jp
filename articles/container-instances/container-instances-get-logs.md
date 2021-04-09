---
title: コンテナー インスタンスのログとイベントの取得
description: コンテナーの問題のトラブルシューティングに役立つように、Azure Container Instances でコンテナーのログとイベントを取得する方法について説明します
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 20b6e1cfe6bb8f6ac721a401c3d0831d4f447edb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92746959"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Azure Container Instances でコンテナーのログとイベントを取得する

Azure Container Instances でコンテナーが正常に動作しない場合、[az container logs][az-container-logs] でそのログを確認し、[az container attach][az-container-attach] でその標準出力と標準エラーをストリーム配信することから始めます。 Azure portal でコンテナー インスタンスのログとイベントを表示したり、コンテナー グループのログとイベント データを [Azure Monitor ログ](container-instances-log-analytics.md)に送信したりすることもできます。

## <a name="view-logs"></a>ログを表示する

アプリケーション コードからコンテナー内のログを表示するには、[az container logs][az-container-logs] コマンドを使用できます。

次に示すのは、[コンテナー インスタンスでのコマンド ラインの設定](container-instances-start-command.md#azure-cli-example)に関する記事のタスク ベースのコンテナーの例に、コマンド ラインのオーバーライドを使用して無効な URL を指定した後のログ出力です。

```azurecli
az container logs --resource-group myResourceGroup --name mycontainer
```

```output
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

## <a name="attach-output-streams"></a>出力ストリームのアタッチ

[az container attach][az-container-attach] コマンドによって、コンテナーの起動中の診断情報が提供されます。 コンテナーが起動すると、ローカルのコンソールにコンテナーの STDOUT と STDERR がストリーミングされます。

例として、[コンテナー インスタンスでのコマンド ラインの設定](container-instances-start-command.md#azure-cli-example)に関する記事のタスク ベースのコンテナーに、処理する大きなテキスト ファイルの有効な URL を指定した後の、コンテナーからの出力を次に示します。

```azurecli
az container attach --resource-group myResourceGroup --name mycontainer
```

```output
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
Container 'mycontainer1' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:52+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Started container

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>診断イベントの取得

コンテナーが正常にデプロイされない場合は、Azure Container Instances のリソース プロバイダーによって提供される診断情報を確認してください。 コンテナーのイベントを表示するには、[az container show][az-container-show] コマンドを実行します。

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

出力には、コンテナーの主要プロパティとデプロイ イベント (抜粋) が含まれます。

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "mcr.microsoft.com/azuredocs/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:22+00:00",
            "lastTimestamp": "2019-03-21T19:46:22+00:00",
            "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:28+00:00",
            "lastTimestamp": "2019-03-21T19:46:28+00:00",
            "message": "Successfully pulled image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Created container",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Started container",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```
## <a name="next-steps"></a>次のステップ
Azure Container Instances の[コンテナーとデプロイに関する一般的な問題をトラブルシューティングする](container-instances-troubleshooting.md)方法を学習します。

コンテナー グループのログおよびイベント データを [Azure Monitor ログ](container-instances-log-analytics.md)に送信する方法を学習します。

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
