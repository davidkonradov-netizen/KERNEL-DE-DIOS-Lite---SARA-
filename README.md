# KERNEL-DE-DIOS-Lite---SARA- 

📦 Paquete listo para repositorio

Guarda estos archivos tal cual (nombres exactos) y tu repo SARA-NeuroOS estará completo.

---

📁 Estructura final

```
SARA-NeuroOS/
├── README.md
├── install_termux.sh
├── requirements.txt
├── neuroos/
│   ├── __init__.py
│   ├── main.py
│   ├── fractal/
│   │   └── main.py
│   └── ai/
│       └── qwen_llm.py
└── .github/
    └── workflows/
        └── ci.yml
```

---

📄 README.md

```markdown
# SARA-NeuroOS v2.2  
**Red neuronal fractal 3-6-9 + LLM local + Termux**

## Instalación
```bash
pkg update -y && pkg install -y git python tigervnc podman x11-repo ollama
git clone https://github.com/tu-usuario/SARA-NeuroOS.git
cd SARA-NeuroOS
bash install_termux.sh
```

Uso

Comando	Acción	
`python neuroos/main.py sara`	Genera red fractal + chat con Qwen	
`python neuroos/main.py fractal`	Solo imagen 3-6-9	
`python neuroos/main.py qwen`	Solo chat	

Salida
- Imagen PNG: `output/sara_fractal.png`
- Chat interactivo: `🤖 SARA>`

Licencia
MIT

```

---

### 📄 **requirements.txt**
```text
numpy
matplotlib
networkx
websockets
psutil
```

---

📄 install_termux.sh

```bash
#!/data/data/com.termux/files/usr/bin/bash
set -e
echo "[+] Instalando SARA en Termux…"
pkg update -y
pkg install -y git python tigervnc podman x11-repo ollama
pip install --prefer-binary -r requirements.txt
echo "[✅] Listo. Ejecuta: python neuroos/main.py sara"
```

---

📁 neuroos/init.py

```python
__version__ = "2.2"
```

---

📁 neuroos/main.py

```python
#!/usr/bin/env python3
import argparse, sys
from neuroos.fractal.main import FractalSNN
from neuroos.ai.qwen_llm import chat

def cli():
    p = argparse.ArgumentParser(description="SARA CLI")
    p.add_argument("cmd", choices=["sara", "fractal", "qwen"])
    args = p.parse_args()
    if args.cmd == "sara":
        FractalSNN().draw()
        while True:
            try:
                msg = input("🤖 SARA> ")
                if msg in {"exit", "quit"}: break
                print(chat(msg))
            except KeyboardInterrupt: break
    elif args.cmd == "fractal":
        FractalSNN().draw()
    elif args.cmd == "qwen":
        while True:
            msg = input("🤖> ")
            if msg in {"exit", "quit"}: break
            print(chat(msg))

if __name__ == "__main__":
    cli()
```

---

📁 neuroos/fractal/main.py

```python
#!/usr/bin/env python3
import numpy as np, matplotlib, os, networkx as nx
matplotlib.use('Agg')

class FractalSNN:
    def draw(self, filename="sara_fractal.png"):
        G = nx.DiGraph()
        def add(parent, d):
            if d == 0: return
            for i in range(3):
                child = f"{parent}-{i}"
                tipo = 'E' if i % 2 == 0 else 'I'
                G.add_node(child, tipo=tipo)
                G.add_edge(parent, child, weight=round(np.random.uniform(-1, 1), 2))
                add(child, d - 1)
        add("SARA", 3)
        colors = ['green' if G.nodes[n]['tipo'] == 'E' else 'red' for n in G.nodes]
        weights = [d['weight'] for _, _, d in G.edges(data=True)]
        plt = matplotlib.pyplot
        plt.figure(figsize=(10, 7))
        nx.draw(G, node_color=colors, edge_color=weights, edge_cmap=plt.cm.coolwarm)
        os.makedirs("output", exist_ok=True)
        plt.savefig(f"output/{filename}", dpi=150)
        plt.close()
        print(f"[✅] Imagen → output/{filename}")

if __name__ == "__main__":
    FractalSNN().draw()
```

---

📁 neuroos/ai/qwen_llm.py

```python
#!/usr/bin/env python3
import shutil, subprocess

def chat(prompt: str) -> str:
    if not shutil.which("ollama"):
        subprocess.run(["pkg", "install", "-y", "ollama"], check=True)
        subprocess.run(["ollama", "pull", "qwen:1.8b"], check=True)
    return subprocess.check_output(["ollama", "run", "qwen:1.8b", prompt], text=True).strip()
```

---

📁 `.github/workflows/ci.yml`

```yaml
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - uses: actions/setup-python@v5
      with:
        python-version: '3.11'
    - run: pip install -r requirements.txt
    - run: python neuroos/main.py fractal
```

---

✅ Crear y subir

```bash
git init
git add .
git commit -m "feat: SARA 2.2 – fractal 3-6-9 + Qwen chat"
gh repo create tu-usuario/SARA-NeuroOS --public --source=. --push
```

¡Copia estos archivos y tendrás el repo completo!
