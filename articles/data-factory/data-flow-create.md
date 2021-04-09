---
title: マッピング データ フローを作成する
description: Azure Data Factory のマッピング データ フローを作成する方法
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: eaf36cc2690b3c0f8922c05432b3197b4ff30d9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93026056"
---
# <a name="create-azure-data-factory-data-flow"></a>Azure Data Factory のデータ フローを作成する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

ADF の Mapping Data Flow は、コーディングなしでデータを大規模に変換する方法を提供します。 データ フロー デザイナーで一連の変換を構築することで、データ変換ジョブを設計できます。 任意の数のソースの変換から始め、その後にデータ変換手順を実行します。 次に、データ フローをシンクで完了して、結果を宛先に書き込みます。

Azure portal で新しい V2 データ ファクトリを作成することから始めます。 新しいファクトリを作成したら、[作成者と監視] タイルをクリックして、Data Factory UI を起動します。

![スクリーンショットに、[新しいデータ ファクトリ] ペインが示され、[バージョン] に [V2] が選択されています。](media/data-flow/v2portal.png "データ フロー作成")

Data Factory UI が表示されたら、サンプル データ フローを使用できます。 サンプルは、ADF テンプレート ギャラリーから入手できます。 ADF で、"テンプレートからパイプライン" を作成し、テンプレート ギャラリーから [データ フロー] カテゴリを選択します。

![スクリーンショットに、選択されたデータ フローを使用したデータ変換がある [Data Flow] タブが示されています。](media/data-flow/template.png "データ フロー作成")

Azure Blob ストレージ アカウント情報の入力を求められます。

![スクリーンショットに、データ フロー ペインを使用したデータの変換が示されています。ここで、ユーザー入力を入力できます。](media/data-flow/template2.png "データ フロー作成 2")

[これらのサンプルで使用されるデータはこちらで見つけることができます](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata)。 サンプル データをダウンロードし、Azure Blob ストレージ アカウントにファイルを格納して、サンプルを実行できるようにします。

## <a name="create-new-data-flow"></a>新しいデータ フローを作成する

ADF UI で、[+] ボタン (リソースの作成) を使用して、データ フローを作成します。

![スクリーンショットに、[ファクトリ リソース] メニューから選択された Data Flow が示されています。](media/data-flow/newresource.png "新しいリソース")

## <a name="next-steps"></a>次のステップ

[ソース変換](data-flow-source.md)を使用してデータ変換の構築を開始します。
