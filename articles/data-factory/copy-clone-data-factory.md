---
title: Azure Data Factory のデータ ファクトリをコピーまたは複製する | Microsoft Docs
description: Azure Data Factory のデータ ファクトリをコピーまたは複製する方法について説明します
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 96ea8142e2f7794d3c15c6efb436eafa585bc8fd
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57575615"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Azure Data Factory のデータ ファクトリをコピーまたは複製する

この記事では、Azure Data Factory のデータ ファクトリをコピーまたは複製する方法について説明します。

## <a name="use-cases-for-cloning-a-data-factory"></a>データ ファクトリの複製に関するユース ケース

以下、データ ファクトリのコピーまたは複製が有効な手段となる可能性のある状況をいくつか挙げます。

-   **リソースの名前を変更する**。 Azure では、リソースの名前変更がサポートされません。 データ ファクトリの名前を変更したい場合は、別の名前でデータ ファクトリを複製した後、既存のデータ ファクトリを削除してください。

-   **変更内容をデバッグする** (デバッグ機能が十分でない場合)。 行った変更をテストするために、別のファクトリでその変更をテストしてから、メインのファクトリに適用したい場合があります。 ほとんどの場合、デバッグ機能を使用することができます。 ただし、トリガーに対する変更 (トリガーが自動的に、または時間の経過に伴って呼び出されたときの振る舞いなど) は、チェックインしなければ、簡単にはテストできません。 このようなケースでは、ファクトリを複製してそこに変更を適用することが賢明な手段となります。 Azure Data Factory の課金は、主に実行回数単位で行われるので、ファクトリが 1 つ増えても料金が増えることはありません。

## <a name="how-to-clone-a-data-factory"></a>データ ファクトリを複製する方法

1. Azure portal の Data Factory UI では、データ ファクトリのペイロード全体を Resource Manager テンプレートにエクスポートすることができます。その際、パラメーター ファイルも一緒にエクスポートできます。ファクトリを複製するときにこのファイルを使用することで、任意の値を変更することができます。

1. 前提条件として、Azure portal からターゲット データ ファクトリを作成する必要があります。

1. ご自分のソース ファクトリ内にセルフホステッド統合ランタイムがある場合は、ターゲット ファクトリ内に同じ名前を使用してそれを事前に作成する必要があります。 異なるファクトリでセルフホステッド IR を共有する場合は、[こちら](author-visually.md#best-practices-for-git-integration)で公開されているパターンを使用できます。

1. GIT モードを使用している場合は、ポータルから発行するたびに、ファクトリの Resource Manager テンプレートが、GIT のリポジトリの adf_publish ブランチに保存されます。

1. それ以外の場合は、ポータルの **[Export Resource Manager template]\(Resource Manager テンプレートのエクスポート\)** ボタンをクリックすることにより、Resource Manager テンプレートをダウンロードできます。

1. Resource Manager テンプレートをダウンロードしたら、Resource Manager テンプレートの標準的なデプロイ方法でそれをデプロイすることができます。

1. セキュリティ上の理由から、生成された Resource Manager テンプレートには、シークレット情報 (リンクされたサービスのパスワードなど) は一切含まれません。 そのため、それらのパスワードは、デプロイのパラメーターとして指定する必要があります。 パラメーターを指定することが望ましくない場合は、リンクされたサービスの接続文字列とパスワードを Azure Key Vault から取得する必要があります。

## <a name="next-steps"></a>次の手順

「[Azure Data Factory UI を使用してデータ ファクトリを作成する](quickstart-create-data-factory-portal.md)」で、Azure portal におけるデータ ファクトリ作成のガイダンスを確認してください。
