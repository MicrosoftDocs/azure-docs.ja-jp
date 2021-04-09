---
title: モデル変換
description: モデルをレンダリング用に変換するプロセスについて説明します。
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: e899b249261ea3238695a2e2be6001cb6a9bc763
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91318060"
---
# <a name="convert-models"></a>モデルの変換

Azure Remote Rendering を使用すると、非常に複雑なモデルをレンダリングできます。 最高のパフォーマンスを実現するには、データを事前に処理して、最適な形式にする必要があります。 データの量によっては、この手順に時間がかかることがあります。 モデルの読み込み時にこの時間が費やされた場合、これは現実的ではありません。 また、複数のセッションでこのプロセスを繰り返すのは無駄です。 これらの理由により、ARR サービスには事前に実行できる専用の "*変換サービス*" が用意されています。
変換されたモデルは、Azure Storage アカウントから読み込むことができます。

## <a name="supported-source-formats"></a>サポートされているソース形式

変換サービスでは、次の形式がサポートされています。

- **FBX**  (バージョン 2011 からバージョン 2020)
- **GLTF**/**GLB** (バージョン 2.x)

素材のプロパティの変換については、「[モデル形式の素材マッピング](../../reference/material-mapping.md)」に掲載されているように、形式ごとにわずかな違いがあります。

## <a name="the-conversion-process"></a>変換プロセス

1. [2 つの Azure Blob Storage コンテナーを用意します](blob-storage.md)。1 つは入力用、もう 1 つは出力用です。
1. モデルを入力コンテナー (必要に応じてサブパス以下) にアップロードします
1. [モデル変換 REST API](conversion-rest-api.md) を介して変換処理をトリガーします
1. 変換の進行状況についてサービスをポーリングします
1. 完了したら、次のようにモデルを読み込みます
    - リンクされたストレージ アカウントから (ストレージ アカウントをリンクするには、[アカウントの作成](../create-an-account.md#link-storage-accounts)に関するページの「ストレージ アカウントのリンク」の手順を参照してください)
    - または、*Shared Access Signature (SAS)* を提供します。

すべてのモデル データ (入力と出力) は、ユーザーが指定した Azure Blob ストレージに格納されます。 Azure Remote Rendering を使用すると、資産管理を完全に制御できます。

## <a name="pricing"></a>価格

変換の料金の詳細については、「[Remote Rendering の価格](https://azure.microsoft.com/pricing/details/remote-rendering)」ページを参照してください。


## <a name="conversion-parameters"></a>変換パラメーター

さまざまな変換オプションについては、[この章](configure-model-conversion.md)を参照してください。

## <a name="examples"></a>例

- [クイック スタート: モデルをレンダリング用に変換する](../../quickstarts/convert-model.md)」では、モデルを変換する基本的な手順が説明されています。
- 変換サービスの使用例を示す「[PowerShell スクリプトの例](../../samples/powershell-example-scripts.md)」は、[ARR サンプル リポジトリ](https://github.com/Azure/azure-remote-rendering)の *Scripts* フォルダーにあります。

## <a name="next-steps"></a>次のステップ

- [モデル変換に Azure Blob Storage を使用する](blob-storage.md)
- [モデル変換 REST API](conversion-rest-api.md)
- [モデル変換を構成する](configure-model-conversion.md)
- [変換用のファイルのレイアウト](layout-files-for-conversion.md)
- [モデル形式の素材マッピング](../../reference/material-mapping.md)
