---
author: baanders
description: Azure Digital Twins のインクルード ファイル - 最新の IoT 拡張機能を設定する
ms.service: digital-twins
ms.topic: include
ms.date: 7/31/2020
ms.author: baanders
ms.openlocfilehash: 984739a728f6ac5e28eeb561e0d7b6ec0485ca13
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496867"
---
まず、次のコマンドを実行して、既にインストールされているすべての拡張機能を一覧表示します。

```azurecli-interactive
az extension list
```

出力は、現在所有するすべての拡張機能の配列です。 一覧のエントリごとに `"name"` フィールドを探し、拡張機能の名前を確認します。

出力結果を見て、拡張機能のセットアップで実行すべきコマンドを次の中から特定します (実行すべきコマンドが複数ある場合もあります)。
* 一覧に `azure-iot` が含まれている場合: 拡張機能は既に存在しています。 このコマンドを実行して、最新の更新プログラムが適用されていること、および利用可能な更新プログラムがないことを確認します。

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* 一覧に `azure-iot` が含まれ**ない**場合: 拡張機能をインストールする必要があります。 次のコマンドを実行します。

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* 一覧に `azure-iot-cli-ext` が含まれている場合: これは従来のバージョンの拡張機能です。 同時にインストールする拡張機能のバージョンは 1 つのみとしてください。従来の拡張機能はアンインストールする必要があります。 次のコマンドを実行します。

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```