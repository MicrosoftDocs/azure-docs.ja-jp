---
title: "Azure Stack ユーザーが仮想マシンを使用できるようにする | Microsoft Docs"
description: "Azure Stack ユーザーが仮想マシンを使用できるようにするためのチュートリアル"
services: azure-stack
documentationcenter: 
author: vhorne
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 9/25/2017
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: afeec92c40262903e6cfd3c6d75a595fead616e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="make-virtual-machines-available-to-your-azure-stack-users"></a>Azure Stack ユーザーが仮想マシンを使用できるようにする

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack のクラウド管理者は、ユーザー (テナントとも呼ばれます) がサブスクライブできるオファーを作成できます。 ユーザーはサブスクリプションを使用して Azure Stack サービスを利用できます。

この記事では、オファーを作成し、それをテストする方法について説明します。 テストの場合は、ユーザーとしてポータルにログインし、オファーにサブスクライブしてから、そのサブスクリプションを使用して仮想マシンを作成します。

学習内容

> [!div class="checklist"]
> * オファーの作成
> * イメージの追加
> * オファーのテスト


Azure Stack では、サブスクリプション、オファー、プランを使用してサービスがユーザーに配信されます。 ユーザーは複数のオファーにサブスクライブできます。 オファーは 1 つまたは複数のプランを含むことができ、プランは 1 つまたは複数のサービスを含むことができます。

![サブスクリプション、オファー、およびプラン](media/azure-stack-key-features/image4.png)

詳細については、「[Azure Stack の主要な機能と概念](azure-stack-key-features.md)」を参照してください。

## <a name="create-an-offer"></a>オファーの作成

これでユーザーのための準備を始められます。 プロセスを開始すると、最初にオファーを、次にプランを、最後にクォータを作成するように指示されます。

3. **オファーの作成**

   オファーは、プロバイダーがユーザーに提示する 1 つまたは複数のプランのグループで、ユーザーはそれを購入またはサブスクライブします。

   a. ポータルにクラウドの管理者として[サインイン](azure-stack-connect-azure-stack.md)してから、**[新規]**  >  **[テナントのプラン]**  >  **[オファー]** とクリックします。
   ![新しいプラン](media/azure-stack-tutorial-tenant-vm/image01.png)

   b. **[新しいプラン]** セクションの **[表示名]** と **[リソース名]** に入力し、新規または既存の **[リソース グループ]** を選択します。 表示名は、オファーのフレンドリ名です。 リソース名は、クラウド オペレーターのみが表示できます。 この名前を使用して、管理者はオファーを Azure リソース マネージャーのリソースとして操作します。

   ![表示名](media/azure-stack-tutorial-tenant-vm/image02.png)

   c. **[基本プラン]** をクリックし、**[プラン]** セクションで **[追加]** をクリックして新しいプランをオファーに追加します。

   ![プランの追加](media/azure-stack-tutorial-tenant-vm/image03.png)

   d. **[新しいプラン]** セクションで **[表示名]** と **[リソース名]** を入力します。 表示名は、ユーザーに表示されるプランのフレンドリ名です。 リソース名は、クラウド オペレーターのみが表示できます。 クラウド オペレーターはこの名前を使用して、プランを Azure Resource Manager のリソースとして操作します。

   ![プランの表示名](media/azure-stack-tutorial-tenant-vm/image04.png)

   e. **[サービス]** をクリックし、**[Microsoft.Compute]**、**[Microsoft.Network]**、および **[Microsoft.Storage]** を選択し、**[選択]** をクリックします。

   ![プランのサービス](media/azure-stack-tutorial-tenant-vm/image05.png)

   f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[クォータ]** をクリックしてから、クォータを作成する最初のサービスを選択します。 IaaS クォータの場合は、以下に示す、コンピューティング サービス、ネットワーク サービス、およびストレージ サービスの手順に従います。

   この例では、最初に、コンピューティング サービスのクォータを作成します。 名前空間の一覧で、**[Microsoft.Compute]** 名前空間を選択し、**[新しいクォータの作成]** をクリックします。
   
   ![新しいクォータの作成](media/azure-stack-tutorial-tenant-vm/image06.png)

   g. **[Create quota]** (クォータの作成) セクションで、クォータの名前を入力し、そのクォータに必要なパラメーターを設定して **[OK]** をクリックします。

   ![クォータ名](media/azure-stack-tutorial-tenant-vm/image07.png)

   h. ここで、**Microsoft.Compute** のために、作成したクォータを選択します。

   ![クォータの選択](media/azure-stack-tutorial-tenant-vm/image08.png)

   これらの手順をネットワーク サービスとストレージ サービスについて繰り返し、**[クォータ]** セクションの **[OK]** をクリックします。

   i. **[New plan]** (新しいプラン) セクションの **[OK]** をクリックします。

   j. **[プラン]** セクションで新しいプランを選択し、**[選択]** をクリックします。

   k. **[New offer]** (新しいオファー) セクションで **[作成]** をクリックします。 オファーが作成されると通知が表示されます。

   l. ダッシュ ボード メニューの **[Offer]** (オファー) をクリックしてから、作成したオファーをクリックします。

   m. **[状態の変更]** をクリックし、**[パブリック]** をクリックします。

   ![[パブリック] の状態](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>イメージの追加

仮想マシンをプロビジョニングする前に、Azure Stack Marketplace にイメージを追加する必要があります。 Azure Marketplace の Linux イメージを含め、任意のイメージを追加できます。

接続されるシナリオで運用していて、Azure に Azure Stack インスタンスを登録済みの場合は、「[Azure から Azure Stack に Marketplace の項目をダウンロードする](azure-stack-download-azure-marketplace-item.md)」というトピックに記載されている手順を使用して、Azure Marketplace から Windows Server 2016 の VM イメージをダウンロードできます。

Marketplace にさまざまな項目を追加する方法については、「[Azure Stack Marketplace](azure-stack-marketplace.md)」を参照してください。

## <a name="test-the-offer"></a>オファーのテスト

オファーを作成したので、そのテストを行うことができます。 ユーザーとしてログインしてオファーにサブスクライブし、仮想マシンを追加します。

1. **プランへのサブスクライブ**

   これで、オファーにサブスクライブするユーザーとしてポータルにログインできます。

   a. ユーザーとしてユーザー ポータルにログインし、**[サブスクリプションの取得]** をクリックします。
   - 統合システムの場合、URL はオペレーターのリージョンと外部ドメイン名によって異なり、https://portal.&lt;*リージョン*&gt;.&lt;*FQDN*&gt; の形式になります。
   - Azure Stack 開発キットを使用している場合、ポータルのアドレスは https://portal.local.azurestack.external です。

   ![サブスクリプションの取得](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   b. **[表示名]** フィールドにサブスクリプションの名前を入力し、**[Offer]** (オファー) をクリックして、**[Choose an offer]** (オファーの選択) セクションでいずれかのオファーをクリックしたら、**[作成]** をクリックします。

   ![オファーの作成](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   c. 作成したサブスクリプションを表示するには、**[その他のサービス]**、**[サブスクリプション]** の順にクリックし、作成した新しいサブスクリプションをクリックします。  

   オファーをサブスクライブしたら、ポータルを更新して、どのサービスが新しいサブスクリプションの一部であるかを確認します。

2. **仮想マシンのプロビジョニング**

   これで、ユーザーとしてポータルにログインし、サブスクリプションを使用して仮想マシンをプロビジョニングすることができます。 

   a. ユーザーとしてユーザー ポータルにログインし、**[新規]** > **[コンピューティング]** > **[Windows Server 2016 Datacenter Eval]** をクリックします。
      - 統合システムの場合、URL はオペレーターのリージョンと外部ドメイン名によって異なり、https://portal.&lt;*リージョン*&gt;.&lt;*FQDN*&gt; の形式になります。
   - Azure Stack 開発キットを使用している場合、ポータルのアドレスは https://portal.local.azurestack.external です。

   b. **[基本]** セクションで、**[名前]**、**[ユーザー名]**、**[パスワード]** を入力します。 **[VM ディスクの種類]** には、**[HDD]** を選択します。 **[サブスクリプション]**を選択します。 **リソース グループ**を作成するか、既存のリソース グループを選択して、**[OK]** をクリックします。  

   c. **[サイズの選択]** セクションで、**[A1 Basic]** (A1 基本) をクリックしてから **[選択]** をクリックします。  

   d. **[設定]** セクションで、**[仮想ネットワーク]** をクリックします。 **[仮想ネットワークの選択]** セクションで **[新規作成]** をクリックします。 **[Create virtual network]** (仮想ネットワークの作成) セクションで、すべての既定値をそのまま使用し、**[OK]** をクリックします。 **[設定]** セクションで **[OK]** をクリックします。

   ![Create virtual network](media/azure-stack-provision-vm/image04.png)

   e. **[概要]** セクションで、**[OK]** をクリックして仮想マシンを作成します。  

   f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 新しい仮想マシンを表示するには、**[すべてのリソース]** をクリックして仮想マシンを検索し、仮想マシン名をクリックします。

    ![すべてのリソース](media/azure-stack-provision-vm/image06.png)

このチュートリアルで学習した内容:

> [!div class="checklist"]
> * オファーの作成
> * イメージの追加
> * オファーのテスト

> [!div class="nextstepaction"]
> [Make web, mobile, and API apps available to your Azure Stack users (Azure Stack ユーザーが Web Apps、Mobile Apps、および API Apps を使用できるようにする)](azure-stack-tutorial-app-service.md)