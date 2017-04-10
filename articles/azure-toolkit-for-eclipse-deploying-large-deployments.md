---
title: "大規模なデプロイ"
description: "Azure Toolkit for Eclipse を使用して大規模なデプロイを行う方法について説明します。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 5e18bace-5df0-4af8-ad86-6151ea8bd823
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: d6b2b28a7e01d5a70cd08f4cd2d8cccff7d918cf


---
# <a name="deploying-large-deployments"></a>大規模なデプロイ
デプロイの規模が大きすぎて既定の approot フォルダーに格納できない場合は、JDK とアプリケーション サーバーのデプロイ ルート フォルダーとしてローカル ストレージ リソースを使用できます。

## <a name="to-use-a-local-storage-resource-as-the-deployment-root-folder-for-large-deployments"></a>大規模なデプロイのデプロイ ルート フォルダーとしてローカル ストレージ リソースを使用するには
1. 新しいローカル ストレージ リソースを作成します。 リソースの名前は問いません。 ストレージ リソースはロール レベルで定義されます。 新しいローカル ストレージ リソースを作成するためのローカル ストレージ構成ダイアログにアクセスする最も簡単な方法は、次の手順に従うことです。**[Project Explorer (プロジェクト エクスプローラー)]** ビューでロールを右クリックします (ロールが表示されていない場合は、Azure プロジェクト ノードを展開します)。**[Azure]** をクリックし、**[Local Storage (ローカル ストレージ)]** をクリックします。 **[Local Storage (ローカル ストレージ)]** ダイアログで、**[Add (追加)]** をクリックして新しいローカル ストレージ リソースを作成します。
2. 2048 MB 以上の目的のサイズを設定します (このサイズより小さくすると、approot のように同一ファイル サイズ問題が発生する可能性があります)。
3. **[Clean the contents when the role instance is recycled (ロール インスタンスをリサイクルするときにコンテンツをクリーニングする)]** がオンになっていることを確認します。これで、ロール インスタンスがリサイクルされるときに、デプロイのスタートアップ ロジックがリソース内の既存のファイルと競合することを防止できます。
4. **[Environment variable storing the resource's directory path after deployment (デプロイ後にリソースのディレクトリ パスを格納する環境変数)]** の値が文字列 **DEPLOYROOT** に設定されていることを確認します。 ローカル ストレージ リソース ダイアログは次のようになります。
    ![][ic667943]

ローカル リソースの*名前*として **DEPLOYROOT** を使用し、自動的に生成される環境変数 (この場合は **DEPLOYROOT_PATH** に設定されます) を変更しないという操作でも、アプリケーションは問題なく動作します。

ローカル ストレージ リソースの作成に関する追加情報については、「[ローカル ストレージのプロパティ][Local storage properties]」を参照してください。

## <a name="see-also"></a>関連項目
[Azure Toolkit for Eclipse][Azure Toolkit for Eclipse]

[Azure 向け Hello World アプリケーションを Eclipse で作成する][Creating a Hello World Application for Azure in Eclipse]

[Azure Toolkit for Eclipse のインストール][Installing the Azure Toolkit for Eclipse] 

Java での Azure の使用の詳細については、[Azure Java デベロッパー センター][Azure Java Developer Center]を参照してください。

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Local storage properties]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties

<!-- IMG List -->

[ic667943]: ./media/azure-toolkit-for-eclipse-deploying-large-deployments/ic667943.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268601.aspx -->



<!--HONumber=Jan17_HO1-->


