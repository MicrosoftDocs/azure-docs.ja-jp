---
title: Azure Cache for Redis への移行
description: 既存のキャッシュを Azure Cache for Redis に移行する方法について説明します
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: yegu
ms.openlocfilehash: d63cafb32dc1db0a901ed3e6004446b450db10c7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102451525"
---
# <a name="migrate-to-azure-cache-for-redis"></a>Azure Cache for Redis への移行
この記事では、オンプレミスまたは別のクラウド サービスで実行されている既存の Redis キャッシュを Azure Cache for Redis に移行するためのさまざまな方法について説明します。

## <a name="migration-scenarios"></a>移行シナリオ
オープンソースの Redis は、多くのコンピューティング環境で実行できます。 一般的な例は、次のとおりです。

- **オンプレミス** - プライベート データセンターで実行されている Redis キャッシュです。
- **クラウドベースの VM** - Azure VM や AWS EC2 などで実行されている Redis キャッシュです。
- **ホスティング サービス** - AWS ElastiCache などの管理対象 Redis サービスです。
- **別のリージョン** - 別の Azure リージョンにある Redis キャッシュです。

このようなキャッシュがあれば、中断やダウンタイムを最小限に抑えながら、Azure Cache for Redis に移行できます。

## <a name="migration-options"></a>移行オプション

別のキャッシュに切り替えるには、さまざまな方法があります。 キャッシュの場所やアプリケーションからの操作方法に応じて、ほかの方法よりも役立ちます。 頻繁に使用されるいくつかの移行戦略について、以下で詳しく説明します。

   | オプション       | 長所 | 短所 |
   | ------------ | ---------- | ------------- |
   | 新しいキャッシュを作成する | 実装が非常に簡単です。 | 新しいキャッシュにデータを再入力する必要があります。多くのアプリケーションで動作しない可能性があります。 |
   | RDB ファイルを使用してデータをエクスポートおよびインポートする | 一般に、Redis キャッシュとの互換性があります。 | RDB ファイルの生成後に既存のキャッシュに書き込まれると、一部のデータが失われる可能性があります。 | 
   | 2 つのキャッシュにデータを二重に書き込む | データの損失やダウンタイムはありません 既存のキャッシュが中断されない操作です。 新しいキャッシュのテストが簡単になります。 | 長期間にわたって 2 つのキャッシュが必要です。 | 
   | プログラムでデータを移行する | データの移動方法を完全に制御できます。 | カスタム コードが必要です。 | 

### <a name="create-a-new-azure-cache-for-redis"></a>新しい Azure Cache for Redis を作成する

このアプローチは、技術的には移行ではありません。 データが失われる心配がない場合、Azure Cache for Redis に移動する最も簡単な方法は、キャッシュ インスタンスを作成し、アプリケーションを接続することです。 たとえば、Redis をデータベース レコードのルックアサイド キャッシュとして使用すると、キャッシュをゼロから簡単に再構築できます。

このオプションを実装する一般的な手順は、次のとおりです。

1. 新しい Azure Cache for Redis インスタンスを作成します。

2. 新しいインスタンスが使用されるようにアプリケーションを更新します。

3. 古い Redis インスタンスを削除します。

### <a name="export-data-to-an-rdb-file-and-import-it-into-azure-cache-for-redis"></a>データを RDB ファイルにエクスポートし、Azure Cache for Redis にインポートする

オープンソースの Redis は、キャッシュのメモリ内データセットのスナップショットを取得してファイルに保存するための標準的なメカニズムが定義されます。 この (RDB と呼ばれる) ファイルは、別の Redis キャッシュで読み取ることもできます。 [Azure Cache for Redis Premium レベル](cache-overview.md#service-tiers)では、RDB ファイルを使用したキャッシュ インスタンスへのデータのインポートがサポートされています。 RDB ファイルを使用すると、既存のキャッシュから Azure Cache for Redis にデータを転送できます。

> [!IMPORTANT]
> RDB ファイルの形式は、Redis のバージョン間で変更されることがあるため、下位互換性が確保されない場合があります。 エクスポート元のキャッシュの Redis バージョンは、Azure Cache for Redis で提供されるバージョン以下である必要があります。
>

このオプションを実装する一般的な手順は、次のとおりです。

1. 既存のキャッシュと同じ (または大きい) サイズの新しい Azure Cache for Redis インスタンスを Premium レベルで作成します。

2. 既存の Redis キャッシュのスナップショットを保存します。 [スナップショット](https://redis.io/topics/persistence)が定期的に保存されるように Redis を構成することも、[SAVE](https://redis.io/commands/save) または [BGSAVE](https://redis.io/commands/bgsave) コマンドを使用して手動でプロセスを実行することもできます。 既定の RDB ファイル名は "dump.rdb" であり、このファイルは *redis.conf* 構成ファイルで指定されたパスに配置されています。

    > [!NOTE]
    > Azure Cache for Redis 内でデータを移行する場合は、[RDB ファイルをエクスポートするための手順](cache-how-to-import-export-data.md)を参照するか、代わりに [PowerShell エクスポート コマンドレット](/powershell/module/azurerm.rediscache/export-azurermrediscache)を使用してください。
    >

3. RDB ファイルを新しいキャッシュが配置されているリージョンの Azure ストレージ アカウントにコピーします。 このタスクでは、AzCopy を使用できます。

4. これらの[インポート手順](cache-how-to-import-export-data.md)または [PowerShell インポート コマンドレット](/powershell/module/azurerm.rediscache/import-azurermrediscache)を使用して、RDB ファイルを新しいキャッシュにインポートします。

5. 新しいキャッシュ インスタンスが使用されるようにアプリケーションを更新します。

### <a name="write-to-two-redis-caches-simultaneously-during-migration-period"></a>移行期間内に 2 つの Redis キャッシュに同時に書き込む

キャッシュ間で直接データを移動するのではなく、アプリケーションを使用して、既存のキャッシュと設定中の新しいキャッシュの両方にデータを書き込むことができます。 最初に既存のキャッシュからデータが読み取られます。 新しいキャッシュに必要なデータがある場合は、そのキャッシュにアプリケーションを切り替え、古いキャッシュを削除します。 たとえば、Redis をセッション ストアとして使用し、アプリケーション セッションが 7 日間有効であるとします。 1 週間で 2 つのキャッシュに書き込むと、新しいキャッシュに期限切れではないセッション情報がすべて含まれていることがわかります。 その時点以降は、データが失われることを心配せずに安全に利用できます。

このオプションを実装する一般的な手順は、次のとおりです。

1. 既存のキャッシュと同じ (または大きい) サイズの新しい Azure Cache for Redis インスタンスを Premium レベルで作成します。

2. 新しいインスタンスと元のインスタンスの両方に書き込まれるようにアプリケーション コードを変更します。

3. 新しいインスタンスに十分なデータが入力されるまで、元のインスタンスからのデータの読み取りを続行します。

4. 新しいインスタンスからの読み取りと書き込みのみが行われるようにアプリケーション コードを更新します。

5. 元のインスタンスを削除します。

### <a name="migrate-programmatically"></a>プログラムで移行する

プログラムで既存のキャッシュからデータを読み込み、Azure Cache for Redis に書き込むことで、カスタム移行プロセスを作成できます。 この[オープンソース ツール](https://github.com/deepakverma/redis-copy)を使用すると、別の Azure Cache for Redis インスタンスにデータをコピーできます。 このツールは、さまざまな Azure Cache for Redis リージョンのキャッシュ インスタンス間でデータを移動する場合に役立ちます。 [コンパイル済みのバージョン](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip)も入手できます。 また、独自の移行ツールを作成する際に役立つガイドとなるソース コードが見つかることもあります。

> [!NOTE]
> このツールは、正式には Microsoft でサポートされていません。 
>

このオプションを実装する一般的な手順は、次のとおりです。

1. 既存のキャッシュが配置されているリージョンで VM を作成します。 データセットが大きい場合は、コピー時間が削減されるように、比較的強力な VM を選択してください。

2. 新しい Azure Cache for Redis インスタンスを作成します。

3. 新しいキャッシュからデータをフラッシュして、空になったことを確認します。 このステップは、コピー ツール自体ではターゲット キャッシュの既存のキーが上書きされないため必須です。

    > [!IMPORTANT]
    > 絶対にソース キャッシュからフラッシュしないでください。
    >

4. 上記のオープンソース ツールなどのアプリケーションを使用して、ソース キャッシュからターゲットへのデータのコピーを自動化します。 データセットのサイズによっては、コピー処理が完了するまでに時間がかかる場合があることに注意してください。

## <a name="next-steps"></a>次の手順
Azure Cache for Redis の機能について

* [Azure Cache for Redis サービス レベル](cache-overview.md#service-tiers)
* [データのインポート](cache-how-to-import-export-data.md#import)