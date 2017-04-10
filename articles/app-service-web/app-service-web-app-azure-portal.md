---
title: "Azure ポータル内の移動に関するリファレンス"
description: "App Service Web のユーザー エクスペリエンスについて、管理ポータルと Azure ポータルの違いを説明します"
services: app-service
documentationcenter: 
author: jaime-espinosa
manager: erikre
editor: jimbe
ms.assetid: 0cc6a3cc-bd89-4a96-9177-d25f6fb737bb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2016
ms.author: jaime-espinosa
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 00617f4d508c843000d69c02464a1bef36320a8e
ms.lasthandoff: 01/20/2017


---
# <a name="reference-for-navigating-the-azure-portal"></a>Azure ポータル内の移動に関するリファレンス
Azure Websites は、現在は [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714)と呼ばれています。 この名前の変更を反映し、Azure ポータルの説明も記載するために、すべてのドキュメントの更新が進められています。 この更新プロセスが完了するまでは、Azure ポータルでの Web Apps の操作ガイドとして、このドキュメントを使用できます。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="the-future-of-the-azure-classic-portal"></a>Azure クラシック ポータルの今後について
Azure クラシック ポータルでブランド変更が行われる一方で、クラシック ポータルを Azure ポータルに置き換えるプロセスが進められています。 クラシック ポータルが廃止されるので、新規開発の焦点は Azure ポータルに移行しつつあります。 Web Apps の今後の新機能はすべて Azure ポータルで提供されます。 Web Apps が提供する優れた最新機能を活用するために、Azure ポータルを使い始めてください。

## <a name="layout-differences-between-the-azure-classic-portal-and-azure-portal"></a>Azure クラシック ポータルと Azure ポータルのレイアウトの違い
クラシック ポータルでは、すべての Azure サービスが左側に一覧表示されています。 クラシック ポータルでの移動はツリー構造に従っており、サービスから各要素へと移動します。 この構造は、独立したコンポーネントを管理する場合には適しています。 しかし、Azure で構築されるアプリケーションは相互に接続されたサービスの集合体であり、このツリー構造は、サービスの集合体を操作するうえで最適な構造ではありません。 

Azure ポータルでは、複数のサービスのコンポーネントを使用して、アプリケーションをエンド ツー エンドで簡単に構築できます。 Azure ポータルは、 *ジャーニー*として構造化されています。 *ジャーニー*とは、さまざまなコンポーネントのコンテナーである*ブレード*が連なったものです。 たとえば、Web アプリに対する自動スケールの設定は*ジャーニー*であり、これによって次の例に示すような、**[Web サイト]** ブレード (ブレード タイトルは、まだ新しい用語を使用するように更新されていません)、**[設定]** ブレード、**[スケール アウト]** ブレードなど、複数のブレードが表示されます。 この例では、CPU の使用状況に応じた自動スケーリングが設定されているので、 **[CPU の割合]** ブレードも同様に表示されています。 *ブレード*内のコンポーネントは*パート*と呼ばれており、タイルに似ています。 

![](./media/app-service-web-app-azure-portal/AutoScaling.png)

## <a name="navigation-example-create-a-web-app"></a>移動の例: Web アプリを作成する
新しい Web アプリの作成方法は、以前と同じように簡単です。 次の画像は、Web アプリを動作させるために必要な手順の数がそれほど変わっていないことを示すために、クラシック ポータルとポータルを並べて表示しています。 

![](./media/app-service-web-app-azure-portal/CreateWebApp.png)

ポータルでは、WordPress のような人気のギャラリー アプリケーションなど、最も一般的な種類の Web アプリから選択することができます。 利用できるすべてのアプリケーションの一覧については、 [Azure Marketplace]を参照してください。

Web アプリを作成するときは、クラシック ポータルの場合と同様に、ポータルで URL、App Service プラン、場所を指定します。 

![](./media/app-service-web-app-azure-portal/CreateWebAppSettings.png)

さらに、ポータルでは、その他の共通設定を定義できます。 たとえば、[リソース グループ](../azure-resource-manager/resource-group-overview.md)は、関連する Azure リソースの表示と管理を単純化します。 

## <a name="navigation-example-settings-and-features"></a>移動の例: 設定と機能
すべての設定と機能は、今では&1; つのブレードへと合理的にグループ化されており、そこから移動を開始することができます。

![](./media/app-service-web-app-azure-portal/WebAppSettings.png)

たとえば、**[設定]** ブレードの **[カスタム ドメインと SSL]** をクリックすると、カスタム ドメインを作成できます。

![](./media/app-service-web-app-azure-portal/ConfigureWebApp.png)

監視のアラートを設定するには、**[要求とエラー]**、**[アラートの追加]** の順にクリックします。

![](./media/app-service-web-app-azure-portal/Monitoring.png)

診断を有効にするには、**[設定]** ブレードの **[診断ログ]** をクリックします。

![](./media/app-service-web-app-azure-portal/Diagnostics.png)

アプリケーション設定を構成するには、**[設定]** ブレードの **[アプリケーション設定]** をクリックします。 

![](./media/app-service-web-app-azure-portal/AppSettingsPreview.png)

ブランド名のほかにも、ポータル内のいくつかの要素が、見つけやすくなるように名前を変更されたり、異なるグループに分けられたりしています。 たとえば、以下のスクリーンショットは、クラシック ポータルのアプリケーション設定 (**構成**) に対応するページです。

![](./media/app-service-web-app-azure-portal/AppSettings.png)

## <a name="more-resources"></a>その他のリソース
[Azure Portal]: https://portal.azure.com
[Azure Marketplace]: /marketplace/

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://azure.microsoft.com/try/app-service/)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

## <a name="whats-changed"></a>変更内容
* Websites から App Service への変更ガイドについては、「 [Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)


