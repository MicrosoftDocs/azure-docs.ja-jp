---
title: 一般的な問題のトラブルシューティング
description: Azure Container Instances をデプロイ、実行、または管理する際の一般的な問題をトラブルシューティングする方法を学習します。
ms.topic: article
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d8e7fb85e369f5f278436370944eafeb1fb6a50e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96779517"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Azure Container Instances における、トラブルシューティングに関する一般的問題

この記事では、Azure Container Instances を管理し、またはこれ にコンテナーをデプロイする際の、一般的問題をトラブルシューティングする方法を示します。 [よく寄せられる質問](container-instances-faq.md)も参照してください。

追加のサポートが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) で利用可能な **[ヘルプとサポート]** オプションをご覧ください。

## <a name="issues-during-container-group-deployment"></a>コンテナー グループのデプロイ時に発生する問題
### <a name="naming-conventions"></a>名前付け規則

コンテナーの仕様を定義するときに、特定のパラメーターは名前付けの制限に準拠している必要があります。 下記は、コンテナーグループの特性のための、特定の要件を持つテーブルです。 詳細については、Azure アーキテクチャ センターの「[名前付け規則][azure-name-restrictions]」と、「[Azure リソースの名前付け規則と制限事項][naming-rules]」を参照してください。

| Scope | 長さ | 大文字小文字の区別 | 有効な文字 | 提案されるパターン | 例 |
| --- | --- | --- | --- | --- | --- |
| コンテナー名<sup>1</sup> | 1 ～ 63 |小文字 | 最初と最後の文字を除く任意の場所の英数字とハイフン |`<name>-<role>-container<number>` |`web-batch-container1` |
| コンテナーポート | 1 ～ 65535 の範囲 |Integer |1 ～ 65535 の整数 |`<port-number>` |`443` |
| DNS 名ラベル | 5-63 |大文字と小文字は区別されない |最初と最後の文字を除く任意の場所の英数字とハイフン |`<name>` |`frontend-site1` |
| 環境変数 | 1 ～ 63 |大文字と小文字は区別されない |最初と最後の文字を除く任意の場所の英数字とアンダースコア (_) |`<name>` |`MY_VARIABLE` |
| ボリューム名 | 5-63 |小文字 |最初と最後の文字を除く任意の場所の英数字とハイフン。 2つの連続するハイフンを含めることはできません。 |`<name>` |`batch-output-volume` |

<sup>1</sup>コンテナー インスタンスとは独立して指定されていない場合 (`az container create` コマンドの展開など)、コンテナー グループ名にも制限がかかります。

### <a name="os-version-of-image-not-supported"></a>イメージの OS バージョンがサポートされていない

Azure Container Instances でサポートされていないイメージを指定した場合は、`OsVersionNotSupported` エラーが返されます。 エラーは次のようになり、`{0}` はデプロイしようとしたイメージの名前です。

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

このエラーは、半期チャネル (SAC) リリース 1709 または 1803 に基づく Windows イメージ (サポートされていない) をデプロイするときに最も多く発生します。 Azure Container Instances でサポートされている Windows イメージについては、[よく寄せられる質問](container-instances-faq.md#what-windows-base-os-images-are-supported)を参照してください。

### <a name="unable-to-pull-image"></a>イメージをプルできない

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
### <a name="resource-not-available-error"></a>リソース使用不可エラー

Azure ではリージョンによってリソースの読み込みに変化があるため、コンテナー インスタンスをデプロイしようとすると、以下のエラーが表示される場合があります。

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

このエラーは、デプロイを試行しているリージョンで高負荷になっているため、コンテナーに指定されたリソースが、その時点では割り当てできないことを示しています。 以下に示す 1 つ以上の軽減策の手順を使用して、問題を解決してください。

* コンテナーのデプロイ設定が、[Azure Container Instances のリージョンでの利用可否](container-instances-region-availability.md)に関する記事で定義されているパラメーター内に収まっていることを確認する
* コンテナーに低い CPU およびメモリ設定を指定する。
* 別の Azure リージョンにデプロイする
* 後でデプロイする

## <a name="issues-during-container-group-runtime"></a>コンテナー グループの実行時に発生する問題
### <a name="container-continually-exits-and-restarts-no-long-running-process"></a>コンテナーが絶えず終了して再起動する (長時間実行されるプロセスがない)

コンテナー グループは [再起動ポリシー](container-instances-restart-policy.md)が既定で **Always** に設定されるため、コンテナー グループ内のコンテナーは実行完了後に必ず再起動します。 タスクベースのコンテナーを実行する場合は、これを **OnFailure** または **Never** に変更することが必要になることがあります。 **OnFailure** を指定してもそのまま再起動された場合、お使いのコンテナーで実行されるアプリケーションまたはスクリプトに問題が生じている可能性があります。

Ubuntu や Alpine などのイメージを使用した場合、長時間実行されるプロセスのないコンテナー グループを実行していると、終了と再起動が繰り返されることがあります。 コンテナーを実行したままにするプロセスがないため、[EXEC](container-instances-exec.md) を使った接続は機能しません。 この問題を解決するには、コンテナー グループのデプロイに次のような起動コマンドを含め、コンテナーを実行したままにします。

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
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

### <a name="container-takes-a-long-time-to-start"></a>コンテナーの起動に時間がかかる

Azure Container Instances のコンテナーの起動時間に関係する 3 つの主な要素を、次に示します。

* [イメージ サイズ](#image-size)
* [イメージの場所](#image-location)
* [キャッシュされたイメージ](#cached-images)

Windows イメージには、[追加の考慮事項](#cached-images)があります。

#### <a name="image-size"></a>イメージ サイズ

コンテナーが起動するまで時間がかかるが、最終的に起動する場合は、コンテナー イメージのサイズを調べることから始めてください。 Azure Container Instances は、要求に応じてコンテナー イメージをプルするため、起動時間はそのサイズと直接的に関係しています。

コンテナー イメージのサイズは、Docker CLI で `docker images` コマンドを使用することで表示できます。

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

イメージのサイズを小さくしておくための鍵は、最終イメージに実行時に不要なものが含まれないようにすることです。 これを行う 1 つの方法は、[マルチステージ ビルド][docker-multi-stage-builds]を使用することです。 マルチステージ ビルドを使用すると、最終イメージにはアプリケーションに必要な成果物のみが含まれ、ビルド時に必要であった余分なコンテンツは含まれないようにすることを簡単に実行できます。

#### <a name="image-location"></a>イメージの場所

コンテナーの起動時に発生するイメージ プルの影響を軽減する別の方法は、コンテナー インスタンスをデプロイする予定のリージョンと同じリージョン内の [Azure Container Registry](../container-registry/index.yml) で、コンテナー イメージをホストすることです。 これにより、コンテナー イメージを伝送する必要があるネットワーク パスが短縮され、ダウンロード時間が大幅に短くなります。

#### <a name="cached-images"></a>キャッシュ イメージ

Azure Container Instances では、キャッシュ メカニズムを使用して、`nanoserver:1809`、`servercore:ltsc2019`、`servercore:1809` など、一般的な [Windows ベースのイメージ](container-instances-faq.md#what-windows-base-os-images-are-supported)に基づいて構築されたイメージに対するコンテナーの起動時間を高速化します。 `ubuntu:1604` や `alpine:3.6` など、よく使用される Linux イメージもキャッシュされます。 Windows と Linux の両方のイメージには、`latest` タグを使用しないようにしてください。 ガイダンスについては、Container Registry の[イメージ タグのベスト プラクティス](../container-registry/container-registry-image-tag-version.md)に関する記事を参照してください。 キャッシュされたイメージとタグの最新の一覧については、[List Cached Images][list-cached-images] API を使用してください。

> [!NOTE]
> Azure Container Instances での Windows Server 2019 ベースのイメージの使用は、プレビュー段階です。

#### <a name="windows-containers-slow-network-readiness"></a>Windows コンテナーの低速のネットワークの準備

接続の初期作成時に、最大で 30 秒間 (まれではありますがさらに長くなることもあります)、Windows コンテナーに受信または送信の接続ができない場合があります。 コンテナー アプリケーションにインターネット接続が必要な場合は、遅延を加えてロジックを再試行し、30 秒間でインターネット接続を確立できるようにします。 初期セットアップ後に、コンテナーのネットワークが適切に再開する必要があります。

### <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>基になる Docker API に接続できないか、特権コンテナーを実行できない

Azure Container Instances は、コンテナー グループをホストする、基になるインフラストラクチャへの直接アクセスを公開しません。 これには、コンテナーのホストで実行されている Docker API へのアクセスと、実行中の特権コンテナーへのアクセスが含まれます。 Docker の相互作用が必要な場合は、[REST リファレンス ドキュメント](/rest/api/container-instances/)を参照して、ACI API でサポートされるものをご確認ください。 不足しているものがある場合は、[ACI フィードバック フォーラム](https://aka.ms/aci/feedback)に要求を送信します。

### <a name="container-group-ip-address-may-not-be-accessible-due-to-mismatched-ports"></a>ポートが一致しないため、コンテナー グループの IP アドレスにアクセスできない

Azure Container Instances では、通常の Docker 構成のようなポート マッピングはまだサポートされていません。 コンテナー グループの IP アドレスにアクセスできるはずの場合にアクセスできない場合は、`ports` プロパティを使用してコンテナー グループで公開しているのと同じポートをリッスンするようにコンテナー イメージを構成してください。

ご自分のコンテナー イメージで構成したポートで Azure Container Instances がリッスンできることを確認したい場合は、ポートを公開する `aci-helloworld` イメージのデプロイをテストします。 さらに、このポートでリッスンするように `aci-helloworld` アプリを実行します。 `aci-helloworld` はオプションの環境変数 `PORT` を受け取り、既定のリッスン ポート 80 を上書きします。 たとえば、ポート 9000 をテストするには、コンテナー グループを作成するときに[環境変数](container-instances-environment-variables.md)を設定します。

1. ポート 9000 を公開するようにコンテナー グループを設定し、そのポート番号を環境変数の値として渡します。 この例は、Bash シェル用に書式設定されています。 PowerShell やコマンド プロンプトなどの別のシェルを使用する場合は、それに応じて変数の代入を調整する必要があります。
    ```azurecli
    az container create --resource-group myResourceGroup \
    --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --ip-address Public --ports 9000 \
    --environment-variables 'PORT'='9000'
    ```
1. `az container create` のコマンド出力でコンテナー グループの IP アドレスを見つけます。 **ip** の値を探します。 
1. コンテナーが正常にプロビジョニングされた後、ブラウザーでコンテナー アプリの IP アドレスおよびポートに移動します (例: `192.0.2.0:9000`)。 

    "Welcome to Azure Container Instances! (Azure Container Instances へようこそ!)" メッセージが Web アプリによって表示されます。
1. コンテナーを使い終えたら、`az container delete` コマンドを使用して削除します。

    ```azurecli
    az container delete --resource-group myResourceGroup --name mycontainer
    ```

## <a name="next-steps"></a>次のステップ

コンテナーのデバッグを支援するために、[コンテナーのログとイベントを取得する](container-instances-get-logs.md)方法を学習します。

<!-- LINKS - External -->
[azure-name-restrictions]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources
[naming-rules]: ../azure-resource-manager/management/resource-name-rules.md
[windows-sac-overview]: /windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/location/listcachedimages
