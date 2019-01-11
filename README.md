cisco_show_commands
===================

Cisco IOS装置に対してshowコマンドを実行して出力結果をファイルに保存します。

これ（↓）をもとに改造したものです。

<https://github.com/takamitsu-iida/ansible-role-cisco_show_run>

大きな違いはshow interfacesコマンドおよびshow ip routeコマンドの出力をCSVに変換してから保存している点です。

以下のコマンドでインストールした後、お好みのロール名になるようにフォルダ名を変更してください。

```bash
ansible-galaxy install -p ./roles git+https://github.com/takamitsu-iida/ansible-role-cisco_show_commands.git
```

Requirements
------------

対象はCisco装置であることを前提にしています。
対象機器にTelnetで接続したい場合は別モジュールが別途必要です。

<https://github.com/takamitsu-iida/ansible-mytelnet>

Role Variables
--------------

PREFIX: ログファイルの先頭につける文字列です。デフォルトは''です。

LOG_PATH: ログを残すフォルダです。デフォルトは `"{{ lookup('env', 'PWD') + '/log' }}"` です。

Dependencies
------------

依存するロールはありません。

Example Playbook
----------------

使い方はinclude_roleで呼び出すだけです。

```yml
- name: execute show commands
  hosts: routers
  gather_facts: false

  tasks:
    - include_role:
        name: cisco_show_commands
```

これを実行すると対象ノードあたり３個のファイルが生成されます。

```bash
log
├── r1_show_commands_2019-01-11@16:31:37.txt
├── r1_show_commands_2019-01-11@16:31:37_interface.csv
├── r1_show_commands_2019-01-11@16:31:37_route.csv
├── r2_show_commands_2019-01-11@16:31:37.txt
├── r2_show_commands_2019-01-11@16:31:37_interface.csv
├── r2_show_commands_2019-01-11@16:31:37_route.csv
├── r3_show_commands_2019-01-11@16:31:37.txt
├── r3_show_commands_2019-01-11@16:31:37_interface.csv
├── r3_show_commands_2019-01-11@16:31:37_route.csv
├── r4_show_commands_2019-01-11@16:31:37.txt
├── r4_show_commands_2019-01-11@16:31:37_interface.csv
└── r4_show_commands_2019-01-11@16:31:37_route.csv
```

show interfacesコマンドの出力結果はCSVに変換され、拡張子.csvで保存されます。

<img width="942" alt="interfaces" src="https://user-images.githubusercontent.com/21165341/51014162-8d8d1300-15a9-11e9-979d-bcb94b09081f.png">

同様に、show ip routeコマンドの出力結果もCSVに変換されます。

<img width="949" alt="route" src="https://user-images.githubusercontent.com/21165341/51019673-267a5900-15bf-11e9-96c3-44b3a0728442.png">

実際に利用するときには、設定変更するタスクの前後に挿入するとよいでしょう。

```yml
- name: execute show commands
  hosts: routers
  gather_facts: false

  tasks:
    - include_role:
        name: cisco_show_commands

    - 設定変更するタスク

    - include_role:
        name: cisco_show_commands
```

このようにすると対象ノードあたり６個ものファイルが生成されてしまい、ファイルの識別が難しくなります。

作業前と作業後でログ置き場を変えたい場合は、このようにします。
作業前はbefore、作業後はafterにログが残ります。

```yml
- name: execute show commands
  hosts: routers
  gather_facts: false

  tasks:
    - include_role:
        name: cisco_show_commands
      vars:
        LOG_PATH: "{{ lookup('env', 'PWD') + '/log/before' }}"

    - 設定変更するタスク

    - include_role:
        name: cisco_show_commands
      vars:
        LOG_PATH: "{{ lookup('env', 'PWD') + '/log/after' }}"
```

ログの置き場は同じにして、ファイル名の先頭になにか付けたい場合は、このようにします。

```yml
- name: execute show commands
  hosts: routers
  gather_facts: false

  tasks:
    - include_role:
        name: cisco_show_commands
      vars:
        PREFIX: before_

    - 設定変更するタスク

    - include_role:
        name: cisco_show_commands
      vars:
        PREFIX: after_
```

License
-------

BSD

Author Information
------------------

Takamitsu IIDA
