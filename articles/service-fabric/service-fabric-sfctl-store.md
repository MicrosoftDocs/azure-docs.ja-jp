---
title: Azure Service Fabric CLI- sfctl store
description: Azure Service Fabric のコマンド ライン インターフェイスである sfctl について説明します。 クラスター イメージ ストアでファイル レベル操作を実行するためのコマンドの一覧が含まれています。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: af5399be44d2946775622598c5c1db3d8355f7cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257072"
---
# <a name="sfctl-store"></a>sfctl store
クラスター イメージ ストアで基本的なファイル レベルの操作を実行します。

## <a name="commands"></a>コマンド

|コマンド|説明|
| --- | --- |
| delete | 既存のイメージ ストアのコンテンツを削除します。 |
| root-info | イメージ ストアのルートにあるコンテンツ情報を取得します。 |
| stat | イメージ ストアのコンテンツ情報を取得します。 |

## <a name="sfctl-store-delete"></a>sfctl store delete
既存のイメージ ストアのコンテンツを削除します。

指定したイメージ ストアの相対パス内で見つかった既存のイメージ ストアのコンテンツを削除します。 このコマンドを使用すると、アップロードされたアプリケーション パッケージを、それらがプロビジョニングされた後に削除することができます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --content-path [必須] | イメージ ストア内のファイルまたはフォルダーへのルートからの相対パス。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-store-root-info"></a>sfctl store root-info
イメージ ストアのルートにあるコンテンツ情報を取得します。

イメージ ストアのルートにあるイメージ ストアのコンテンツに関する情報を返します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-store-stat"></a>sfctl store stat
イメージ ストアのコンテンツ情報を取得します。

指定した contentPath にあるイメージ ストアのコンテンツに関する情報を返します。 contentPath の基準はイメージ ストアのルートです。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --content-path [必須] | イメージ ストア内のファイルまたはフォルダーへのルートからの相対パス。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |


## <a name="next-steps"></a>次のステップ
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](./scripts/sfctl-upgrade-application.md)を使用して、Service Fabric CLI の使用方法を学習します。
