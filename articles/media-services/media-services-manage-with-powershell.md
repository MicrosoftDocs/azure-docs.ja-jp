---
title: "PowerShell を使用して Azure Media Services アカウントを管理する"
description: "PowerShell コマンドレットを使用して Azure Media Services アカウントを管理する方法を説明します。"
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: 17a10c25-d94f-421c-b6bc-ae0958e2ac96
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2549c876ee35d6a7fa425d43e2f86d24131ca791
ms.openlocfilehash: 3d999d9e27844bc0164cc3572522b9ec022118a1


---
# <a name="manage-azure-media-services-accounts-with-powershell"></a>PowerShell を使用して Azure Media Services アカウントを管理する
> [!div class="op_single_selector"]
> * [ポータル](media-services-portal-create-account.md)
> * [PowerShell](media-services-manage-with-powershell.md)
> * [REST ()](https://docs.microsoft.com/rest/api/media/mediaservice)
> 
> [!NOTE]
> Azure Media Services アカウントを作成するには、Azure アカウントが必要です。 アカウントがない場合は、無料試用アカウントを数分で作成することができます。 詳細については、「<a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Azure の無料試用版サイト</a>」をご覧ください。
> 
> 

## <a name="overview"></a>Overview
この記事では、Azure Resource Manager フレームワークにある Azure Media Services (AMS) 用の Azure PowerShell コマンドレットについて説明します。 コマンドレットは、 **Microsoft.Azure.Commands.Media** 名前空間にあります。

## <a name="versions"></a>バージョン
**ApiVersion**: "2015-10-01"

## <a name="new-azurermmediaservice"></a>New-AzureRmMediaService
メディア サービスを作成します。

### <a name="syntax"></a>構文
パラメーター セット: StorageAccountIdParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccountId] <string> [-Tags <hashtable>]  [<CommonParameters>]

パラメーター セット: StorageAccountsParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccounts] <PSStorageAccount[]> [-Tags <hashtable>]  [<CommonParameters>]

### <a name="parameters"></a>parameters
**-ResourceGroupName &lt;String&gt;**

このメディア サービスが属するリソース グループの名前を指定します。

| 別名 | なし |
| --- | --- |
| 必須 |true |
| 位置 |0 |
| 既定値 |なし |
| パイプライン入力を許可する |true(ByPropertyName) |
| ワイルドカード文字を許可する |false |

**-AccountName &lt;String&gt;**

メディア サービスの名前を指定します。

| 別名 | Name |
| --- | --- |
| 必須 |true |
| 位置 |1 |
| 既定値 |なし |
| パイプライン入力を許可する |false |
| ワイルドカード文字を許可する |false |

**-Location &lt;String&gt;**

メディア サービスのリソースの場所を指定します。

| 別名 | なし |
| --- | --- |
| 必須 |true |
| 位置 |2 |
| 既定値 |なし |
| パイプライン入力を許可する |true(ByPropertyName) |
| ワイルドカード文字を許可する |false |

**-StorageAccountId &lt;String&gt;**

メディア サービスに関連付けられているプライマリ ストレージ アカウントを指定します。

* (Resource Manager API で作成された) 新しいストレージ アカウントのみがサポートされます。
* このストレージ アカウントは、存在し、メディア サービスと同じ場所を持っている必要があります。

| 別名 | なし |
| --- | --- |
| 必須 |true |
| 位置 |3 |
| 既定値 |なし |
| パイプライン入力を許可する |true(ByPropertyName) |
| パラメーター セット名 |StorageAccountIdParamSet |
| ワイルドカード文字を許可する |false |

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

メディア サービスに関連付けられたストレージ アカウントを指定します。

* (Resource Manager API で作成された) 新しいストレージ アカウントのみがサポートされます。
* このストレージ アカウントは、存在し、メディア サービスと同じ場所を持っている必要があります。
* プライマリとして指定できるストレージ アカウントは 1 つだけです。

| 別名 | なし |
| --- | --- |
| 必須 |true |
| 位置 |3 |
| 既定値 |なし |
| パイプライン入力を許可する |true(ByPropertyName) |
| パラメーター セット名 |StorageAccountsParamSet |
| ワイルドカード文字を許可する |false |

**-Tags &lt;Hashtable&gt;**

メディア サービスに関連付けられているタグのハッシュ テーブルを指定します。

* 例: @{"tag1"="value1";"tag2"=:value2"}

| 別名 | なし |
| --- | --- |
| 必須 |false |
| 位置 |named |
| 既定値 |なし |
| パイプライン入力を許可する |false |
| ワイルドカード文字を許可する |false |

**&lt;CommandParameters&gt;**

このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。

### <a name="inputs"></a>入力
入力の型は、コマンドレットにパイプできるオブジェクトの型です。

### <a name="outputs"></a>出力

出力の型は、コマンドレットが出力するオブジェクトの型です。

## <a name="set-azurermmediaservice"></a>Set-AzureRmMediaService
メディア サービスを更新します。

### <a name="syntax"></a>構文
    Set-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Tags <hashtable>] [-StorageAccounts <PSStorageAccount[]>]  [<CommonParameters>]

### <a name="parameters"></a>parameters
**-ResourceGroupName &lt;String&gt;**

このメディア サービスが属するリソース グループの名前を指定します。

| 別名 | なし |
| --- | --- |
| 必須 |true |
| 位置 |0 |
| 既定値 |なし |
| パイプライン入力を許可する |true(ByPropertyName) |
| ワイルドカード文字を許可する |false |

**-AccountName &lt;String&gt;**

メディア サービスの名前を指定します。

| 別名 | Name |
| --- | --- |
| 必須 |true |
| 位置 |1 |
| 既定値 |なし |
| パイプライン入力を許可する |true(ByPropertyName) |
| ワイルドカード文字を許可する |False |

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

メディア サービスに関連付けられたストレージ アカウントを指定します。

* (Resource Manager API で作成された) 新しいストレージ アカウントのみがサポートされます。
* このストレージ アカウントは、存在し、メディア サービスと同じ場所を持っている必要があります。
* プライマリとして指定できるストレージ アカウントは 1 つだけです。

| 別名 | なし |
| --- | --- |
| 必須 |false |
| 位置 |named |
| 既定値 |なし |
| パイプライン入力を許可する |true(ByPropertyName) |
| パラメーター セット名 |StorageAccountsParamSet |
| ワイルドカード文字を許可する |false |

**-Tags &lt;Hashtable&gt;**

このメディア サービスに関連付けられているタグのハッシュ テーブルを指定します。

* メディア サービスに関連付けられているタグは、お客様が指定した値に置き換えられます。

| 別名 | なし |
| --- | --- |
| 必須 |false |
| 位置 |named |
| 既定値 |なし |
| パイプライン入力を許可する |true(ByPropertyName) |
| ワイルドカード文字を許可する |false |

**&lt;CommandParameters&gt;**

このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。

### <a name="inputs"></a>入力
入力の型は、コマンドレットにパイプできるオブジェクトの型です。

### <a name="outputs"></a>出力

出力の型は、コマンドレットが出力するオブジェクトの型です。

## <a name="remove-azurermmediaservice"></a>Remove-AzureRmMediaService
メディア サービスを削除します。

### <a name="syntax"></a>構文
    Remove-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>parameters
**-ResourceGroupName &lt;String&gt;**

このメディア サービスが属するリソース グループの名前を指定します。

| 別名 | なし |
| --- | --- |
| 必須 |true |
| 位置 |0 |
| 既定値 |なし |
| パイプライン入力を許可する |true(ByPropertyName) |
| ワイルドカード文字を許可する |false |

**-AccountName &lt;String&gt;**

メディア サービスの名前を指定します。

| 別名 | なし |
| --- | --- |
| 必須 |true |
| 位置 |2 |
| 既定値 |なし |
| パイプライン入力を許可する |true(ByPropertyName) |
| ワイルドカード文字を許可する |False |

**&lt;CommandParameters&gt;**

このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。

### <a name="inputs"></a>入力
入力の型は、コマンドレットにパイプできるオブジェクトの型です。

### <a name="outputs"></a>出力

出力の型は、コマンドレットが出力するオブジェクトの型です。

## <a name="get-azurermmediaservice"></a>Get-AzureRmMediaService
リソース グループ内のすべてのメディア サービス、または指定された名前を持つメディア サービスを取得します。

### <a name="syntax"></a>構文
ParameterSet: ResourceGroupParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string>  [<CommonParameters>]    

ParameterSet: AccountNameParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>parameters
**-ResourceGroupName &lt;String&gt;**

このメディア サービスが属するリソース グループの名前を指定します。

| 別名 | なし |
| --- | --- |
| 必須 |true |
| 位置 |0 |
| 既定値 |なし |
| パイプライン入力を許可する |true(ByPropertyName) |
| パラメーター セット名 |ResourceGroupParameterSet、AccountNameParameterSet |

ワイルドカード文字を許可する   false

**-AccountName &lt;String&gt;**

メディア サービスの名前を指定します。

| 別名 | なし |
| --- | --- |
| 必須 |true |
| 位置 |1 |
| 既定値 |なし |
| パイプライン入力を許可する |true(ByPropertyName) |
| パラメーター セット名 |AccountNameParameterSet |
| ワイルドカード文字を許可する |false |

**&lt;CommandParameters&gt;**

このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。

### <a name="inputs"></a>入力
入力の型は、コマンドレットにパイプできるオブジェクトの型です。

### <a name="outputs"></a>出力

出力の型は、コマンドレットが出力するオブジェクトの型です。

## <a name="get-azurermmediaservicekeys"></a>Get-AzureRmMediaServiceKeys
メディア サービスのキーを取得します。

### <a name="syntax"></a>構文
    Get-AzureRmMediaServiceKeys [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>parameters
**-ResourceGroupName &lt;String&gt;**

このメディア サービスが属するリソース グループの名前を指定します。

| 別名 | なし |
| --- | --- |
| 必須 |true |
| 位置 |0 |
| 既定値 |なし |
| パイプライン入力を許可する |true(ByPropertyName) |
| ワイルドカード文字を許可する |false |

**-AccountName &lt;String&gt;**

メディア サービスの名前を指定します。

| 別名 | なし |
| --- | --- |
| 必須 |true |
| 位置 |1 |
| 既定値 |なし |
| パイプライン入力を許可する |true(ByPropertyName) |
| ワイルドカード文字を許可する |false |

**&lt;CommandParameters&gt;**

このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。

### <a name="inputs"></a>入力
入力の型は、コマンドレットにパイプできるオブジェクトの型です。

### <a name="outputs"></a>出力

出力の型は、コマンドレットが出力するオブジェクトの型です。

## <a name="set-azurermmediaservicekey"></a>Set-AzureRmMediaServiceKey
メディア サービスのプライマリまたはセカンダリ キーを再生成します。

### <a name="syntax"></a>構文
    Set-AzureRmMediaServiceKey [-ResourceGroupName] <string> [-AccountName] <string> [-KeyType] <KeyType> {Primary | Secondary}  [<CommonParameters>]

### <a name="parameters"></a>parameters
**-ResourceGroupName &lt;String&gt;**

このメディア サービスが属するリソース グループの名前を指定します。

| 別名 | なし |
| --- | --- |
| 必須 |true |
| 位置 |0 |
| 既定値 |なし |
| パイプライン入力を許可する |true(ByPropertyName) |
| ワイルドカード文字を許可する |false |

**-AccountName &lt;String&gt;**

メディア サービスの名前を指定します。

| 別名 | なし |
| --- | --- |
| 必須 |true |
| 位置 |1 |
| 既定値 |なし |
| パイプライン入力を許可する |true(ByPropertyName) |
| ワイルドカード文字を許可する |false |

**-KeyType &lt;KeyType&gt;**

メディア サービスのキーの種類を指定します。

* Primary または Secondary

| 別名 | なし |
| --- | --- |
| 必須 |true |
| 位置 |2 |
| 既定値 |なし |
| パイプライン入力を許可する |false |
| ワイルドカード文字を許可する |false |

**&lt;CommandParameters&gt;**

このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。

### <a name="inputs"></a>入力
入力の型は、コマンドレットにパイプできるオブジェクトの型です。

### <a name="outputs"></a>出力

出力の型は、コマンドレットが出力するオブジェクトの型です。

## <a name="sync-azurermmediaservicestoragekeys"></a>Sync-AzureRmMediaServiceStorageKeys
メディア サービスに関連付けられたストレージ アカウントのストレージ アカウント キーを同期します。

### <a name="syntax"></a>構文
    Sync-AzureRmMediaServiceStorageKeys [-ResourceGroupName] <string> [-MediaServiceAccountName] <string>    [-StorageAccountId] <string>  [<CommonParameters>]

### <a name="parameters"></a>parameters
**-ResourceGroupName &lt;String&gt;**

このメディア サービスが属するリソース グループの名前を指定します。

| 別名 | なし |
| --- | --- |
| 必須 |true |
| 位置 |0 |
| 既定値 |なし |
| パイプライン入力を許可する |true(ByPropertyName) |
| ワイルドカード文字を許可する |false |

**-AccountName &lt;String&gt;**

メディア サービスの名前を指定します。

| 別名 | なし |
| --- | --- |
| 必須 |true |
| 位置 |1 |
| 既定値 |なし |
| パイプライン入力を許可する |true(ByPropertyName) |
| ワイルドカード文字を許可する |false |

**-StorageAccountId &lt;String&gt;**

メディア サービスに関連付けられたストレージ アカウントを指定します。

| 別名 | ID |
| --- | --- |
| 必須 |true |
| 位置 |2 |
| 既定値 |なし |
| パイプライン入力を許可する |true(ByPropertyName) |
| ワイルドカード文字を許可する |false |

**&lt;CommandParameters&gt;**

このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。

### <a name="inputs"></a>入力
入力の型は、コマンドレットにパイプできるオブジェクトの型です。

### <a name="outputs"></a>出力

出力の型は、コマンドレットが出力するオブジェクトの型です。

## <a name="next-step"></a>次のステップ
Media Services のラーニング パスをご覧ください。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO5-->


