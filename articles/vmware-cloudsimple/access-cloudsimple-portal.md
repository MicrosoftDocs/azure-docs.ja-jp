---
title: CloudSimple による Azure VMware ソリューションへのアクセス - ポータル
description: Azure portal から CloudSimple による Azure VMware ソリューションにアクセスする方法について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 61c3d37f365034984231c780199e181872c010c6
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544125"
---
# <a name="accessing-the-vmware-solution-by-cloudsimple-portal-from-azure-portal"></a>Azure portal から CloudSimple による Azure VMware ソリューションにアクセスする

CloudSimple ポータルへのアクセスにはシングル サインオンがサポートされています。 Azure portal にサインインした後は、もう一度サインインしなくても CloudSimple ポータルにアクセスできます。 初めて CloudSimple ポータルにアクセスすると、[CloudSimple Service Authorization](#consent-to-cloudsimple-service-authorization-application) アプリケーションを承認するように求められます。  承認は、1 回限りの操作です。

## <a name="before-you-begin"></a>開始する前に

組み込みの**所有者**ロールを持っているユーザーと、組み込みの**共同作成者**ロールを持っているユーザーが、CloudSimple ポータルにアクセスできます。  ロールは、CloudSimple サービスがデプロイされているリソース グループに対して構成されている必要があります。  CloudSimple サービス オブジェクトに対してロールを構成することもできます。  ご自身のロールの確認について詳しくは、「[ロールの割り当てを表示する](https://docs.microsoft.com/azure/role-based-access-control/check-access)」の記事を参照してください。

カスタム ロールを使用している場合、そのロールの ```Actions``` に次のいずれかの操作が必要です。  カスタム ロールの詳細については、「[Azure リソースのカスタム ロール](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)」を参照してください。  いずれかの操作が ```NotActions``` に含まれている場合、ユーザーは CloudSimple ポータルにアクセスできません。 

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="access-the-cloudsimple-portal"></a>CloudSimple ポータルにアクセスする

1. **[すべてのサービス]** を選択します。

2. **CloudSimple Services** を検索します。

3. プライベート クラウドを作成する CloudSimple サービスを選択します。

4. **[概要]** ページで、 **[Go to the CloudSimple portal]\(CloudSimple ポータルに移動\)** をクリックします。  Azure portal から初めて CloudSimple ポータルにアクセスする場合は、[CloudSimple Service Authorization](#consent-to-cloudsimple-service-authorization-application) アプリケーションを承認するように求められます。 

    ![CloudSimple ポータルを起動する](media/launch-cloudsimple-portal.png)

> [!TIP]
> Azure portal から直接プライベート クラウド操作 (プライベート クラウドの作成や拡張など) を選択すると、CloudSimple ポータルの指定のページが表示されます。

CloudSimple ポータルで、サイド メニューの **[ホーム]** を選択してプライベート クラウドに関する概要情報を表示します。 プライベート クラウドのリソースと容量が、注意が必要なアラートとタスクと共に表示されます。 一般的な作業については、ページの上部にある名前付きアイコンをクリックします。

![ホーム ページ](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>CloudSimple Service Authorization アプリケーションに同意する

Azure portal から CloudSimple ポータルを初めて起動するには、CloudSimple Service Authorization アプリケーションに同意する必要があります。  要求されたアクセス許可を付与し、CloudSimple ポータルにアクセスするには、 **[同意する]** を選択します。 

![CloudSimple Service Authorization に同意する - 管理者](media/cloudsimple-azure-consent.png)

全体管理者特権を持っている場合は、組織に代わって同意することができます。  **[組織の代理として同意する]** を選択します。

![CloudSimple Service Authorization に同意する - 全体管理者](media/cloudsimple-azure-consent-global-admin.png)

お持ちのアクセス許可で CloudSimple ポータルへのアクセスが許可されていない場合は、テナントの全体管理者に必要なアクセス許可の付与を依頼します。  全体管理者は、組織に代わって同意することができます。

![CloudSimple Service Authorization に同意する - 管理者が必要](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>次の手順

* [プライベート クラウドを作成する](https://docs.azure.cloudsimple.com/create-private-cloud/)方法を学習する
* [プライベート クラウドの環境を構成する](quickstart-create-private-cloud.md)方法を学習する
