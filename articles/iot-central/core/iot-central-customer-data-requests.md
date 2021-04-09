---
title: Azure IoT Central の顧客データ要求機能 | Microsoft Docs
description: この記事では、Azure IoT Central アプリケーションで顧客データを識別、削除、およびエクスポートする方法について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: dabcadea96f4ced5bdf73a35ef533e6d290595c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87001878"
---
# <a name="azure-iot-central-customer-data-request-features"></a>Azure IoT Central の顧客データ要求機能

Azure IoT Central は、モノのインターネット (IoT) 資産の接続、監視、管理を簡単かつ大規模に行い、IoT データから深い洞察を作成し、情報に基づくアクションを起こせるようにする、フルマネージド IoT の SaaS (サービスとしてのソフトウェア) ソリューションです。

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>顧客データの識別

Azure Active Directory オブジェクト ID は、ユーザーの識別とロールの割り当てに使用されます。 Azure IoT Central のポータルには、ロール割り当てのためのユーザーの電子メール アドレスが表示されますが、格納されるのは Azure Active Directory オブジェクト ID のみで、電子メール アドレスは Azure Active Directory から動的にクエリが実行されます。 Azure IoT Central の管理者は、Azure IoT Central アプリケーションのユーザー管理セクションで、アプリケーション ユーザーの表示、エクスポート、削除を行うことができます。

アプリケーション内で、アラートを受信するように電子メール アドレスを構成できます。 この場合、電子メール アドレスは IoT Central 内に格納され、アプリ内のアカウント管理ページから管理する必要があります。

Microsoft は、デバイスについて情報を保持せず、デバイスがユーザーの関連付けをできるようにするデータにアクセスできません。 Azure IoT Central で管理されているデバイスの多くは個人用デバイスではなく、たとえば自動販売機やコーヒー メーカーなどです。 しかし、お客様が一部のデバイスで個人を特定できるようにすることを検討し、自己判断で、自身の資産やデバイスと個人を結び付けるインベントリ追跡システムを維持する場合があります。 Azure IoT Central はデバイスに関連付けられたすべてのデータを個人データであるかのように管理し、格納します。

Microsoft Enterprise Services を使用すると、システム生成ログと呼ばれる情報がいくつか生成されます。 これらのログは、ユーザー アクティビティではなく個々のデバイスに関連するサービスと診断データ内で実行された実際のアクションで構成されています。 Azure IoT Central によって生成されたログは、アプリケーション管理者がアクセスしたりエクスポートしたりすることはできません。

## <a name="deleting-customer-data"></a>顧客データの削除

ユーザー データを削除する機能は、IoT Central の管理ページでのみ提供されます。 アプリケーション管理者は、削除するユーザーを選択し、アプリケーションの右上隅の **[削除]** を選択するとレコードを削除できます。 アプリケーション管理者は、対象のアプリケーションとの関連がなくなった個々のアカウントも削除できます。

ユーザーが削除されると、そのユーザーには以後アラートが電子メールで送信されなくなります。 ただし、ユーザーの電子メール アドレスは、構成された各アラートから個別に削除する必要があります。

## <a name="exporting-customer-data"></a>顧客データのエクスポート

ユーザー データをエクスポートする機能は、IoT Central の管理ページでのみ提供されます。 割り当てられているロールなどの顧客データは、アプリケーション管理者が選択し、コピーし、貼り付けることができます。

## <a name="links-to-additional-documentation"></a>その他のドキュメントへのリンク

ロールの定義を含む、アカウント管理の詳細については、「[How to administer your application](howto-administer.md)」(アプリケーションを管理する方法) を参照してください。
