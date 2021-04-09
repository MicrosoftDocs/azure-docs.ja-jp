---
title: IT Service Management Connector - Azure Monitor のセキュア エクスポート
description: この記事では、ITSM 製品/サービスを Azure Monitor のセキュア エクスポートに接続して、ITSM 作業項目を一元的に監視および管理する方法について説明します。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 330b97e66ba1a9b95f2ef7a34a7ea046414b8bd8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037515"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>セキュア エクスポートを使用して Azure を ITSM ツールに接続する

この記事では、セキュア エクスポートを使用して、IT Service Management (ITSM) 製品またはサービスの間の接続を構成する方法について説明します。

セキュア エクスポートは、[IT Service Management Connector (ITSMC)](./itsmc-overview.md) の更新されたバージョンです。 どちらのバージョンでも、Azure Monitor がアラートを送信するときに、ITSM ツールで作業項目を作成できます。 この機能には、メトリック、ログ、およびアクティビティ ログ アラートが含まれます。

ITSMC では、ユーザー名とパスワードの資格情報が使用されます。 セキュア エクスポートでは Azure Active Directory (Azure AD) が使用されるため、認証がより強力です。 Azure AD は、Microsoft のクラウドベースの ID およびアクセス管理サービスです。 ユーザーがサインインしたり、内部または外部のリソースにアクセスしたりするために役立ちます。 ITSM と共に Azure AD を使用すると、外部システムに送信された Azure アラートを識別できます (Azure AD アプリケーション ID を使用)。

> [!NOTE]
> セキュア エクスポートを使用して Azure を ITSM ツールに接続する機能は、プレビュー段階です。

## <a name="secure-export-architecture"></a>セキュア エクスポートのアーキテクチャ

セキュア エクスポートのアーキテクチャには、次の新機能が導入されています。

* **新しいアクション グループ**:アラートは、ITSMC で使用される ITSM アクション グループではなく、セキュア Webhook アクション グループを介して ITSM ツールに送信されます。
* **Azure AD の認証**:認証は、ユーザー名とパスワードの資格情報ではなく Azure AD を通じて行われます。

## <a name="secure-export-data-flow"></a>セキュア エクスポートのデータ フロー

セキュア エクスポートのデータ フローのステップは次のとおりです。

1. Azure Monitor が、セキュア エクスポートを使用するように構成されたアラートを送信します。
2. アラートのペイロードが、セキュア Webhook アクションによって ITSM ツールに送信されます。
3. アラートが ITSM ツールに送られることが承認されているかどうかについて、ITSM アプリケーションが Azure AD で確認します。
4. アラートが承認されている場合、アプリケーションは次のように動作します。
   
   1. ITSM ツールで作業項目 (インシデントなど) を作成します。
   2. 構成項目 (CI) の ID を顧客管理データベース (CMDB) にバインドします。

![ITSM ツールが Azure AD、Azure アラート、およびアクション グループと通信する方法を示す図。](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="benefits-of-secure-export"></a>セキュア エクスポートの利点

統合の主な利点は次のとおりです。

* **認証の強化**:Azure AD は、ITSMC で一般的に発生するタイムアウトを発生させることなく、より安全な認証を提供します。
* **アラートが ITSM ツールで解決される**:メトリック アラートは、"発生" と "解決済み" の状態を実装しています。 条件が満たされると、アラートの状態は "発生" になります。 条件が満たされなくなると、アラートの状態は "解決済み" になります。 ITSMC では、アラートを自動的に解決することはできません。 セキュア エクスポートでは、解決された状態が ITSM ツールに送られるため、自動的に更新されます。
* **[共通アラート スキーマ](./alerts-common-schema.md)** :ITSMC では、アラートのペイロードのスキーマは、アラートの種類によって異なります。 セキュア エクスポートでは、すべてのアラートの種類に共通のスキーマがあります。 この共通スキーマには、すべてのアラートの種類の CI が含まれています。 すべてのアラートの種類で、その CI を CMDB にバインドすることができます。

## <a name="next-steps"></a>次のステップ

* [Azure アラートから ITSM 作業項目を作成する](./itsmc-overview.md)