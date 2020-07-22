# qa_docker
qa_dockerのリポジトリ
https://git-codecommit.ap-northeast-1.amazonaws.com/v1/repos/qa_docker

※CodeCommit リポジトリへの HTTPS 接続の方法は下記です。
https://docs.aws.amazon.com/ja_jp/codecommit/latest/userguide/setting-up-https-unixes.html

## 起動方法について記載
### dockerファイルとソースを取得

1. Hostの任意パスの上でqa_dockerのリポジトリをcloneし、ディレクトリに移動
```
$ git clone https://git-codecommit.ap-northeast-1.amazonaws.com/v1/repos/qa_docker
$ cd （任意パス）/qa_docker
```

2. Host上でqa_apiのリポジトリをcloneします。
```
$ cd （任意パス）/qa_docker/qa_api
$ git clone https://git-codecommit.ap-northeast-1.amazonaws.com/v1/repos/qa_api
```

3. Host上でqa-appのリポジトリをcloneします。
```
$ cd （任意パス）/qa_docker/qa-app
$ git clone https://git-codecommit.ap-northeast-1.amazonaws.com/v1/repos/qa-app
```

4. Host上でqa-solrのリポジトリをcloneします。
```
$ cd （任意パス）/qa_docker
$ git clone https://git-codecommit.ap-northeast-1.amazonaws.com/v1/repos/qa-solr
```

フォルダの構造は下記です。

```
qa_docker
│ 
├─docker-compose.yml
│ 
├─nginxフォルダ
│       ├-Dockerfile
│       └-local.conf
├─qa-appフォルダ
│       ├-Dockerfile
│       └-qa-appソース
├─qa_apiフォルダ
│       ├-Dockerfile
│       └-qa_apiソース
└─qa-solrフォルダ
         ├-Dockerfile
         └-core定義ファイル
```
------------

下記コマンドを実行前に、ディレクトリに移動
$ cd （任意パス）/qa_docker

### docker-composeよりコンテナを起動
docker-compose up -d --build を実行

### 起動状態の確認
docker-compose ps を実行

```
[root@ip-10-60-52-253 qa_docker]# docker-compose ps
       Name                      Command               State           Ports
-------------------------------------------------------------------------------------
qa_docker_angular_1   docker-entrypoint.sh npm start   Up      0.0.0.0:8080->8080/tcp
qa_docker_django_1    bash -c python3 manage.py  ...   Up      0.0.0.0:8000->8000/tcp
qa_docker_nginx_1     /docker-entrypoint.sh ngin ...   Up      0.0.0.0:80->80/tcp
qa_docker_solr_1      docker-entrypoint.sh bash  ...   Up      0.0.0.0:8983->8983/tcp
```
※ StateがUpになければ、OKです。

ボリュームを使っていますので、一回に削除して再起動する方がいいです。
docker-compose down --volumes ボリュームを削除
docker-compose up -d 再起動

### バッチのコマンドを実行方法
docker-compose exec django python manage.py solr_upd_index
docker-compose exec django python manage.py datafileupload

### ports 指定
```
例：<Host Port>:<Container Port> port指定できます。
    ports:
      - "80:80"
```

### ソースを修正である場合
1. qa_apiはHost上でそのままソースを取得すれば、OKです。
2. qa-appはdocker中にbuildしますので、下記のコマンドを実行
docker-compose down --volumes ボリュームを削除
docker-compose build angular qa-appのみをbuildします
docker-compose up -d 再起動
