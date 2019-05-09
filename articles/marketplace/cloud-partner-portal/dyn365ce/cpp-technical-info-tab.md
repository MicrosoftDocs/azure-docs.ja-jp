---
title: Dynamics 365 for Customer Engagement の [技術情報] タブ - Azure Marketplace | Microsoft Docs
description: AppSource Marketplace で Dynamics 365 Customer Engagement アプリケーション用の技術情報を指定する方法です。
services: Dynamics 365 for Customer Engagement Offer, Azure, Marketplace, Cloud Partner Portal, AppSource
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: pbutlerm
ms.openlocfilehash: 1dd488c2eb419b5e210a48d7a94f7d0bb423a2b1
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731482"
---
# <a name="dynamics-365-for-customer-engagement-technical-info-tab"></a>Dynamics 365 for Customer Engagement の [技術情報] タブ

**[New Offer]\(新しいオファー\)** ページの **[技術情報]** タブでは、CRM パッケージやマーケティング ロゴ アセットなど、Dynamics 365 for Customer Engagement アプリケーションに関する詳細な情報を指定することができます。  このタブは、次の 4 つのセクションに分かれています:**[Application Info]\(アプリケーション情報\)**、**[CRM Package]\(CRM パッケージ\)**、**[CRM Package Availability]\(CRM パッケージの利用可能性\)**、**[Marketing Artifacts]\(マーケティングの成果物\)**。 フィールド名に付いているアスタリスク (*) は、そのフィールドが必須であることを示します。 


## <a name="application-info-section"></a>[Application Info]\(アプリケーション情報\) セクション

このセクションでは、Dynamics 365 アプリケーションに関する詳細情報を指定します。

![[技術情報] タブの [Application Info]\(アプリケーション情報\) セクション](./media/dynce-technical-info-tab1.png)

次の表ではこれらのフィールドについて説明します。

|      フィールド                    |    説明                  |
|    ---------                  |  ---------------                |
|   基本ライセンス モデル          |  ライセンス モデルにより、Dynamics 365 管理センターで顧客にアプリケーションが割り当てられる方法が決まります。 **リソース** ライセンスがインスタンス ベースであるのに対し、**ユーザー** ライセンスはテナントごとに割り当てられます。  |
|  S2S 送信と CRM Secure Store アクセス |  CRM Secure Store またはサーバー間 (S2S) 送信アクセスの構成を有効にします。 "*この機能を使用するには、認定フェーズの間に Dynamics 365 チームによる特別な考慮が必要です。*" この機能をサポートするための追加手順の実行について、Microsoft からご連絡します。  |
| CRM ライフサイクル イベントのサブスクライブ | Dynamics 365 ライフサイクル イベントと統合するには、ユーザーは Microsoft との特別な契約によって登録される専用のサービスを提供する必要があります。 "*この機能を使用するには、認定フェーズの間に Dynamics 365 チームによる特別な考慮が必要です。*" この機能をサポートするための追加手順の実行については、こちらからご連絡します。  |
| アプリケーション構成 URL | ユーザーがアプリケーションを構成できる Web ページの URL です |
| 該当する Dynamics 365 製品  | このオファーが適用される Dynamics 365 製品を選択します。 このオファーは、AppSource で選択した製品の下に表示されます。  |
| マーケティングのみの変更         | このオプションを [はい] に設定すると、マーケティング/説明的な変更のみが既存のオファーに対して行われたことを示します。  そのような変更が行われたオファーについては、認定およびプロビジョニングのステージをバイパスできます。  |
|  |  |


## <a name="crm-package-section"></a>[CRM Package]\(CRM パッケージ\) セクション

このセクションでは、AppSource パッケージ ファイルに関する詳細情報を指定します。  この情報は、Dynamics 365 の検証と認定チームによって使用されます。

![[技術情報] タブの [CRM Package]\(CRM パッケージ\) セクション](./media/dynce-technical-info-tab2.png)

次の表ではこれらのフィールドについて説明します。

|      フィールド                    |    説明                  |
|    ---------                  |  ---------------                |
|  パッケージのファイル名     |  パッケージ (.zip) のファイル名です。  この名前は公開 "*されず*"、Dynamics 365 認定チームによって内部的に使用されます。  |
|  Url                          |  アップロードされたパッケージ ファイルを含む Azure Storage アカウントの URL です。 チームが検証用にパッケージを取得できるよう、この URL には読み取り専用の SAS キーが含まれている必要があります。  |
| 複数の CRM パッケージ     | 異なるパッケージで CRM の複数のバージョンをサポートしている場合にのみ、[はい] を選択します。  バージョンごとに、対応するパッケージ ファイルを個別に作成する必要があります。  |
| シナリオとユース ケースのアセット   | Dynamics 365 の検証チームが使用するため、アプリケーションの機能仕様ドキュメントのアップロードを有効にします。  この仕様の推奨される形式は、[E2E ユーザー シナリオ テンプレート](https://isvdocumentation.blob.core.windows.net/d365documentation/Power%20Platform%20E2E%20document.docx)です。  |
|  |  |


## <a name="crm-package-availability-section"></a>[CRM Package Availability]\(CRM パッケージの利用可能性\) セクション

このセクションでは、顧客がアプリケーションを利用できる地理的なリージョンを選択します。  次のソブリン リージョンにデプロイするには、認定プロセスの間に "*特別なアクセス許可と検証が必要*" です:[ドイツ](https://docs.microsoft.com/azure/germany/)、[米国政府クラウド](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)、TIP。


## <a name="marketing-artifacts-section"></a>[マーケティングの成果物] セクション

このセクションでは、AppSource Marketplace でパッケージを表すために使用されるアプリケーション ロゴをアップロードする必要があります。  ロゴの画像は、PNG 形式で、サイズ 255 x 115 ピクセルにする必要があります。


## <a name="next-steps"></a>次の手順

[[体験版] タブ](./cpp-testdrive-tab.md)を設定して、アプリケーションのデモを提供することをお勧めします
