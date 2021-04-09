---
title: ONNX を使用して Azure SQL Edge に ML モデルをデプロイする
description: 鉄鉱石の不純物を予測する、この 3 部構成の Azure SQL Edge チュートリアルの第 3 部では、SQL Edge で ONNX 機械学習モデルを実行します。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 9e5bb037b88b7c370e31d05c2d20fc6f558a8b39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422197"
---
# <a name="deploy-ml-model-on-azure-sql-edge-using-onnx"></a>ONNX を使用して Azure SQL Edge に ML モデルをデプロイする 

Azure SQL Edge で鉄鉱石の不純物を予測する、この 3 部構成チュートリアルの第 2 部では、次を行います。

1. Azure Data Studio を使用して、Azure SQL Edge インスタンスで SQL Database に接続します。
2. Azure SQL Edge の ONNX を使用して、鉄鉱石の不純物を予測します。

## <a name="key-components"></a>主なコンポーネント

1. このソリューションでは、Edge ハブに送信される各メッセージの間隔を既定の 500 ミリ秒にします。 この設定は、**Program.cs** ファイルで変更できます 
   ```json
   TimeSpan messageDelay = configuration.GetValue("MessageDelay", TimeSpan.FromMilliseconds(500));
   ```
2. ソリューションでは、以下の属性のメッセージが生成されました。 要件に応じて、属性を追加または削除します。 
```json
{
    timestamp 
    cur_Iron_Feed
    cur_Silica_Feed 
    cur_Starch_Flow 
    cur_Amina_Flow 
    cur_Ore_Pulp_pH
    cur_Flotation_Column_01_Air_Flow
    cur_Flotation_Column_02_Air_Flow
    cur_Flotation_Column_03_Air_Flow
    cur_Flotation_Column_04_Air_Flow
    cur_Flotation_Column_01_Level
    cur_Flotation_Column_02_Level
    cur_Flotation_Column_03_Level
    cur_Flotation_Column_04_Level
    cur_Iron_Concentrate
}
```

## <a name="connect-to-the-sql-database-in-the-azure-sql-edge-instance-to-train-deploy-and-test-the-ml-model"></a>Azure SQL Edge インスタンスの SQL Database に接続し、ML モデルをトレーニング、デプロイ、およびテストします

1. Azure Data Studio を開きます。

2. **[ようこそ]** タブで、次の詳細情報を使用して新しい接続を開始します。

   |_フィールド_|_Value_|
   |-------|-------|
   |接続の種類| Microsoft SQL Server|
   |サーバー|このデモのために作成された VM で示されているパブリック IP アドレス|
   |ユーザー名|sa|
   |Password|Azure SQL Edge インスタンスの作成時に使用された強力なパスワード|
   |データベース|Default|
   |[サーバー グループ]|Default|
   |Name (名前) (省略可能)|必要に応じて名前を付けます。|

3. **[接続]**

4. **[ファイル]** セクションで、マシンにプロジェクト ファイルを複製したフォルダーの **/DeploymentScripts/MiningProcess_ONNX.jpynb** を開きます。

5. カーネルを Python 3 に設定します。


## <a name="next-steps"></a>次のステップ

Azure SQL Edge での ONNX モデルの使用の詳細については、「[SQL Edge での ONNX を使用した機械学習と AI](onnx-overview.md)」を参照してください。