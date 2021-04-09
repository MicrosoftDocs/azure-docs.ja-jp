---
title: クイック スタート - Azure Analysis Services サーバーのファイアウォールの構成 | Microsoft Docs
description: このクイック スタートでは、Azure portal を利用し、Azure Analysis Services サーバーのファイアウォールを構成できます。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: e4953137cf939c35c6ac73fe51ca43eca6e99edc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88192437"
---
# <a name="quickstart-configure-server-firewall---portal"></a>クイック スタート:サーバーのファイアウォールの構成 - ポータル

このクイック スタートでは、Azure Analysis Services サーバーのファイアウォールを構成できます。 ファイアウォールを有効にし、IP アドレス範囲をサーバーにアクセスするコンピューターのみに構成することは、サーバーとデータの保護の重要な部分です。

## <a name="prerequisites"></a>前提条件

- サブスクリプションの Analysis Services サーバー。 詳しくは、「[クイック スタート: サーバーの作成 - ポータル](analysis-services-create-server.md)」または「[クイック スタート: サーバーの作成 - PowerShell](analysis-services-create-powershell.md)」をご覧ください。
- クライアント コンピューターの 1 つまたは複数の IP アドレス範囲 (必要な場合)。

> [!NOTE]
> Microsoft Cloud Germany では現在、ファイアウォールが有効であるときに、Power BI Premium からのデータのインポート (更新) やページ分割されたレポートの接続はサポートされません。[Power BI からのアクセスを許可する] がオンに設定されていても同様です。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする 

[ポータルにサインイン](https://portal.azure.com)します。

## <a name="configure-a-firewall"></a>ファイアウォールを構成する

1. サーバーをクリックして [概要] ページを開きます。 
2. **[設定]**  >  **[ファイアウォール]**  >  **[ファイアウォールを有効にする]** で、 **[オン]** を選択します。
3. Power BI や Power BI Premium からの接続を有効にするために、 **[Power BI からのアクセスを許可する]** で **[オン]** を選択します。  
4. (省略可能) 1 つまたは複数の IP アドレス範囲を指定します。 各範囲の名前、開始 IP アドレス、終了 IP アドレスを入力します。 ファイアウォール規則の名前は 128 文字に制限され、大文字、小文字、数値、アンダースコア、およびハイフンのみを使用できます。 空白スペースとその他の特殊文字は使用できません。
5. **[保存]** をクリックします。

     ![ファイアウォールの設定](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になった場合は、IP アドレス範囲を削除するか、ファイアウォールを無効にします。

## <a name="next-steps"></a>次の手順
このクイック スタートでは、サーバーのファイアウォールを構成する方法を説明しました。 サーバーが存在し、ファイアウォールで保護されたので、ポータルからそれに基本的なサンプル データ モデルを追加できます。 サンプル モデルがあると、モデル データベース ロールの構成とクライアント接続のテストについて理解するのに役立ちます。 詳しくは、サンプル モデルの追加のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル: サーバーにサンプル モデルを追加する](analysis-services-create-sample-model.md)
