---
title: AppSource パッケージを Azure Storage に保存し、SAS キーで URL を生成する | Microsoft Docs
description: AppSource パッケージをアップロードし、セキュリティで保護するために必要な手順について説明します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: ad0e6eaae5c0fad74ea484827e0f8d535cfbf579
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57884661"
---
<a name="store-your-appsource-package-to-azure-storage-and-generate-a-url-with-sas-key"></a>AppSource パッケージを Azure Storage に保存し、SAS キーで URL を生成する
=============================================================================

ファイルのセキュリティを保守管理するには、すべてのパートナーが AppSource パッケージ ファイルを Azure BLOB ストレージ アカウントに保存し、SAS キーで共有する必要があります。 Microsoft は認証と AppSource 試用版での使用のために Azure ストレージの場所からパッケージ ファイルを取得します。

次の手順でパッケージを BLOB ストレージにアップロードします。

1. <https://azure.microsoft.com> に移動し、無料試用版または課金アカウントを作成します。

2. [Azure Portal](https://portal.azure.com/) にサインインします。

3. **[+ 新規]** をクリックして **[データ + ストレージ]** アカウントに進み、ストレージ アカウントを新規作成します。

   ![Microsoft Azure portal の [データ + ストレージ] ブレード](media/CRMScreenShot7.png)

4. **[名前]** と **[リソース グループ]** 名を入力し、**[作成]** ボタンをクリックします。

   ![Microsoft Azure portal でストレージ アカウントを作成する](media/CRMScreenShot8.png)

5. 新しく作成したリソース グループに移動し、新しい BLOB コンテナーを作成します。

   ![Microsoft Azure portal を利用し、BLOB としてパッケージをアップロードする](media/CRMScreenShot9.png)

6. まだインストールしていない場合は、Microsoft [Azure ストレージ エクスプローラー](https://storageexplorer.com/)をダウンロードしてインストールします。

7. ストレージ エクスプローラーを起動し、アイコンを使用して Azure ストレージ アカウントに接続します。

8. 作成した BLOB コンテナーに移動し、**[アップロード]** をクリックしてパッケージ zip ファイルを追加します。

   ![Microsoft ストレージ エクスプローラーを使用してパッケージをアップロードする](media/CRMScreenShot10.png)

9. ファイルを右クリックし、**[Get Shared Access Signature]\(Shared Access Signature の取得\)** を選択します。

   ![Azure ファイルの Shared Access Signature を取得する](media/CRMScreenShot11.png)

10. **[有効期限]** を修正して SAS を 1 か月間有効にし、**[作成]** をクリックします。

    ![Azure ファイルの SAS 有効期限を修正する](media/CRMScreenShot12.png)

11. URL フィールドをコピーし、後のために保存します。 関連するプランを作成するとき、この URL を入力する必要があります。 

    ![Azure ファイルの SAS URL をコピーする](media/CRMScreenShot13.png)

