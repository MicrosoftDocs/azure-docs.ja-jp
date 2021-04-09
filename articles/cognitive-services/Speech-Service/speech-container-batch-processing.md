---
title: 音声コンテナー用のバッチ処理キット
titleSuffix: Azure Cognitive Services
description: バッチ処理キットを使用して、音声コンテナー要求をスケーリングします。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/22/2020
ms.author: aahi
ms.openlocfilehash: cc6bcef77ca1601b76468586aa6af202836f1438
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97631994"
---
# <a name="batch-processing-kit-for-speech-containers"></a>音声コンテナー用のバッチ処理キット

バッチ処理キットを使用して、音声コンテナー上のワークロードを補完およびスケールアウトします。 このオープン ソース ユーティリティは、コンテナーとして利用可能であり、任意の数のオンプレミスおよびクラウド ベースの音声コンテナー エンドポイントにわたって、多数のオーディオ ファイルのバッチ文字起こしを容易にすることができます。 

:::image type="content" source="media/containers/general-diagram.png" alt-text="バッチキットのコンテナー ワークフローの例を示す図。":::

バッチ キット コンテナーは [GitHub](https://github.com/microsoft/batch-processing-kit) と [Docker ハブ](https://hub.docker.com/r/batchkit/speech-batch-kit/tags)で無料で入手できます。 使用する音声コンテナーに対してのみ、[課金](speech-container-howto.md#billing)されます。

| 特徴量  | 説明  |
|---------|---------|
| バッチ オーディオ ファイルの配布     | オンプレミスまたはクラウドベースの音声コンテナー エンドポイントに多数のファイルを自動的にディスパッチします。 ファイルは、ネットワーク ファイル システムなど、POSIX 準拠のボリュームに配置することができます。       |
| Speech SDK の統合 | n-best 仮説、ダイアライゼーション、言語、冒とく的表現のマスキングなど、一般的なフラグを Speech SDK に渡します。  |
|実行モード     | バッチ クライアントを 1 回、バックグラウンドで継続的に実行するか、オーディオ ファイルの HTTP エンドポイントを作成します。         |
| フォールト トレランス | 進行状況を失うことなく文字起こしの再試行と続行を自動的に行い、再試行できるエラーとそうでないものを区別します。 |
| エンドポイントの可用性検出 | エンドポイントが使用不能になった場合、バッチ クライアントによって他のコンテナー エンドポイントが使用され、文字起こしが続行されます。 再び使用可能になると、クライアントによってエンドポイントの使用が自動的に開始されます。   |
| エンドポイントのホットスワップ | バッチの進行を中断せずに、実行時に音声コンテナー エンドポイントの追加、削除、または変更を行います。 更新は即時に行われます。 |
| リアルタイムのログ記録 | 各オーディオ ファイルの Speech SDK ログ ファイルを使用した、試行された要求、タイムスタンプ、およびエラーの理由のリアルタイムのログ記録。 |

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull` によるコンテナー イメージの取得

[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用して、最新のバッチ キット コンテナーをダウンロードします。

```bash
docker pull docker.io/batchkit/speech-batch-kit:latest
```

## <a name="endpoint-configuration"></a>Endpoint configuration (エンドポイントの構成)

バッチ クライアントで、オンプレミス コンテナー エンドポイントが指定された "yaml" 構成ファイルを受け取ります。 次の例は、以下の例で使用される `/mnt/my_nfs/config.yaml` に書き込むことができます。 

```yaml
MyContainer1: 
  concurrency: 5 
  host: 192.168.0.100 
  port: 5000 
  rtf: 3 
MyContainer2: 
  concurrency: 5 
  host: BatchVM0.corp.redmond.microsoft.com 
  port: 5000 
  rtf: 2 
MyContainer3: 
  concurrency: 10 
  host: localhost 
  port: 6001 
  rtf: 4 
```

この yaml の例では、3 つのホストに 3 つの音声コンテナーを指定しています。 最初のホストは IPv4 アドレスで指定され、2 つ目のホストはバッチ クライアントと同じ VM で実行され、3 つ目のコンテナーは別の VM の DNS ホスト名で指定されています。 `concurrency` 値には、同じコンテナーで実行できる最大同時ファイル文字起こし数を指定します。 `rtf` (リアルタイム係数) の値は省略可能であり、パフォーマンスの調整に使用できます。
 
バッチ クライアントを使用すると、(たとえば、コンテナーの再起動やネットワークの問題などが原因で) エンドポイントが使用不能になった場合と、再び使用可能になったときを動的に検出できます。 文字起こし要求は使用不能なコンテナーには送信されず、クライアントでは他の使用可能なコンテナーが引き続き使用されます。 バッチの進行を中断することなく、いつでもエンドポイントを追加、削除、または編集できます。


## <a name="run-the-batch-processing-container"></a>バッチ処理コンテナーを実行する
  
> [!NOTE] 
> * この例では、構成ファイルと入力、出力、およびログのディレクトリに同じディレクトリ (`/my_nfs`) を使用しています。 これらのフォルダーには、ホストされたディレクトリまたは NFS でマウントされたディレクトリを使用できます。
> * `–h` を使用してクライアントを実行すると、使用できるコマンドライン パラメーターとその既定値が一覧表示されます。 
> * バッチ処理コンテナーは、Linux でのみサポートされています。

Docker の `run` コマンドを使用してコンテナーを開始します。 その結果、コンテナー内で対話型シェルが開始されます。

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs --entrypoint /bin/bash /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest
```

バッチ クライアントを実行するには:  

```Docker
run-batch-client -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -file_log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```

バッチ クライアントとコンテナーを 1 つのコマンドで実行するには:

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest  -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -file_log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```


クライアントの実行が開始されます。 オーディオ ファイルが以前の実行で既に文字起こしされている場合、クライアントでは自動的にそのファイルがスキップされます。 一時的なエラーが発生した場合、ファイルは自動再試行によって送信されます。また、クライアントで再試行するエラーを区別できます。 文字起こしのエラーが発生した場合、クライアントでは文字起こしが続行され、進行状況を失うことなく再試行されます。  

## <a name="run-modes"></a>実行モード 

バッチ処理キットには、`--run-mode` パラメーターを使用して 3 つのモードが用意されています。

#### <a name="oneshot"></a>[Oneshot](#tab/oneshot)

`ONESHOT` モードを使用すると、(入力ディレクトリと省略可能なファイル一覧の) オーディオ ファイルの 1 つのバッチを文字起こしして出力フォルダーに出力できます。

:::image type="content" source="media/containers/batch-oneshot-mode.png" alt-text="oneshot モードでファイルを処理するバッチキット コンテナーを示す図。":::

1. バッチ クライアントから使用される音声コンテナー エンドポイントを `config.yaml` ファイルに定義します。 
2. 文字起こし用のオーディオ ファイルを入力ディレクトリに配置します。  
3. ファイルの処理を開始するディレクトリ上でコンテナーを呼び出します。 同じ出力ディレクトリ (同じファイル名とチェックサム) を使用して、以前の実行でオーディオ ファイルが既に文字起こしされている場合、クライアントではそのファイルがスキップされます。 
4. ファイルは、手順 1 のコンテナー エンドポイントにディスパッチされます。
5. ログと音声コンテナーの出力は、指定された出力ディレクトリに返されます。 

#### <a name="daemon"></a>[Daemon](#tab/daemon)

> [!TIP]
> 定期的に追加するのではなく、複数のファイルを同時に入力ディレクトリに追加すると、パフォーマンスを向上させることができます。

`DAEMON` モードを使用すると、指定されたフォルダー内の既存のファイルが文字起こしされ、新しいオーディオ ファイルが追加されると継続的に文字起こしされます。          

:::image type="content" source="media/containers/batch-daemon-mode.png" alt-text="daemon モードでファイルを処理するバッチキット コンテナーを示す図。":::

1. バッチ クライアントから使用される音声コンテナー エンドポイントを `config.yaml` ファイルに定義します。 
2. 入力ディレクトリ上でコンテナーを呼び出します。 バッチ クライアントによって、受信ファイルのディレクトリの監視が開始されます。 
3. 入力ディレクトリへの継続的なオーディオ ファイルの配信を設定します。 同じ出力ディレクトリ (同じファイル名とチェックサム) を使用して、以前の実行でオーディオ ファイルが既に文字起こしされている場合、クライアントではそのファイルがスキップされます。 
4. ファイルの書き込みまたは POSIX 信号が検出されると、コンテナーがトリガーされて応答します。
5. ファイルは、手順 1 のコンテナー エンドポイントにディスパッチされます。
6. ログと音声コンテナーの出力は、指定された出力ディレクトリに返されます。 

#### <a name="rest"></a>[REST](#tab/rest)

`REST` モードは、オーディオ ファイルのバッチ送信、状態チェック、およびロング ポーリング用の HTTP エンドポイントの基本セットを提供する API サーバー モードです。 また、Python モジュール拡張機能を使用したプログラムによる消費、またはサブモジュールとしてのインポートを行うこともできます。

:::image type="content" source="media/containers/batch-rest-api-mode.png" alt-text="REST モードでファイルを処理するバッチキット コンテナーを示す図。":::

1. バッチ クライアントから使用される音声コンテナー エンドポイントを `config.yaml` ファイルに定義します。 
2. API サーバーのエンドポイントのいずれかに HTTP 要求要求を送信します。 
        
    |エンドポイント  |説明  |
    |---------|---------|
    |`/submit`     | 新しいバッチ要求を作成するためのエンドポイント。        |
    |`/status`     | バッチ要求の状態を確認するためのエンドポイント。 バッチが完了するまで、接続は開いたままになります。       |
    |`/watch`     | バッチが完了するまで HTTP のロング ポーリングを使用するためのエンドポイント。        |

3. オーディオ ファイルは入力ディレクトリからアップロードされます。 同じ出力ディレクトリ (同じファイル名とチェックサム) を使用して、以前の実行でオーディオ ファイルが既に文字起こしされている場合、クライアントではそのファイルがスキップされます。 
4. 要求が `/submit` エンドポイントに送信されると、ファイルは手順 1 のコンテナー エンドポイントにディスパッチされます。
5. ログと音声コンテナーの出力は、指定された出力ディレクトリに返されます。 

---

## <a name="logging"></a>ログ記録

> [!NOTE]
> *run.log* ファイルは大きくなりすぎると、バッチ クライアントによって定期的に上書きされる場合があります。

クライアントでは、Docker の `run` コマンドの `-log_folder` 引数で指定されたディレクトリに *run.log* ファイルが作成されます。 ログは既定でデバッグ レベルでキャプチャされます。 同じログが `stdout/stderr` に送信され、`-file_log_level` 引数または `console_log_level` 引数に応じてフィルター処理されます。 このログは、デバッグ目的、またはサポートのためにトレースを送信する必要がある場合にのみ必要です。 ログ フォルダーには、各オーディオ ファイルの Speech SDK ログも含まれています。

`-output_folder` で指定された出力ディレクトリには、*run_summary.json*  ファイルが格納されています。このファイルは、30 秒ごとに定期的に、または新しい文字起こしが完了するたびに書き換えられます。 このファイルを使用すると、バッチの進行状況を確認できます。 また、バッチが完了したときのすべてのファイルの最終的な実行の統計情報と最終的な状態も含まれます。 プロセスに clean exit があると、バッチは完了します。 

## <a name="next-steps"></a>次のステップ

* [コンテナーのインストールおよび実行方法](speech-container-howto.md)
