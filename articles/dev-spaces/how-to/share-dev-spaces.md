---
title: Azure Dev Spaces を共有する方法
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: conceptual
description: Azure のコンテナーとマイクロサービスを使用した迅速な Kubernetes 開発
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s '
ms.openlocfilehash: 62d4affa5ef49de7600f9ccc800ea6bf83e4bd49
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58099578"
---
# <a name="share-azure-dev-spaces"></a>Azure Dev Spaces を共有する

Azure Dev Spaces を使用すると、チームの他のメンバーと開発スペースを共有できます。 それぞれの開発者は、他の開発者に影響を及ぼす心配なしに、独自のスペースで作業できます。 また、1 つのスペースで共同作業することで、モックを作成する必要なしにコードをエンドツーエンドでテストしたり、依存関係をシミュレートしたりできます。 詳細については、「[チーム開発について学ぶ](../team-development-nodejs.md)」ガイドを参照してください。

## <a name="set-up-a-dev-space-for-multiple-developers"></a>複数の開発者向けに開発空間を設定する

1. Azure で Dev Space を作成します。 [[.NET Core and VS Code] (.NET Core と VS Code)](../get-started-netcore.md)、[[.NET Core and Visual Studio] (.NET Core と Visual Studio)](../get-started-netcore-visualstudio.md)、または [[Node.js and VS Code] (Node.js と VS Code)](../get-started-nodejs.md) を選択します。 選択した Azure サブスクリプションの所有者または共同作成者のアクセスが必要になります。
1. Azure Dev Space の**リソース グループ**を構成して、各チーム メンバーの[共同作成者アクセスを付与](/azure/active-directory/role-based-access-control-configure)します。 開発空間のリソース グループは、`azds list-up` というコマンドを実行して確認できます。
1. その中で開発を行うために**開発空間を選択**するよう、チーム メンバーに依頼します。
   * **コマンド ラインまたは VS Code**:アクセスできる既存の Azure Dev Spaces を確認する場合: `azds space list`。 開発空間を選択する場合: `azds space select`。
   * **Visual Studio IDE**:プロジェクトを Visual Studio で開き、起動設定ドロップダウンから **[Azure Dev Spaces]** を選択します。 表示されるダイアログ ボックスで、既存のクラスターを選択します。

     ![Visual Studio の起動設定ドロップダウン](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>次の手順

詳細については、「[チーム開発について学ぶ](../team-development-nodejs.md)」を参照してください。
