---
title: "Azure Marketplace 向けプランを作成するための技術以外の前提条件 | Microsoft Docs"
description: "他のユーザーが購入できるプランを作成して、Azure Marketplace にデプロイするための要件を理解します。"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 3dae463b-8f48-4f52-8fa8-4e3975f09f43
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/18/2016
ms.author: hascipio
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 8bc9d40daf0b9bc8256e88b39039fca7e4cb8a53


---
# <a name="general-prerequisites-for-creating-an-offer-for-the-azure-marketplace"></a>Azure Marketplace 向けプラン作成の一般的な前提条件
プランの作成プロセスに必要な、ビジネス処理中心の一般的な前提条件を理解します。

## <a name="ensure-that-you-are-registered-as-a-seller-with-microsoft"></a>Microsoft 販売者として登録されていることを確認します。
Microsoft で販売者アカウントを登録する方法の詳細な手順については、「 [アカウントの作成および登録](marketplace-publishing-accounts-creation-registration.md)」を参照してください。

* **デベロッパー センターで会社が既に販売者として登録されており、新しいプランを作成する必要がある場合は** 、デベロッパー センター登録で使用した電子メール ID で発行ポータルにログインします。 この手順により、デベロッパー センターと発行ポータルが相互に関連付けられます。
* **デベロッパー センターで会社が既に販売者として登録されており、既存のプランを編集する必要がある場合は、** 管理者アカウントか、発行ポータルで共同管理者として追加されているアカウントを使用して、発行ポータルにログインします。 共同管理者アカウントを追加する手順は次のとおりです。

## <a name="steps-to-add-a-co-admin-in-the-publishing-portal"></a>発行ポータルで共同管理者を追加する手順
発行ポータルの管理者が、アプリケーションで作業している会社の他のメンバーを、発行ポータルの共同管理者として後で追加できます。 **管理者である必要があります** 。

> [!NOTE]
> 新規ユーザーの場合、発行ポータルで共同管理者を追加する前に、発行ポータルで少なくとも 1 つのアプリケーションが作成されていることを確認します。 **[パブリッシャー]** タブは、発行ポータルでアプリケーションを作成しないと表示されないからです。
> 
> 

1. 共同管理者の電子メール ID が Microsoft アカウント (MSA) であることを確認します。 MSA でない場合は、こちらの [リンク](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1)を使用して、MSA として登録します。
2. 共同管理者の追加を試みる前に、管理者アカウントのアプリケーションが少なくとも 1 つ存在していることを確認します。
3. 上記の手順が完了したら、共同管理者の電子メール ID で発行ポータルにログインし、その後、ログアウトします。
4. ここで、管理者の電子メール ID で発行ポータルにログインします。
5. [パブリッシャー] に移動してアカウントを選択し、[管理者] に移動して共同管理者を追加します (以下のスクリーンショットを参照)
   
    ![図](media/marketplace-publishing-pre-requisites/imgAddAdmin_05.png)
6. 発行プロセスのさまざまな段階 (デベロッパー センター、発行ポータルなど) で指定された電子メール ID で、Microsoft からのすべての通信が監視されていることを確認します。
7. デベロッパー センター登録の場合、1 人のユーザーに関連付けられているアカウントの使用は避けます。 これは、1 人のユーザーからの依存関係をなくすための推奨事項です。
8. デベロッパー センター登録で問題が発生した場合は、こちらの[リンク](https://developer.microsoft.com/en-us/windows/support)を使用してチケットを作成します。

## <a name="steps-to-delete-a-co-admin-in-the-publishing-portal"></a>発行ポータルで共同管理者を削除する手順
**管理者である必要があります** 。

1. 管理者の電子メール ID で発行ポータルにログインします。
2. **[パブリッシャー]** > 自分のアカウントを選択 > **[管理者]**  ->  **[Co-Admins (共同管理者)]** の順に移動します。
3. 削除する共同管理者の横にある **[X]** ボタンをクリックします (以下のスクリーンショットを参照)。
   
    ![図](media/marketplace-publishing-pre-requisites/imgDeleteAdmin_03.png)

> [!IMPORTANT]
> 無料プランのみを発行する予定 (またはライセンス持ち込み) であれば、会社の税金や銀行情報を入力する必要はありません。
> 
> プラン作成を開始するには、会社の登録を完了する必要があります。 ただし、会社が Microsoft デベロッパー アカウントで税金と銀行情報を入力している途中でも、開発者は [発行ポータル](https://publish.windowsazure.com)で仮想マシン イメージの作成を開始し、それらのイメージの認定取得、および Azure ステージング環境でのテストを実行できます。 販売者アカウントの承認が必要になるのは、プランを Azure Marketplace に発行する最後の手順のみです。
> 
> 

## <a name="acquire-an-azure-pay-as-you-go-subscription"></a>Azure の "従量課金制" サブスクリプションを取得する
これは、VM イメージを作成して、それらのイメージを [Azure Marketplace](https://azure.microsoft.com/marketplace/) に渡すために使用するサブスクリプションです。 既存のサブスクリプションがない場合は、https://account.windowsazure.com/signup?offer=ms-azr-0003p でサインアップしてください。

## <a name="sell-from-countries"></a>「販売元」の国
> [!WARNING]
> Azure Marketplace でサービスを販売するには、登録済みエンティティが、承認されたいずれかの「販売元」の国からのものである必要があります。 この制限は、支払いおよび課税上の理由から生じます。 「販売元」の国の範囲は今後拡大される予定です。 完全な一覧については、 [Azure Marketplace 参加ポリシー](http://go.microsoft.com/fwlink/?LinkID=526833)のセクション 1b を参照してください。
> 
> 

## <a name="next-steps"></a>次のステップ
技術面以外の前提条件を満たしたら、プランに固有の技術的な前提条件を満たす必要があります。 Azure Marketplace 向けに作成するプランの種類に合わせて、記事へのリンクをクリックしてください。

* [VM の技術的な前提条件](marketplace-publishing-vm-image-creation-prerequisites.md)
* [ソリューション テンプレートの技術的な前提条件](marketplace-publishing-solution-template-creation-prerequisites.md)

## <a name="see-also"></a>関連項目
* [Microsoft Azure Marketplace へのプランの発行方法](marketplace-publishing-getting-started.md)




<!--HONumber=Nov16_HO3-->


