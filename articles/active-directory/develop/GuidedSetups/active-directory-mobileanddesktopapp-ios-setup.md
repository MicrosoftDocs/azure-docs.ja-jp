---
title: "Azure AD v2 iOS の概要 - 設定 | Microsoft Docs"
description: "iOS (Swift) アプリケーションで、アクセス トークンを必要とする API を Azure Active Directory v2 エンドポイントから呼び出す方法"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.openlocfilehash: d25353a61b2a60bff28aa0679d38110e77d19e64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
## <a name="setting-up-your-ios-application"></a>iOS アプリケーションをセットアップする

このセクションでは、新しいプロジェクトの作成方法について順を追って説明し、iOS アプリケーション (Swift) に *"Microsoft でサインイン"* を統合して、トークンを必要とする Web API でクエリを実行できるようにする方法を示します。

> 代わりにこのサンプルの XCode プロジェクトをダウンロードすることもできます。 [プロジェクトをダウンロード](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)したら[構成](#create-an-application-express)手順に進み、実行前にコード サンプルを構成します。


## <a name="install-carthage-to-download-and-build-msal"></a>Carthage をインストールし、MSAL をダウンロードしてビルドする
Carthage パッケージ マネージャーは MSAL のプレビュー期間中に使用されます。XCode との統合が行われますが、その間、Microsoft はライブラリを変更できます。

- Carthage の最新リリースを[ここ]からダウンロードし、インストールします(https://github.com/Carthage/Carthage/releases "Carthage ダウンロード URL")

## <a name="creating-your-application"></a>アプリケーションの作成

1.  Xcode を開き、`Create a new Xcode project` を選択します
2.  `iOS` > `Single view Application` を選択し、*[次へ]* をクリックします
3.  製品に名前を付け、*[次へ]* をクリックします
4.  アプリを作成するフォルダーを選択し、*[作成]* をクリックします

## <a name="build-the-msal-framework"></a>MSAL フレームワークをビルドする

下の手順に従い、Carthage を利用して MSAL ライブラリの最新版をプルし、ビルドします。

1.  バッシュ ターミナルを開き、アプリのルート フォルダーに移動します
2.  下のコマンドをコピーし、バッシュ ターミナルに貼り付け、‘Cartfile’ ファイルを作成します

```bash
echo "github \"AzureAD/microsoft-authentication-library-for-objc\" \"master\"" > Cartfile
```
<!-- Workaround for Docs conversion bug -->
<ol start="3">
<li>
下のコマンドをコピーして貼り付けます。 このコマンドは依存性を取り出して Carthage/Checkouts フォルダーに入れ、MSAL ライブラリをビルドします。
</li>
</ol>

```bash
carthage update
```

> 上記のプロセスは Microsoft Authentication Library (MSAL) のダウンロードとビルドに使用されます。 MSAL は、Azure Active Directory v2 で保護される API へのアクセスで使用するユーザー トークンの取得、キャッシュ、更新を処理します。

## <a name="add-the-msal-framework-to-your-application"></a>MSAL フレームワークをアプリケーションに追加する
1.  Xcode で、[`General`] タブを開きます
2.  [`Linked Frameworks and Libraries`] セクションに進み、[`+`] をクリックします
3.  `Add other…` を選択します
4.  `Carthage` > `Build` > `iOS` > `MSAL.framework` の順に選択し、*[開く]* をクリックします。 `MSAL.framework` が一覧に追加されるはずです。
5.  [`Build Phases`] タブに進み、`+` アイコンをクリックし、`New Run Script Phase` を選択します
6.  *スクリプト領域*に次の内容を追加します

```text
/usr/local/bin/carthage copy-frameworks
```

<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
<code>+</code> をクリックし、<code>Input Files</code> に次の内容を追加します
</li>
</ol>

```text
$(SRCROOT)/Carthage/Build/iOS/MSAL.framework
```

## <a name="creating-your-applications-ui"></a>アプリケーションの UI を作成する
Main.storyboard ファイルは、プロジェクト テンプレートの一部として自動的に作成されます。 アプリの UI を作成するには、以下の手順に従います。

1.  Ctrl キーを押しながら `Main.storyboard` をクリックしてコンテキスト メニューを表示し、`Open As` > `Source Code` の順にクリックします
2.  `<scenes>` ノードを下のコードに変更します

```xml
 <scenes>
    <scene sceneID="tne-QT-ifu">
        <objects>
            <viewController id="BYZ-38-t0r" customClass="ViewController" customModule="MSALiOS" customModuleProvider="target" sceneMemberID="viewController">
                <layoutGuides>
                    <viewControllerLayoutGuide type="top" id="y3c-jy-aDJ"/>
                    <viewControllerLayoutGuide type="bottom" id="wfy-db-euE"/>
                </layoutGuides>
                <view key="view" contentMode="scaleToFill" id="8bC-Xf-vdC">
                    <rect key="frame" x="0.0" y="0.0" width="375" height="667"/>
                    <autoresizingMask key="autoresizingMask" widthSizable="YES" heightSizable="YES"/>
                    <subviews>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Microsoft Authentication Library" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="ifd-fu-zjm">
                            <rect key="frame" x="64" y="28" width="246" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Logging" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="98g-dc-BPL">
                            <rect key="frame" x="16" y="277" width="62" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="2rX-Vv-T1i">
                            <rect key="frame" x="87" y="100" width="200" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Call Microsoft Graph API"/>
                            <connections>
                                <action selector="callGraphButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="Kx0-JL-Bv9"/>
                            </connections>
                        </button>
                        <textView clipsSubviews="YES" multipleTouchEnabled="YES" contentMode="scaleToFill" fixedFrame="YES" editable="NO" textAlignment="natural" selectable="NO" translatesAutoresizingMaskIntoConstraints="NO" id="qXW-2z-J7K">
                            <rect key="frame" x="16" y="324" width="343" height="291"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <color key="backgroundColor" white="1" alpha="1" colorSpace="calibratedWhite"/>
                            <accessibility key="accessibilityConfiguration">
                                <accessibilityTraits key="traits" updatesFrequently="YES"/>
                            </accessibility>
                            <fontDescription key="fontDescription" type="system" pointSize="14"/>
                            <textInputTraits key="textInputTraits" autocapitalizationType="sentences"/>
                        </textView>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="9u4-b8-vmX">
                            <rect key="frame" x="137" y="138" width="100" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Sign-Out"/>
                            <connections>
                                <action selector="signoutButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="kZT-P8-0Zy"/>
                            </connections>
                        </button>
                    </subviews>
                    <color key="backgroundColor" red="1" green="1" blue="1" alpha="1" colorSpace="custom" customColorSpace="sRGB"/>
                </view>
                <connections>
                    <outlet property="loggingText" destination="qXW-2z-J7K" id="uqO-Yw-AsK"/>
                    <outlet property="signoutButton" destination="9u4-b8-vmX" id="OCh-qk-ldv"/>
                </connections>
            </viewController>
            <placeholder placeholderIdentifier="IBFirstResponder" id="dkx-z0-nzr" sceneMemberID="firstResponder"/>
        </objects>
        <point key="canvasLocation" x="140" y="137.18140929535232"/>
    </scene>
</scenes>
```
