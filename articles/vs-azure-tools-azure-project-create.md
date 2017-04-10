---
title: "Visual Studio での Azure クラウド サービス プロジェクトの作成 | Microsoft Docs"
description: "Visual Studio で Azure クラウド サービス プロジェクトを作成する方法を説明します。"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: ec580df7-3dcc-45a9-a1d9-8c110678dfb5
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: b1870b5db6e194f61d2f8b526e0c81b4d46699e4
ms.lasthandoff: 03/22/2017


---
# <a name="creating-an-azure-cloud-service-project-with-visual-studio"></a>Visual Studio での Azure クラウド サービス プロジェクトの作成
Azure Tools for Visual Studio には、Azure クラウド サービスを作成できるプロジェクト テンプレートが用意されています。 プロジェクトを作成したら、Visual Studio でクラウド サービスを構成し、デバッグして、Azure にデプロイできます。

## <a name="steps-to-create-an-azure-cloud-service-project-in-visual-studio"></a>Visual Studio で Azure クラウド サービス プロジェクトを作成する手順
このセクションでは、Visual Studio で 1 つ以上の Web ロールを追加して Azure クラウド サービス プロジェクトを作成する手順について説明します。  

1. 管理者として Visual Studio を起動します。

1. メイン メニューで、**[ファイル]** > **[新規作成]** > **[プロジェクト]** を選択します。

1. Visual C# または Visual Basic プロジェクト テンプレート ノードで **[クラウド]** を選択し、テンプレートの一覧から **[Azure クラウド サービス]** を選択します。

    ![新しい Azure クラウド サービス](./media/vs-azure-tools-azure-project-create/new-project-wizard-for-cloud-service.png)

1. プロジェクトの開発に使用する .NET Framework のバージョンを指定します。

1. プロジェクトの名前と場所、およびソリューションの名前を入力します。 

1. **[OK]**を選択します。

1. **[新しい Microsoft Azure クラウド サービス]** ダイアログで、追加するロールを選択し、右矢印ボタンをクリックしてロールをソリューションに追加します。

    ![新しい Azure クラウド サービス ロールの選択](./media/vs-azure-tools-azure-project-create/new-cloud-service.png)

1. 追加したロールの名前を変更するには、**[新しい Microsoft Azure クラウド サービス]** ダイアログでそのロールの上にマウス ポインターを置き、コンテキスト メニューの **[名前の変更]** を選択します。 ロールを追加した後に、(**ソリューション エクスプローラー**で) ソリューション内でロールの名前を変更することもできます。

    ![Azure クラウド サービス ロールの名前の変更](./media/vs-azure-tools-azure-project-create/new-cloud-service-rename.png)

Visual Studio の Azure プロジェクトは、ソリューション内のロール プロジェクトに関連付けられています。 また、プロジェクトには、*サービス定義ファイル*と*サービス構成ファイル*が含まれます。

- **サービス定義ファイル** - 必要なロール、エンドポイント、仮想マシンのサイズなど、アプリケーションのランタイム設定を定義します。 
- **サービス構成ファイル** - 実行されるロールのインスタンス数とロールに定義されている設定の値を構成します。 

これらのファイルの詳細については、[Visual Studio を使用した Azure クラウド サービスのロールの構成](vs-azure-tools-configure-roles-for-cloud-service.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ
- [Visual Studio での Azure クラウド サービス プロジェクトのロールの管理](./vs-azure-tools-cloud-service-project-managing-roles.md)

