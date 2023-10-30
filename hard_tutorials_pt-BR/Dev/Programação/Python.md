Conexão com o banco de dados PostgreSQL
PSYCOPG
```
apt install python3-dev libpq-dev
pip install psycopg2
```

```
    import psycopg2
```

Adaptador de bando de dados PostgreSQL
PY-POSTGRESQL

Conexão com o banco de dados ODBC
```
apt-get install python python-pip gcc g++ build-essential
```

```
    import pyodbc
```

Atualizando pip
```
python -m pip install --upgrade pip
```
    ..ou
```
import os
import tkinter as tk

root= tk.Tk()

canvas1 = tk.Canvas(root, width = 300, height = 350, bg = 'lightsteelblue2', relief = 'raised')
canvas1.pack()

label1 = tk.Label(root, text='Upgrade PIP', bg = 'lightsteelblue2')
label1.config(font=('helvetica', 20))
canvas1.create_window(150, 80, window=label1)

def upgradePIP ():
    os.system('start cmd /k python.exe -m pip install --upgrade pip')
    
button1 = tk.Button(text='      Upgrade PIP     ', command=upgradePIP, bg='green', fg='white', font=('helvetica', 12, 'bold'))
canvas1.create_window(150, 180, window=button1)

root.mainloop()
```
