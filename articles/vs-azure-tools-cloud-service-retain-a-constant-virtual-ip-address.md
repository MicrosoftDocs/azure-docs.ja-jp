---
title: "Azure クラウド サービスの固定仮想 IP アドレスを保持する方法 | Microsoft Docs"
description: "Azure クラウド サービスの仮想 IP アドレス (VIP) が変化しないようにする方法について説明します。"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 4a58e2c6-7a79-4051-8a2c-99182ff8b881
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/21/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 12d73e2f490acc92bb4a2e1014bb24f7f672f713
ms.lasthandoff: 03/22/2017


---
# <a name="how-to-retain-a-constant-virtual-ip-address-for-an-azure-cloud-service"></a>Azure クラウド サービスの固定仮想 IP アドレスを保持する方法
Azure でホストされているクラウド サービスを更新するとき、サービスの仮想 IP アドレス (VIP) が変更されないようにしなければならない場合があります。 ドメイン管理サービスの多くは、ドメイン ネーム システム (DNS) を使用してドメイン名の登録を行います。 DNS が正しく機能するためには、VIP が不変であることが必要です。 Azure ツールの **公開ウィザード** を使用すると、クラウド サービスを更新するときに、その VIP が変更されないようにすることができます。 Cloud Services で DNS ドメイン管理を使用する方法の詳細については、「[Azure クラウド サービスのカスタム ドメイン名の構成](cloud-services/cloud-services-custom-domain-name.md)」を参照してください。

## <a name="publishing-a-cloud-service-without-changing-its-vip"></a>VIP を変更せずにクラウド サービスを発行する
運用環境など特定の環境で初めてクラウド サービスを Azure にデプロイするとき、クラウド サービスの VIP が割り当てられます。 この VIP が変更されるのは、デプロイを明示的に削除した場合またはデプロイの更新プロセスによってデプロイが暗黙的に削除された場合だけです。 VIP を維持するには、自らデプロイを削除しないようにすると共に、Visual Studio によって自動的にデプロイが削除されないようにする必要があります。 その動作は、**公開ウィザード**に用意されている各種オプションで、デプロイの設定を指定することによって制御できます。 指定できるデプロイとして新規と更新があります。更新デプロイには、増分更新と同時更新とがあり、どちらも VIP が維持されます。 各種デプロイの定義については、[Azure アプリケーションの公開ウィザード](vs-azure-tools-publish-azure-application-wizard.md)に関するページを参照してください。  さらに、エラーが発生した場合に、それまでのクラウド サービスのデプロイを削除するかどうかを制御できます。 そのオプションが正しく設定されていない場合、VIP が予期せず変化する可能性があります。

## <a name="updating-a-cloud-service-without-changing-its-vip"></a>VIP を変更せずにクラウド サービスを更新する
1. Visual Studio で Azure クラウド サービス プロジェクトを開くか新たに作成します。 

1. **ソリューション エクスプローラー**でそのプロジェクトを右クリックし、コンテキスト メニューの **[発行]** を選択します。

    ![[発行] メニュー](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/solution-explorer-publish-menu.png)

1. **[Azure アプリケーションの公開]** ダイアログで、デプロイ先の Azure サブスクリプションを選択し、必要に応じてサインインして、**[次へ]** をクリックします。

    ![Azure アプリケーションの公開 - サインイン](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-signin.png)

1. **[共通設定]** タブで、デプロイ先のクラウド サービスの名前、**[環境]**、**[ビルド構成]**、**[サービス構成]** がすべて正しいことを確認します。

    ![Azure アプリケーションの公開 - 共通設定](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-common-settings.png)

1. **[詳細設定]** タブで、ストレージ アカウントとデプロイ ラベルが正しいこと、**[失敗時に配置を削除]** チェック ボックスがオフになっていること、**[配置の更新]** チェック ボックスがオンになっていることを確認します。 **[配置の更新]** チェック ボックスをオンにすると、アプリケーションを再発行したときに、デプロイが削除され、VIP が失われることはなくなります。 **[失敗時に配置を削除]** チェック ボックスをオフにすると、デプロイ中にエラーが発生した場合に、VIP が失われることはなくなります。

    ![Azure アプリケーションの公開 - 詳細設定](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-advanced-settings.png)

1. (省略可能) ロールの更新方法をさらに詳しく指定するには、**[配置の更新]** の横の **[設定]** をクリックします。 **[増分更新]** または **[同時更新]** を選択します。 **[増分更新]** を選択した場合は、アプリケーションのインスタンスが 1 つずつ更新されるので、アプリケーションをいつでも利用できます。 **[同時更新]** を選択した場合は、アプリケーションのすべてのインスタンスが同時に更新されます。 同時更新の方が高速ですが、更新処理中はサービスが利用できない可能性があります。 操作が完了したら、**[次へ]** をクリックします。

    ![Azure アプリケーションの公開 - 配置の更新設定](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-deployment-update-settings.png)

1. **[Azure アプリケーションの公開]** ダイアログに戻ったら、**[概要]** ページが表示されるまで **[次へ]** をクリックします。 設定を確認し、**[発行]** をクリックします。
   
    ![Azure アプリケーションの公開 - 概要](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-summary.png)

## <a name="next-steps"></a>次のステップ
- [Visual Studio の Azure アプリケーションの公開ウィザードの使用](vs-azure-tools-publish-azure-application-wizard.md)


