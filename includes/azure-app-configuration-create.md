---
title: インクルード ファイル
description: インクルード ファイル
services: azure-app-configuration
author: yegu
ms.service: azure-app-configuration
ms.topic: include
ms.date: 01/22/2019
ms.author: yegu
ms.custom: include file
ms.openlocfilehash: 9b86f2e05e2cb42470061bd6398b4200607f2418
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60012287"
---
1. 新しいアプリ構成ストアを作成するには、[Azure portal](https://aka.ms/azconfig/portal) にサインインします。 ページの左上にある **[+ リソースの作成]** を選択します。 **[Marketplace を検索]** ボックスに「**App Configuration**」と入力し、Enter キーを押します。

    ![App Configuration を検索する](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

2. 検索結果の **[アプリ構成]** を選択し、**[作成]** を選択します。

3. **[アプリの構成]** > **[作成]** ページで、次の設定を入力します。

    | Setting | 推奨値 | 説明 |
    |---|---|---|
    | **リソース名** | グローバルに一意の名前 | アプリ構成ストア リソースに使用する一意のリソース名を入力します。 名前は 1 から 63 文字の文字列で、数字、英字、`-` 文字のみを使用する必要があります。 名前の先頭と末尾には `-` 文字を使用できません。また、連続する `-` 文字は無効です。  |
    | **サブスクリプション** | 該当するサブスクリプション | App Configuration のテストに使用する Azure サブスクリプションを選択します。 アカウントにサブスクリプションが 1 つしかない場合は自動的に選択されるため、**[サブスクリプション]** ドロップダウンは表示されません。 |
    | **リソース グループ** | *AppConfigTestResources* | アプリ構成ストア リソースのリソース グループを選択または作成します。 このグループで複数のリソースをまとめておくと、そのリソース グループを削除することで複数のリソースを同時に削除できるため、便利です。 詳細については、[リソース グループを使用した Azure リソースの管理](/azure/azure-resource-manager/resource-group-overview)に関するページを参照してください。 |
    | **場所** | *米国中部* | **[場所]** を使用して、アプリ構成ストアがホストされている地理的位置を指定します。 最高のパフォーマンスを得るには、アプリケーションの他のコンポーネントと同じリージョンにリソースを作成します。 |

    ![アプリ構成ストア リソースを作成する](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

4. **作成** を選択します。 デプロイが完了するまでに数分かかることがあります。

5. デプロイが完了したら、**[設定]** > **[アクセス キー]** の順に選択します。 プライマリ読み取り専用キー接続文字列またはプライマリ読み取り/書き込みキー接続文字列を書き留めます。 この接続文字列は、後で、作成したアプリ構成ストアと通信するようにアプリケーションを構成する際に使用します。
