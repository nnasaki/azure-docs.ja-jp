---
title: 'チュートリアル: Azure Active Directory と Zoom の統合 | Microsoft Docs'
description: Azure Active Directory と Zoom 間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/28/2017
ms.author: jeedes
ms.openlocfilehash: 57ae31245a356a4cd5769fe71ef471922bf6faf9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440136"
---
# <a name="tutorial-azure-active-directory-integration-with-zoom"></a>チュートリアル: Azure Active Directory と Zoom の統合

このチュートリアルでは、Zoom と Azure Active Directory (Azure AD) を統合する方法について説明します。

Zoom と Azure AD の統合には、次の利点があります。

- Zoom にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで Zoom に自動サインオン (シングル サインオン) できるようになります。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Zoom と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Zoom でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Zoom の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-zoom-from-the-gallery"></a>ギャラリーからの Zoom の追加
Azure AD への Zoom の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Zoom を追加する必要があります。

**ギャラリーから Zoom を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Zoom**」と入力し、結果パネルで **[Zoom]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Zoom](./media/zoom-tutorial/tutorial_zoom_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Zoom で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Zoom ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Zoom の関連ユーザーの間で、リンク関係が確立されている必要があります。

Zoom で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Zoom で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Zoom テスト ユーザーの作成](#create-a-zoom-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Zoom で作成します。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Zoom アプリケーションでシングル サインオンを構成します。

**Zoom で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Zoom** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/zoom-tutorial/tutorial_zoom_samlbase.png)

1. **[Zoom のドメインと URL]** セクションで、次の手順を実行します。

    ![[Zoom のドメインと URL] のシングル サインオン情報](./media/zoom-tutorial/tutorial_zoom_url.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.zoom.us` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`<companyname>.zoom.us` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 この値を取得するには、[Zoom クライアント サポート チーム](https://support.zoom.us/hc)にお問い合わせください。

1. Zoom アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 このアプリケーションには、次の要求を構成します。 この属性の値は、アプリケーション統合ページの **[User Attributer]** セクションで管理できます。 

    ![Configure single sign-on](./media/zoom-tutorial/tutorial_attribute.png)

1. **[Single sign-on]\(シングル サインオン\)** ダイアログの **[User Attributes]\(ユーザー属性\)** セクションで、上記の図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 | 名前空間の値 |
    | ------------------- | -----------|--------- |    
    | 電子メール アドレス | User.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/mail`|
    | 名 | User.givenname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`|
    | 姓 | User.surname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname `|
    | 電話番号 | user.telephonenumber | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/phone`|
    | 部署 | user.department | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/department`|

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/zoom-tutorial/tutorial_attribute_04.png)

    ![Configure single sign-on](./media/zoom-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d. **[名前空間]** ボックスに、その行に表示される名前空間の値を入力します。
    
    e. **[OK]** をクリックします。 
 
1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/zoom-tutorial/tutorial_zoom_certificate.png)

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/zoom-tutorial/tutorial_general_400.png)

1. **[Zoom 構成]** セクションで、**[Zoom の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Zoom 構成](./media/zoom-tutorial/tutorial_zoom_configure.png)

1. 別の Web ブラウザー ウィンドウで、Zoom 企業サイトに管理者としてログインします。

1. **[シングル サインオン]** タブをクリックします。
   
    ![[Single sign-on] \(シングル サインオン\) タブ](./media/zoom-tutorial/IC784700.png "シングル サインオン")

1. **[セキュリティ制御]** タブをクリックし、**[シングル サインオン]** に移動します。

1. [Single Sign-On] セクションで、次の手順に従います。
   
    ![[Single sign-on] \(シングル サインオン\) セクション](./media/zoom-tutorial/IC784701.png "シングル サインオン")
   
    a. **[サインイン ページの URL]** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。
   
    b. **[サインアウト ページの URL]** ボックスに、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。
     
    c. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、 **[ID プロバイダー証明書]** ボックスに貼り付けます。

    d. **[発行者]** テキストボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。 

    e. **[Save]** をクリックします。

    > [!NOTE] 
    > 詳しくは、Zoom のドキュメント [https://zoomus.zendesk.com/hc/en-us/articles/115005887566](https://zoomus.zendesk.com/hc/en-us/articles/115005887566) をご覧ください。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/zoom-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/zoom-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/zoom-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/zoom-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-zoom-test-user"></a>Zoom テスト ユーザーの作成

Azure AD ユーザーが Zoom にログインできるようにするには、ユーザーを Zoom にプロビジョニングする必要があります。 Zoom の場合、プロビジョニングは手動で行います。

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1. **Zoom** 企業サイトに管理者としてログインします。
 
1. **[アカウント管理]** タブをクリックし、**[ユーザー管理]** をクリックします。

1. [ユーザー管理] セクションで、 **[ユーザーの追加]** をクリックします。
   
    ![ユーザー管理](./media/zoom-tutorial/IC784703.png "ユーザー管理")

1. **[ユーザーの追加]** ページで、次の手順を実行します。
   
    ![Add users](./media/zoom-tutorial/IC784704.png "Add users")
   
    a. **[ユーザー タイプ]** として、**[基本]** を選択します。

    b. **[Emails]\(電子メール\)** ボックスに、プロビジョニングする有効な Azure AD アカウントの電子メール アドレスを入力します。

    c. **[追加]** をクリックします。

> [!NOTE]
> Zoom から提供されている他の Zoom ユーザー アカウント作成ツールまたは API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Zoom へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Zoom に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Zoom]** を選択します。

    ![アプリケーションの一覧の [Zoom] リンク](./media/zoom-tutorial/tutorial_zoom_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [Zoom] タイルをクリックすると、自動的に Zoom アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zoom-tutorial/tutorial_general_01.png
[2]: ./media/zoom-tutorial/tutorial_general_02.png
[3]: ./media/zoom-tutorial/tutorial_general_03.png
[4]: ./media/zoom-tutorial/tutorial_general_04.png

[100]: ./media/zoom-tutorial/tutorial_general_100.png

[200]: ./media/zoom-tutorial/tutorial_general_200.png
[201]: ./media/zoom-tutorial/tutorial_general_201.png
[202]: ./media/zoom-tutorial/tutorial_general_202.png
[203]: ./media/zoom-tutorial/tutorial_general_203.png

