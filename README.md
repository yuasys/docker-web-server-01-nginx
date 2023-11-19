# docker-web-server-01-nginxリポジトリについて

<b>このリポジトリの使い方</b>

1. 作業用のリポジトリに移動する

```
cd ~/work
```

2. lsコマンドで作業用ディレクトリ配下にdocker-web-server-01-nginxリポジトリ（ディレクトリ）があるか確認する

```
ls 
```

3. もし、docker-web-server-01-nginxリポジトリ（ディレクトリ）があったら「５．」に進む

```
# もし、docker-web-server-01-nginxリポジトリ（ディレクトリ）がなかったら下記コマンドを実行
git clone https://github.com/yuasys/docker-web-server-01-nginx.git 
```

4. docker-web-server-01-nginxリポジトリ（ディレクトリ）に移動

```
cd docker-web-server-01-nginx 
```
5. 必要に応じて、下記の作業を行う

## 概要
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
