---
title: Azure App Configuration でのデータのインポートまたはエクスポート | Microsoft Docs
description: Azure App Configuration でのデータのインポートまたはエクスポート方法について説明します
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 846472e00bc048de906ee8e14f6de38e366f3571
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225231"
---
# <a name="import-or-export-configuration-data"></a>構成データのインポートまたはエクスポート

Azure App Configuration はデータのインポート操作とエクスポート操作をサポートします。 これらの操作を使用して、構成データを一括処理し、アプリ構成ストアとコード プロジェクト間でデータを交換することができます。 たとえば、1 つのアプリケーション構成ストアをテスト用に、もう 1 つのアプリ構成ストアを実稼働用に設定することができます。 こうすると、ファイルを介してアプリケーション設定をコピーできるので、データを 2 回入力する必要がなくなります。

この記事では、App Configuration でデータをインポートおよびエクスポートするためのガイドを提供します。

## <a name="import-data"></a>データのインポート

インポートでは、手動で入力するのではなく、既存のソースから App Configuration ストアに構成データが取り込まれます。 インポート機能は、データをアプリ構成ストアに移行したり、複数ソースのデータを集計するために使用します。 App Configuration は、JSON、YAML、またはプロパティ ファイルからのインポートをサポートしています。

データのインポートには、[Azure portal](https://aka.ms/azconfig/portal) または [Azure CLI](./scripts/cli-import.md) のいずれかを使用します。 Azure portal から次の手順を実行します。

1. アプリ構成ストアを参照し、**[インポート/エクスポート]** を選択します。

2. **[インポート]** タブで、**[Source service]\(ソース サービス\)** > **[構成ファイル]** を選択します。

3. **[For language]\(言語\)** > **[ファイルの種類]** を選択します。

4. **フォルダー** アイコンを選択し、インポートするファイルを参照します。

    ![ファイルのインポート](./media/import-file.png)

5. **[区切り記号]** を選択し、必要に応じて、インポートされるキー名に使用する **[プレフィックス]** を入力します。

6. 必要に応じて、**[ラベル]** を選択します。

7. **[適用]** を選択してインポートを完了します。

    ![完了したファイルのインポート](./media/import-file-complete.png)

## <a name="export-data"></a>データのエクスポート

エクスポートでは、App Configuration に保存されている構成データが別の保存先に書き込まれます。 たとえば、デプロイ時に、アプリケーション構成ストア内のデータをアプリケーション コードに埋め込まれるファイルに保存するときにエクスポート機能を使用します。

データのエクスポートには、[Azure portal](https://aka.ms/azconfig/portal) または [Azure CLI](./scripts/cli-export.md) のいずれかを使用します。 Azure portal から次の手順を実行します。

1. アプリ構成ストアを参照し、**[インポート/エクスポート]** を選択します。

2. **[エクスポート]** タブで、**[Target service]\(ターゲット サービス\)** > **[構成ファイル]** を選択します。

3. 必要に応じて **[プレフィックス]** を入力し、エクスポートするキーの **[ラベル]** と特定の時点を選択します。

4. **[ファイルの種類]** > **[区切り記号]** を選択します。

5. **[適用]** を選択してエクスポートを完了します。

    ![完了したファイルのエクスポート](./media/export-file-complete.png)

## <a name="next-steps"></a>次の手順

* [クイック スタート:ASP.NET Web アプリを作成する](./quickstart-aspnet-core-app.md)  
