---
title: Azure Functions の従量課金プラン ホスティング
description: Azure Function の従量課金プラン ホスティングを使用して、動的にスケーリングされる環境でコードを実行しながら、実行中に使用されたリソースに対してのみ支払うようにできる方法について説明します。
ms.date: 8/31/2020
ms.topic: conceptual
ms.openlocfilehash: d292a70a8dfaa4cebdb99f2bcb5420c8b8ab9cd8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98760541"
---
# <a name="azure-functions-consumption-plan-hosting"></a>Azure Functions の従量課金プラン ホスティング

従量課金プランを使用する場合、Azure Functions ホストのインスタンスは、受信イベントの数に基づいて動的に追加および削除されます。 従量課金プランは、Azure Functions 向けの完全に<em>サーバーレス</em>のホスティング オプションです。

## <a name="benefits"></a>利点

従量課金プランでは、高負荷の期間中であっても、自動的にスケーリングされます。 従量課金プランで関数を実行している場合は、それらの関数を実行しているときのコンピューティング リソースに対してのみ課金されます。 従量課金プランでは、構成可能な期間が経過すると関数の実行はタイムアウトします。

従量課金プランと、他のプランやホスティングの種類との比較については、[関数のスケーリングとホスティング オプション](functions-scale.md)に関するページを参照してください。

## <a name="billing"></a>課金

課金は、実行数、実行時間、およびメモリの使用量に基づいて行われ、 使用量は、関数アプリ内のすべての関数にわたって集計されます。 詳細については、[Azure Functions の価格に関するページ](https://azure.microsoft.com/pricing/details/functions/)を参照してください。

従量課金プランで実行しているときのコストを見積もる方法の詳細については、[従量課金プランのコストの概要](functions-consumption-costs.md)に関するページを参照してください。

## <a name="create-a-consumption-plan-function-app"></a>従量課金プランの関数アプリを作成する

Azure portal で関数アプリを作成する場合は、従量課金プランが既定になります。 API を使用して関数アプリを作成している場合は、Premium プランや専用プランとは異なり、最初に App Service プランを作成する必要はありません。

プログラムまたは Azure portal で、従量課金プランでサーバーレス関数アプリを作成する方法については、次のリンクを使用してください。

+ [Azure CLI](./scripts/functions-cli-create-serverless.md)
+ [Azure Portal](./functions-get-started.md)
+ [Azure Resource Manager テンプレート](functions-create-first-function-resource-manager.md)

[Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) または [Visual Studio](functions-create-your-first-function-visual-studio.md#publish-the-project-to-azure) から Functions プロジェクトを発行する場合も、従量課金プランで関数アプリを作成できます。

## <a name="multiple-apps-in-the-same-plan"></a>同じプランでの複数のアプリ

同じリージョンの関数アプリを同じ従量課金プランに割り当てることができます。 同じ従量課金制プランで複数のアプリケーションを実行しても、マイナス面や影響はありません。 同じ従量課金プランに複数のアプリを割り当てても、各アプリの回復性、スケーラビリティ、または信頼性には影響しません。

## <a name="next-steps"></a>次のステップ

+ [Azure Functions のホスティング オプション](functions-scale.md)
+ [Azure Functions でのイベント ドリブン スケーリング](event-driven-scaling.md)
