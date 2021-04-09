---
title: Azure に SAP ERP 6.0 向け SAP IDES EHP7 SP3 をデプロイする | Microsoft Docs
description: Azure に SAP ERP 6.0 向け SAP IDES EHP7 SP3 をデプロイする
author: hermanndms
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 09/16/2016
ms.author: hermannd
ms.openlocfilehash: 5184df38b6a6b2757a5d5f7c0970258657cf5c0f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671001"
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Azure に SAP ERP 6.0 向け SAP IDES EHP7 SP3 をデプロイする
この記事では、SAP Cloud Appliance Library (SAP CAL) 3.0 を使用して、SQL Server および Windows オペレーティング システムで実行されている SAP IDES システムを Azure にデプロイする方法について説明します。 手順をスクリーンショットに示します。 別のソリューションをデプロイするには、同じ手順に従います。

SAP CAL を開始するには、[SAP Cloud Appliance Library](https://cal.sap.com/) の Web サイトにアクセスします。 SAP には新しい [SAP Cloud Appliance Library 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience) に関するブログも用意されています。 

> [!NOTE]
> 2017 年 5 月 29 日より、SAP CAL のデプロイに、クラシック デプロイ モデルに加えて Azure Resource Manager デプロイ モデルを使用できるようになりました。 クラシック デプロイ モデルの代わりに新しい Resource Manager デプロイ モデルを使用することをお勧めします。

クラシック モデルを使用する SAP CAL アカウントを既に作成している場合は、"*別の SAP CAL アカウントを作成する必要があります*"。 このアカウントは、Resource Manager モデルを使用して Azure に排他的にデプロイする必要があります。

SAP CAL にサインインすると、通常は **[Solutions]\(ソリューション\)** ページが最初のページになります。 SAP CAL で提供されているソリューションは増え続けているため、目的のソリューションを見つけるのにスクロールが必要な場合があります。 Azure で排他的に利用できる強調表示された Windows ベースの SAP IDES ソリューションがデプロイ プロセスを示します。

![SAP CAL のソリューション](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>SAP CAL にアカウントを作成する
1. 初めて SAP CAL にサインインするには、SAP S-User または SAP に登録された他のユーザーを使用します。 次に、SAP CAL で使用される SAP CAL アカウントを定義し、アプライアンスを Azure にデプロイします。 アカウントの定義では、次を行う必要があります。

    a. Azure のデプロイメント モデルを選択します (Resource Manager またはクラシック)。

    b. Azure サブスクリプションを入力します。 SAP CAL アカウントは 1 つのサブスクリプションにのみ割り当てることができます。 複数のサブスクリプションが必要な場合は、別の SAP CAL アカウントを作成する必要があります。
    
    c. Azure サブスクリプションにデプロイするために、SAP CAL にアクセス許可を付与します。

   > [!NOTE]
   >  次の手順では、Resource Manager デプロイメント用の SAP CAL アカウントを作成する方法を示します。 クラシック デプロイ モデルにリンクされている SAP CAL アカウントが既にある場合は、次の手順に従って新しい SAP CAL アカウントを作成する "*必要があります*"。 新しい SAP CAL アカウントは、Resource Manager モデルにデプロイする必要があります。

1. 新しい SAP CAL アカウントを作成する方法として、 **[Accounts]\(アカウント\)** ページに次の 2 つの選択肢が用意されています。 

    a. **[Microsoft Azure (classic)]\(Microsoft Azure (クラシック)\)** はクラシック デプロイ モデルで、推奨されなくなりました。

    b. **[Microsoft Azure]** は新しい Resource Manager デプロイ モデルです。

    ![スクリーンショットには、Microsoft Azure が強調表示された S A P CAL アカウントが示されています。](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Resource Manager モデルでデプロイするには、 **[Microsoft Azure]** を選択します。

    ![スクリーンショットには、S A P CAL アカウントが示されています。](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Azure Portal で見つかった Azure の **サブスクリプション ID** を入力します。 

    ![SAP CAL のサブスクリプション ID](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. SAP CAL を承認し、定義した Azure サブスクリプションにデプロイするには、 **[Authorize]\(承認\)** をクリックします。 次のページがブラウザー タブに表示されます。

    ![Internet Explorer のクラウド サービスのサインイン](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

1. 複数のユーザーが列挙されている場合は、選択した Azure サブスクリプションの共同管理者としてリンクされている Microsoft アカウントを選択します。 次のページがブラウザー タブに表示されます。

    ![Internet Explorer のクラウド サービスの確認](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

1. **[Accept]\(受け入れる\)** をクリックします。 承認が成功した場合は、SAP CAL アカウントの定義が再び表示されます。 短い時間が経過すると、承認プロセスが成功したことを示すメッセージが表示されます。

1. ユーザーに新しく作成した SAP CAL アカウントを割り当てるには、右のテキスト ボックスに **ユーザー ID** を入力し、 **[Add]\(追加\)** をクリックします。 

    ![アカウントからユーザーへの関連付け](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

1. アカウントを SAP CAL へのサインインに使用するユーザーに関連付けるには、 **[Review]\(レビュー\)** をクリックします。 

1. ユーザーと新しく作成した SAP CAL アカウントの間の関連付けを作成するには、 **[Create]\(作成\)** をクリックします。

    ![ユーザーからアカウントへの関連付け](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

正しく作成した SAP CAL アカウントでは、次のことが可能です。

- Resource Manager デプロイ モデルを使用する。
- SAP システムを Azure サブスクリプションにデプロイする。

> [!NOTE]
> Windows や SQL Server に基づく SAP IDES ソリューションをデプロイする前に、SAP CAL サブスクリプションにサインアップする必要がある場合があります。 サインアップしないと、概要ページでソリューションが **[ロック]\(Locked\)** と表示される場合があります。

### <a name="deploy-a-solution"></a>ソリューションのデプロイ
1. SAP CAL アカウントをセットアップしたら、 **[The SAP IDES solution on Windows and SQL Server]\(Windows および SQL Server 上の SAP IDES ソリューション\)** ソリューションを選択します。 **[Create Instance]\(インスタンスの作成\)** をクリックし、使用状況と使用条件を確認します。 

1. **[Basic Mode: Create Instance]\(基本モード: インスタンスの作成\)** ページでは、次のことを行う必要があります。

    a. インスタンスの **名前** を入力します。

    b. Azure の **リージョン** を選択します。 複数の Azure リージョンが提供されるには、SAP CAL のサブスクリプションが必要な場合があります。

    c.  ソリューションのマスター **パスワード** を、次のように入力します。

    ![SAP CAL の基本モード: インスタンスの作成](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

1. **Create** をクリックしてください。 ソリューションのサイズと複雑さに応じた一定の時間が経過すると (推定時間が SAP CAL によって示されます)、状態がアクティブであることが示され、使用する準備が整います。 

    ![SAP CAL のインスタンス](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

1. SAP CAL によって作成されたリソース グループおよびそのすべてのオブジェクトを探すには、Azure Portal にアクセスします。 SAP CAL で指定したのと同じインスタンス名で始まる仮想マシンがあります。

    ![リソース グループ オブジェクト](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

1. SAP CAL ポータルで、デプロイ済みのインスタンスにアクセスし、 **[Connect]\(接続\)** をクリックします。 次のポップアップ ウィンドウが表示されます。 

    ![インスタンスに接続する](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

1. いずれかのオプションを使用してデプロイ済みのシステムに接続する前に、 **[Getting Started Guide]\(ファースト ステップ ガイド\)** をクリックします。 ドキュメントによって各接続メソッドのユーザーが命名されます。 これらのユーザーのパスワードはデプロイ プロセスの最初に定義したマスター パスワードに設定されます。 ドキュメントにはより高機能なユーザーがパスワードとともに列挙されており、これを使用してデプロイ済みのシステムにサインインできます。

    ![SAP のようこそドキュメント](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

数時間のうちに、正常な SAP IDES システムが Azure にデプロイされます。

SAP CAP サブスクリプションを購入した場合、SAP が SAP CAL を介した Azure へのデプロイを全面的にサポートします。 サポート キューは BC-VCM-CAL です。

