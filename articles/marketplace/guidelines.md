---
title: Azure Marketplace と AppSource のパブリッシャー ガイド | Azure
description: アプリ パブリッシャーとサービス パブリッシャー向け Azure Marketplace および AppSource のガイドライン
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: a0ab88b1375f5178ca6f41689de8d2f30f8d7808
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728133"
---
# <a name="guidelines"></a>ガイドライン  

<!--
## Guidelines for AppSource  
-->
---

## <a name="guidelines-for-azure-marketplace"></a>Azure Marketplace のガイドライン  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Marketplace アカウントを管理するための Microsoft ID の作成に関するガイドライン  
Marketplace アカウントを作成するために使用される同じ Microsoft ID に複数のユーザーがアクセスする必要がある場合は、会社のアカウントの作成に役立つ、以下のガイドラインに従う必要があります。 

>[!IMPORTANT]
>複数のユーザーに Microsoft Developer Center (デベロッパー センター) アカウントへのアクセスを承認するには、Microsoft では Azure Active Directory (Azure AD) を使用して、個々のユーザーにロールを割り当てることをお勧めしています。 各ユーザーは、個人の Azure AD 資格情報を使ってサインインすることで、アカウントにアクセスできます。 会社に登録されているドメインのメール アドレスを使用して Microsoft ID を作成します。Microsoft では、メールを個人に割り当てないことを提案しています。 例: `windowsapps@fabrikam.com`。  
>*   詳細については、「[問題: Azure AD フェデレーション ドメインでの Microsoft ID](#issue-microsoft-id-in-an-azure-ad-federated-domain)」を参照してください。  

*   Microsoft ID へのアクセスは、最小限の数の開発者に制限してください。 
*   デベロッパー センター アカウントにアクセスする必要があるすべての従業員が含まれる会社のメール配布リスト (DL) を設定します。 DL メール アドレスをセキュリティ情報に追加します。 DL により、リストに登録されたすべての従業員が、要求に応じてセキュリティ コードを受信し、Microsoft ID のセキュリティ情報を管理できるようになります。 配布リストの設定が適していない場合は、求められたときに、個々の電子メール アカウントの所有者がセキュリティ コードにアクセスして共有できる状態になっている必要があります。  
    *   たとえば、新しいセキュリティ情報が Microsoft ID に追加されたときや、新しいデバイスから Microsoft ID にアクセスしなければならないときに、所有者はこの操作を求められます。  
*   会社の電話番号を追加する際には、内線番号なしで主要なチーム メンバーに連絡できる番号を使用してください。  
*   通常、開発者は信頼済みデバイスを使用して、デベロッパー センター アカウントにサインインする必要があります。 主要なチーム メンバーの全員が、信頼済みデバイスにアクセスできるようにする必要があります。 信頼済みデバイスを使用してアクセスすると、ユーザーがデベロッパー センター アカウントにアクセスしているときにセキュリティ コードを送信するための要件が少なくなります。  
*   信頼されていないコンピューターからのデベロッパー センター アカウントへのアクセスを許可する必要がある場合は、アクセスを 5 人以下の開発者に制限する必要があります。 開発者はなるべく、地理的位置やネットワーク上の場所が同じであるコンピューターからアカウントにアクセスするようにしてください。  
*   セキュリティ情報は頻繁に見直し、確認してください。  
    *   セキュリティ情報を表示するには、「セキュリティ設定」ページ ([account.live.com/proofs/Manage](https://account.live.com/proofs/Manage)) にアクセスしてください。

デベロッパー センター アカウントには、主に信頼されているコンピューターからアクセスする必要があります。 1 週間あたり 1 つのデベロッパー センター アカウントに生成されるコード数には制限があるため、信頼されているコンピューターからアクセスすることが重要です。 信頼されているコンピューターを使用すると、最も安全で一貫したサインイン エクスペリエンスも実現できます。 
*   デベロッパー センター アカウントに関する追加のガイドラインとセキュリティの詳細については、「開発者アカウントを開く」ページ ([docs.microsoft.com/windows/uwp/publish/opening-a-developer-account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account)) を参照してください。 

---

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>問題: Azure AD フェデレーション ドメインでの Microsoft ID  
会社のアカウントは、Azure Active Directory (Azure AD) 経由でフェデレーションできます。 Azure AD とフェデレーションされている会社のメール アドレスを使用して Microsoft ID を作成しようとすると、エラーが発生します。 エラーが発生した場合は、IT チームに問い合わせて、Azure AD を介してアカウントがフェデレーションされていることを確認する必要があります。 Azure AD のフェデレーション メールは、既知の問題であり、Microsoft はこの問題の解決に取り組んでいます。  
*   Azure AD の詳細については、「Azure Active Directory のドキュメント」ページ ([docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory)) を参照してください。

Microsoft では回避策を推奨しています。 次の手順に従って、`outlook.com` ドメインに新しいメール アドレスを作成し、通信を転送するルールを作成します。  
1.  [アカウントの作成] ページに移動し、[新しいメール アドレスを取得] リンクをクリックします。 
    *   Microsoft ID にサインアップするには、「アカウントの作成」ページ ([signup.live.com/signup](https://signup.live.com/signup)) にアクセスしてください。  
2.  新しいメール アドレスを作成し、パスワードを入力します。 新しい Microsoft ID と `outlook.com` ドメインのメールボックスが作成されます。 アカウントが作成されるまで、登録プロセスを続行します。  

    >[!IMPORTANT]
    >デベロッパー センターに登録するには、Microsoft ID として登録されているメール アドレスまたは配布リストを使用する必要があります。 Microsoft では、配布リストを使用して、個人から依存関係を削除することをお勧めしています。 メール アドレスまたは配布リストが登録されていない場合は、すぐに登録する必要があります。    

    >[!Important]
    >いずれかのメール アドレスが `Microsoft` 社のドメインにある場合は、デベロッパー センターの登録にそのメール アドレスを使用することはできません。  

3.  Outlook のメール アドレスで Microsoft ID を作成した後、Outlook のメールボックスにサインインします。 メール転送ルールを作成します。 メール転送ルールでは、Outlook メールボックスで受信したすべてのメールを、Marketplace プレース アカウントを管理するために作成したドメインのメール アドレスに移動する必要があります。  
    *   Outlook メールボックスにサインインするには、「Outlook」ページ ([outlook.live.com/owa](https://outlook.live.com/owa)) にアクセスしてください。  
    *   転送ルールの詳細については、「Outlook Web App のルールを使って、メッセージを別のアカウントに自動転送する」ページ ([support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed)) を参照してください。  

1.  転送ルールでは、Outlook のメール アカウントで受信したすべてのメールと通信を、会社に登録されているドメインのメール アドレスに送信します。 `outlook.com` メール アドレスは、デベロッパー センターと Cloud パートナー ポータルの両方での認証に使用される必要があります。  

## <a name="next-steps"></a>次の手順
*   「[Azure Marketplace と AppSource のパブリッシャー ガイド](./marketplace-publishers-guide.md)」ページを参照してください。  
 
---
