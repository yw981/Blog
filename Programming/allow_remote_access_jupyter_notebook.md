Ĭ������£������ļ� ~/.jupyter/jupyter_notebook_config.py �������ڣ���Ҫ���д�����ʹ�������������������ļ���

jupyter notebook --generate-config

ִ�гɹ��󣬻�����������Ϣ��

Writing default config to: /root/.jupyter/jupyter_notebook_config.py

2. ��������

�Զ�����

�� jupyter notebook 5.0 �汾��ʼ���ṩ��һ���������������룺jupyter notebook password�����ɵ�����洢�� jupyter_notebook_config.json

3. �޸������ļ�
�� jupyter_notebook_config.py ���ҵ�������У�ȡ��ע�Ͳ��޸ġ�

c.NotebookApp.ip='*'
c.NotebookApp.password = u'sha:ce...�ղŸ��Ƶ��Ǹ�����'
c.NotebookApp.open_browser = False
c.NotebookApp.port =8888 #������ָ��һ���˿�, ����ʱʹ�øö˿�

�����������Ժ�Ϳ����ڷ����������� jupyter notebook��jupyter notebook, root �û�ʹ�� jupyter notebook --allow-root���� IP:ָ���Ķ˿�, ��������Ϳ��Է����ˡ�
