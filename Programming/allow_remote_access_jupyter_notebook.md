默认情况下，配置文件 ~/.jupyter/jupyter_notebook_config.py 并不存在，需要自行创建。使用下列命令生成配置文件：

jupyter notebook --generate-config

执行成功后，会出现下面的信息：

Writing default config to: /root/.jupyter/jupyter_notebook_config.py

2. 生成密码

自动生成

从 jupyter notebook 5.0 版本开始，提供了一个命令来设置密码：jupyter notebook password，生成的密码存储在 jupyter_notebook_config.json

3. 修改配置文件
在 jupyter_notebook_config.py 中找到下面的行，取消注释并修改。

c.NotebookApp.ip='*'
c.NotebookApp.password = u'sha:ce...刚才复制的那个密文'
c.NotebookApp.open_browser = False
c.NotebookApp.port =8888 #可自行指定一个端口, 访问时使用该端口

以上设置完以后就可以在服务器上启动 jupyter notebook，jupyter notebook, root 用户使用 jupyter notebook --allow-root。打开 IP:指定的端口, 输入密码就可以访问了。
