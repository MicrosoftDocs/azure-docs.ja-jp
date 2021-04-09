---
title: Azure AD Connect クラウド同期の詳細情報 - しくみ
description: このトピックでは、クラウド同期のしくみについて詳細に説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e1079ab4a523fbbc99c1e9190aef960b3f0723f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98612835"
---
# <a name="cloud-sync-deep-dive---how-it-works"></a>クラウド同期の詳細情報 - しくみ

## <a name="overview-of-components"></a>コンポーネントの概要

![しくみ](media/concept-how-it-works/how-1.png)

クラウド同期は Azure AD サービスの上に構築されており、次の 2 つの主要コンポーネントがあります。

- **プロビジョニング エージェント**:Azure AD Connect クラウド プロビジョニング エージェントは Workday 受信と同じエージェントであり、アプリ プロキシおよびパススルー認証と同じサーバー側テクノロジで構築されています。 必要となるのは送信接続のみであり、エージェントは自動更新されます。 
- **プロビジョニング サービス**:送信プロビジョニング、およびスケジューラに基づいたモデルを使用する Workday 受信プロビジョニングと同じプロビジョニング サービス。 クラウド同期の場合、変更は 2 分ごとにプロビジョニングされます。


## <a name="initial-setup"></a>初期セットアップ
初期セットアップ中に、いくつかの操作を行うことでクラウド同期が実行されます。  次のとおりです。 

- **エージェントのインストール中**:プロビジョニングするための AD ドメインのエージェントを構成します。  この構成でドメインがハイブリッド ID サービスに登録され、要求をリッスンする Service Bus への送信接続が確立されます。
- **プロビジョニングを有効にするとき**:AD ドメインを選択し、2 分ごとに実行されるプロビジョニングを有効にします。 必要に応じて、パスワード ハッシュ同期を選択解除して通知用メールを定義することもできます。 Microsoft Graph API を使用して属性変換を管理することもできます。


## <a name="agent-installation"></a>エージェントのインストール
以下では、クラウド プロビジョニング エージェントのインストール時に行われることを説明します。

- 最初に、インストーラーによってエージェント バイナリとエージェントサービスがインストールされ、仮想サービス アカウント (NETWORK SERVICE\AADProvisioningAgent) で実行されます。  仮想サービス アカウントは、パスワードのない特殊な種類のアカウントで、Windows によって管理されます。
- 次にインストーラーによって、ウィザードが開始されます。
- ウィザードで Azure AD 資格情報の入力が求められ、認証が行われて、トークンが取得されます。
- 次にウィザードで、現在のコンピューターのドメイン管理者の資格情報が求められます。
- これらの資格情報を使用して、このドメインのエージェント一般管理サービス アカウント (GMSA) が作成されるか、既に存在する場合は再利用されます。
- エージェント サービスは、GMSA で動作するよう再構成されます。
- ウィザードで、エージェントによってサービスを提供するドメイン毎に、ドメイン構成とエンタープライズ管理者 (EA) またはドメイン管理者 (DA) アカウントが求められます。
- GMSA アカウントがアクセス許可で更新され、これにより、上記で入力した各ドメインにアクセスできるようになります。
- 次に、ウィザードでエージェント登録がトリガーされます。
- エージェントは、証明書を作成し、Azure AD トークンを使用して、自身と証明書をハイブリッド ID サービス (HIS) 登録サービスに登録します。
- ウィザードで AgentResourceGrouping 呼び出しがトリガーされます。 この HIS 管理サービスへの呼び出しでは、HIS 構成内の 1 つ以上の AD ドメインにエージェントが割り当てられます。
- ウィザードで、エージェント サービスが再起動します。
- エージェントは再起動時 (およびその後 10 分ごと) にブートストラップ サービスを呼び出して、構成に更新がないか確認します。  ブートストラップ サービスは、エージェントの ID を検証します。  最後のブートストラップ時刻も更新されます。  これは、エージェントがブートストラップしなければ、Service Bus エンドポイントが更新されず、要求を受信できなくなる可能性があるため、重要です。 


## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>System for Cross-domain Identity Management (SCIM) とは

[SCIM 仕様](https://tools.ietf.org/html/draft-scim-core-schema-01)は、Azure AD などの ID ドメイン間でのユーザーまたはグループ ID 情報の交換を自動化するために使用される標準です。 SCIM は、プロビジョニングに対する事実上の標準になりつつあり、SAML や OpenID Connect などのフェデレーション標準と組み合わせて使用すると、エンドツーエンドの標準ベースのアクセス管理用ソリューションが管理者に提供されます。

Azure AD Connect クラウド プロビジョニング エージェントは、Azure AD による SCIM を使用して、ユーザーとグループのプロビジョニングとプロビジョニング解除を行います。

## <a name="synchronization-flow"></a>同期フロー
![プロビジョニング](media/concept-how-it-works/provisioning-4.png) エージェントをインストールしてプロビジョニングを可能にすると、以下のフローが進みます。

1.  構成が完了すると、Azure AD プロビジョニング サービスにより Azure AD ハイブリッド サービスが呼び出され、要求が Service Bus に追加されます。 エージェントは、要求をリッスンする Service Bus への送信接続を常に維持し、クロスドメイン ID 管理システム (SCIM) 要求を即座に取得します。 
2.  エージェントは、オブジェクトの種類に基づいて要求を個別のクエリに分割します。 
3.  AD が結果をエージェントに返し、エージェントはこのデータをフィルター処理したのち、Azure AD に送信します。  
4.  エージェントが SCIM 応答を Azure AD に返します。  これらの応答は、エージェント内で行われたフィルター処理に基づいています。  エージェントは、スコーピングを使用して結果をフィルター処理します。 
5.  プロビジョニング サービスは、変更を Azure AD に書き込みます。
6. これが完全同期でなく差分同期の場合は、クッキーまたはウォーターマークが使用されます。 新しいクエリでは、変更がそのクッキーまたはウォーターマーク以降から取得されます。

## <a name="supported-scenarios"></a>サポートされているシナリオ:
クラウド同期では、次のシナリオがサポートされています。


- **新しいフォレストがある既存のハイブリッドのお客様**:Azure AD Connect 同期がプライマリ フォレストに使用されます。 クラウド同期が AD フォレストからのプロビジョニング (切断済みを含む) に使用されます。 詳細については、チュートリアル ([こちら](tutorial-existing-forest.md)) を参照してください。

 ![既存のハイブリッド](media/tutorial-existing-forest/existing-forest-new-forest-2.png)
- **新規のハイブリッドのお客様**:    Azure AD Connect 同期は使用されません。 クラウド同期が AD フォレストからのプロビジョニングに使用されます。  詳細については、チュートリアル ([こちら](tutorial-single-forest.md)) を参照してください。
 
 ![新規のお客様](media/tutorial-single-forest/diagram-2.png)

- **既存のハイブリッドのお客様**:Azure AD Connect 同期がプライマリ フォレストに使用されます。 クラウド同期のパイロットがプライマリ フォレストの小数のユーザーを対象に実施されています ([こちら](tutorial-existing-forest.md))。

 ![既存のパイロット](media/tutorial-migrate-aadc-aadccp/diagram-2.png)

詳細については、[サポートされるトポロジ](plan-cloud-sync-topologies.md)に関するページを参照してください。



## <a name="next-steps"></a>次のステップ 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド同期とは](what-is-cloud-sync.md)
