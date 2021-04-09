---
title: Azure Data Factory のデータ ファクトリをコピーまたは複製する
description: Azure Data Factory のデータ ファクトリをコピーまたは複製する方法について説明します
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: e13aa0a66d1c1a65462e80f14efc048dd2f06c8c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780278"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Azure Data Factory のデータ ファクトリをコピーまたは複製する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事では、Azure Data Factory のデータ ファクトリをコピーまたは複製する方法について説明します。

## <a name="use-cases-for-cloning-a-data-factory"></a>データ ファクトリの複製に関するユース ケース

以下、データ ファクトリのコピーまたは複製が有効な手段となる可能性のある状況をいくつか挙げます。

- 新しいリージョンに **データ ファクトリを移動する**。 データ ファクトリを別のリージョンに移動する場合は、ターゲット リージョンにコピーを作成し、既存のものを削除するのが最善の方法です。

- **データ ファクトリの名前を変更する**。 Azure では、リソースの名前変更がサポートされません。 データ ファクトリの名前を変更したい場合は、別の名前でデータ ファクトリを複製した後、既存のものを削除してください。

- **変更内容をデバッグする** (デバッグ機能が十分でない場合)。 ほとんどの場合、[デバッグ機能](iterative-development-debugging.md)を使用することができます。 また、複製されたサンドボックス環境で変更をテストする方が適切である場合もあります。 たとえば、ファイルの到着時にトリガーが起動した場合とタンブリング時間枠でトリガーが起動した場合のパラメーター化された ETL パイプラインの動作は、デバッグだけでは簡単にテストできないことがあります。 このような場合は、実験のためにサンドボックス環境を複製することができます。 Azure Data Factory の課金は、主に実行回数単位で行われるので、もう 1 つのファクトリによって追加料金が発生することはありません。

## <a name="how-to-clone-a-data-factory"></a>データ ファクトリを複製する方法

1. 前提条件として、まず Azure portal からターゲット データ ファクトリを作成する必要があります。

1. Git モードの場合:
    1. ポータルから発行するたびに、ファクトリの Resource Manager テンプレートが、Git の adf\_publish ブランチに保存されます。
    1. 新しいファクトリを "_同じ_" リポジトリに接続し、adf\_publish ブランチからビルドします。 パイプライン、データセット、トリガーなどのリソースが対象になります

1. ライブ モードの場合:
    1. Data Factory UI では、データ ファクトリのペイロード全体を Resource Manager テンプレート ファイルとパラメーター ファイルにエクスポートできます。 それらには、ポータルの **[ARM テンプレート] \ [Export Resource Manager template]\(Resource Manager テンプレートのエクスポート\)** ボタンからアクセスできます。
    1. パラメーター ファイルに適切な変更を加え、新しいファクトリの新しい値をスワップすることができます。
    1. 次に、Resource Manager テンプレートの標準的なデプロイ方法でそれをデプロイすることができます。

1. ご自分のソース ファクトリ内にセルフホステッド統合ランタイムがある場合は、ターゲット ファクトリ内に同じ名前を使用してそれを事前に作成する必要があります。 異なるファクトリ間でセルフホステッド統合ランタイムを共有する場合は、セルフホステッド IR の共有について[こちら](create-shared-self-hosted-integration-runtime-powershell.md)で公開されているパターンを使用できます。

1. セキュリティ上の理由から、生成された Resource Manager テンプレートには、シークレット情報 (リンクされたサービスのパスワードなど) は一切含まれません。 そのため、資格情報をデプロイのパラメーターとして指定する必要があります。 資格情報を手動で入力することが設定で望ましくない場合は、代わりに Azure Key Vault から接続文字列とパスワードを取得することを検討してください。 [詳細情報](store-credentials-in-key-vault.md)

## <a name="next-steps"></a>次のステップ

「[Azure Data Factory UI を使用してデータ ファクトリを作成する](quickstart-create-data-factory-portal.md)」で、Azure portal におけるデータ ファクトリ作成のガイダンスを確認してください。
