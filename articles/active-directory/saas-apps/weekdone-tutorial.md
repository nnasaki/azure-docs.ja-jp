---
title: 'チュートリアル: Azure Active Directory と Weekdone の統合 | Microsoft Docs'
description: Azure Active Directory と Weekdone の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 34921f9a-5637-4420-ab4c-9beb34421909
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: jeedes
ms.openlocfilehash: 7f7946ece91013696969dafda17b02c972f4b780
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36230405"
---
# <a name="tutorial-azure-active-directory-integration-with-weekdone"></a>チュートリアル: Azure Active Directory と Weekdone の統合

このチュートリアルでは、Weekdone と Azure Active Directory (Azure AD) を統合する方法について説明します。

Weekdone と Azure AD の統合には、次の利点があります。

- Weekdone にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Weekdone にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Weekdone の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Weekdone でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Weekdone の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-weekdone-from-the-gallery"></a>ギャラリーからの Weekdone の追加
Azure AD への Weekdone の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Weekdone を追加する必要があります。

**ギャラリーから Weekdone を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

4. 検索ボックスに、「 **Weekdone**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/weekdone-tutorial/tutorial_weekdone_search.png)

5. 結果ウィンドウで **[Weekdone]** を選択し、**[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/weekdone-tutorial/tutorial_weekdone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Weekdone で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Weekdone ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Weekdone の関連ユーザーの間で、リンク関係が確立されている必要があります。

Weekdone で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Weekdone テスト ユーザーの作成](#creating-a-weekdone-test-user)** - Weekdone で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Weekdone アプリケーションでシングル サインオンを構成します。

**Weekdone で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Weekdone** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[Configure Single Sign-On]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[Configure Single Sign-On]](./media/weekdone-tutorial/tutorial_weekdone_samlbase.png)

3. **[Weekdone のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[Configure Single Sign-On]](./media/weekdone-tutorial/tutorial_weekdone_url1.png)

    a. **[識別子]** ボックスに、`https://weekdone.com/a/<tenant>/metadata` の形式で URL を入力します。

    > [!NOTE]
    > Weekdone からのメタデータ ファイルは、同じ URL を使用して取得できます。

    b. **[応答 URL]** ボックスに、`https://weekdone.com/a/<tenantname>` のパターンを使用して URL を入力します。

4. **[詳細な URL 設定の表示]** をクリックします。 **SP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[Configure Single Sign-On]](./media/weekdone-tutorial/tutorial_weekdone_url2.png)

    **[サインオン URL]** ボックスに、`https://weekdone.com/a/<tenantname>` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Weekdone クライアント サポート チーム](mailto:hello@weekdone.com)に問い合わせてください。 

5. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[Configure Single Sign-On]](./media/weekdone-tutorial/tutorial_weekdone_certificate.png) 

6. **[保存]** ボタンをクリックします。

    ![[Configure Single Sign-On]](./media/weekdone-tutorial/tutorial_general_400.png)
    
7. **Weekdone Configuration (Weekdone 構成)** セクションで、**Configure Weekdone (Weekdone を構成する)** をクリックして、**サインオンの構成** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![[Configure Single Sign-On]](./media/weekdone-tutorial/tutorial_weekdone_configure.png) 

8. **Weekdone** 側でシングル サインオンを構成するには、ダウンロードした**メタデータ XML、サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** を [Weekdone サポート チーム](mailto:hello@weekdone.com)に送る必要があります。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/weekdone-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/weekdone-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/weekdone-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/weekdone-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-weekdone-test-user"></a>Weekdone テスト ユーザーの作成

このセクションの目的は、Weekdone で Britta Simon というユーザーを作成することです。 Weekdone では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 Weekdone にユーザーがいない場合、アクセスしようとすると新しいユーザーが自動的に作成されます。

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、[Weekdone クライアント サポート チーム](mailto:hello@weekdone.com)にお問い合わせください。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Weekdone へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Weekdone に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Weekdone]** を選択します。

    ![[Configure Single Sign-On]](./media/weekdone-tutorial/tutorial_weekdone_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで [Weekdone] タイルをクリックすると、自動的に Weekdone アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/weekdone-tutorial/tutorial_general_01.png
[2]: ./media/weekdone-tutorial/tutorial_general_02.png
[3]: ./media/weekdone-tutorial/tutorial_general_03.png
[4]: ./media/weekdone-tutorial/tutorial_general_04.png

[100]: ./media/weekdone-tutorial/tutorial_general_100.png

[200]: ./media/weekdone-tutorial/tutorial_general_200.png
[201]: ./media/weekdone-tutorial/tutorial_general_201.png
[202]: ./media/weekdone-tutorial/tutorial_general_202.png
[203]: ./media/weekdone-tutorial/tutorial_general_203.png
