---
title: Azure Service Fabric CLI- sfctl store | Microsoft Docs
description: "Service Fabric CLI sfctl store のコマンドについて説明します。"
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: 2af6dff4ffcdf295731f2d61b5f9e35af40615e5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-store"></a>sfctl store
クラスター イメージ ストアで基本的なファイル レベルの操作を実行します。

## <a name="commands"></a>コマンド

|コマンド|Description|
| --- | --- |
|    削除| 既存のイメージ ストアのコンテンツを削除します。|
|    root-info| イメージ ストアのルートにあるコンテンツ情報を取得します。|
|    stat  | イメージ ストアのコンテンツ情報を取得します。|


## <a name="sfctl-store-delete"></a>sfctl store delete
既存のイメージ ストアのコンテンツを削除します。

指定したイメージ ストアの相対パス内で見つかった既存のイメージ ストアのコンテンツを削除します。 このコマンドを使用すると、アップロードされたアプリケーション パッケージを、それらがプロビジョニングされた後に削除することができます。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --content-path [必須]| イメージ ストア内のファイルまたはフォルダーへのルートからの相対パス。|
| --timeout -t          | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug               | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h             | このヘルプ メッセージを表示して終了します。|
| --output -o           | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query               | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose             | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-store-stat"></a>sfctl store stat
イメージ ストアのコンテンツ情報を取得します。

イメージ ストアのルートに対する指定した contentPath にあるイメージ ストアのコンテンツに関する情報を返します。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --content-path [必須]| イメージ ストア内のファイルまたはフォルダーへのルートからの相対パス。|
| --timeout -t          | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug               | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h             | このヘルプ メッセージを表示して終了します。|
| --output -o           | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query               | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose             | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="next-steps"></a>次のステップ
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。