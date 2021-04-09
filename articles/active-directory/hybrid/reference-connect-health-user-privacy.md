---
title: Azure AD Connect Health とユーザー プライバシー | Microsoft Docs
description: このドキュメントでは、Azure AD Connect Health とユーザー プライバシーについて説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58ecdfefa79716bcc0a69063293d5a1067693a78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89182362"
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>ユーザー プライバシーと Azure AD Connect Health 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>この記事では、Azure AD Connect Health とユーザー プライバシーについて説明します。  Azure AD Connect とユーザー プライバシーについては、[こちら](reference-connect-user-privacy.md)の記事をご覧ください。

## <a name="user-privacy-classification"></a>ユーザー プライバシーの分類
Azure AD Connect Health は、GDPR の **データ プロセッサ** カテゴリに分類されます。 このサービスは、データ プロセッサ パイプラインとして、重要なパートナーやエンド コンシューマーにデータ処理サービスを提供するものです。 Azure AD Connect Health は、ユーザー データを生成するものではなく、どのような個人データが収集されるかや、それらのデータの用途について、独立した制御を持つものでもありません。 Azure AD Connect Health でのデータの取得、集計、分析、およびレポートは、既存のオンプレミス データに基づいて行われます。 

## <a name="data-retention-policy"></a>データ リテンション期間ポリシー
Azure AD Connect Health では、レポートの生成、分析の実行、またはインサイトの提供を、30 日間を超えて行うことはありません。 そのため Azure AD Connect Health では、いかなるデータも 30 日間を超えて格納、処理、保持されることはありません。 この設計は、GDPR の規制、Microsoft のプライバシー コンプライアンス規則、および Azure AD のデータ リテンション ポリシーに準拠したものです。 

**Health サービス データが最新ではありません** という **エラー** アラートが30 日以上連続でアクティブになっている場合、そのサーバーでは、その期間中にデータが Connect Health に一切到達していません。 これらのサーバーは無効化され、Connect Health ポータルに表示されません。 サーバーを再度有効にするには、[正常性エージェントをアンインストールし、再インストールする](how-to-connect-health-agent-install.md)必要があります。 なお、このことは同じアラート タイプの **警告** には当てはまりません。 警告は、一部のデータがアラート対象のサーバーに見つからないことを示します。 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Azure AD Connect Health でのデータの収集と 監視を無効にする
Azure AD Connect Health では、各監視対象サーバーや、監視対象サービスのインスタンスについて、データ収集を停止させることができます。 たとえば、Azure AD Connect Health を使用して監視されている個々の AD FS (Active Directory フェデレーション サービス) サーバーについて、データ収集を停止することもできます。 また、Azure AD Connect Health を使用して監視されている ADFS インスタンス全体について、データ収集を停止することもできます。 これを行った場合は、データ収集が停止された後に、対応するサーバーが Azure AD Connect Health ポータルから削除されます。 

>[!IMPORTANT]
> Azure AD Connect Health から監視対象サーバーを削除するには、Azure AD グローバル管理者特権か、Azure RBAC の共同作成者ロールが必要です。
>
> Azure AD Connect Health からサーバーまたはサービス インスタンスを削除した場合、その操作を元に戻すことはできません。 

### <a name="what-to-expect"></a>予期される事柄
個々の監視対象サーバー、または監視対象サービスのインスタンスについてデータの収集と監視を停止する場合には、次のことに注意してください。

- 監視対象サービスのインスタンスを削除すると、そのインスタンスは、ポータルにある Azure AD Connect Health 監視サービスの一覧から削除されます。 
- 監視対象サーバー、または監視対象サービスのインスタンスを削除しても、正常性エージェントがサーバーからアンインストールされたり、削除されることはありません。 正常性エージェントは、Azure AD Connect Health にデータを送信しないように構成されています。 ユーザーは、以前の監視対象サーバーから正常性エージェントを手動でアンインストールする必要があります。
- Health エージェントをアンインストールせずにこの手順を実行した場合、Health エージェントに関連したエラー イベントがそのサーバー上に表示されます。
- 監視対象サービスのインスタンスに属するすべてのデータは、Microsoft Azure のデータ リテンション期間ポリシーに従って削除されます。

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>監視対象サービスのインスタンスのデータ収集と監視を無効にする
「[Azure AD Connect Health サービスからのサービス インスタンスの削除](how-to-connect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service)」をご覧ください。

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>監視対象サーバーのデータ収集と監視を無効にする
「[Azure AD Connect Health サービスからサーバーを削除するには](how-to-connect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service)」をご覧ください。

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Azure AD Connect Health でのすべての監視対象サービスのデータ収集と監視を無効にする
Azure AD Connect Health では、テナントの **すべて** の登録済みサービスのデータ収集を停止することもできます。 操作を実行する前に、すべてのグローバル管理者の慎重な検討と十分な確認をお勧めします。 プロセスが開始されると、Connect Health サービスはすべてのサービスのすべてのデータの受信、処理、およびレポートを停止します。 Connect Health サービスの既存のデータは 30 日を超えない範囲で保持されます。
特定のサーバーのデータ収集を停止する場合は、特定のサーバーの削除時の手順に従ってください。 テナントのデータ収集を停止するには、次の手順に従って、データの収集を停止し、テナントのすべてのサービスを削除します。

1. メイン ブレードの構成で **[全般設定]** をクリックします。 
2. ブレードの上部にある **[データ収集の停止]** ボタンをクリックします。 プロセスが開始されると、テナント構成設定のその他のオプションは使用できなくなります。  
 
   ![データ収集の停止](./media/reference-connect-health-user-privacy/gdpr4.png)
  
3. データ収集の停止による影響を受けるオンボード サービスの一覧を確認します。 
4. 正確なテナント名を入力し、 **[削除]** アクション ボタンを有効にします
5. **[削除]** をクリックし、すべてのサービスの削除を実行します。 Connect Health は、オンボード サービスから送信されたすべてのデータの受信、処理、レポートを停止します。 プロセス全体で最大 24 時間かかることがあります。 この手順は元に戻せないことに注意してください。 
6. プロセスが完了した後は、Connect Health に登録されているサービスは表示されなくなります。 

   ![データ収集の停止後](./media/reference-connect-health-user-privacy/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Azure AD Connect Health でのデータの収集と 監視を再度有効にする
以前に削除された監視対象サービスについて Azure AD Connect Health での監視を再度有効にするには、正常性エージェントをアンインストールし、すべてのサーバーに[正常性エージェントを再インストールする](how-to-connect-health-agent-install.md)必要があります。

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>すべての監視対象サービスのデータ収集と監視を再度有効にする

Azure AD Connect Health でテナントのデータ収集を再開することができます。 操作を実行する前に、すべてのグローバル管理者の慎重な検討と十分な確認をお勧めします。

>[!IMPORTANT]
> アクションが 24 時間無効になった後、次の手順が使用可能になります。
> データの収集を有効にすると、Connect Health の分析情報の提示とデータの監視では、以前に収集されたレガシ データは表示されません。 

1. メイン ブレードの構成で **[全般設定]** をクリックします。 
2. ブレードの上部にある **[データ収集の有効化]** ボタンをクリックします。 
 
   ![データ収集を有効にする](./media/reference-connect-health-user-privacy/gdpr6.png)
 
3. 正確なテナント名を入力し、 **[有効化]** ボタンをアクティブにします。
4. **[有効化]** ボタンをクリックし、Connect Health サービスのデータ収集の権限を許可します。 変更はすぐに適用されます。 
5. [インストール プロセス](how-to-connect-health-agent-install.md)に関する記事に従って監視対象のサーバーにエージェントを再インストールすると、ポータルにサービスが表示されます。  


## <a name="next-steps"></a>次のステップ
* [セキュリティ センターで Microsoft のプライバシー ポリシーを確認する](https://www.microsoft.com/trustcenter)
* [Azure AD Connect とユーザー プライバシー](reference-connect-user-privacy.md)

