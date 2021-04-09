---
title: IT Service Management Connector - Azure Monitor のセキュア エクスポート - Azure の構成
description: この記事では、ITSM 製品/サービスを Azure Monitor のセキュア エクスポートに接続して ITSM 作業項目を一元的に監視および管理するために、Azure を構成する方法について説明します。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 01/03/2021
ms.openlocfilehash: 8eb9430e3d280c52cf84c61f0a44cb12152ac054
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037542"
---
# <a name="configure-azure-to-connect-itsm-tools-using-secure-export"></a>セキュア エクスポートを使用して Azure を ITSM ツールに接続するように Azure を構成する

この記事では、"セキュア エクスポート" を使用するために Azure を構成する方法について説明します。
"セキュア エクスポート" を使用するには、次の手順に従います。

1. [アプリを Azure AD に登録する。](./itsm-connector-secure-webhook-connections-azure-configuration.md#register-with-azure-active-directory)
1. [サービス プリンシパルを定義します。](./itsm-connector-secure-webhook-connections-azure-configuration.md#define-service-principal)
1. [セキュア Webhook アクション グループを作成します。](./itsm-connector-secure-webhook-connections-azure-configuration.md#create-a-secure-webhook-action-group)
1. パートナー環境を構成します。
    セキュア エクスポートは、次の ITSM ツールとの接続をサポートしています。
    * [ServiceNow](./itsmc-secure-webhook-connections-servicenow.md)
    * [BMC Helix](./itsmc-secure-webhook-connections-bmc.md)

## <a name="register-with-azure-active-directory"></a>Azure Active Directory に登録する

アプリケーションを Azure AD に登録するには、次の手順に従います。

1. 「[Microsoft ID プラットフォームにアプリケーションを登録する](../../active-directory/develop/quickstart-register-app.md)」の手順に従います。
2. Azure AD で、 **[アプリケーションの公開]** を選択します。
3. **[アプリケーション ID の URI]** に **[設定]** を選択します。

   [![[アプリケーション ID の URI] を設定するためのオプションのスクリーンショット。](media/itsm-connector-secure-webhook-connections-azure-configuration/azure-ad.png)](media/itsm-connector-secure-webhook-connections-azure-configuration/azure-ad-expand.png#lightbox)
4. **[保存]** を選択します。

## <a name="define-service-principal"></a>サービス プリンシパルを定義する

アクション グループ サービスはファースト パーティ アプリケーションです。そのため、ServiceNow を使用して認証するために、AAD アプリケーションから認証トークンを取得するためのアクセス許可があります。
省略可能な手順として、作成したアプリのマニフェストにアプリケーション ロールを定義できます。これによって、さらにアクセスを制限して、この特定のロールを持つ特定のアプリケーションのみがメッセージを送信できるようになります。 このロールは、アクション グループのサービス プリンシパルに割り当てられている必要があります (テナント管理特権が必要です)。

この手順は、同じ [PowerShell コマンド](../alerts/action-groups.md#secure-webhook-powershell-script)を使用して実行できます。

## <a name="create-a-secure-webhook-action-group"></a>セキュア Webhook アクション グループを作成する

アプリケーションが Azure AD に登録された後、アクション グループでセキュア Webhook アクションを使用することによって、Azure アラートに基づいて ITSM ツールで作業項目を作成できます。

アクション グループでは、Azure アラートのアクションをトリガーする再利用可能なモジュール化された方法が提供されます。 Azure Portal で、アクション グループと、メトリック アラート、アクティビティ ログ アラート、および Azure Log Analytics アラートを使用できます。
アクション グループの詳細については、「[Azure Portal でのアクション グループの作成および管理](../alerts/action-groups.md)」を参照してください。

アクションに Webhook を追加するには、セキュア Webhook の手順に従います。

1. [Azure portal](https://portal.azure.com/) で、 **[モニター]** を検索して選択します。 **[モニター]** ウィンドウでは、すべての監視設定とデータが 1 つのビューにまとめられています。
2. **[アラート]**  >  **[アクションの管理]** を選択します。
3. [[アクション グループの追加]](../alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal) を選択し、フィールドに入力します。
4. **[アクション グループ名]** ボックスおよび **[短い名前]** ボックスに名前を入力します。 短い名前は、通知がこのグループを使用して送信されるときに長い名前の代わりに使用されます。
5. **[セキュア Webhook]** を選択します。
6. 次の詳細を選択します。
   1. 登録した Azure Active Directory インスタンスのオブジェクト ID を選択します。
   2. URI に、[ITSM ツール環境](#configure-the-itsm-tool-environment)からコピーした Webhook URL を貼り付けます。
   3. **[共通アラート スキーマを有効にする]** に **[はい]** を設定します。 

   次の図は、セキュア Webhook アクションの構成の例を示しています。

   ![セキュア Webhook アクションを示すスクリーンショット。](media/itsm-connector-secure-webhook-connections-azure-configuration/secure-webhook.png)

## <a name="configure-the-itsm-tool-environment"></a>ITSM ツール環境を構成する

構成には次の 2 つのステップが含まれます。

1. セキュア エクスポート定義の URI を取得します。
2. ITSM ツールのフローに従った定義。

## <a name="next-steps"></a>次のステップ

* [ServiceNow セキュア エクスポートの構成](./itsmc-secure-webhook-connections-servicenow.md)
* [BMC セキュア エクスポートの構成](./itsmc-secure-webhook-connections-bmc.md)
