---
title: "Azure Portal からの関数アプリを作成する | Microsoft Docs"
description: "ポータルで Azure App Service の新しい関数アプリを作成します。"
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/15/2017
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: f9eea56f7f7b4f8f47a159c0c444d0d0aacc99cc
ms.lasthandoff: 03/22/2017


---
# <a name="create-a-function-app-from-the-azure-portal"></a>Azure Portal から関数アプリを作成する

Azure Function App では、Azure App Service のインフラストラクチャが使用されます。 このトピックでは、Azure Portal で関数アプリを作成する方法を説明します。 関数アプリは、個々の関数の実行をホストするコンテナーです。 App Service ホスティング プランで関数アプリを作成すると、その関数アプリは App Service のすべての機能を使用できます。

## <a name="create-a-function-app"></a>Function App を作成する

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

関数アプリを作成するときに、有効な**アプリ名**を入力します。名前に使用できるのは文字、数字、およびハイフンだけです。 アンダースコア (**_**) は使用できません。 

ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。 ストレージ アカウント名は Azure 内で一意である必要があります。 

関数アプリを作成したら、1 つ以上の言語で個々の関数を作成できます。 関数を作成するには、[ポータルを使用](functions-create-first-azure-function-azure-portal.md#create-a-function)するか、[継続的なデプロイ](functions-continuous-deployment.md)を使用するか、[FTP でアップロード](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp)します。  

## <a name="service-plans"></a>サービス プラン

Azure Functions には、従量課金プランと App Service プランの 2 つの異なるサービス プランがあります。 従量課金プランでは、コードの実行時にコンピューティング能力を自動的に割り当て、負荷の処理の必要性に応じてスケールアウトし、コードを実行していないときはスケールインします。 App Service プランでは、関数アプリが App Service のすべての機能にアクセスできます。 サービス プランは関数アプリの作成時に選択する必要があり、現時点では変更できません。 詳細については、[Azure Functions のホスティング プランの選択](functions-scale.md)に関するページをご覧ください。

## <a name="storage-account-requirements"></a>ストレージ アカウントの要件

App Service で関数アプリを作成するときに、BLOB、キュー、および Table Storage をサポートする汎用の Azure ストレージ アカウントを作成するか、そのアカウントにリンクする必要があります。 内部的には、Functions はトリガーの管理や関数実行のログなどの操作に Storage を使用します。 BLOB 専用のストレージ アカウント、Azure Premium Storage、ZRS レプリケーションを使用する汎用ストレージ アカウントなど、一部のストレージ アカウントでは、キューとテーブルがサポートされません。 こうしたアカウントは、関数アプリの作成時に [ストレージ アカウント] ブレードから除外されます。

>[!NOTE]
>従量課金ホスティング プランを使用する場合、関数コード ファイルおよびバインディング構成ファイルは、メイン ストレージ アカウントの Azure File Storage に保存されます。 メイン ストレージ アカウントを削除すると、このコンテンツは削除され、復元できません。

ストレージ アカウントの種類の詳細については、Azure Storage サービスの概要に関する記事 (../storage/storage-introduction.md#introducing-the-azure-storage-services) をご覧ください。

## <a name="next-steps"></a>次のステップ
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


