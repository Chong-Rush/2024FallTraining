# ���׵ĸ��˲��ʹ
---
> �ο�����
>
> <https://blog.fiveth.cc/p/bb32/>
>
> [����ɱ���Hexo���˲��ʹ�̳� | ���������_��������_bilibili](https://www.bilibili.com/video/BV1Ju4m1c7WR/?spm_id_from=333.1007.top_right_bar_window_default_collection.content.click&vd_source=a01a62f772bf2e577e11004dfef0bde1)
���ǲο���Monday��blog����̣�ͬ������Hexo��ܣ�
>
>�ο�Mondaying��md��д��ʽ
---
## һ.׼������

1.����ȥ������[node.js](https://nodejs.org/en)��[git](https://git-scm.com/downloads),Ȼ������Ƿ����سɹ�

  ![](https://picture.gptkong.com/20241101/005852fb2ae31b47a7a80a6a5ea5e9da4d.png)

  �����ܺã�û��ʲô����

2.����������Hexo��ʱ���������

  ���ǲ���������express����cmd����npm install express -g���ֱ���

 ```
 npm error code ECONNRESET
npm error syscall read
npm error errno ECONNRESET
npm error network request to https://registry.npmjs.org/express failed, reason: read ECONNRESET
npm error network This is a problem related to network connectivity.
npm error network In most cases you are behind a proxy or have bad network settings.
npm error network
npm error network If you are behind a proxy, please make sure that the
npm error network 'proxy' config is set properly.  See: 'npm help config'
npm error A complete log of this run can be found in: E:\nodejs\node_cache\_logs\2024-10-31T17_24_20_852Z-debug-0.log
 ```
 ����һ��Ӧ���ǹ������ӵ�ԭ�����ӳ��ִ����ټ�������Դ�ٶȲ���

 �����ӹص�������Դ�����Ա�����Դ������npm config set registry https://registry.npmmirror.com

 ����npm install hexo-cli -g����ʾ

 ```
 added 53 packages in 7s

14 packages are looking for funding
  run `npm fund` for details
 ```

 ~~ʹ����ߣ�С������˼�ʮ���ӣ�~~

 ---

## ��.��ֿ�

 ����GitHub��¼�Լ����˺ţ����������ҳ�����²ֿ�

![](https://img.picui.cn/free/2024/11/01/6724ceaa44777.png)
����Ϊ

�Լ�github�û���.github.io

��ѡpublic��Add a README file

���������͵��create

---

## ��.����SSH

�������˵�Ǽ���ʹ��һ�鲿�֣����˺ܶ�ʱ���Դ�

������˵��ֻ��Ҫ��git bashȻ������```ssh-keygen -t rsa -C "�ʼ���ַ"```��������enter�Ϳ�����

�������ڵ�������Windowsϵͳ�û�ʱ��������ҵ��û���Ϊ���ģ�C�̵�Users�ļ��µ��û���ҲΪ����

����һ��ʼ��rsa��Կû��������������ʾ�������

�������ⲻ�󣬶������һЩ·�����ƣ�ʹ�����ҵ�����һ���̴���.ssh�ļ���rsa��Կ

����```ssh-keygen -t rsa -b 4096 -C "����ʼ���ַ" -f E:\.ssh\id_rsa```

��.ssh�ļ��У��ñʼǱ���id_rsa.pub����ssh key

��GitHub��settings��ѡ��SSH and GPS keys,���New SSH key���������⣬�Ѹ��Ƶ�ssh keyճ����ȥ����

�ص�git bash,����```ssh -T git@github.com```��������yes������ֱ���ÿ�α���������ͬ

```
The authenticity of host 'github.com (20.205.243.166)' can't be established.
ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Could not create directory '/c/Users/\301\364\260\327/.ssh' (No such file or directory).
Failed to add the host to the list of known hosts (/c/Users/\301\364\260\327/.ssh/known_hosts).
git@github.com: Permission denied (publickey).
```

��������Ӧ����git bash����Ĭ�ϴ��ҵ�C:\Users\���� ������.ssh�ļ����̶��������ı����Լ�û���κ��ļ�

Ӧ������ʶ������E�̴�����.ssh�ļ���������һЩ����

 1.��.ssh�ļ������洴��known_hosts�ļ�����������

```
github.com ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOMqqnkVzrm0itCRTdd22hHhD1u5VZg2593EaO4zX3mU
```

 2.��.ssh�ļ������洴��config�ļ�����������

```
Host github.com
    HostName github.com
    User git
    IdentityFile E:/ssh/id_rsa
    UserKnownHostsFile E:/ssh/known_hosts
```

 3.git bash����

```
eval "$(ssh-agent -s)"
ssh-add E:/.ssh/id_rsa
```

������ϳ��ԣ����ٴ�����```ssh -T git@github.com```������

```
The authenticity of host 'github.com (20.205.243.166)' can't be established.
ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Could not create directory '/c/Users/\301\364\260\327/.ssh' (No such file or directory).
Failed to add the host to the list of known hosts (/c/Users/\301\364\260\327/.ssh/known_hosts).
Hi liubaiqing! You've successfully authenticated, but GitHub does not provide shell access.
```

��������������ЩС���⣬�������һ�仰�Ѿ�success�ˣ����������Ա��ز���

---


## ��.���ز���

����һ���̴����ļ��У��������blog������ļ�

������ļ�����հ״��Ҽ���git bash

����```hexo init```,����

```
INFO  Cloning hexo-starter https://github.com/hexojs/hexo-starter.git
INFO  Install dependencies
INFO  Start blogging with Hexo!
```

����```hexo install```,����

```
INFO  Validating config
Usage: hexo <command>

Commands:
  clean     Remove generated files and cache.
  config    Get or set configurations.
  deploy    Deploy your website.
  generate  Generate static files.
  help      Get help on a command.
  init      Create a new Hexo folder.
  list      List the information of the site
  migrate   Migrate your site from other system to Hexo.
  new       Create a new post.
  publish   Moves a draft post from _drafts to _posts folder.
  render    Render files with renderer plugins.
  server    Start the server.
  version   Display version information.

Global Options:
  --config  Specify config file instead of using _config.yml
  --cwd     Specify the CWD
  --debug   Display all verbose messages in the terminal
  --draft   Display draft posts
  --safe    Disable all plugins and scripts
  --silent  Hide output on console

For more help, you can use 'hexo help [command]' for the detailed information
or you can check the docs: https://hexo.io/docs/
```

����```hexo g```,����

```
INFO  Validating config
INFO  Start processing
INFO  Files loaded in 276 ms
INFO  Generated: index.html
INFO  Generated: js/jquery-3.6.4.min.js
INFO  Generated: archives/index.html
INFO  Generated: css/style.css
INFO  Generated: archives/2024/11/index.html
INFO  Generated: archives/2024/index.html
INFO  Generated: fancybox/jquery.fancybox.min.js
INFO  Generated: css/images/banner.jpg
INFO  Generated: js/script.js
INFO  Generated: fancybox/jquery.fancybox.min.css
INFO  Generated: 2024/11/01/hello-world/index.html
INFO  11 files generated in 254 ms
```

����```hexo s```,����

```
INFO  Validating config
INFO  Start processing
INFO  Hexo is running at http://localhost:4000/ . Press Ctrl+C to stop.
```

������ַ��Ϳ��Գ��Խ����ˣ�������Ӧ����������
![](https://img.picui.cn/free/2024/11/01/6724d8a5a5287.png)

---

## ��.���߲���

����Ĳ����ļ��������ҵ�_config.yml�ļ����ü��±���
�������ҵ�deploy�����������ȫ��ɾ����ճ�����

```
  type: git
  repository: 
  branch: main
```

ȥ�Լ���GitHub�ֿ���code������https���ӣ�ճ����_config.yml���±������```repository: ```����
�����˳�

�ص������ļ��е�git bash

��װ�Զ����𷢲�����

```
npm install hexo-deployer-git --save
hexo g
hexo d
```

����

```
INFO  Validating config
INFO  Deploying: git
INFO  Clearing .deploy_git folder...
INFO  Copying files from public folder...
INFO  Copying files from extend dirs...
warning: in the working copy of '2024/11/01/hello-world/index.html', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'archives/2024/11/index.html', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'archives/2024/index.html', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'archives/index.html', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'css/style.css', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'fancybox/jquery.fancybox.min.js', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'index.html', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'js/jquery-3.6.4.min.js', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'js/script.js', LF will be replaced by CRLF the next time Git touches it
Author identity unknown

*** Please tell me who you are.

Run

  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"

to set your account's default identity.
Omit --global to set the identity only in this repository.

fatal: unable to auto-detect email address (got '����@LAPTOP-QJ1JI6GM.(none)')
error: src refspec HEAD does not match any
error: failed to push some refs to 'https://github.com/liubaiqing/liubai.github.io.git'
FATAL Something's wrong. Maybe you can find the solution here: https://hexo.io/docs/troubleshooting.html
Error: Spawn failed
    at ChildProcess.<anonymous> (E:\my box\my blog\node_modules\hexo-deployer-git\node_modules\hexo-util\lib\spawn.js:51:21)
    at ChildProcess.emit (node:events:518:28)
    at cp.emit (E:\my box\my blog\node_modules\cross-spawn\lib\enoent.js:34:29)
    at ChildProcess._handle.onexit (node:internal/child_process:293:12)
```

��Ϊ�ǵ�һ�����ã���ʾ��������������û������Ѿ�����ʾ��

```
 git config --global user.email "you@example.com"
 git config --global user.name "Your Name"
```

���ݸ�ʽ�޸����뼴��

������```hexo d```�ϴ������ܻ����

```
INFO  Validating config
INFO  Deploying: git
INFO  Clearing .deploy_git folder...
INFO  Copying files from public folder...
INFO  Copying files from extend dirs...
warning: in the working copy of '2024/11/01/hello-world/index.html', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'archives/2024/11/index.html', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'archives/2024/index.html', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'archives/index.html', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'css/style.css', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'fancybox/jquery.fancybox.min.js', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'index.html', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'js/jquery-3.6.4.min.js', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'js/script.js', LF will be replaced by CRLF the next time Git touches it
On branch master
nothing to commit, working tree clean
fatal: unable to access 'https://github.com/liubaiqing/liubai.github.io.git/': Failed to connect to github.com port 443 after 21179 ms: Could not connect to server
FATAL Something's wrong. Maybe you can find the solution here: https://hexo.io/docs/troubleshooting.html
Error: Spawn failed
    at ChildProcess.<anonymous> (E:\my box\my blog\node_modules\hexo-deployer-git\node_modules\hexo-util\lib\spawn.js:51:21)
    at ChildProcess.emit (node:events:518:28)
    at cp.emit (E:\my box\my blog\node_modules\cross-spawn\lib\enoent.js:34:29)
    at ChildProcess._handle.onexit (node:internal/child_process:293:12)
```

��������ƺ�����Ϊ��������������⣬���Գ��ԹҸ����ӻ��߷�����������```hexo d```������������

֮��ͻᵯ��GitHub�˻���½�ĵ������ɹ�����ʾ���
```
INFO  Validating config
INFO  Deploying: git
INFO  Clearing .deploy_git folder...
INFO  Copying files from public folder...
INFO  Copying files from extend dirs...
warning: in the working copy of '2024/11/01/hello-world/index.html', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'archives/2024/11/index.html', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'archives/2024/index.html', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'archives/index.html', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'css/style.css', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'fancybox/jquery.fancybox.min.js', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'index.html', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'js/jquery-3.6.4.min.js', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'js/script.js', LF will be replaced by CRLF the next time Git touches it
On branch master
nothing to commit, working tree clean
info: please complete authentication in your browser...
Enumerating objects: 24, done.
Counting objects: 100% (24/24), done.
Delta compression using up to 24 threads
Compressing objects: 100% (18/18), done.
Writing objects: 100% (24/24), 278.64 KiB | 17.41 MiB/s, done.
Total 24 (delta 3), reused 0 (delta 0), pack-reused 0 (from 0)
remote: Resolving deltas: 100% (3/3), done.
To https://github.com/liubaiqing/liubai.github.io.git
 + 676aa38...c1a17b6 HEAD -> main (forced update)
branch 'master' set up to track 'https://github.com/liubaiqing/liubai.github.io.git/main'.
INFO  Deploy done: git
```

֮��Ϳ��Դ��������ַ�������GitHub�ֿ��������ɽ��������վ

�ҵ�blog

[https://liubai.github.io](https://liubai.github.io)

---

## ��.�ܽ����
һ�����ʹ�����Ҳ��һ�죬���СС������������ϣ�
�ܶ�ζ�������������������϶��������Դ���Բο���
�������ǿ���һ����������blog
����ȷʵҲѧ�˺ܶණ��������markdown�ĵ����Ժ�git�Ļ���ʹ�ã���ǰ������˵�����ǣ�
����һֱ�Ҳ���ʱ��Ҳû��ʲô����ȥѧϰ��Щ������HnuSec-Star���＾ѵ����
��������ȥ��������ǰû����������

---



