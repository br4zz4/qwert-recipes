# QWERT Recipes

Catálogo oficial de recipes do [qwert](https://github.com/br4zz4/qwert). Cada recipe
é um pacote que o qwert sabe instalar e configurar.

Este repo também serve de **modelo** para você criar o seu próprio repo de recipes e
usá-lo com `qwert plugin add <url>`.

## Uso

O catálogo default é baixado automaticamente (git clone) com:

```sh
qwert recipes update
```

Para instalar uma recipe do catálogo:

```sh
qwert use tmux
```

## Estrutura

Cada recipe é um diretório `<name>/` na raiz do repo com até dois arquivos — ambos opcionais
(o qwert clona o catálogo e lê direto de `~/.local/share/qwert/recipes/<name>/`):

```
tmux/
├── install.toml   # como instalar/atualizar/remover a ferramenta + metadados
└── setup.toml     # como configurar pontos (symlinks, cópias, comandos)
```

## Schema

### `install.toml`

```toml
[meta]
name = "tmux"
version = "1.0.0"
description = "Terminal multiplexer"
type = "brew"          # brew | apt | pacman | qwert
depends = []           # outras recipes a instalar antes
pkg = "git-delta"      # opcional: sobrescreve o nome do pacote (default: meta.name)

[check]
command = "tmux"       # binário que confirma a instalação
version_flag = "-V"    # flag para obter a versão

# Só para type = "qwert" (comandos custom) ou fallback por plataforma
[install]
macos = "comando custom de instalação"
debian = ["passo um", "passo dois"]

[upgrade]
macos = "comando custom de upgrade"

[uninstall]
macos = "comando custom de uninstall"
```

**Tipos:**

| Type | Comportamento |
|------|---------------|
| `brew` | instala/atualiza/remove via `brew`, a partir de `meta.name` (ou `meta.pkg`) |
| `apt` | idem via `apt-get` |
| `pacman` | idem via `pacman` |
| `qwert` | usa as seções `[install]`, `[upgrade]`, `[uninstall]` |

Para tipos `brew`/`apt`/`pacman` não escreva seções `[install]` — o qwert deriva os
comandos do próprio gerenciador. Seções explícitas são só para `qwert` ou fallback.

### `setup.toml`

```toml
# symlink: ~/.tmux.conf -> diretório do recipe (~/.qwert/<name>)
to = "~/.tmux.conf"
symlink = true

# comandos: executados no setup (undo = seção [undo])
macos = ["defaults write com.apple.something value"]
debian = ["comando para debian"]

[undo]
macos = ["defaults delete com.apple.something"]
```

**Tipos de setup e undo:**

- `symlink = true` — undo remove o symlink
- cópia (`to` sem symlink) — undo faz backup em `~/.local/share/qwert/backups/<name>/` e remove
- comandos — undo roda a seção `[undo]`; avisa se não estiver definida

## Criando seu próprio repo de recipes

1. Clone este repo como modelo (ou crie em branco com a mesma estrutura):

   ```sh
   git clone https://github.com/br4zz4/qwert-recipes meu-qwert-recipes
   ```

2. Crie os diretórios e arquivos das suas recipes (veja os schemas acima). Pode começar
   com uma recipe só.

3. Faça push para o seu GitHub:

   ```sh
   git push origin main
   ```

4. Aponte o qwert para o seu repo:

   ```sh
   qwert plugin add https://github.com/SEU_USUARIO/SEU_REPO
   ```

5. Use suas recipes normalmente:

   ```sh
   qwert use <sua-recipe>
   # recipes do seu repo + do catálogo default
   ```

## Testando localmente

Você pode testar o plugin antes de publicar apontando para um caminho local:

```sh
qwert plugin add /caminho/para/o/seu/repo
```

## Dicas

- Recipes com só `setup.toml` em plugins são tratadas como install default do
  gerenciador da plataforma.
- Mantenha o README e o exemplo de schema atualizados quando mudar o formato.