---
title: Azure Portal からの関数アプリを作成する | Microsoft Docs
description: ポータルで Azure App Service の新しい関数アプリを作成します。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 04/11/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: ad9c50953447c1effee48eec5b0cb9f64386e6cc
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155565"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Azure Portal から関数アプリを作成する

Azure Function App では、Azure App Service のインフラストラクチャが使用されます。 このトピックでは、Azure Portal で関数アプリを作成する方法を説明します。 関数アプリは、個々の関数の実行をホストするコンテナーです。 App Service ホスティング プランで関数アプリを作成すると、その関数アプリは App Service のすべての機能を使用できます。

## <a name="create-a-function-app"></a>Function App を作成する

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

関数アプリを作成するときに、有効な**アプリ名**を入力します。名前に使用できるのは文字、数字、およびハイフンだけです。 アンダースコア ( **_** ) は使用できません。

ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。 ストレージ アカウント名は Azure 内で一意である必要があります。 

関数アプリを作成したら、1 つ以上の言語で個々の関数を作成できます。 関数を作成するには、[ポータルを使用](functions-create-first-azure-function.md#create-function)するか、[継続的なデプロイ](functions-continuous-deployment.md)を使用するか、[FTP でアップロード](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp)します。

## <a name="service-plans"></a>サービス プラン

Azure Functions には、従量課金プランと App Service プランの 2 つの異なるサービス プランがあります。 従量課金プランでは、コードの実行時にコンピューティング能力を自動的に割り当て、負荷の処理の必要性に応じてスケールアウトし、コードを実行していないときはスケールインします。 App Service プランでは、関数アプリが App Service のすべての機能にアクセスできます。 サービス プランは関数アプリの作成時に選択する必要があり、現時点では変更できません。 詳細については、[Azure Functions のホスティング プランの選択](functions-scale.md)に関するページをご覧ください。

App Service プランで JavaScript 関数を実行する場合、コアの少ないプランを選択してください。 詳細については、「[JavaScript 関数リファレンス](functions-reference-node.md#choose-single-vcpu-app-service-plans)」を参照してください。

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>ストレージ アカウントの要件

App Service で関数アプリを作成するときに、BLOB、キュー、および Table Storage をサポートする汎用の Azure ストレージ アカウントを作成するか、そのアカウントにリンクする必要があります。 内部的には、Functions はトリガーの管理や関数実行のログなどの操作に Storage を使用します。 BLOB 専用のストレージ アカウント、Azure Premium Storage、ZRS レプリケーションを使用する汎用ストレージ アカウントなど、一部のストレージ アカウントでは、キューとテーブルがサポートされません。 こうしたアカウントは、関数アプリの作成時に [ストレージ アカウント] ブレードから除外されます。

>[!NOTE]
>従量課金ホスティング プランを使用する場合、関数コード ファイルおよびバインディング構成ファイルは、メイン ストレージ アカウントの Azure File Storage に保存されます。 メイン ストレージ アカウントを削除すると、このコンテンツは削除され、復元できません。

ストレージ アカウントの種類の詳細については、「[Azure Storage サービスの概要](../storage/common/storage-introduction.md#azure-storage-services)」を参照してください。 

## <a name="next-steps"></a>次の手順

Azure portal を使用すると関数を簡単に作成したり試したりできますが、[ローカル開発](functions-develop-local.md)をお勧めします。 ポータルで関数アプリを作成した後、さらに関数を追加する必要があります。 

> [!div class="nextstepaction"]
> [HTTP によってトリガーされる関数の追加](functions-create-first-azure-function.md#create-function)
