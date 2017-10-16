---
title: "Azure AD v2 Android の概要 - セットアップ | Microsoft Docs"
description: "Android アプリがアクセス トークンを取得し、Microsoft Graph API またはアクセス トークンを必要とする API を、Azure Active Directory v2 エンドポイントから呼び出す方法"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: a43d7e30a6f4176afba27f0de2c2c116df741080
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
## <a name="set-up-your-project"></a>プロジェクトの設定

> 代わりにこのサンプルの Android Studio プロジェクトをダウンロードしますか。 [プロジェクトをダウンロード](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)したら[構成](#create-an-application-express)手順に進み、実行前にコード サンプルを構成します。


### <a name="create-a-new-project"></a>新しいプロジェクトを作成する 
1.  Android Studio を開き、[`File`] \(ファイル)  >  [`New`] \(新規)  >  [`New Project`] \(新しいプロジェクト) に移動します。
2.  アプリケーションに名前を付けて、[`Next`] \(次へ) をクリックします。
3.  *API 21 以降 (Android 5.0)* を選択して、[`Next`] \(次へ) をクリックします。
4.  [`Empty Activity`] \(空のアクティビティ) はそのままにして [`Next`] \(次へ) をクリックし、[`Finish`] \(完了) をクリックします。


### <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>プロジェクトへの Microsoft Authentication Library (MSAL) の追加
1.  Android Studio で、[`Gradle Scripts`]  >  [`build.gradle (Module: app)`] \(Gradle のビルド) に移動します。
2.  [`Dependencies`] \(依存関係) で次のコードをコピーして貼り付けます。

```ruby  
compile ('com.microsoft.identity.client:msal:0.1.+') {
    exclude group: 'com.android.support', module: 'appcompat-v7'
}
compile 'com.android.volley:volley:1.0.0'
```

<!--start-collapse-->
### <a name="about-this-package"></a>このパッケージについて

上記パッケージにより、Microsoft Authentication Library (MSAL) がインストールされます。 MSAL は、Azure Active Directory v2 エンドポイントで保護される API へのアクセスで使用するユーザー トークンの取得、キャッシュ、更新を処理します。
<!--end-collapse-->

## <a name="create-your-applications-ui"></a>アプリケーションの UI の作成

1.  `activity_main.xml` を開きます ([`res`]  >  [`layout`] \(レイアウト) の順)。
2.  アクティビティのレイアウトを `android.support.constraint.ConstraintLayout` やその他から `LinearLayout` に変更します。
3.  プロパティ `android:orientation="vertical"` をノード `LinearLayout` に追加します。
4.  次のコードをノード `LinearLayout` にコピーして貼り付け、現在の内容を置き換えます。

```xml
<TextView
    android:text="Welcome, "
    android:textColor="#3f3f3f"
    android:textSize="50px"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginLeft="10dp"
    android:layout_marginTop="15dp"
    android:id="@+id/welcome"
    android:visibility="invisible"/>

<Button
    android:id="@+id/callGraph"
    android:text="Call Microsoft Graph"
    android:textColor="#FFFFFF"
    android:background="#00a1f1"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginTop="200dp"
    android:textAllCaps="false" />

<TextView
    android:text="Getting Graph Data..."
    android:textColor="#3f3f3f"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginLeft="5dp"
    android:id="@+id/graphData"
    android:visibility="invisible"/>

<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="0dip"
    android:layout_weight="1"
    android:gravity="center|bottom"
    android:orientation="vertical" >

    <Button
        android:text="Sign Out"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="15dp"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:textAllCaps="false"
        android:id="@+id/clearCache"
        android:visibility="invisible" />
</LinearLayout>
```

