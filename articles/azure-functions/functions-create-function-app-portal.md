---
title: Azure Portal から Function App を作成する
description: ポータルから Azure の新しい Function App を作成します。
ms.topic: how-to
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 001b4e4f0ea7fbacd232b2a87abfe353f34919bd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "80985016"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Azure Portal から Function App を作成する

このトピックでは、Azure Functions を使用して Azure portal で Function App を作成する方法について示します。 Function App は、個々の関数の実行をホストするコンテナーです。 

## <a name="create-a-function-app"></a>Function App を作成する

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Function App を作成したら、1 つ以上の言語で個々の関数を作成できます。 関数を作成するには、[ポータルを使用](functions-create-first-azure-function.md#create-function) するか、[継続的なデプロイ](functions-continuous-deployment.md) を使用するか、[FTP でアップロード](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp) します。

## <a name="service-plans"></a>サービス プラン

Azure Functions には、次の 3 つの異なるサービス プランが用意されています。従量課金プラン、Premium プラン、専用 (App Service) プランです。 Function App の作成時にサービス プランを選択する必要があり、後で変更することはできません。 詳細については、[Azure Functions のホスティング プランの選択](functions-scale.md) に関するページをご覧ください。

専用 (App Service) プランで JavaScript 関数を実行する予定がある場合は、コアのより少ないプランを選択してください。 詳細については、「[JavaScript 関数リファレンス](functions-reference-node.md#choose-single-vcpu-app-service-plans)」を参照してください。

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>ストレージ アカウントの要件

Function App を作成するときは、BLOB、キュー、テーブル ストレージをサポートする汎用の Azure Storage アカウントを作成またはリンクする必要があります。 内部的には、Functions はトリガーの管理や関数実行のログなどの操作に Storage を使用します。 BLOB 専用のストレージ アカウント、Azure Premium Storage、ZRS レプリケーションを使用する汎用ストレージ アカウントなど、一部のストレージ アカウントでは、キューとテーブルがサポートされません。 こうしたアカウントは、Function App の作成時に [ストレージ アカウント] ブレードから除外されます。

>[!NOTE]
>従量課金ホスティング プランを使用する場合、関数コード ファイルおよびバインディング構成ファイルは、メイン ストレージ アカウントの Azure File Storage に保存されます。 メイン ストレージ アカウントを削除すると、このコンテンツは削除され、復元できません。

ストレージ アカウントの種類の詳細については、「[Azure Storage サービスの概要](../storage/common/storage-introduction.md#core-storage-services)」を参照してください。 

## <a name="next-steps"></a>次のステップ

Azure portal を使用すると関数を簡単に作成したり試したりできますが、[ローカル開発](functions-develop-local.md) をお勧めします。 ポータルで Function App を作成した後、さらに関数を追加する必要があります。 

> [!div class="nextstepaction"]
> [HTTP によってトリガーされる関数の追加](functions-create-first-azure-function.md#create-function)
