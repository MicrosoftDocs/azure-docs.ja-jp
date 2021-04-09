---
title: Azure VM に SAP S/4HANA または BW/4HANA をデプロイする | Microsoft Docs
description: Azure VM に SAP S/4HANA または BW/4HANA をデプロイします
author: hermanndms
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 09/15/2016
ms.author: hermannd
ms.reviewer: cynthn
ms.openlocfilehash: 4b739efcf2695a5385351f531efb6201bbea1cd5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671938"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>Azure に SAP S/4HANA または BW/4HANA をデプロイする
この記事では、SAP Cloud Appliance Library (SAP CAL) 3.0 を使用して Azure に S/4HANA をデプロイする方法について説明します。 BW/4HANA などの他の SAP HANA ベースのソリューションをデプロイする手順は同じです。

> [!NOTE]
> SAP CAL について詳しくは、[SAP Cloud Appliance Library](https://cal.sap.com/) の Web サイトにアクセスします。 SAP には [SAP Cloud Appliance Library 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience) に関するブログも用意されています。
> 
> [!NOTE]
> 2017 年 5 月 29 日より、SAP CAL のデプロイに、クラシック デプロイ モデルに加えて Azure Resource Manager デプロイ モデルを使用できるようになりました。 クラシック デプロイ モデルの代わりに新しい Resource Manager デプロイ モデルを使用することをお勧めします。

## <a name="step-by-step-process-to-deploy-the-solution"></a>ソリューションを配置するための段階的なプロセス

次の一連のスクリーンショットは、SAP CAL を使用して Azure に S/4HANA をデプロイする方法を示します。 BW/4HANA などの他のソリューションでもプロセスは同じです。

**[Solutions]\(ソリューション\)** ページには、Azure で使用できる SAP CAL HANA ベースのソリューションの一部が掲載されています。 **SAP S/4HANA 1610 FPS01, Fully-Activated Appliance** は中央の行にあります。

![SAP CAL のソリューション](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>SAP CAL にアカウントを作成する
1. 初めて SAP CAL にサインインするには、SAP S-User または SAP に登録された他のユーザーを使用します。 次に、SAP CAL で使用される SAP CAL アカウントを定義し、アプライアンスを Azure にデプロイします。 アカウントの定義では、次を行う必要があります。

    a. Azure のデプロイメント モデルを選択します (Resource Manager またはクラシック)。

    b. Azure サブスクリプションを入力します。 SAP CAL アカウントは 1 つのサブスクリプションにのみ割り当てることができます。 複数のサブスクリプションが必要な場合は、別の SAP CAL アカウントを作成する必要があります。

    c. Azure サブスクリプションにデプロイするために、SAP CAL にアクセス許可を付与します。

   > [!NOTE]
   >  次の手順では、Resource Manager デプロイメント用の SAP CAL アカウントを作成する方法を示します。 クラシック デプロイ モデルにリンクされている SAP CAL アカウントが既にある場合は、次の手順に従って新しい SAP CAL アカウントを作成する "*必要があります*"。 新しい SAP CAL アカウントは、Resource Manager モデルにデプロイする必要があります。

1. 新しい SAP CAL アカウントを作成します。 **[Accounts]\(アカウント\)** ページには、Azure の 3 つの選択肢が用意されています。 

    a. **[Microsoft Azure (classic)]\(Microsoft Azure (クラシック)\)** はクラシック デプロイ モデルで、推奨されなくなりました。

    b. **[Microsoft Azure]** は新しい Resource Manager デプロイ モデルです。

    c. **[Windows Azure operated by 21Vianet]\(21Vianet が運用する Windows Azure\)** は中国向けのオプションで、クラシック デプロイ モデルを使用します。

    Resource Manager モデルでデプロイするには、 **[Microsoft Azure]** を選択します。

    ![SAP CAL アカウントの詳細](./media/cal-s4h/s4h-pic-2a.png)

1. Azure Portal で見つかった Azure の **サブスクリプション ID** を入力します。

   ![SAP CAL アカウント](./media/cal-s4h/s4h-pic3c.png)

1. SAP CAL を承認し、定義した Azure サブスクリプションにデプロイするには、 **[Authorize]\(承認\)** をクリックします。 次のページがブラウザー タブに表示されます。

   ![Internet Explorer のクラウド サービスのサインイン](./media/cal-s4h/s4h-pic4c.png)

1. 複数のユーザーが列挙されている場合は、選択した Azure サブスクリプションの共同管理者としてリンクされている Microsoft アカウントを選択します。 次のページがブラウザー タブに表示されます。

   ![Internet Explorer のクラウド サービスの確認](./media/cal-s4h/s4h-pic5a.png)

1. **[Accept]\(受け入れる\)** をクリックします。 承認が成功した場合は、SAP CAL アカウントの定義が再び表示されます。 短い時間が経過すると、承認プロセスが成功したことを示すメッセージが表示されます。

1. ユーザーに新しく作成した SAP CAL アカウントを割り当てるには、右のテキスト ボックスに **ユーザー ID** を入力し、 **[Add]\(追加\)** をクリックします。

   ![アカウントからユーザーへの関連付け](./media/cal-s4h/s4h-pic8a.png)

1. アカウントを SAP CAL へのサインインに使用するユーザーに関連付けるには、 **[Review]\(レビュー\)** をクリックします。 
 
1. ユーザーと新しく作成した SAP CAL アカウントの間の関連付けを作成するには、 **[Create]\(作成\)** をクリックします。

   ![ユーザーから SAP CAL アカウントへの関連付け](./media/cal-s4h/s4h-pic9b.png)

正しく作成した SAP CAL アカウントでは、次のことが可能です。

- Resource Manager デプロイ モデルを使用する。
- SAP システムを Azure サブスクリプションにデプロイする。

これで Azure のユーザー サブスクリプションに S/4HANA のデプロイを開始できます。

> [!NOTE]
> 続行する前に、Azure H シリーズの VM 用の Azure の vCPU クォータがあるかどうかを判定します。 現時点では、SAP CAL は Azure H シリーズの VM を使用して、SAP HANA ベースの一部のソリューションをデプロイします。 お使いの Azure サブスクリプションには H シリーズ用の H シリーズ vCPU クォータがない可能性があります。 ない場合は、Azure サポートに問い合わせて 16 H シリーズ vCPU 以上のクォータを入手する必要がある場合があります。
> 
> [!NOTE]
> SAP CAL で Azure にソリューションをデプロイするときに、1 つの Azure リージョンのみを選択できることがわかる場合があります。 SAP CAL で提案されたリージョン以外の Azure リージョンにデプロイするには、SAP から CAL のサブスクリプションを購入する必要があります。 また、最初に提案された Azure リージョン以外に送るには、SAP でメッセージを開き、CAL アカウントを有効にする必要がある場合があります。

### <a name="deploy-a-solution"></a>ソリューションのデプロイ

SAP CAL の **[Solutions]\(ソリューション\)** ページからソリューションをデプロイしてみましょう。 SAP CAL では、次の 2 つのシーケンスでデプロイできます。

- 1 ページでデプロイされるシステムを定義する基本シーケンス
- VM のサイズをある程度選択できる高度なシーケンス 

ここでは、基本のパスを使用してデプロイします。

1. **[Account Details]\(アカウントの詳細\)** ページで、次のことを行う必要があります。

    a. SAP CAL アカウントを選択します (Resource Manager デプロイ モデルを使用したデプロイに関連付けられているアカウントを使用します)。

    b. インスタンスの **名前** を入力します。

    c. Azure の **リージョン** を選択します。 SAP CAL がリージョンを提案します。 別の Azure リージョンを使用する必要があるものの、SAP CAL のサブスクリプションがない場合は、SAP で CAL のサブスクリプションを注文する必要があります。

    d. ソリューションのマスター **パスワード** を 8 ～ 9 文字で入力します。 パスワードは別のコンポーネントの管理者に対して使用されます。

   ![SAP CAL の [Basic Mode: Create Instance]\(基本モード: インスタンスの作成\)](./media/cal-s4h/s4h-pic10a.png)

1. **[Create]\(作成\)** をクリックし、表示されたメッセージ ボックスで **[OK]** をクリックします。

   ![SAP CAL でサポートされる VM のサイズ](./media/cal-s4h/s4h-pic10b.png)

1. **[Private Key]\(秘密キー\)** ダイアログ ボックスで、 **[Store]\(格納\)** をクリックしてSAP CAL に秘密キーを格納します。 秘密キーに対してパスワード保護を使用するには、 **[Download]\(ダウンロード\)** をクリックします。 

   ![SAP CAL の秘密キー](./media/cal-s4h/s4h-pic10c.png)

1. SAP CAL の **警告** メッセージに目を通し、 **[OK]** をクリックします。

   ![SAP CAL の警告](./media/cal-s4h/s4h-pic10d.png)

    これで、デプロイが行われます。 ソリューションのサイズと複雑さに応じた一定の時間が経過すると (推定時間が SAP CAL によって示されます)、状態がアクティブであることが示され、使用する準備が整います。

1. あるリソース グループの他の関連リソースで収集された仮想マシンを見つけるには、Azure Portal にアクセスします。 

   ![新しいポータルにデプロイされている SAP CAL オブジェクト](./media/cal-s4h/sapcaldeplyment_portalview.png)

1. SAP CAL ポータルで、状態が **[Active]\(アクティブ\)** として表示されます。 ソリューションに接続するには、 **[Connect]\(接続\)** をクリックします。 別のコンポーネントに接続するための別のオプションはこのソリューション内にデプロイされています。

   ![SAP CAL のインスタンス](./media/cal-s4h/active_solution.png)

1. いずれかのオプションを使用してデプロイ済みのシステムに接続する前に、 **[Getting Started Guide]\(ファースト ステップ ガイド\)** をクリックします。 

   ![インスタンスに接続する](./media/cal-s4h/connect_to_solution.png)

    ドキュメントによって各接続メソッドのユーザーが命名されます。 これらのユーザーのパスワードはデプロイ プロセスの最初に定義したマスター パスワードに設定されます。 ドキュメントにはより高機能なユーザーがパスワードとともに列挙されており、これを使用してデプロイ済みのシステムにサインインできます。 

    たとえば、Windows リモート デスクトップ マシンにプレインストールされている SAP GUI を使用している場合、S/4 システムは次のように表示されることがあります。

   ![プレインストールされた SAP GUI の SM50](./media/cal-s4h/gui_sm50.png)

    また、DBACockpit を使用している場合、インスタンスは次のように表示されることがあります。

   ![DBACockpit SAP GUI の SM50](./media/cal-s4h/dbacockpit.png)

数時間のうちに、正常な SAP S/4 アプライアンスが Azure にデプロイされます。

SAP CAP サブスクリプションを購入した場合、SAP が SAP CAL を介した Azure へのデプロイを全面的にサポートします。 サポート キューは BC-VCM-CAL です。







