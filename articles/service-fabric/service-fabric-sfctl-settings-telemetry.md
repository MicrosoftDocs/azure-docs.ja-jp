---
title: Azure Service Fabric CLI - sfctl settings telemetry
description: Azure Service Fabric のコマンド ライン インターフェイスである sfctl について説明します。 sfctl テレメトリを構成するためのコマンドの一覧が含まれています。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: ef720a14617b4131474d50875701d0ef27df4151
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86245511"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
sfctl のこのインスタンスに対してローカルなテレメトリ設定を構成します。

Sfctl テレメトリでは、パラメーターやパラメーター値が指定されていないコマンドの名前、sfctl バージョン、OS の種類、python のバージョン、コマンドの成否、返されたエラー メッセージが収集されます。

## <a name="commands"></a>コマンド

|コマンド|説明|
| --- | --- |
| set-telemetry | テレメトリを有効または無効にします。 |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl settings telemetry set-telemetry
テレメトリを有効または無効にします。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --off | テレメトリを無効にします。 |
| --on | テレメトリを有効にします。 これが既定値です。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

### <a name="examples"></a>例

テレメトリを無効にします。

```
sfctl settings telemetry set_telemetry --off
```

テレメトリを有効にします。

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>次のステップ
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](./scripts/sfctl-upgrade-application.md)を使用して、Service Fabric CLI の使用方法を学習します。
