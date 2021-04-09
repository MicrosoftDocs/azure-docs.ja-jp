---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 43c8c578587c65b6e0317caf77ff2d0604cb4fa3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102044668"
---
### <a name="build-and-deploy-the-app"></a>アプリを構築してデプロイする

Unity によって生成された `.sln` ファイルを開きます。 ビルド構成を次のように変更します。

:::image type="content" source="./media/object-anchors-quickstarts-unity/aoa-unity-vs-build-config.png" alt-text="ビルド構成":::

次に、アプリをデプロイし、デバッグするために、 **[Remote Machine IP address]\(リモート マシンの IP アドレス\)** を構成する必要があります。

[App project]\(アプリ プロジェクト\) を右クリックし、 **[Properties]\(プロパティ\)** をクリックします。 [Properties]\(プロパティ\) ページで、 **[Configuration Properties]\(構成プロパティ\) -> [Debugging]\(デバッグ\)** を選択します。 **[Machine Name]\(マシン名\)** の値を HoloLens デバイスの IP アドレスに変更し、 **[Apply]\(適用\)** をクリックします。

:::image type="content" source="./media/object-anchors-quickstarts-unity/aoa-unity-vs-remote-debug.png" alt-text="リモート デバッグ":::

プロパティ ページを閉じます。 **[Remote Machine]\(リモート マシン\)** をクリックします。 アプリのビルドとリモート デバイスへのデプロイが開始されます。 デバイスがアクティブであることを確認します。
