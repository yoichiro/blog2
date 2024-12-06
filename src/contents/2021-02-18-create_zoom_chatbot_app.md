---
layout: post
title: Create Zoom Chatbot Appの勝手日本語訳です
categories:
- zoom
---

Zoomにはチャット機能がついていることは皆さんご存知だと思いますが、Chatbotを作れることもご存知でしょうか？Zoom はプラットフォーム化していて、API やユーザ認証など開発者向けに多くの機能を提供してくれているのですが、日本語での解説がほとんどないのが実情です。

Chatbotについてちょっと調べつ機会があったので、読むついでに日本語訳をしてみました。もし興味があれば、参考になると嬉しいです。

以下、2021年2月18日時点での、 [Create a Chatbot App](https://marketplace.zoom.us/docs/guides/build/chatbot-app) の勝手日本語訳です。

---
  
# チャットボットの作り方

チャットボットは、Zoomクライアントにインストールされるアプリであり、チャットインタフェースを使ってユーザと相互にやり取りします。チャットボットは、Zoomミーティング、カレンダーイベント、またはZoomクライアントから直接サーバにリクエストすることをユーザが簡単に行えるようにするために作成されます。

あなたはまた、Zoomマーケットプレースに公開されるアプリを作成することができ、Zoomクライアント内に直接拡張や統合を提供することで、Zoomユーザがあなたのプロダクトやサービスと相互にやり取りすることを助けます。
# アプリの登録

アプリを登録するために、Zoom App Marketplace に訪問し、右上の角にあるドロップダウン内の Develop オプションをクリックして、Build App を選択してください。いくつかのアプリの種別が表示されます。アプリ種別として Chatbot を選択し、Create をクリックしてください。
# アプリクレデンシャルの生成

チャットアプリを認可するために、Zoomプラットフォームは、開発向けと本番環境向けの両方のために、ユニークなClient IDとClient Secretをクレデンシャルとして生成します。開発向けのクレデンシャルは、アプリの開発とテストを行っている際に利用してください。また、本番環境向けのクレデンシャルは、非公開でアプリをインストールする、またはマーケットプレースにアプリを公開する際に利用してください。

アプリの認可がユーザによって成功したあとにそのユーザがリダイレクトされる有効でセキュアなURLを、Redirect URL for OAuth として提供します。

加えて、Whitelist URLを追加します。それは、セキュリティの向上を達成するために、Redirect URL for OAuth としてホワイトリスト化される全てのURLを含みます。あなたが Redirect URL for OAuth としてホワイトリストにしたい全体のURLまたはそのプレフィックスのどちらかであるかどうかを確認してください。例: https://zoom.us またはhttps://marketplace.zoom.us
# アプリ情報

ここで、あなたのアプリのShort DescriptionとLong Descriptionを入力し、アプリに与えられたNameを更新します。このユニークなアプリ名は、たとえあなたのアプリが公開するために提出されなくても閲覧されることに注意してください。

インストールにて、Chatbotは、アプリのNameおよびShort Desriptionを含むメッセージをユーザに送信します。Short Descriptionは、短い例や、Chatbotの使い方として最初の操作方法を含むべきです。

同時に、要求されるApp iconをアップロードする良いタイミングです。アイコンは、GIF/JPG/JPEG/PNGの中で、幅と高さが160pxから400pxの間の四角い画像であるべきです。1MBを超えるサイズではいけません。

Preview Imagesは、あなたのユーザにアプリの視覚的な詳細を提供します。それには、画像または動画のどちらかが使われます。ここで、チャットプロンプトや結果のスクリーンショットを使うことで、アプリの主要機能やユースケースを見せることができます。JPG/GIF/JPEG/PNGの画像やMP4の動画のみがサポートされます。動画の最大のサイズは、10MBです。画像は、1200 x 780 px でなければなりません。

Categoryは、似たようなアプリと一緒にあなたのアプリが組織化され分類されるようにします。そして、ユーザがアプリを検索し探しやすくします。あなたのアプリ向けに、3つのカテゴリーをここで選択可能です。

あなたのCompany Nameは、マーケットプレースのホームページ、およびあなたのアプリの詳細ページに表示され、公開されます。

次に、Developer Contact 名称とメールアドレスを追加します。これらは、Zoom Marketplace Teamまたはあなたのユーザによって、アプリに関する問い合わせのために使われます。

注意: @gmail.com や @outlook.com といった、メールサービスプロバイダのドメインは受理されません。提出されるメールアドレスは、有効なドメインでなければならず、Zoom Marketplace Teamがレビュープロセスの間に確認可能である運用されているメールアドレスでなければなりません。

ユーザがPrivacy Policy や Support ページにアクセスするためのリンクを追加します。それらは、承認のために必要です。また、あなたはDocumentationやConfigureページへのリンクを提供することも可能です。それらは必須ではありませんが、ユーザがアプリを操作するために不可欠です。
# 機能のセット

Featureページは、Chatbotに対して、Slash Command、Usage Hints、Welcome Message、Trusted Domain、そしてEvent Subscriptionsをカスタマイズすることになる場所です。
## Chat Subscription

Chat Subscription設定を展開するために、トグルスイッチをクリックしてONにします。


![]({{ "/images/2021/02/create-zoom-chatbot-app-1.webp" | prepend: site.baseurl }})


## Slash Command

Slash Command は、あなたのChatbotを呼び出すために使われるテキスト文字列です（例: /zoom meeting）。


![]({{ "/images/2021/02/create-zoom-chatbot-app-2.webp" | prepend: site.baseurl }})


使用可能なコマンドをユーザが知るのに役立つ usage hint をChatbotに追加することも可能です。
## Welcome Message

Welcome Messageは、Chatbotをユーザがインストールしたあとに送信されるメッセージです。Welcome Message は、Titleの入力とBodyのテキストエリアを介して、またはアプリによるプログラムでカスタマイズできます。

静的なカスタムウェルカムメッセージをセットするために、"Zoom App Marketplace" オプションを選択し、Title と Body フィールドを埋めてください。


![]({{ "/images/2021/02/create-zoom-chatbot-app-3.webp" | prepend: site.baseurl }})


プログラムで動的に変更するカスタムウェルカムメッセージをセットするために、"Your App"オプションを選択して、add a Welcome Message の指示に従ってください。


![]({{ "/images/2021/02/create-zoom-chatbot-app-4.webp" | prepend: site.baseurl }})


## Trusted Domain List

Trusted Domain Listは、あなたのChatbotがユーザにメッセージを送るための信頼されたURLの一覧です。

信頼されたドメインとして一覧にないURLは、依然としてChatbotにメッセージが送信されますが、ユーザがそれらをクリックした際には、リンクに進む前に、信頼できないURLを通知するポップアップが開きます。


![]({{ "/images/2021/02/create-zoom-chatbot-app-5.webp" | prepend: site.baseurl }})


信頼できるドメインの承認をリクエストすると、更新内容がレビューされている間は、ステータスが「処理中」に設定されます。


![]({{ "/images/2021/02/create-zoom-chatbot-app-6.webp" | prepend: site.baseurl }})


信頼できるドメインが承認されると、ステータスは "Approved" にセットされます。


![]({{ "/images/2021/02/create-zoom-chatbot-app-7.webp" | prepend: site.baseurl }})


## Bot Endpoint URL

Bot Endpoint URLは、Chatbotがプログラムによるウェルカムメッセージ用にインストールされたとき、ユーザーがスラッシュコマンドを送信したとき、およびユーザーがChatbotメッセージのUI要素を操作したときに、Zoomプラットフォームが呼び出すサーバーのエンドポイントURLです。サーバーのエンドポイントURLに送信されるリクエストは、それぞれのイベントのJSON本文を含むPOSTリクエストになります。


![]({{ "/images/2021/02/create-zoom-chatbot-app-8.webp" | prepend: site.baseurl }})


入力が終わったら、Saveを必ずクリックしてください！

## Event Subscription

Event Subscription は、特定のイベントまたはアクションがトリガーされたときに、アプリがZoomのWebhookを使用して情報を返すことを可能にするオプション機能です。多くの場合、Event Subscription は、API呼び出しを繰り返す必要性を置き換えることができます。例えば、ユーザーがアカウントをアクティブ化するたび、または録画が開始されるたびに、アプリに自動通知を送信する機能を追加したい場合があげられます。

Event Subscription を作成するには、トグルをONにクリックします。 + Add new event subscription をクリックします。オプションの Subscription Name を選択し、開発と本番の両方の Event Notification Endpoint URL を追加します。これらのURLは、サブスクライブされた各イベントの通知に関するデータを含むPOSTリクエストを受信します。注: Event Notification Endpoint URL は、HTTPSで保護される必要があります。

注意: Event Subscription 名は、リクエストのペイロードには影響しません。

この Event Subscription のイベントタイプを追加し、Save をクリックします。

Event Subscription を更に追加するために、+ Add new event subscription を再びクリックします。各イベントサブスクリプションに必要な数のイベントをサブスクライブできますが、アプリごとに最大10のイベントサブスクリプションしか持つことができません。

イベントサブスクリプションは、重複するイベントを持つことができます。例えば、1つの Event Subscription に会議とユーザーイベントを含めることができ、2番目の Event Subscription に会議と録画のイベントを含めることができます。

入力が終わったら、Saveを必ずクリックしてください！


![]({{ "/images/2021/02/create-zoom-chatbot-app-9.webp" | prepend: site.baseurl }})


# スコープのセット

Scopes は、ユーザーに関連する指定されたリソースにアクセスするために、アプリが使用できる API エンドポイントとメソッドを定義します。

利用可能な Scopes を探して追加するには、"Add Scopes" をクリックして、アプリに必要な利用可能なアクセスポイントをスクロールします。Chatbots の一般的なスコープの1つは、"Chat: Enable Chatbot within Zoom Chat Client.” にあります。

アプリが公開されると、承認されたスコープによってアプリに付与されたリソースへのアクセスのみが許可されます。スコープの各リクエストは、提出時に Zoom Marketplace チームによって具体的に確認されます。


![]({{ "/images/2021/02/create-zoom-chatbot-app-10.webp" | prepend: site.baseurl }})


# ローカルでのテスト

もしあなたのアプリが有効なRedirect URL、Whitelist URL、そして一つ以上のスコープを含んでいる場合、テストのためにインストールすることができます。"Install" をクリックして、あなたの Zoom アカウントにアプリをインストールするためのプロンプトに従ってください。これは、あなたの Zoom クライアント（あなたの Zoom アプリ）に Chatbot をインストールすることになります。

あなたの Chatbot のインストールはまた、さらなるテストのために、あなたのアカウント上で他のユーザに共有可能なテスト可能なURLを生成します。

注意: OAuthアプリでは、通常、ブラウザに表示される認証コードを使用して、各ユーザーのアクセストークンをリクエストします。Chatbot では、これを無視して、代わりにgrant_type、client_id、client_secret を使用してPOSTリクエストを送信してアクセストークンを取得できます。Chatbot 認証用の更新トークンはありません。アクセストークンの有効期限が切れた後、アプリは同じ手順を使用して新しいトークンをリクエストする必要があります。これらのトークンを使用してアプリを承認する方法の完全な例については、Chatbot Service Authorization ガイドを参照してください。


![]({{ "/images/2021/02/create-zoom-chatbot-app-11.webp" | prepend: site.baseurl }})


# 提出

Bot が内部利用のみを目的としている場合は、Publishable URLを使用して、あなたのアカウントにおいてユーザーに対してアプリを共有および認可できます。このため、本番キーが機能するためには、Publishable URLを生成するだけで済みます。

Chatbot を Zoom Marketplace に公開する予定の場合、アプリはレビュープロセスを経て、Zoom Marketplace チームによって承認される必要があります。

提出する前に、Zoom Marketplaceでは、各アプリがコールバックの受信に使用されたドメインを検証し、それがユーザーによって所有されていることを確認する必要があります。概説されている手順に従って、確認コードを /zoomverify というルートフォルダーにアップロードします。これは1回限りの検証プロセスです。

注: ドメインの確認手順を完了するのが難しい場合は、このドメインの所有権を証明するメールを marketplace-support@zoom.us までメールでお送りください。

必要な情報をすべて入力したら、'Check' をクリックし、概説されているすべての手順に従って、必要事項を記入して、Checklist の提出を完了します。

Release Note へのリンクを追加します。このリンクは、バージョン参照のためにZoom Marketplace チームとユーザーの両方によってレビューされます。

アプリでアカウントへのアクセスが必要な場合は、アプリの Test Account and Credentials へのリンクを、Free Trial ページまたは Login ページへのURL と共に提供します。

最後のステップは、Zoom Marketplace Developer Agreement に署名することです。この必要な契約は、会社の認定エージェント（CEO、CTO、法務チームメンバーなど）が確認して署名する必要があります。 Zoom Marketplaceチームは、承認されたエージェントの署名がない限り、アプリを確認または承認しません。

アプリをレビューする準備ができたら、Submit をクリックして、Zoom Marketplaceチームによる承認のためにアプリを送信します。

