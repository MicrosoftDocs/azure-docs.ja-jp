---
title: Azure クラウド サービス (延長サポート) をデプロイする - Azure portal
description: Azure portal を使用して Azure クラウド サービス (延長サポート) をデプロイする
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 9c1abe1323bc095fe4dfbfc559ef7e159d1f7532
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880677"
---
# <a name="deploy-a-azure-cloud-services-extended-support-using-the-azure-portal"></a>Azure portal を使用して Azure クラウド サービス (延長サポート) をデプロイする
この記事では、Azure portal を使ってクラウド サービス (延長サポート) のデプロイを作成する方法を説明します。 

> [!IMPORTANT]
> Cloud Services (延長サポート) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="before-you-begin"></a>始める前に

クラウド サービス (延長サポート) の[デプロイの前提条件](deploy-prerequisite.md)を確認し、関連するリソースを作成します。 

## <a name="deploy-a-cloud-services-extended-support"></a>クラウド サービス (延長サポート) をデプロイする 
1. [Azure ポータル](https://portal.azure.com)

2.  Azure portal 最上部にある検索バーを使用し、 **[Cloud Services (extended support)]\(Cloud Services (延長サポート)\)** を検索および選択します。

    :::image type="content" source="media/deploy-portal-1.png" alt-text="Azure portal 内の [すべてのリソース] ブレードを示した画像。":::
 
3.  [Cloud Services (extended support)]\(Cloud Services (延長サポート)\) ペインで、 **[作成]** を選択します。 

    :::image type="content" source="media/deploy-portal-2.png" alt-text="マーケットプレースからクラウド サービスを購入している画像。":::

4. **[基本]** タブでクラウド サービス (延長サポート) の作成ウィンドウが開きます。 
    - サブスクリプションを選択します。
    - リソース グループを選択するか、新しく作成します。
    - クラウドサービス (延長サポート) のデプロイに付ける名前を入力します。
        - クラウド サービスの DNS 名は、これとは別個のもので、パブリック IP アドレスの DNS 名ラベルで指定します。DNS 名の変更は、[構成] タブのパブリック IP セクションから行えます。
    -  デプロイ先のリージョンを選択します。

    :::image type="content" source="media/deploy-portal-3.png" alt-text="Cloud Services (延長サポート) のホーム ブレードの画像。":::

5. クラウド サービスの構成、パッケージ、定義ファイルを追加します。 BLOB ストレージから既存のファイルを追加することも、ローカル マシンからこれらをアップロードすることもできます。 ローカル マシンからアップロードする場合、これらはストレージ アカウントに格納されます。 

    :::image type="content" source="media/deploy-portal-4.png" alt-text="作成中の [基本] タブのアップロード セクションの画像。":::

6. すべてのフィールドに入力を終えたら、 **[構成]** タブに移動し、項目を入力します。 
    - クラウド サービスに関連付ける仮想ネットワークを選択するか、新たに作成します。 
        - クラウド サービス (延長サポート) のデプロイは、仮想ネットワーク内に存在する **必要があります**。 また、その仮想ネットワークは、サービス構成 (.cscfg) ファイル内の `NetworkConfiguration` セクションで参照されている **必要があります**。
    - クラウド サービスに関連付ける既存のパブリック IP アドレスを選択するか、新たに作成します。
        - サービス定義 (.csdef) ファイルの中で **IP 入力エンドポイント** を定義していた場合には、クラウド サービス用にパブリック IP アドレスを作成する必要があります。 
        - クラウド サービス (延長サポート) では、Basic の IP アドレス SKU のみをサポートしています。
        - サービス構成 (.cscfg) に予約済み IP アドレスが含まれる場合には、パブリック IP の割り当ての種類を **[静的]** に設定する必要があります。 
        - 必要に応じて、クラウド サービス エンドポイントに DNS 名を割り当てます。これは、クラウド サービスに関連付けられているパブリック IP アドレスの DNS ラベル プロパティを更新することによって行います。  
    - (省略可能) クラウド サービスを起動します。 作成後すぐにサービスを起動するか、起動しないかを選択します。
    - キー コンテナーを選択します。 
        - サービス構成 (.cscfg) ファイルで証明書を 1 つ以上指定している場合には、キー コンテナーが必要です。 キー コンテナーを選択すると、選択されている証明書が、その拇印に基づいて、サービス構成 (.cscfg) ファイルから検索されます。 キー コンテナーにない証明書がある場合は、証明書をアップロードし、 **[最新の情報に更新]** をクリックします。   

 :::image type="content" source="media/deploy-portal-5.png" alt-text="クラウド サービス (延長サポート) を作成しているときの Azure portal 内の [構成] ブレードの画像。":::

7. すべてのフィールドに入力を終えたら、 **[確認と作成]** タブに移動し、デプロイ構成を検証して、クラウド サービス (延長サポート) を作成します。

## <a name="next-steps"></a>次のステップ 
- Cloud Services (延長サポート) に関して[よく寄せられる質問](faq.md)を確認します。
- [Azure portal](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[テンプレート](deploy-template.md)、または [Visual Studio](deploy-visual-studio.md) を使用してクラウド サービス (延長サポート) をデプロイします。
- [Cloud Services (延長サポート) のサンプル リポジトリ](https://github.com/Azure-Samples/cloud-services-extended-support)を確認します。