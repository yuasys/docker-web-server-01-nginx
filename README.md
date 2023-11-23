# docker-web-server-01-nginxリポジトリについて

このリポジトリは[Udemy講座](https://www.udemy.com/course/web-app-development/learn/lecture/27725522#notes)に触発され、筆者独自の知見を加えて作成したものです。

## このリポジトリはどのようにして作られたか



### 1.作業用ディレクトリ（のルート）に移動する

```
cd ~/work
```
【解説】このコマンドによって作業ディレクトリのルート（基点）となる場所に移動します  

### ２．docker-web-server-01-nginxリポジトリに移動する

#### 2-1.lsコマンドで、リポジトリが既にあるかどうかを確認する

##### 移動先のリポジトリがまだないケース

```
# 他のリポジトリが一つあったが求めるものではなかったケース
nob@v133-18-242-166:~/work$ ls
docker-web-server-02-sandbox 

# 作業ディレクトリ配下にはなにもなかったケース
nob@v133-18-242-166:~/work$ ls
nob@v133-18-242-166:~/work$ 
```

##### 移動抱きのリポジトリが既にあるケース

```
nob@v133-18-242-166:~/work$ ls
docker-web-server-01-nginx 
```

#### ２−２．リポジトリ新規作成の２つの方法

##### ２−２−１． git cloneコマンドでGitHubからダウンロードする【簡便な方法】

```
ob@v133-18-242-166:~/work$ git clone https://github.com/yuasys/docker-web-server-01-nginx.git
```

##### ２−２−２． お手本を見ながら手作業で作成する方法

次のように、ディレクトリやファイルを設置し、内容は[お手本](https://github.com/yuasys/docker-web-server-01-nginx.git)を見て編集する。

```
nob@v133-18-242-166:~/work$ tree
.                              # 作業ディレウトリルート
└── docker-web-server-01-nginx # リポジトリディレクトリ
    ├── Dockerfile             # Dockerfile
    └── static-html-directory  # ディレクトリ
```

### 3. Docker コンテナのステータスを理解する

![コンテナのステータス](https://hackmd.io/_uploads/BJXCtjqEa.jpg)

[出典元（Udemy教材）](https://www.udemy.com/course/web-app-development/learn/lecture/27725350#overview)の一部を転用させていただいた画像です。

#### ３−１．新規portで秒数変換アップリを作ってみる

##### ３−１−１．準備（使えるポートとコンテナ名を調べる）

```
# アプリを書く場所（ローカルリポジトリ）に移動する
nob@v133-18-242-166:~$ cd ~/work/docker-web-server-01-nginx/

# 既に使っている（た）ポートをpsコマンドで調べる
nob@v133-18-242-166:~/work/docker-web-server-01-nginx$ docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED      STATUS      PORTS                                   NAMES
80cf3aecf5c3   nginx:1.19.6   "/docker-entrypoint.…"   5 days ago   Up 5 days   0.0.0.0:8082->80/tcp, :::8082->80/tcp   docker-web-server-02-sandbox-nginx-1
811fab0aa134   php:8.0-fpm    "docker-php-entrypoi…"   5 days ago   Up 5 days   9000/tcp                                docker-web-server-02-sandbox-php-fpm-1
e7c9abb564af   my-nginx       "/docker-entrypoint.…"   5 days ago   Up 5 days   0.0.0.0:8081->80/tcp, :::8081->80/tcp   web-ser
f0afe4c2289d   my-nginx       "/docker-entrypoint.…"   6 days ago   Up 6 days   0.0.0.0:8080->80/tcp, :::8080->80/tcp   web-server
```

調査結果わかったこと、及びこれからの方針策定：
1. ホスト側では既に8080、8081、8082が使われていることがわかった。今回は、新たに<b>8083</b>ポートを使うこととする。  
2. コンテナ名はデフォルトでシステムが名付けた名前を除くとweb-serverとweb-serが使われていたので、新たに<b>web-app03</b>を使うこととする。

##### ３−１−2．準備（HTMLでアプリを書く）
1. アプリの設計：時間、分、秒を入力し、変換ボタンをクリック（タップ）すると入力値を秒に変換した値を表示する
2. アプリの実装：VSCodeのCopilot拡張機能を使ってコードを取得して既存のindex.htmlファイルのbody部に追記する
    - プロンプト：「HTML言語でhh:mm:ssを入力すると、秒に変換して表示するアプリを書いてください。」
    - Copilot回答（一部）
    ```
        <input type="text" id="timeInput" placeholder="hh:mm:ss">
    <button onclick="convertToSeconds()">変換</button>
    <p id="result"></p>

    <script>
        function convertToSeconds() {
            const timeInput = document.getElementById('timeInput').value;
            const timeParts = timeInput.split(':');
            const seconds = (+timeParts[0]) * 60 * 60 + (+timeParts[1]) * 60 + (+timeParts[2]);
            document.getElementById('result').innerText = seconds + ' 秒';
        }
    </script>
    ```

※新規アプリを追加したindex.htmlは[ここ](https://github.com/yuasys/docker-web-server-01-nginx/blob/main/static-html-directory/index.html)を参照すること

##### ３−１−3． 新規のコンテナを作成`create`と起動`run`を一気に実行する`run`コマンドの構成を考える

1. docker run コマンドを使う：`docker run`
2. コンテナ名は web-app03 とする：`docker run --name web-app03`
3. Dockerfileはカレントディレクトリにあるものを使う


---

4. docker-web-server-01-nginxリポジトリ（ディレクトリ）に移動

```
cd docker-web-server-01-nginx 
```
5. 必要に応じて、下記の作業を行う

- これはDockerを利用したローカル開発用のWebサーバーです
- WebサーバーにNGINXが利用されています
- 静的サイト（HTML/CSS/JavaScritp）開発に適しています

### 注意点
- NGINXの開発環境での学習用を想定しています

### 学習の狙い

#### Step1 オリジナルイメージを作る方法

ここでは、<b>オリジナルのイメージ</b>を作って起動する方法を習得することを目標にしています。  

コンテンツの内容を追加・修正してイメージに反映するという練習を重ねることが有効です。練習するには、いくつかのDockerコマンドを使いこなす必要があります。  
以下は、`static-html-directory`内のファイルを追加変更してから、あたらしいイメージを作成する手順の一部です。

> - docker ps  コンテナの状況(status)を確認します
> - docker stop  コンテナの実行を停止します
> - docker rm [コンテナ名、コンテナID] コンテナを削除します
> - docker rmi [コンテナ名、コンテナID] イメージを削除します  
>  ※これ以降の手順は次の「使い方」２．〜３．の項を参照

#### Step2　ホストとコンテナでディレクトリ・ファイルを共有する方法

`-v (--volume)`オプションを使って、ローカル（ホスト側）のファイルをコンテナ内の指定された場所からアクセス可能にする。  

## 使い方
### １．ダウンロード
```
git clone https://github.com/yuasys/docker-web-server-01-nginx.git
```

起動や停止は、以下でディレクトリを移動してから行ってください。
```
cd docker-web-server-01-nginx
```

### ２．イメージを作成
```
docker build -t my-nginx .
```

オプションの解説  

> -t イメージ名:タグ をつける  
    ここではイメージ名はmy-nginx  
    タグ名はなし（なしの場合、デフォルトでlatestとなる）  
.  DockerFileの相対パス指定  
    ここではカレントディレクトリ内を指定している

### ３．起動

#### 3-1 独自のイメージを作成し、実行する

```
docker run --name web-server -p 8080:80 -d --rm my-nginx
```

#### 3-2 ディレクトリ・ファイルを共有する

```
docker run --name web-server -p 8080:80 -d -v $(pwd)/static-html-directory:/usr/share/nginx/html my-nginx
```

カレントディレクトリの `static-html-directory` ディレクトリが
コンテナ内の `/usr/share/nginx/html` に配置される。この方法は、コンテンツの追加修正などが多いケースでは有効です。一旦立ち上げてしまうとコンテンツ変更はホスト側から変更すると即時コンテナ側に反映されるので便利です。しかし、各種設定データには向いていません。

- コンテナからローカルのファイルを操作可能になる
- ローカルからコンテナ内で参照されるファイルを変更できる
　(イメージを作り直さずに、ファイルの更新が反映できる)

#### 3-3 注意事項

既存のイメージは予め`docker rmi <イメージID>`で削除しておくこと、そうしないと既存のイメージがそのまま利用されるので内容が更新されないままの状態となります。


起動後、以下のURLにアクセスして動作を確認できます。  
http://localhost:8080

※注意 VPSなどクラウド上のDocker環境で開発している場合はlocalhostの部分をVPSのurlまたはグローバルアドレスに適宜読み替えてください  
      例：http://xxx.xxxxx.xxx:8080

### ４．状態確認
```
docker ps
```
StateがUpになっていたらOKです。


### ５．コンテナの中に入って操作する
コンテナが動いているときに以下で、それぞれのコンテナの中で操作することができます。
```
docker exec -it web-server /bin/bash
```


コンテナから出るには以下を実行してください
```
exit
```


### ６．停止
```
docker stop web-server
```
