---
title: 削除されたアプリを復元する
description: Azure App Service で削除されたアプリを復元する方法について説明します。 誤ってアプリを削除した場合の問題を解決します。
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: 04e496806f2c388eb3a69df1b4cc3897b8132f6c
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962912"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>PowerShell を使用して、削除された App Service アプリを復元する

Azure App Service で誤ってアプリを削除した場合は、[Az PowerShell モジュール](/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0)のコマンドを使用して復元できます。

> [!NOTE]
> - 削除されたアプリは、最初の削除から 30 日後にシステムから削除されます。 削除されたアプリは、復元できません。
> - 削除の取り消し機能は、従量課金プランではサポートされていません。
> - App Service Environment で実行されている App Service アプリでは、スナップショットはサポートされていません。 そのため、App Service Environment で実行されている App Service アプリでは、削除の取り消し機能と複製機能はサポートされていません。
>

## <a name="re-register-app-service-resource-provider"></a>App Service リソース プロバイダーを再登録します。

一部のお客様では、削除されたアプリの一覧を取得できないという問題が発生する可能性があります。 この問題を解決するには、次のコマンドを実行します。

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>削除したアプリの一覧を表示する

削除されたアプリのコレクションを取得するには、`Get-AzDeletedWebApp` を使用します。

削除された特定のアプリの詳細については、次を使用できます。

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

詳細情報には次のものが含まれます。

- **DeletedSiteId**:同じ名前の複数のアプリが削除されたシナリオで使用される、アプリの一意の識別子
- **SubscriptionID**:削除されたリソースを含むサブスクリプション
- **[場所]** :元のアプリの場所
- **ResourceGroupName**:元のリソース グループの名前
- **Name**:元のアプリの場所。
- **Slot**: スロットの名前。
- **削除時刻**:アプリが削除された日時  

## <a name="restore-deleted-app"></a>削除したアプリを復元する

>[!NOTE]
> `Restore-AzDeletedWebApp` は、関数アプリではサポートされていません。

復元するアプリが特定されたら、`Restore-AzDeletedWebApp` を使用して復元できます。

```powershell
Restore-AzDeletedWebApp -TargetResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> アプリの一部としてデプロイ スロットは復元されません。 ステージング スロットを復元する必要がある場合は、`-Slot <slot-name>` フラグを使用します。
>

コマンドの入力は次のとおりです。

- **ターゲット リソース グループ**:アプリが復元されるターゲット リソース グループ
- **Name**:アプリの名前。グローバルに一意である必要があります。
- **TargetAppServicePlanName**:アプリにリンクされている App Service プラン

既定では `Restore-AzDeletedWebApp` によって、アプリの構成とすべてのコンテンツの両方が復元されます。 コンテンツのみを復元する場合は、このコマンドレットで `-RestoreContentOnly` フラグを使用します。

> [!NOTE]
> アプリが App Service Environment 上でホストされ、そこから削除された場合は、対応する App Service Environment が引き続き存在する場合にのみ復元できます。
>

コマンドレットの完全な参照は、次をご覧ください。[Restore-AzDeletedWebApp](/powershell/module/az.websites/restore-azdeletedwebapp)。