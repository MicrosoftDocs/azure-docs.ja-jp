---
title: Azure Container Instances のトラブルシューティング
description: Azure Container Instances に関する問題のトラブルシューティングを行う方法について説明します
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 02/15/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: bf783c988c0163fe562669a8331c332dbf8d535e
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371878"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Azure Container Instances における、トラブルシューティングに関する一般的問題

この記事では、Azure Container Instances を管理し、またはこれ にコンテナーをデプロイする際の、一般的問題をトラブルシューティングする方法を示します。

## <a name="naming-conventions"></a>名前付け規則

コンテナーの仕様を定義するときに、特定のパラメーターは名前付けの制限に準拠している必要があります。 下記は、コンテナーグループの特性のための、特定の要件を持つテーブルです。 Azure の名前付け規則の詳細については、Azure Architecture Center 内の[名前付け規則][azure-name-restrictions]を参照してください。

| Scope (スコープ) | Length | 大文字小文字の区別 | 有効な文字 | 提案されるパターン | 例 |
| --- | --- | --- | --- | --- | --- |
| コンテナー グループ名 | 1 ～ 64 |大文字と小文字は区別されない |最初と最後の文字を除く任意の場所の英数字とハイフン |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| コンテナー名 | 1 ～ 64 |大文字と小文字は区別されない |最初と最後の文字を除く任意の場所の英数字とハイフン |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| コンテナーポート | 1 ～ 65535 の範囲 |整数 |1 ～ 65535 の整数 |`<port-number>` |`443` |
| DNS 名ラベル | 5-63 |大文字と小文字は区別されない |最初と最後の文字を除く任意の場所の英数字とハイフン |`<name>` |`frontend-site1` |
| 環境変数 | 1 ～ 63 |大文字と小文字は区別されない |最初と最後の文字を除く任意の場所の英数字とアンダースコア (_) |`<name>` |`MY_VARIABLE` |
| ボリューム名 | 5-63 |大文字と小文字は区別されない |最初と最後の文字を除く任意の場所の小文字のアルファベット、数字、およびハイフン。 2つの連続するハイフンを含めることはできません。 |`<name>` |`batch-output-volume` |

## <a name="os-version-of-image-not-supported"></a>イメージの OS バージョンがサポートされていない

Azure Container Instances でサポートされていないイメージを指定した場合は、`OsVersionNotSupported` エラーが返されます。 エラーは次のようになり、`{0}` はデプロイしようとしたイメージの名前です。

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

このエラーは、半期チャネル (SAC) リリースに基づく Windows イメージを展開するときに最も多く発生します。 たとえば、Windows バージョン 1709 および 1803 は SAC リリースであり、展開時にこのエラーを生成します。

Azure Container Instances は現在、**Windows Server 2016 の長期的なサービス チャネル (LTSC)** リリースにのみ基づいた Windows イメージをサポートしています。 Windows コンテナーのデプロイ時にこの問題を軽減するには、常に Windows Server 2016 (LTSC) ベースのイメージをデプロイしてください。 Windows Server 2019 (LTSC) に基づいたイメージはサポートされていません。

Windows の LTSC および SAC バージョンについて詳しくは、「[Windows Server の半期チャネルの概要][windows-sac-overview]」をご覧ください。

## <a name="unable-to-pull-image"></a>イメージをプルできない

Azure Container Instances は、最初にイメージをプルできなかった場合に、一定期間再試行します。 イメージのプル操作の失敗が続く場合、ACI は最終的に展開に失敗し、`Failed to pull image` エラーが表示されることがあります。

この問題を解決するには、コンテナー インスタンスを削除し、展開を再試行します。 イメージがレジストリに存在し、イメージの名前を正しく入力したことをご確認ください。

イメージをプルできない場合は、[az container show][az-container-show] の出力に次のようなイベントが表示されます。

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"mcr.microsoft.com/azuredocs/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

## <a name="container-continually-exits-and-restarts-no-long-running-process"></a>コンテナーが絶えず終了して再起動する (長時間実行されるプロセスがない)

コンテナー グループは[再起動ポリシー](container-instances-restart-policy.md)が既定で **Always** に設定されるため、コンテナー グループ内のコンテナーは実行完了後に必ず再起動します。 タスクベースのコンテナーを実行する場合は、これを **OnFailure** または **Never** に変更することが必要になることがあります。 **OnFailure** を指定してもそのまま再起動された場合、お使いのコンテナーで実行されるアプリケーションまたはスクリプトに問題が生じている可能性があります。

Ubuntu や Alpine などのイメージを使用した場合、長時間実行されるプロセスのないコンテナー グループを実行していると、終了と再起動が繰り返されることがあります。 コンテナーを実行したままにするプロセスがないため、[EXEC](container-instances-exec.md) を使った接続は機能しません。 この問題を解決するには、コンテナー グループのデプロイに次のような起動コマンドを含め、コンテナーを実行したままにします。

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2016
 --command-line "ping -t localhost"
```

Container Instances API と Azure portal には `restartCount` プロパティが含まれています。 コンテナーの再起動の回数を確認するには、Azure CLI の [az container show][az-container-show] コマンドを使用できます。 次の出力例 (簡略化のため一部のみ) では、出力の末尾に `restartCount` プロパティを確認できます。

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> Linux ディストリビューションのほとんどのコンテナー イメージは、既定のコマンドとして、bash などのシェルを設定します。 独自のシェルは実行時間の長いサービスではないため、既定の **[常時]** 再起動ポリシーを利用して構成された場合、これらのコンテナーはすぐに終了し、再起動ループ状態に陥ります。

## <a name="container-takes-a-long-time-to-start"></a>コンテナーの起動に時間がかかる

Azure Container Instances のコンテナーの起動時間に関係する 2 つの主な要素を、次に示します。

* [イメージ サイズ](#image-size)
* [イメージの場所](#image-location)

Windows イメージには、[追加の考慮事項](#cached-windows-images)があります。

### <a name="image-size"></a>イメージ サイズ

コンテナーが起動するまで時間がかかるが、最終的に起動する場合は、コンテナー イメージのサイズを調べることから始めてください。 Azure Container Instances は、要求に応じてコンテナー イメージをプルするため、起動時間はそのサイズと直接的に関係しています。

コンテナー イメージのサイズは、Docker CLI で `docker images` コマンドを使用することで表示できます。

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

イメージのサイズを小さくしておくための鍵は、最終イメージに実行時に不要なものが含まれないようにすることです。 これを行う 1 つの方法は、[マルチステージ ビルド][docker-multi-stage-builds]を使用することです。 マルチステージ ビルドを使用すると、最終イメージにはアプリケーションに必要な成果物のみが含まれ、ビルド時に必要であった余分なコンテンツは含まれないようにすることを簡単に実行できます。

### <a name="image-location"></a>イメージの場所

コンテナーの起動時に発生するイメージ プルの影響を軽減する別の方法は、コンテナー インスタンスをデプロイする予定のリージョンと同じリージョン内の [Azure Container Registry](/azure/container-registry/) で、コンテナー イメージをホストすることです。 これにより、コンテナー イメージを伝送する必要があるネットワーク パスが短縮され、ダウンロード時間が大幅に短くなります。

### <a name="cached-windows-images"></a>キャッシュされた Windows イメージ

Azure Container Instances では、キャッシュ メカニズムを使用して、一般的な Windows および Linux イメージに基づくイメージに対するコンテナーの起動時間を高速化します。 キャッシュされたイメージとタグの詳細な一覧については、[List Cached Images][list-cached-images] API を使用してください。

Windows コンテナーの起動時間を最速にするには、次の **2 つのイメージ**の**最新の 3 つ**のバージョンのいずれかを、基本イメージとして使用します。

* [Windows Server Core 2016][docker-hub-windows-core] (LTSC のみ)
* [Windows Server 2016 Nano Server][docker-hub-windows-nano]

### <a name="windows-containers-slow-network-readiness"></a>Windows コンテナーの低速のネットワークの準備

接続の初期作成時に、最大で 30 秒間 (まれではありますがさらに長くなることもあります)、Windows コンテナーに受信または送信の接続ができない場合があります。 コンテナー アプリケーションにインターネット接続が必要な場合は、遅延を加えてロジックを再試行し、30 秒間でインターネット接続を確立できるようにします。 初期セットアップ後に、コンテナーのネットワークが適切に再開する必要があります。

## <a name="resource-not-available-error"></a>リソース使用不可エラー

Azure ではリージョンによってリソースの読み込みに変化があるため、コンテナー インスタンスをデプロイしようとすると、以下のエラーが表示される場合があります。

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

このエラーは、デプロイを試行しているリージョンで高負荷になっているため、コンテナーに指定されたリソースが、その時点では割り当てできないことを示しています。 以下に示す 1 つ以上の軽減策の手順を使用して、問題を解決してください。

* コンテナーのデプロイ設定が、[Azure Container Instances のリージョンでの利用可否](container-instances-region-availability.md)に関する記事で定義されているパラメーター内に収まっていることを確認する
* コンテナーに低い CPU およびメモリ設定を指定する。
* 別の Azure リージョンにデプロイする
* 後でデプロイする

## <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>基になる Docker API に接続できないか、特権コンテナーを実行できない

Azure Container Instances は、コンテナー グループをホストする、基になるインフラストラクチャへの直接アクセスを公開しません。 これには、コンテナーのホストで実行されている Docker API へのアクセスと、実行中の特権コンテナーへのアクセスが含まれます。 Docker の相互作用が必要な場合は、[REST リファレンス ドキュメント](https://aka.ms/aci/rest)を参照して、ACI API でサポートされるものをご確認ください。 不足しているものがある場合は、[ACI フィードバック フォーラム](https://aka.ms/aci/feedback)に要求を送信します。

## <a name="ips-may-not-be-accessible-due-to-mismatched-ports"></a>ポートが一致しないために IP にアクセスできない

現在、Azure Container Instances は、通常の docker 構成のようなポート マッピングをサポートしていませんが、この修正はロードマップにあります。 IP にアクセスできるはずの場合にアクセスできない場合は、`ports` プロパティを使用してコンテナー グループで公開しているものと同じポートをリッスンするようにコンテナー イメージを構成してください。

## <a name="next-steps"></a>次の手順

コンテナーのデバッグを支援するために、[コンテナーのログとイベントを取得する](container-instances-get-logs.md)方法を学習します。

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/listcachedimages
