---
title: "Azure 管理グループのリソースの整理 - Azure | Microsoft Docs"
description: "管理グループとその使用方法について説明します。"
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: rithorn
ms.openlocfilehash: 18541c68b02ae1b59ae4a6a85122dff614c9978c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Azure 管理グループのリソースを整理する 

サブスクリプションが複数ある場合は、"管理グループ" と呼ばれるコンテナーにサブスクリプションをまとめて、サブスクリプション間のアクセス、ポリシー、コスト、コンプライアンスを管理しやすくすることができます。 たとえば、作成できるリソースの種類を制限するポリシーを管理グループに適用できます。

> [!Note]
> 現在、この機能はプライベート プレビュー段階にあります。 [ここからサインアップして](https://aka.ms/MGPreviewSignup)、プレビューに参加登録してください。   
 


管理グループは階層に整理できます。 次の構造は、存在し得る管理グループの階層のサンプル表示です。


![階層ツリー](media/billing-enterprise-mgmt-groups/tree.png)



## <a name="how-management-groups-are-related-to-your-azure-enterprise-enrollment"></a>管理グループを Azure エンタープライズ加入契約に関連付ける方法

管理グループの導入は、[Enterprise Portal](https://ea.azure.com) 機能から [Azure Portal](https://portal.azure.com) に移行する大規模な作業の第一歩です。

管理グループの構造は、Enterprise Portal で定義したとおりの構造で作成されます。 登録、部署、アカウントで構成される階層全体は、対応する管理グループにマップされます。 

現在の EA 構造が管理グループの階層にどのようにマップされるかを次に示します。 

![階層ツリー](media/billing-enterprise-mgmt-groups/tree2.png)

次の表は、Enterprise Portal のユーザーが管理グループの階層にどのようにマップされるかを示しています。

|    EA ロール                                       |    マップされた管理グループ ノード上のロール    |    管理グループ ノード上のアクセス許可                                                          |
|--------------------------------------------------|--------------------------------------------------|----------------------------------------------------------------------------------------------------|
|    EA 管理者                              |    リソース ポリシー共同作成者                   |    登録ノード以下のコストの表示、リソース ポリシーの管理、階層の表示が可能    |
|    読み取り専用モードの EA 管理者            |    課金リーダー                                |    登録ノード以下のコストの読み取り、階層の表示が可能                              |
|    部門管理者                      |    課金リーダー                                |    部署ノード以下のコストの読み取り、階層の表示が可能                                 |
|    読み取り専用モードの部署管理者    |    課金リーダー                                |    部署ノード以下のコストの読み取り、階層の表示が可能                                 |
|    アカウント所有者                                 |    リソース ポリシー共同作成者                   |    アカウント ノード以下のコストの表示、リソース ポリシーの管理、階層の表示が可能       |




## <a name="view-management-groups-in-the-azure-portal"></a>Azure Portal で管理グループを表示する

登録、部署、またはアカウントをプレビューで表示するには、ウェルカム メールに記載されているリンクから Azure Portal にアクセスしてサインインします。   

![階層](media/billing-enterprise-mgmt-groups/hierarchy.png)

### <a name="viewing-costs"></a>コストを表示する 
管理グループの詳細画面には、過去 1 か月のコストが表示されます。 このコストは使用量に基づいて計算されており、前払い額、超過分、含まれる数量、調整、税は考慮されていません。 コストに関するセクションには、登録に対応する管理グループの残りのコミットメントが表示されます。  

![登録の詳細](media/billing-enterprise-mgmt-groups/enrollment.png)

 “個別請求されるコスト” は、マーケットプレース、超過分、お客様の登録コミットメントに違反しないその他のコストなどの個別の変更が月ごとに累積されたものです。  コストの内訳の詳細については、[Enterprise Portal](https://ea.azure.com) をご覧ください。 

### <a name="enabling-access-to-costs"></a>コストへのアクセスを有効にする
コストが表示されない場合は、「[Troubleshoot enterprise cost views (Enterprise コスト ビューのトラブルシューティング)](https://aka.ms/enableazurecosts)」のヘルプ ドキュメントをご覧ください。  

### <a name="delays-between-the-enterprise-portal-and-azure-portal"></a>Enterprise Portal と Azure Portal の間の遅延 
* プレビュー期間中、Azure Portal に表示される金額は、Enterprise Portal に表示される値よりも遅れて表示される場合があります。 この問題は一時的なものであり、現在修正中です。
* Enterprise Portal で更新した設定は Azure Portal に反映されるまで数分かかります。 

## <a name="management-groups-have-a-relationship-with-your-directory"></a>管理グループとご利用のディレクトリとの関係   
サブスクリプション同様、管理グループにも Azure AD との間に信頼関係があります。 管理グループの階層は、ユーザーを認証するために 1 つのディレクトリを信頼します。 管理グループの階層に関連付けられているすべての管理者は、同じディレクトリに属している必要があります。 

ご利用の登録の階層は管理グループにマップされ、1 つのディレクトリとの間に信頼関係が確立されます。 選択可能な場合は、その登録のユーザー アカウントに関連付けられている既存のディレクトリが選択されます。 場合によっては新しいディレクトリが作成され、すべての既存の登録ユーザーがそのディレクトリに招待されます。 その登録のサブスクリプションに関連付けられているディレクトリには影響しません。 そのため、サブスクリプションとは異なるディレクトリに階層が作成される場合があります。 このプロセスが階層とサブスクリプションとの間のやりとりに与える影響について詳しくは、[こちら](billing-enterprise-mgmt-grp-find.md)をご覧ください。

## <a name="administering-your-management-groups"></a>管理グループを管理する
Azure Portal 内の管理グループは現在プレビュー段階にあり、この最初のリリースでは読み取り専用になっています。 更新する場合は、Enterprise Portal で更新してください。 更新すると、自動的に Azure Portal に反映されます。 編集方法と追加方法については、Enterprise Portal 内のヘルプ ドキュメントをご覧ください。   

## <a name="policy-management"></a>ポリシー管理
リソース マネージャーでは、リソースを管理するためにカスタマイズされたポリシーを作成できます。 管理グループを使用して、階層のすべての層にポリシーを割り当てることができ、リソースはそれらのポリシーを継承します。  [詳細情報](https://go.microsoft.com/fwlink/?linkid=858942)

> [!Note]
> ポリシーはディレクトリ間には適用されません。 


