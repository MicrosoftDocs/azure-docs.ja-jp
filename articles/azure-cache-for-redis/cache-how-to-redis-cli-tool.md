---
title: Azure Cache for Redis での redis-cli の使用
description: クライアントとして Azure Cache for Redis とやり取りするためのコマンドライン ツールとして *redis-cli.exe* を使用する方法について説明します。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: e4f5fc7290b45f65067f6711f70476e13a010223
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183388"
---
# <a name="use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Azure Cache for Redis での Redis コマンドライン ツールの使用

*redis-cli.exe* は、クライアントとして Azure Cache for Redis と対話するための一般的なツールです。 このツールは、Azure Cache for Redis でも利用することができます。

Windows プラットフォームでこのツールを利用するには､[Redis command-line tools for Windows](https://github.com/MSOpenTech/redis/releases/) をダウンロードします｡ 

別のプラットフォームからこのコマンドライン ツールを実行するには、[https://redis.io/download](https://redis.io/download) から Azure Cache for Redis をダウンロードします。

## <a name="gather-cache-access-information"></a>キャッシュ アクセス情報を収集する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

キャッシュにアクセスするために必要な情報を収集する方法は 3 通りあります｡

1. Azure CLI から [az redis list-keys](/cli/azure/redis#az-redis-list-keys) を使用する
2. Azure PowerShell から [Get-AzRedisCacheKey](/powershell/module/az.rediscache/Get-AzRedisCacheKey) を使用する
3. Azure Portal を使用する

このセクションでは､Azure Portal からキーを取得します｡

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>redis-cli.exe に対するアクセスを有効化する

Azure Cache for Redis は、既定で TLS ポート (6380) のみが有効化されています。 `redis-cli.exe` コマンドライン ツールは TLS をサポートしていません｡ SSL を使用するための構成上の選択肢は 2 通りあります｡

1. [非 TLS ポート (6379) を有効にします](cache-configure.md#access-ports) -  **アクセス キーがクリア テキスト形式で TCP を使って送信されるため､この構成はお勧めしません**｡ この変更を行うと､キャッシュに対するアクセスが危うくなる可能性があります｡ この構成を検討する唯一のシナリオは､テスト用 キャッシュにアクセスするだけの場合です｡

2. [stunnel](https://www.stunnel.org/downloads.html) ダウンロードして､インストールします｡

    **stunnel GUI Start** を実行してサーバーを起動する

    タスクバーにある stunnel サーバーのアイコンを右クリックして､**Show Log Window** をクリックします｡

    stunnel ログ ウィンドウのメニューで **Configuration** > **Edit Configuration** をクリックして現在の構成ファイルを開きます｡

    **Service definitions** の部分に以下の *redis-cli.exe* 用のエントリを追加します｡ `yourcachename` の部分は実際のキャッシュ名に置き換えてください｡ 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    構成ファイルを保存して閉じます｡ 
  
    stunnel ログ ウィンドウのメニューで **Configuration** > **Reload Configuration** をクリックします｡


## <a name="connect-using-the-redis-command-line-tool"></a>Redis コマンドライン ツールで接続する

stunnel を使用してキャッシュに接続するには､*redis-cli.exe* を起動して､*ポート* と *アクセス キー* (プライマリかセカンダリ) のに指定します｡

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![キャッシュへの接続が成功したことを示すスクリーンショット。](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

**安全度の低い** 非 TLS ポート経由でテスト用キャッシュを使用する場合は､`redis-cli.exe` を実行して､*ホスト名* と *ポート*､*アクセス キー* (プライマリかセカンダリ) を指定することでテスト用キャッシュに接続できます｡

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel と redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>次のステップ

[Redis Console](cache-configure.md#redis-console) を使用してコマンドを発行する