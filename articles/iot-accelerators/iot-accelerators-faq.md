---
title: IoT ソリューション アクセラレータに関してよく寄せられる質問 - Azure | Microsoft Docs
description: この記事では、IoT ソリューション アクセラレータに関してよく寄せられる質問に回答します。 GitHub リポジトリへのリンクも含まれています。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 1fd2b8461bd66c826dc4890c331b740c4703f896
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96903991"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>IoT ソリューション アクセラレータに関してよく寄せられる質問

コネクテッド ファクトリ固有の [FAQ](iot-accelerators-faq-cf.md) に関するページもご覧ください。

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>ソリューション アクセラレータのソース コードはどこで入手できますか。

ソース コードは、次の GitHub リポジトリに格納されています。

* [接続済みファクトリ ソリューション アクセラレータ](https://github.com/Azure/azure-iot-connected-factory)
* [デバイス シミュレーション ソリューション アクセラレータ](https://github.com/Azure/device-simulation-dotnet)

### <a name="where-can-i-find-the-remote-monitoring-and-predictive-maintenance-solution-accelerators"></a>リモート監視と予測メンテナンスのソリューション アクセラレータはどこで入手できますか?

2020 年 12 月 10 日の時点で、リモート監視と予測メンテナンスのアクセラレータは [Azure IoT Solution Accelerators ](https://www.azureiotsolutions.com/Accelerators)のサイトから削除され、新しいデプロイでは利用できなくなりました。 両方のアクセラレータの GitHub リポジトリがアーカイブされました。 コードにはだれでもアクセスできますが、リポジトリに新しい投稿が寄せられることはありません。

### <a name="what-happens-to-my-existing-remote-monitoring-and-predictive-maintenance-deployments"></a>既存のリモート監視と予測メンテナンスのデプロイはどうなりますか?

リモート監視と予測メンテナンスのソリューション アクセラレータが削除されても、既存のデプロイは影響を受けず、引き続き機能します。 フォークされたリポジトリも影響を受けません。 GitHub のマスター リポジトリはアーカイブされています。

### <a name="how-do-i-deploy-device-simulation-solution-accelerator"></a>デバイス シミュレーション ソリューション アクセラレータはどのようにデプロイしますか?

デバイス シミュレーション ソリューション アクセラレータをデプロイするには、[デバイス シミュレーション](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md)の GitHub リポジトリを参照してください。

### <a name="where-can-i-find-information-about-the-removed-solution-accelerators"></a>削除されたソリューション アクセラレータに関する情報はどこで入手できますか?

前のバージョンのサイトの次のページを参照してください。

* [リモート監視](/previous-versions/azure/iot-accelerators/about-iot-accelerators)
* [予測メンテナンス](/previous-versions/azure/iot-accelerators/about-iot-accelerators)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>ソリューション アクセラレータ用のデバイス クライアントの開発では、どの SDK を使用できますか?

[Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) GitHub リポジトリで、異なる言語 (C、.NET、Java、Node.js、Python) IoT デバイス SDK へのリンクを見つけることができます。

DevKit デバイスを使用する場合は、[IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk) GitHub リポジトリで、リソースとサンプルを見つけることができます。

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>サービス管理者が自分のサブスクリプションと特定の Azure AD テナントの間のディレクトリ マッピングを変更するには どうすればよいですか

「[既存のサブスクリプションを Azure AD ディレクトリに追加する方法](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)」をご覧ください

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>組織アカウントでログインするときにサービス管理者または共同管理者を変更するにはどうすればよいですか

[組織アカウントでログインしたときのサービス管理者および共同管理者の変更](https://azure.microsoft.com/support/changing-service-admin-and-co-admin)に関するサポート記事をご覧ください。

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>次のエラーが表示されるのはなぜですか。 「お使いのアカウントにはソリューションを作成する適切なアクセス許可がありません。 アカウント管理者に確認するか、別のアカウントを使用してください。」

次の図を見てみましょう。

![アクセス許可のフローチャート](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> 自分が Azure AD テナントの全体管理者になっていることとサブスクリプションの共同管理者になっていることを確認した後もエラーが表示される場合は、ユーザーを削除してから必要なアクセス許可を再度割り当てるよう、アカウント管理者に依頼してください。 その場合はまず、ユーザーを全体管理者として追加したうえで、Azure サブスクリプションの共同管理者としてそのユーザーを追加することになります。 問題が解決しない場合は、[ヘルプとサポート](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)にお問い合わせください。

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Azure サブスクリプションがあるのに次のエラーが表示されるのはなぜですか。 「構成済みソリューションを作成するには Azure サブスクリプションが必要です。 数分で無料試用版のアカウントを作成することができます。」

Azure サブスクリプションが確かにある場合は、サブスクリプションのテナント マッピングを調べ、ドロップダウンで正しいテナントが選択されていることを確認してください。 テナントが正しいことを確認したら、上の図に従って、サブスクリプションとこの Azure AD テナントのマッピングを確認してください。

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Azure portal でのリソース グループの削除と、azureiotsolutions.com のソリューション アクセラレータで削除をクリックすることの違いは何ですか?

* [azureiotsolutions.com](https://www.azureiotsolutions.com/) でソリューション アクセラレータを削除すると、ソリューション アクセラレータの作成時にデプロイしたすべてのリソースが削除されます。 リソース グループにリソースを追加してある場合、それらも削除されます。
* [Azure Portal](https://portal.azure.com) でリソース グループを削除した場合は、そのリソース グループ内のリソースのみが削除されます。 さらに、ソリューション アクセラレータに関連付けられている Azure Active Directory アプリケーションを削除する必要があります。

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Azure IoT ソリューション アクセラレータで既存の投資の活用を続行できますか?

はい。 現在存在しているすべてのソリューションは、Azure サブスクリプションで動作を続行し、ソース コードは GitHub で利用可能であり続けます。

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>サブスクリプションでプロビジョニングできる IoT Hub インスタンスはいくつですか?

既定では、[サブスクリプションごとに 10 個の IoT Hub](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits) をプロビジョニングできます。 この上限は、[Azure サポート チケット](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)を作成して引き上げることができます。 このため、ソリューション アクセラレータごとに新しい IoT ハブをプロビジョニングするため、特定のサブスクリプションでプロビジョニングできるソリューション アクセラレータの数は最大 10 個になります。

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>サブスクリプションには何個の Azure Cosmos DB インスタンスをプロビジョニングできますか?

50 個です。 [Azure サポート チケット](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)を作成してこの上限を引き上げることができますが、既定では、サブスクリプションごとにプロビジョニングできる Cosmos DB インスタンスは 50 個のみです。

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Microsoft Azure for DreamSpark があれば、ソリューション アクセラレータを作成できますか?

> [!NOTE]
> Microsoft Azure for DreamSpark は現在、学生向けの Microsoft Imagine として知られています。

現時点では、[Microsoft Azure for DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) アカウントを使用して、ソリューション アクセラレータを作成することはできません。 ただし、[Azure の無料試用版アカウント](https://azure.microsoft.com/free/)を数分で作成して、ソリューション アクセラレータを作成できるようにすることは可能です。

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Azure AD テナントを削除するにはどうすればよいですか。

[Azure AD テナントの削除方法](/archive/blogs/ericgolpe/walkthrough-of-deleting-an-azure-ad-tenant)に関する Eric Golpe のブログ記事を参照してください。

### <a name="next-steps"></a>次のステップ

IoT ソリューション アクセラレータの他の機能についても学習できます。

* [接続済みファクトリ ソリューション アクセラレータの展開](quickstart-connected-factory-deploy.md)
* [徹底的な IoT セキュリティ](../iot-fundamentals/iot-security-ground-up.md)