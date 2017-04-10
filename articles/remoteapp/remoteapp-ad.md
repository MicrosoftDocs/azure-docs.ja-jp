---
title: "Azure RemoteApp に関する Azure AD と Active Directory の要件 | Microsoft Docs"
description: "Azure RemoteApp で動作するように Active Directory をセットアップする方法について説明します。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 66366b25-6012-45fa-a4f6-da0ddfe0b486
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 816305fb3ace5bfc7cf50bac5e42fde83e9697d3


---
# <a name="azure-ad--active-directory-requirements-for-azure-remoteapp"></a>Azure RemoteApp に関する Azure AD と Active Directory の要件
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

AD Connect を使用してフェデレーションするクラウド コレクションまたは Azure RemoteApp ハイブリッド コレクションでは、次を行う必要があります。

### <a name="connect-azure-ad-and-active-directory"></a>Azure AD と Active Directory を接続する
Azure AD テナントとオンプレミスの Active Directory 環境を接続する場合、AD Connect を使用します。 2 つのディレクトリを接続するには、 [4 回クリック](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) するのみです。

注 - ハイブリッド コレクションではディレクトリの同期が必要です。

### <a name="make-sure-your-domaincom-match"></a>"@domain.com" が一致することを確認する
作業を開始する前に、オンプレミス フォレストの UPN が、Azure AD ドメインのサフィックスと一致していることを確認してください。 

Azure AD に UPN ドメインのサフィックスをセットアップしたら、Azure RemoteApp にログインするすべてのユーザーが "user@<the suffix you set up>" としてログインするようになります。 ユーザーが同じ user@suffix を使用してオンプレミス ドメインにログインできることも確認してください。 場合によっては、Azure AD に&1; つのドメイン名をセットアップし、オンプレミスのユーザー用に別のドメイン サフィックスを指定することもできます。 この場合、ユーザーは Azure RemoteApp を介してドメインに参加しているコンピューターまたはリソースに接続することはできません。

たとえば、UPN ドメイン サフィックスを AAD に contoso.com としてセットアップした場合に、オンプレミス/AD の一部のユーザーが @contoso.uk, を使用してログインするように構成されている場合、そのようなユーザーは ARA コレクションに正しくログインすることはできません。 ログインするには、AAD と AD のユーザー UPN が同じである必要があります。

### <a name="create-objects-for-azure-remoteapp"></a>Azure RemoteApp のオブジェクトを作成する
また、次のオンプレミス Active Directory オブジェクトを作成する必要もあります。

* オンプレミスのドメインに RDSH エンド ポイントを結合して、RemoteApp プログラムのドメイン リソースにアクセスするサービス アカウント。
* RemoteApp マシン オブジェクトを含む組織単位 (OU)。 RemoteApp で使用するアカウントとポリシーを特定するために OU の使用を推奨します (必須ではありません)。

RemoteApp コレクションを作成するときは、これらのオブジェクトが両方とも必要であるため、これらの手順を最初に行ってください。




<!--HONumber=Dec16_HO2-->


