---
title: Azure AD ギャラリー アプリケーションへのユーザー プロビジョニングを構成する方法 | Microsoft Docs
description: 既に Azure AD アプリケーション ギャラリーに一覧表示されているアプリケーションに対するユーザー アカウントのプロビジョニングとその解除を行えるように、さまざまな機能が用意されています。これらをすばやく構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 028c93e1d250606ccf1516e9393bf61140961831
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56174794"
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Azure AD ギャラリー アプリケーションへのユーザー プロビジョニングを構成する方法

"*ユーザー アカウントのプロビジョニング*" とは、アプリケーションのローカル ユーザー プロファイル ストアのユーザー アカウント レコードを作成、更新、無効化することです。 ほとんどのクラウドおよび SaaS アプリケーションでは、ユーザー ロールとアクセス許可を独自のローカル ユーザー プロファイル ストアに格納しています。このようにローカル ストアにユーザー レコードを格納することは、シングル サインオンとアクセスの実行のために "*必須*" となっています。

Azure Portal では、左側のナビゲーション ウィンドウのエンタープライズ アプリ用の **[プロビジョニング]** タブに、そのアプリでサポートされているプロビジョニング モードが表示されます。 1 つまたは 2 つの値が表示されます。

## <a name="configuring-an-application-for-manual-provisioning"></a>手動プロビジョニング用にアプリケーションを構成する

"*手動*" プロビジョニングとは、そのアプリに用意されている手法を使用して、ユーザー アカウントを手動で作成する必要があることを意味します。 この場合、そのアプリの管理ポータルにログインし、Web ベースのユーザー インターフェイスを使用してユーザーを追加する場合があります。 または、そのアプリケーションに用意されているメカニズムを使用して、ユーザー アカウントの詳細を記載したスプレッドシートをアップロードする場合があります。 アプリから提供されるドキュメントを確認するか、アプリの開発者に問い合わせて、どのようなメカニズムが使用できるか判断してください。

アプリケーションに対して手動モードしか表示されない場合は、そのアプリ用の自動の Azure AD プロビジョニング コネクタがまだ作成されていないことを表しています。 または、自動プロビジョニング コネクタを構築するための前提条件であるユーザー管理 API をアプリがサポートしていないことを意味する場合もあります。

特定のアプリの自動プロビジョニングに関するサポートを依頼する必要がある場合は、「[Azure Active Directory Application Requests (Azure Active Directory アプリケーション要求)](https://aka.ms/aadapprequest)」を使用して要求を入力できます。

## <a name="configuring-an-application-for-automatic-provisioning"></a>自動プロビジョニング用にアプリケーションを構成する

"*自動*" とは、このアプリケーション用の Azure AD プロビジョニング コネクタが開発済みであることを意味します。 Azure AD プロビジョニング サービスとそのしくみの詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)」を参照してください。

特定のユーザーとグループをアプリケーションにプロビジョニングする方法の詳細については、[エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning)に関するページを参照してください。

自動プロビジョニングを有効にし、構成するために実際に必要な手順は、アプリケーションによって異なります。

>[!NOTE]
>まず、アプリケーションのプロビジョニングの設定を取り上げた設定チュートリアルを探します。次に、その手順に従ってアプリと Azure AD の両方を構成し、プロビジョニングの接続を作成します。 
>
>

アプリのチュートリアルについては、「[SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)」を参照してください。

プロビジョニングを設定するときの重要な考慮事項の 1 つは、属性マッピングとワークフローを確認し、構成することです。これらは、どのユーザー (またはグループ) のプロパティが Azure AD からアプリケーションに適用されるかを定義します。 これには、2 つのシステム間でユーザーまたはグループを一意に識別して照合するために使用される "照合プロパティ" の設定も含まれます。 この重要なプロセスに関する詳細。

## <a name="next-steps"></a>次の手順
[Azure Active Directory の SaaS アプリケーションに対するユーザー プロビジョニング属性マッピングのカスタマイズ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

