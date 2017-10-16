---
title: "Azure Functions ランタイムの概要 | Microsoft Docs"
description: "Azure Functions ランタイム プレビューの概要"
services: functions
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/08/2017
ms.author: anwestg
ms.openlocfilehash: cb98d5f2aaa526555820c15ba5a32fb7e78ffc5a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-runtime-overview"></a>Azure Functions ランタイム プレビューの概要

Azure Functions ランタイムは、オンプレミスの Azure Functions プログラミング モデルのシンプルさと柔軟性を活用するための新たな手段です。 Azure Functions ランタイムは、Azure Functions と同一のオープン ソースのルートで構築すると、オンプレミスにデプロイされて、クラウド サービスとほぼ同じ開発環境を提供します。

![Azure Functions ランタイム プレビュー ポータル][1]

Azure Functions ランタイムは、Azure Functions をクラウドにコミットする前に使用する手段を提供します。 これにより、構築したコード資産は、移行する際にクラウドに移動することができます。  ランタイムは、オンプレミス コンピューターの予備のコンピューティング能力を使用して夜間バッチ処理を実行するなど、新しいオプションも提供します。 組織内でデバイスを使用して、条件に応じてオンプレミスとクラウドの両方の別のシステムにデータを送信することもできます。

Azure Functions ランタイムは、次の 2 つの部分で構成されます。
* Azure Functions ランタイム管理ロール
* Azure Functions ランタイム worker ロール

## <a name="azure-functions-management-role"></a>Azure Functions 管理ロール

Azure Functions 管理ロールは、関数をオンプレミスで管理するためのホストを提供します。 このロールは次のタスクを実行します。

* [Azure Portal](https://portal.azure.com) に表示されているのと同じ Azure Functions 管理ポータルのホスト。 Azure Portal の場合と同じ方法で関数を開発できます。
* 複数の Functions worker 全体での関数の分配。
* Microsoft Visual Studio から関数を直接発行可能にする発行エンドポイントの提供。

## <a name="azure-functions-worker-role"></a>Azure Functions worker ロール

Azure Functions worker ロールは、Windows コンテナーにデプロイされ、ここが関数コードの実行場所となります。  組織全体に複数の worker ロールをデプロイできます。顧客が予備のコンピューティング能力を使用できるようにする主な方法です。

## <a name="minimum-requirements"></a>最小要件

Azure Functions ランタイムを使用するには、**Windows Server 2016 または Windows 10 Creators Update** がインストールされていて、**SQL Server** インスタンスにアクセスできるコンピューターが必要です。

## <a name="next-steps"></a>次のステップ

[Azure Functions ランタイム プレビュー](https://aka.ms/azafr)をインストールする

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
