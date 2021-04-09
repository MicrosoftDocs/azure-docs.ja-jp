---
title: Datadog のトラブルシューティング - Azure パートナー ソリューション
description: この記事では、Azure での Datadog のトラブルシューティングに関する情報を提供します。
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: a8bb28892fe42215876b5cc8771ae73c7d2aab7f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744009"
---
# <a name="troubleshooting-datadog-on-azure"></a>Azure での Datadog のトラブルシューティング

このドキュメントには、Datadog を使用するソリューションのトラブルシューティングに関する情報が含まれています。

## <a name="unable-to-create-datadog-resource"></a>Datadog リソースを作成できない

Azure Datadog 統合を設定するには、Azure サブスクリプションに対する **所有者** アクセス権が必要です。 設定を開始する前に、適切なアクセス権があることをご確認ください。

## <a name="single-sign-on-errors"></a>シングル サインオン エラー

**シングル サインオンの設定を保存できない** - このエラーは、Datadog SAML 識別子を使用している別のエンタープライズ アプリがある場合に発生します。 使用しているアプリを検索するには、[基本的な SAML 構成] セクションの **[編集]** を選択します。

この問題を解決するには、他のアプリを無効にするか、他のアプリをエンタープライズ アプリとして使用して Datadog で SAML SSO を設定します。 他のアプリを使用する場合は、アプリに[必要な設定](create.md#configure-single-sign-on)があることを確認します。

**シングル サインオンの設定ページにアプリが表示されない** - 最初に、アプリケーション ID を検索します。 結果が表示されない場合は、アプリの SAML の設定を調べます。 グリッドには、SAML の設定が正しいアプリのみが表示されます。 

識別子の URL が `https://us3.datadoghq.com/account/saml/metadata.xml` である必要があります。

応答 URL は `https://us3.datadoghq.com/account/saml/assertion` である必要があります。

次の図は正しい値を示したものです。
  
:::image type="content" source="media/troubleshoot/troubleshooting.png" alt-text="AAD で Datadog アプリケーションの SAML の設定を確認します。" border="true":::

**テナントに招待されたゲスト ユーザーがシングル サインオンにアクセスできない** - 一部のユーザーは Azure portal に 2 つのメール アドレスを持っています。 通常、1 つのメールはユーザー プリンシパル名 (UPN) で、もう 1 つのメールは代替メールです。

ゲスト ユーザーを招待するときは、ホーム テナントの UPN を使用します。 UPN を使用すると、シングル サインオン プロセスの間にメール アドレスの同期が維持されます。 UPN は、ユーザーの Azure portal の右上隅にあるメール アドレスを検索することで見つけることができます。
  
## <a name="logs-not-being-emitted"></a>ログが送信されない

Datadog にログを送信するのは、Azure Monitor リソースのログ カテゴリの一覧に含まれるリソースだけです。 リソースから Datadog にログが送信されているかどうかを確認するには、特定のリソースの Azure 診断設定に移動します。 Datadog の診断設定があることを確認します。

:::image type="content" source="media/troubleshoot/diagnostic-setting.png" alt-text="Azure リソースでの Datadog の診断設定" border="true":::

## <a name="metrics-not-being-emitted"></a>メトリックが送信されない

Datadog リソースには、適切な Azure サブスクリプションで **監視閲覧者** ロールが割り当てられます。 このロールにより、Datadog リソースはメトリックを収集し、それらのメトリックを Datadog に送信できます。

リソースに適切なロールが割り当てられていることを確認するには、Azure portal を開き、サブスクリプションを選択します。 左側のペインで **[アクセス制御 (IAM)]** を選択します。 Datadog リソースの名前を検索します。 次に示すように、Datadog リソースに **[監視閲覧者]** ロールが割り当てられていることを確認します。

:::image type="content" source="media/troubleshoot/datadog-role-assignment.png" alt-text="Azure サブスクリプションでの Datadog ロールの割り当て" border="true":::

## <a name="datadog-agent-installation-fails"></a>Datadog エージェントのインストールが失敗する

Azure と Datadog の統合により、仮想マシンまたは App Service に Datadog エージェントをインストールできるようになります。 Datadog エージェントの構成には、[API キー] 画面で **[既定のキー]** として選択されている API キーが使用されます。 既定のキーが選択されていない場合、Datadog エージェントのインストールは失敗します。

Datadog エージェントが正しくないキーで構成されている場合は、[API キー] 画面に移動し、 **[既定のキー]** を変更します。 新しい API キーを使用して仮想マシンを構成するには、Datadog エージェントをアンインストールして再インストールする必要があります。

## <a name="next-steps"></a>次のステップ

Datadog の[インスタンスの管理](manage.md)について確認します。
