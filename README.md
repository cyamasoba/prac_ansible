GB換装FY25において、環境構築を自動で行うためのansibleプログラム

01_setup_ansible.yml
    ・ansible用のユーザを作成して、ansible実行用に鍵交換を行うplaybook
    ・このplaybookを使用するには、OSインストール時にrootユーザを作成し、
    SSHでの接続を許可しておく必要あり
    ・実行時に--ask-passオプションを使ってrootパスワードを入力する必要あり
        group_vars/all/user.yml
            ansible_user_group:ansible実行ユーザの属するグループを定義
            ansible_user_info:ansible実行ユーザのユーザ情報を定義
        group_vars/all/default.yml
            controller_user_info:コントローラPCのユーザ名を定義
        コントローラpcの~/.ssh/にid_rsa.pubが存在する必要がある

02_create_user.yml
    ・指定したユーザを各サーバに作成するplaybook
        group_vars/all/user.yml
            common_user_groups:全てのホストに作成するグループを定義
            common_user_info:全てのホストに作成するユーザ情報を定義
        group_vars/*.yml
            group_user_groups:あるグループに属するホストに作成するグループを定義
            group_user_info:あるグループに属するホストに作成するユーザの情報を定義
        host_vars/*.yml
            host_user_groups:各ホストに作成するグループを定義
            host_user_info:各ホストに作成するユーザの情報を定義

03_install_packages.yml
    ・指定したパッケージを各サーバにインストールするplaybook
    ※dnfでインストールできるものは以下の変数に格納
        --------------------------
        ex. ***_pkg:
            - php-8.3.15-1.el10
        --------------------------
        group_vars/all/user.yml
            common_pkg:全てのホストにインストールするパッケージを定義
        group_vars/*.yml
            group_pkg:あるグループに属するホストにインストールするパッケージを定義
        host_vars/*.yml
            host_pkg:各ホストにインストールするパッケージを定義

04_replication.yml
    ・db-srcとdb-rep間でMySQLのレプリケーション構成を構築するplaybook
    ・インベントリでdb-srcとdb-repをまとめたreplication_groupを定義する
        group_vars/all/user.yml
            ansible_user_info:ansible実行ユーザのユーザ情報を定義
        group_vars/replication_group.yml
            mysql_info:レプリケーション構成に必要な情報を定義

05_setup_ntp.yml
    ・ntpの設定を行うplaybook
        #未完成

06_mount_filesvr.yml
    ・ファイルサーバのマウントを行うplaybook
        #未完成

07_setup_fw.yml
    ・ファイヤウォールの設定を行うplaybook
        group_vars/all/firewalld.yml
            firewalld.services:全てのホストに設定するファイヤーウォール設定をサービス名で定義
            firewalld.ports:全てのホストに設定するファイヤーウォール設定をポート名で定義
        group_vars/*.yml
            firewalld_group.services:あるグループに設定するファイヤーウォール設定をサービス名で定義
            firewalld_group.ports:あるグループに設定するファイヤーウォール設定をポート名で定義
        host_vars/*.yml
            firewalld_host.services:各ホストに設定するファイヤーウォール設定をサービス名で定義
            firewalld_host.ports:各ホストに設定するファイヤーウォール設定をポート名で定義