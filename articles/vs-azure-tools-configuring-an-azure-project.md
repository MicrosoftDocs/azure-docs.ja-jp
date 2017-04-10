---
title: "Visual Studio で Azure クラウド サービス プロジェクトを構成する | Microsoft Docs"
description: "Visual Studio で、そのプロジェクトの要件に応じて、Azure クラウド サービス プロジェクトを構成する方法について説明します。"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 609d6965-05cc-47b1-82dc-c76a92d4f295
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/06/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 11a56418156a46e1fcef7b8d1c6003990000046f
ms.lasthandoff: 03/27/2017


---
# <a name="configure-an-azure-cloud-service-project-with-visual-studio"></a>Visual Studio で Azure クラウド サービス プロジェクトを構成する
Azure クラウド サービス プロジェクトは、そのプロジェクトの要件に応じて構成できます。 次のカテゴリに関して、プロジェクトのプロパティを設定できます。

- **クラウド サービスを Azure に発行する** - Azure にデプロイされた既存のクラウド サービスが誤って削除されないようにするためのプロパティを設定できます。
- **ローカル コンピューターでクラウド サービスを実行またはデバッグする** - 使用するサービス構成を選択し、Azure ストレージ エミュレーターを開始するかどうかを指定できます。
- **クラウド サービス パッケージを作成時に検証する** - クラウド サービス パッケージを問題のない状態で確実にデプロイできるように、すべての警告をエラーとして処理できます。 

## <a name="steps-to-configure-an-azure-cloud-service-project"></a>Azure クラウド サービス プロジェクトを構成する手順
1. Visual Studio でクラウド サービス プロジェクトを開くか作成します。

1. **ソリューション エクスプローラー**でそのプロジェクトを右クリックし、コンテキスト メニューから **[プロパティ]** を選択します。
   
1. プロジェクトのプロパティ ページで **[開発]** タブを選択します。

    ![プロジェクトのプロパティ メニュー](./media/vs-azure-tools-configuring-an-azure-project/solution-explorer-project-properties-menu.png)

1. **[既存の配置を削除する前にプロンプトで確認します]** を **[True]** に設定します。 この設定により、Azure で既存のデプロイが誤って削除されないようにすることができます。

1. クラウド サービスをローカルで実行またはデバッグするときに使用する**サービス構成**を選択します。 ロールのサービス構成を変更する方法の詳細については、[Visual Studio で Azure クラウド サービスのロールを構成する方法](./vs-azure-tools-configure-roles-for-cloud-service.md)に関する記事をご覧ください。

1. クラウド サービスをローカルで実行またはデバッグするときに Azure ストレージ エミュレーターを開始するには、**[Start Azure storage emulator (Azure ストレージ エミュレーターの起動)]** を **[True]** に設定します。

1. パッケージの検証エラーがある場合には発行できないようにするには、**[警告をエラーとして扱う]** を **[True]** に設定します。

1. IIS Express で Web ロールがローカルで開始されるときに毎回同じポートが使用されるようにするには、**[Web プロジェクト ポートの使用]** を **[True]** に設定します。

1. Visual Studio ツール バーの **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ
- [複数のサービス構成を使用した Azure プロジェクトの構成](vs-azure-tools-multiple-services-project-configurations.md)


