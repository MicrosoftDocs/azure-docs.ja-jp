---
author: baanders
description: Azure Digital Twins チュートリアルのインクルード ファイル - サンプル プロジェクトの前提条件
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 43cc3dfc5b425df6d9dd5e2c2f35a792907ccdea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103622187"
---
## <a name="prerequisites"></a>前提条件

このチュートリアルの手順を実行するには、まず次の前提条件を満たしておく必要があります。 

Azure サブスクリプションをお持ちでない場合は、開始する前に **[無料のアカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成** してください。

### <a name="get-required-resources"></a>必要なリソースを入手する

このチュートリアルを実行するには、ご利用の開発用コンピューターに **[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) バージョン 16.5 以降をインストール** します。 以前のバージョンが既にインストールされている場合は、ご利用のコンピューター上で "*Visual Studio インストーラー*" アプリを開き、画面の指示に従ってインストールを更新できます。

>[!NOTE]
> Visual Studio 2019 のインストールに **[Azure 開発ワークロード](/dotnet/azure/configure-visual-studio)** が含まれていることを確認します。 このワークロードにより、アプリケーションでは Azure functions を発行し、その他の Azure 開発タスクを実行できます。

このチュートリアルは、C# で記述されたサンプル プロジェクトによって進められます。 サンプルについては、こちらを参照してください。[Azure Digital Twins のエンドツーエンド サンプル](/samples/azure-samples/digital-twins-samples/digital-twins-samples)。 サンプルのリンクに移動し、タイトルの下にある " *[Browse Code]\(コードの参照\)* " ボタンを選択して、お使いのマシン上で **サンプル プロジェクトを取得** します。 これにより、サンプル用の GitHub リポジトリに移動します。サンプルは、" *[Code]\(コード\)* " ボタンと、" *[Download ZIP]\(ZIP のダウンロード\)* " を選択して、 *.ZIP* としてダウンロードできます。

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="GitHub の digital-twins-samples リポジトリのビュー。[Code]\(コード\) ボタンが選択されて、小さなダイアログ ボックスが生成されます。ここでは、[Download ZIP]\(ZIP のダウンロード\) ボタンが強調表示されています。" lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

これにより、 *.ZIP* フォルダーが **digital-twins-samples-master.zip** としてお使いのマシンにダウンロードされます。 フォルダーを解凍し、ファイルを抽出します。

### <a name="prepare-an-azure-digital-twins-instance"></a>Azure Digital Twins インスタンスを準備する

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]
