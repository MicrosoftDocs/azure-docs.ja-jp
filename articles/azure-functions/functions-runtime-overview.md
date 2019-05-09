---
title: Azure Functions ランタイムの概要 | Microsoft Docs
description: Azure Functions ランタイム プレビューの概要
services: functions
author: apwestgarth
manager: stefsch
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 2af9575c50ee522d6330ddf46c75b666132b7a84
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59546835"
---
# <a name="azure-functions-runtime-overview-preview"></a>Azure Functions Runtime の概要 (プレビュー)

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Azure Functions Runtime (プレビュー) は、オンプレミスの Azure Functions プログラミング モデルのシンプルさと柔軟性を活用するための新たな手段です。 Azure Functions ランタイムは、Azure Functions と同一のオープン ソースのルートで構築すると、オンプレミスにデプロイされて、クラウド サービスとほぼ同じ開発環境を提供します。

![Azure Functions ランタイム プレビュー ポータル][1]

Azure Functions ランタイムは、Azure Functions をクラウドにコミットする前に使用する手段を提供します。 これにより、構築したコード資産は、移行する際にクラウドに移動することができます。  ランタイムは、オンプレミス コンピューターの予備のコンピューティング能力を使用して夜間バッチ処理を実行するなど、新しいオプションも提供します。 組織内でデバイスを使用して、条件に応じてオンプレミスとクラウドの両方の別のシステムにデータを送信することもできます。

Azure Functions ランタイムは、次の 2 つの部分で構成されます。

* Azure Functions ランタイム管理ロール
* Azure Functions ランタイム worker ロール

## <a name="azure-functions-management-role"></a>Azure Functions 管理ロール

Azure Functions 管理ロールは、関数をオンプレミスで管理するためのホストを提供します。 このロールは次のタスクを実行します。

* [Azure Portal](https://portal.azure.com) に表示されているのと同じ Azure Functions 管理ポータルのホスト。 このポータルには、Azure Portal の場合と同じ方法で関数を開発できる一貫した操作性が備わっています。
* 複数の Functions worker 全体での関数の分配。
* 発行エンドポイントの提供。発行プロファイルをダウンロードしてインポートすることによって、Microsoft Visual Studio から関数を直接発行することができます。

## <a name="azure-functions-worker-role"></a>Azure Functions worker ロール

Azure Functions worker ロールは、Windows コンテナーにデプロイされ、ここが関数コードの実行場所となります。  組織全体に複数の worker ロールをデプロイできます。顧客が予備のコンピューティング能力を使用できるようにする主な方法です。  多くの組織に存在する予備のコンピューティング能力の例としては、常時電源が入っているものの大半の時間使われていないマシンが挙げられます。

## <a name="minimum-requirements"></a>最小要件

Azure Functions ランタイムを使用するには、Windows Server 2016 または Windows 10 Creators Update がインストールされていて、SQL Server インスタンスにアクセスできるコンピューターが必要です。

## <a name="next-steps"></a>次の手順

[Azure Functions ランタイム プレビュー](https://aka.ms/azafrdoc)をインストールする

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
