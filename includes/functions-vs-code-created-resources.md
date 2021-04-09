---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/31/2021
ms.author: glenga
ms.openlocfilehash: 9a5c143927f549124cb6e69a4c8eb4829709a9e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99493499"
---
+ [リソース グループ](../articles/azure-resource-manager/management/overview.md)。関連リソースの論理コンテナーです。
+ Standard [Azure ストレージ アカウント](../articles/storage/common/storage-account-create.md)。プロジェクトについての状態とその他の情報を保持します。
+ 従量課金プラン。サーバーレス関数アプリの実行環境となるホストを定義します。 
+ 関数アプリ。関数コードを実行するための環境となります。 関数アプリを使用すると、同じホスティング プランに含まれるリソースの管理、デプロイ、共有を容易にするための論理ユニットとして関数をグループ化できます。
+ 関数アプリに接続された Application Insights インスタンス。サーバーレス関数の使用を追跡します。