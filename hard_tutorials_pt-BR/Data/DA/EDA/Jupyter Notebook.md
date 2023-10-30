**Inicializando serviço**
```
jupyter notebook
jupyter notebook --version
```

**Rodando servidor público**
```
jupyter notebook --generate-config
cd ~/.jupyter
nano jupyter_notebook_config.py
# Set options for certfile, ip, password, and toggle off

# browser auto-opening

c.NotebookApp.certfile = u'/absolute/path/to/your/certificate/mycert.pem'

c.NotebookApp.keyfile = u'/absolute/path/to/your/certificate/mykey.key'
# Set ip to '*' to bind on all interfaces (ips) for the public server
c.NotebookApp.ip = '*'
c.NotebookApp.password = u'sha1:bcd259ccf...<your hashed password here>'
c.NotebookApp.open_browser = False

# It is a good idea to set a known, fixed port for server access
c.NotebookApp.port = 9999

jupyter notebook
```

**Instalação**

Linux
`conda install -c conda-forge jupyterlab`
    .. ou
`pip install jupyterlab`
    .. ou
`pip3 install jupyterlab`
    .. ou
```
pipenv install jupyterlab
pipenv shell
```
    .. ou
```
pipenv install git+git://github.com/jupyterlab/jupyterlab.git#egg=jupyterlab
pipenv shell

pipenv shell
jupyter lab
```

Docker
```
docker pull jupyter/datascience-notebook:latest
docker run --rm -p 8888:8888 -e JUPYTER_ENABLE_LAB=yes -v "$PWD":/home/jovyan/work jupyter/datascience-notebook

http://<hostname>:8888/?token=<token>
```

```
jupyter/base-notebook
jupyter/minimal-notebook
jupyter/r-notebook
jupyter/scipy-notebook
jupyter/tensorflow-notebook
jupyter/datascience-notebook
jupyter/pyspark-notebook
jupyter/all-spark-notebook
```

  

![](https://jupyter-docker-stacks.readthedocs.io/en/latest/_images/inherit.svg)

  

**Configurações**

Senhas
```
jupyter notebook password
Enter password: ****
Verify password: ****
```
    .. ou
```
In [1]: from notebook.auth import passwd
In [2]: passwd()
Enter password:
Verify password:
```
```
    .. ou
```
```
cd ~/.jupyter
nano jupyter_notebook_config.py


`c.NotebookApp.password = u'sha1:67c9e60bb8b6:9ffede0825894254b2e042ea597d771089e11aed'`
```
    .. ou
```
cat ~/.jupyter/jupyter_notebook_config.py

echo "c.NotebookApp.token = u''" >> ~/.jupyter/jupyter_notebook_config.py
c.NotebookApp.token = ''
c.NotebookApp.password = u''
c.NotebookApp.open_browser = True
c.NotebookApp.ip = 'localhost'
```

```
jupyter notebook --ip='*' --NotebookApp.token='' --NotebookApp.password=''
```

```
export JUPYTER_TOKEN='password'
docker run -it --rm -p 8888:8888 -u $(id -u ${USER}):$(id -g ${USER}) -e JUPYTER_TOKEN=$JUPYTER_TOKEN -v /home/<user>/jupyter:/tf/ tensorflow/tensorflow:latest-py3-jupyter
```

`docker run -d -v ~/src/work:/home/jovyan/work -p 8888:8888 jupyter/datascience-notebook start-notebook.sh --NotebookApp.token=''  -e GRANT_SUDO=yes`

`docker run -it --rm -p 8888:8888 -e GRANT_SUDO=yes --user root gcr.io/kubeflow-images-staging/tensorflow-notebook-cpu`

Cell execution history
`_ih[-5:] # code of the 5 most recently run cells`

Autoreload
```
%load_ext autoreload
%autoreload 2
```

%debug and the iPython debugger
`%debug`

  

From IPython.core.debugger import ```
set_trace
set_trace()

JupyterLab
pip install jupyterlab
```

.. ou
```
jupyter serverextension enable --py jupyterlab --sys-prefix
```

jupyter lab


------
[https://github.com/markusschanta/awesome-jupyter](https://github.com/markusschanta/awesome-jupyter)