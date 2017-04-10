---
title: "Azure ポータルを使用した Web アプリの複製"
description: "Azure ポータルを使用して既存の Web アプリを新しい Web アプリに複製する方法を説明します。"
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: 20b0ae4e-67e8-4bae-9d74-8a24dc445cce
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 6b77e338e1c7f0f79ea3c25b0b073296f7de0dcf
ms.openlocfilehash: 9ebfa91c7972ab3c264032ead8376c23c1197a4b


---
# <a name="azure-app-service-app-cloning-using-azure-portal"></a>Azure ポータルを使用した Azure App Service アプリの複製
[Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) の複製機能を使用すると、既存の Web アプリを複製して、別のリージョンまたは同じリージョンにアプリを新規作成することができます。 これにより、お客様は、リージョンをまたいでさまざまなアプリを迅速かつ簡単にデプロイできるようになります。

アプリの複製は、現在、Premium レベルの App Service プランでのみサポートされています。 この新機能には Web アプリのバックアップ機能と同じ制限が適用されます。[Azure App Service での Web アプリのバックアップ](web-sites-backup.md)に関するページを参照してください。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="cloning-an-existing-app"></a>既存のアプリの複製
Web アプリの複製を作成するには、その Web アプリが **Premium** モードで実行されている必要があります。

1. [Azure ポータル](https://portal.azure.com/)の Web アプリのブレードを開きます。
2. **[ツール]**をクリックします。 次に、**[ツール]** ブレードで **[アプリの複製]** をクリックします。
   
    ![][1]
   
   > [!NOTE]
   > Web アプリがまだ **Premium** モードでない場合は、アプリの複製がサポートされているモードを示すメッセージが表示されます。 この時点で、 **[アップグレード]**を選択することもできます。
   > 
   > 
3. **[アプリの複製]** ブレードで、新しい Web アプリの名前、リソース グループ、App Service プランを入力します。 また、複数のソース Web アプリの設定を複製するかどうかも選択できます。
   
    ![][2]
4. **[作成]** をクリックすると、ソース Web アプリの複製の作成が開始されます。

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>App Service 環境への既存のアプリの複製
**[アプリの複製]** ブレードでは、既存の App Service 環境のアプリ プールを選択することもできます。

## <a name="current-restrictions"></a>現在の制限
この機能は現在プレビュー段階にあり、新機能を追加する作業が続けられています。次の一覧に、Azure ポータルでの現在のアプリの複製のサポートに関する既知の制限を示します。

* Azure Traffic Manager の設定は複製されない
* 自動スケールの設定は複製されない
* バックアップ スケジュールの設定は複製されない
* VNET の設定は複製されない
* App Insights は複製先の Web アプリで自動的にセットアップされない
* 簡単認証の設定は複製されない
* Kudu 拡張機能は複製されない
* TiP ルールは複製されない
* データベースの内容は複製されない

### <a name="references"></a>参照
* [PowerShell を使用した Web アプリの複製](app-service-web-app-cloning.md)
* [Azure App Service での Web アプリのバックアップ](web-sites-backup.md)
* [App Service 環境の作成方法](app-service-web-how-to-create-an-app-service-environment.md)
* [App Service 環境で Web アプリを作成する](app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [App Service 環境の概要](app-service-app-service-environment-intro.md)

<!--Image references-->
[1]: ./media/app-service-web-app-cloning-portal/CloningBlade.png
[2]: ./media/app-service-web-app-cloning-portal/CloneSettings.png



<!--HONumber=Dec16_HO2-->


