# Web API Excersise 1

## この課題の目的

- Web API をライブラリを用いず REST API でコールする体験を通して、Web API について概要を理解する  
- HTML/JavaScript を用いた Web 開発の基本となる開発知識を得る
- OAuth認証による現代的なログインフローを体験する

## 課題

以下の Single Page Application をローカルサーバー上で構築し、ページを表示する。

- "LOG IN" ボタンにより、Microsoft Account でアプリへのログインを行えるようにする。
- Microsoft Account に登録されている情報を取得し、`Hello {ユーザー名}!` 等のユーザー情報を含む文章を表示する。
- テキストエリアと"LOAD"ボタンを持ち、ユーザーの、あるOneDrive上のテキストファイルを読み込み、テキストエリア内に表示できるようにする。
- "SAVE"ボタンを持ち、テキストエリア内のテキストを、そのOneDrive上のファイルに上書きできるようにする。

## Step 1: 空のWebページの作成

タイトルのみが表示されるウェブページを作り、ブラウザ上で確認する。

1. この課題用のディレクトリを作成し、`index.html`と`index.js`の２ファイルを作成する。
1. `index.html`上にHTMLで適当なページを作る。`<h1>`タグを用いてタイトルを足す。`<script>`タグを用いて`index.js`をロードする。
1. `index.js`には`console.log("js is loaded")`の１行だけを書いておく。（正しく読み込まれたらブラウザの Developer Tool のコンソールにログが出る。）

### Tips

- ローカルに置かれた html ファイルをブラウザ上で表示するには、`file:///C:/Users/XXX/XXX/index.html` のようにFileスキームを用いたURLにアクセスすればよい。
- Web 開発では、`F12 Developer Tool`を多用する。ブラウザでウェブページを開き、`F12`キーを押すと、ブラウザの右側に開発用のツールセットが表示される。特に、以下のページで何ができるのかを把握すること。
    - "Console": JavaScriptのログを表示する。ここにコードを直接書き込み、現在のWebページに対してJavaScriptを走らせることもできる。
    - "Elements": 現在のDOM（HTMLタグの構造）の構成を表示する
    - "Application": CookieやWebストレージ（ブラウザに保存されているデータ）などの情報を見る、操作する
    - "Network": HTTPリクエストとレスポンスの詳細を表示する
- `console.log("xxxx")`, `console.warn("xxxx")`, `console.error("xxxxx")`をコードの色々なところに入れておくことで開発がしやすくなる。関数の最初など。
- 今回作成するWebアプリケーションは「シングルページアプリケーション（SPA）」という。従来のWebアプリでは、機能をサーバーの中で実装し、ページを次々に生成する方式が大多数を占めていた。つまり、クライアント（ブラウザ上のHTMLとJavascript）はただ情報を表示しているだけのページであり、ボタンにはデータ付きのリンクが貼られていて、そのボタンが押される（＝データを投げて次のページをサーバーに要求する）と、サーバーが機能に必要なことを全部行い、結果ページを返す、というような形式が多かった。（Webゲームの例を挙げると、攻撃ボタンがクリックされるとクライアントはサーバーに次のページを要求。サーバーが戦闘状況を計算し、次の場面を表すページを返す、など。）これは、かつてブラウザやPCは弱く、巨大なJavaScriptで複雑な描画や計算をするのは現実的でなかったためである。しかし現在、巨大で複雑なJavaScriptであってもブラウザは難なく動くようになってきたため、機能もクライアント内で実装してしまい、サーバーとは（新しいページではなく）データのみをやり取りする方式が増え始めている。（例：攻撃ボタンがクリックされるとクライアントはサーバーに攻撃されたというデータを送信。サーバーが戦闘状況を計算し、結果のHPのデータを返信。クライアントがそれをもとにHPバーの長さだけを変更、など。あるいは、戦闘状況もクライアントが計算してしまい、サーバーにデータを送ることなく、HPバーの長さを変更、重要なセーブポイントのみデータをサーバーに投げる、など。）ページ遷移を行わず１つのページ＋データのやりとりのみで実現されるこちらの方式を「シングルページアプリケーション（SPA）」という。サーバーを構築する必要がなく簡単であるため、今回はこちらを扱う。

## Step 2: ローカルサーバー上にWebページをホストする

ローカルサーバーを立て、そこにStep1の空のウェブページをホストし、ブラウザからローカルホストにアクセスしてページが表示されるのを確認する。

1. ローカルサーバーを立てる。Step1の2つのファイルが置かれているディレクトリへ行き、以下のコマンドを走らせるのが最も簡単である。


    ```cmd
    > npx http-server
    ```

    - `npx`コマンドは、`node.js`に入っているコマンドである。`node.js`は、本来ブラウザ上でしか動作しないJavaScriptをPC上で動かすことのできる「実行環境」であり、今日のウェブ開発で主流となっている。PCにインストールされていない場合はここからインストールすること。https://nodejs.org/ja
    - "http-server"をインストールしてよいか許可を求められた場合は、許可する。

1. 表示されたアドレスにブラウザでアクセスし、ウェブページが表示されるかどうかを確認する。多くの場合 `http://127.0.0.1:8080`になると思われる。また、`http://localhost:8080`でもアクセスできるかどうかを確かめる。以降は数字（IPアドレス）直書きの方ではなく、こちらの"localhost"アドレスを用いる。

### Tips

- ローカルサーバーとは何か。普通インターネットのIPアドレスを用いた通信は、アクセス元である「クライアント」（=PC）と、「サーバー」の間で行われる。すなわち、２つの別のマシン間の通信となる。ローカルサーバーは、**クライアントPC**上に作られ、自分自身を指す特別なアドレス（127.0.0.1 == "localhost"）に設置される特別なサーバーで、いわばPCの「独り言」を可能にする。（クライアントはあたかも別マシンのサーバーと通信しているように見せて、その実その通信先も自分自身であるということ。）開発中に２つのマシンを触るのは面倒この上ないので、ローカルサーバーを用いてWebアプリの開発を行い、完成したら別マシンの本番サーバーに配置する、という開発フローを行うことが多い。
- `npx`コマンドによって、「node module（ノード　モジュール）」と呼ばれるJavaScriptで書かれた公開パッケージ（ライブラリ）を一時的にインストールし、実行できる。これらのモジュールは`node.js`に同梱されている`npm`という名前のパッケージ管理システムで管理される。`http-server`はnpmで管理できる公開パッケージの一つであり、同時に、そのパッケージに含まれるコマンドの名前でもある。(https://www.npmjs.com/package/http-server)　今回はこのライブラリによって、サーバー側のコードを一切書くことなく、ローカルサーバーを立てることができている。興味があればこのライブラリなしでローカルサーバーを自前で書いてみるのも良い勉強になる。
- 今回は使用しないが、`npm`コマンドというのもある。こちらはパッケージをPCにインストールしたり、アンインストールしたりするのに使う。
- URLの構造の基本的な知識は得ておくこと。https://developer.mozilla.org/ja/docs/Learn/Common_questions/Web_mechanics/What_is_a_URL
- なぜStep1の"Fileスキームを使った表示ではなく、わざわざローカルサーバーが必要なのか。のちに出てくるAPIのコールにおいて、その返答を受け取るためには「"https"スキームか、"http://localhost"にページがホストされていること」が必須となるためである。
- ちなみに、ローカルサーバーはコマンド上で`Ctrl + C`で止められる。

## Step 3: この課題で行うAPIコールの全体像を理解する

この課題では、以下の２つのサービスに対してAPIコールを行う。

- [Microsoft Identity Platform](https://learn.microsoft.com/ja-jp/azure/active-directory/develop/v2-overview): (login.microsoftonline.com) - Microsoft Account についての認証情報を管理しているサービス。
- [Microsoft Graph](https://learn.microsoft.com/ja-jp/graph/overview): (graph.microsoft.com) - Microsoft 365 (ユーザーデータ、Word、メール、OneDrive、等々) のデータを管理しているサービス。

Microsoft Graph の保持しているデータにアクセスするためには、Microsoft Identitiy Platform が発行する、アクセス許可「トークン」（token, 有効期限付のパスワードのようなもの）が必要になる。この「トークン」を得るためには様々な方法が提供されているが、今回は「OAuth2.0 認証コードフロー」に従う。https://learn.microsoft.com/ja-jp/azure/active-directory/develop/v2-oauth2-auth-code-flow を参照。このフローは以下の２ステップからなる。

1. ユーザーを Microsoft Identity Platform の「ログインページ」にリダイレクト（誘導）し、Web アプリがユーザーのデータにアクセスすることを許可させる。ユーザーがアクセスを許可すると、Microsoft Identity Platform はユーザーをWebアプリのページへとリダイレクトする。その際「認証コード」と呼ばれるユーザー許可が降りた"証"をWeb アプリに渡す。（はいわば「トークン発行許可証」である。）
1. Web アプリは、 Microsoft Identity Platform に「トークン」の発行をリクエストする。先ほどの「認証コード」を共に送信する必要がある。Microsoft Identity Platform は「トークン」をWeb アプリに返信する。

### Tips

- この、「ユーザーが認証サービスのページに移動し、その認証サービス上でログインを行い、アプリケーションは認証サービスから発行されたトークンを用いて別サービスのリソースにアクセスする」方式を定めた標準を「OAuth」という。https://datatracker.ietf.org/doc/html/rfc6749　この方式が登場する前は、アプリケーションが別サービスのリソースにアクセスしたければ、その別サービスのパスワードをアプリ上で入力してもらい、保持しなくてはならなかった。（例えば、TwitterのAPIにアクセスしたければ、ユーザーにTwitterのパスワードをアプリ上で入れてもらい、それをアプリ代わりに用いてアクセスする、など。Twitter以外のアプリケーションがTwitterのパスワードを知っていることになり、危険であった。）また、別サービスのリソースを使用していなくても、アプリがユーザーログインを実装するためには、最初にそのアプリ用のパスワードをユーザーに作成してもらい、ユーザーに覚えておいてもらうと同時に、アプリ側もパスワードを安全に覚えておく必要があった。このOAuth認証方式では、ユーザーはWebアプリではなく認証サービス上でログインを行うため、Webアプリは他サービスのパスワード等を管理する必要が一切ない。（Webアプリは他サービスのパスワードではなく、発行されるトークンを使ってアクセスを行う。）また、パスワードをそのアプリ用に作ってもらう必要もない。（Webアプリはトークンを用いて認証サービスのユーザーIDを取得でき、そのユーザーIDにデータを結び付けられる。）最近よく見る「Googleでログイン」「Twitterでログイン」「FaceBookでログイン」「Microsoft Accoutでログイン」などがOAuth認証の例。例えば、「Googleでログイン」を用いると、Googleの認証サービスを用いて、GoogleのパスワードでそのWebアプリに（アカウントを作らずとも）ログインできる。
- 今回の課題では学習のためこれらのAPIアクセスを手動で実装するが、これは本来**推奨されていない**。Microsoft Identity Platform には ["MSAL"](https://learn.microsoft.com/ja-jp/azure/active-directory/develop/msal-overview)、Microsoft Graph には ["Microsoft Graph Javascript Client Library"](https://github.com/microsoftgraph/msgraph-sdk-javascript) という公式ライブラリが存在しているため、こちらを利用すべきである。

## Step 4: 認証コードを獲得する

ログインボタンを押すと、Microsoft Identity Platform の /authorize ページに飛び、ログインできるボタンを作成する。ログイン後に戻ったページのURLに、「認証コード」が付いているのを確認する。

1. `index.html`上にログインボタンを設置し、idを振る。
1. `index.js`上で、idを元にログインボタンのElementを取得し(`document.getElementById('xxx')`)、ログインボタンの`onclick`イベントに関数を登録する。その関数の最初で、コンソールに適当なログを出力させておく。ここまでで、ブラウザ上で`Shift + F5`（あるいはShiftを押しながらブラウザの更新ボタン）を押しページを再読み込みし、ボタンを押せばログが出力されることを確認すること。
1. ウェブアプリを Microsoft Identity Platform に事前に登録しておく必要がある。[アプリの登録](https://learn.microsoft.com/ja-jp/azure/active-directory/develop/scenario-spa-app-registration#create-the-app-registration)、[承認コードフローを使用したMSAL.js 2.0](https://learn.microsoft.com/ja-jp/azure/active-directory/develop/scenario-spa-app-registration#redirect-uri-msaljs-20-with-auth-code-flow)を参考に登録する。「リダイレクトURL」には、`http://localhost:8080`を入力する。
1. ボタンクリックの際に発火する関数の中にコードを追加し、以下のページへ遷移させる。

    ```js
    const url = "https://login.microsoftonline.com/consumers/oauth2/v2.0/authorize" +
        "?client_id=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx" +
        "&response_type=code" +
        `&redirect_uri=${encodeURIComponent("http://localhost:8080")}` +
        "&scope=User.Read" +
        "&response_mode=query" +
        `&state=XXXXXXXXXX` +
        `&code_challenge=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX` +
        "&code_challenge_method=plain"; 
    
    window.open(url, "_self"); // url ページへ遷移
    ```

    - 詳細：https://learn.microsoft.com/ja-jp/azure/active-directory/develop/v2-oauth2-auth-code-flow#request-an-authorization-code
    - `client_id` は アプリの登録ページのOverviewで確認できる、`Application (client) ID`.
    - `state` は 任意の英数字文字列。実際のプロジェクトでは毎回ランダムに生成する必要がある。今回は適当な文字列を直接書いてよい
    - `code_challenge` は 43文字以上128文字以下の英数字文字列。同じく実際のプロジェクトでは`PKCE`というルールに従い暗号理論に基づいて生成する必要があるが、今回は適当でよい。
1. テストを行う。ボタンをクリックすると、ページが切り替わり、アプリに権限を与えてよいかのログインページが現れる。ログインすると元のページに戻り、URLに「code」と「state」が付与されていることを確認する。https://learn.microsoft.com/ja-jp/azure/active-directory/develop/v2-oauth2-auth-code-flow#successful-response

### Tips

- なお、一度アプリケーションを許可すると、次回以降、認証ページでの確認はスキップされることが多い。元に戻したい場合は、https://account.live.com/consent/Manage にアクセスし、アプリケーションの許可を取り消すと、初期状態に戻る。

## Step 5: 認証コードを用いて、アクセストークンを獲得する

URL パラメータに「code」と「state」がついている時に、それらを用いて Microsoft Idntity Platform の /token エンドポイントに対して API コールを行い、アクセストークンを取得する。

1. `index.json`のグローバルスコープ（一番外側、onclickの中ではないので注意）で、URLのパラメータに、1. `code` が含まれていて、2. `state` が Step4の `state` と同一の際にtrueとなるif文を開く
1. そのif文の中で、`fetch`関数を用いてアクセストークンを取得するためのAPIコールを行う。https://learn.microsoft.com/ja-jp/azure/active-directory/develop/v2-oauth2-auth-code-flow#redeem-a-code-for-an-access-token

    ```js
    const tokenRequestBody =
        "client_id=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx" +
        `&redirect_uri=${encodeURIComponent("http://localhost:8080")}` +
        "&scope=User.Read" +
        "&grant_type=authorization_code" +
        `&code=${code}` +
        `&code_verifier=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX`

    fetch("https://login.microsoftonline.com/consumers/oauth2/v2.0/token", {
            method: "POST",
            headers: { "Content-Type": "application/x-www-form-urlencoded" },
            body: tokenRequestBody
    });
    ```

    - `code` は、URL パラメータから取得した認証コード
    - `code_verifier` は、今回はStep4の`code_challenge`で送信した値とまったく同じ値。

1. ログインボタンを押し、コードを走らせてみる。F12 Developer Tool の "Network" タブを開き、"token" リクエストがStatus Code 200 で成功していること、"Response"の中に"access_token"が含まれていることを確認する。
1. ...

## Step 6: アクセストークンを用いてGraph APIをコールし、ユーザー名を取得する


## Step 7: 様々なGraph APIをコールして機能をつくる

## (応用) PKCE を用いたコードチャレンジの実装