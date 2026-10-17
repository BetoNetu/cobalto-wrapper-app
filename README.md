# Cobalto Webapp

Um wrapper desktop nativo e leve para a aplicação web Cobalto. Usa o
[pywebview](https://pywebview.flowrl.com/) para renderizar o site dentro de uma
janela nativa do sistema operacional, aproveitando o motor web que já existe na
sua máquina.

---

## 📦 Instalação

### Opção 1: Binários pré-compilados (recomendado)

A forma mais fácil de obter o Cobalto é baixar um binário pronto na página de
[**Releases**](../../releases). Os binários são gerados automaticamente pelo
GitHub Actions a cada tag de release.

| Plataforma | Arquivo | Observações |
| :--- | :--- | :--- |
| Windows | `Cobalto-windows.exe` | Usa EdgeChromium nativo (WebView2) |
| Linux   | `Cobalto-linux`     | Usa GTK se disponível, senão Qt |
| macOS   | `Cobalto-macos.app` | Usa Cocoa/WebKit nativo |
| Android | `` | Usa xxxxx |

Basta baixar, dar permissão de execução (no Linux/macOS) e rodar.

### Opção 2: Compilar do código-fonte

Você vai precisar de:

- **Python 3.12+**
- **[uv](https://docs.astral.sh/uv/)** (recomendado) **ou** `pip` + `venv`
- Um **compilador C** (apenas para alguns backends — veja abaixo) - recomendado [gcc](https://gcc.gnu.org/) 

Clone o repositório:

```bash
git clone <url-do-seu-repo> "Cobalto webapp"
cd "Cobalto webapp"
```
Agora escolha **um** dos dois fluxos abaixo. Não misture os dois — use `uv`
**ou** `pip`, nunca ambos no mesmo ambiente.

#### Fluxo A — com `uv` (recomendado)

<<<<<<< Updated upstream
- `./make.sh build [config]` - Apply configuration and build
- `./make.sh keygen` - Generate signing key
- `./make.sh test` - Install and test APK on connected device
- `./make.sh clean` - Delete generated sources and build files
-
- `./make.sh apk` - Build APK from already generated sources
- `./make.sh aab` - Build AAB from already generated sources
- `./make.sh apply_config` - Apply settings from configuration file
- `./make.sh get_java` - Download OpenJDK 17 locally
=======
O `uv` cria o ambiente virtual automaticamente e gerencia o `pyproject.toml`:
>>>>>>> Stashed changes

```bash
uv sync
```

Isso já lê o `pyproject.toml` do repositório e instala tudo que está declarado.

#### Fluxo B — com `pip` + `venv` (tradicional)

O `pip` **não** lê o `pyproject.toml` automaticamente e **não** cria o
ambiente virtual por você. Você precisa fazer os dois passos manualmente:

```bash
python -m venv .venv

# Linux / macOS
source .venv/bin/activate

# Windows (PowerShell)
.venv\Scripts\Activate.ps1

# Windows (cmd)
.venv\Scripts\activate.bat
```

Depois instale o pacote em modo editável (para que o entry point
`cobalto-webapp` funcione):

```bash
pip install --upgrade pip
pip install -e .
```

---

## 🛠️ Compilando do código-fonte — opções de backend gráfico

O `pywebview` suporta vários backends de renderização. O backend "melhor"
depende do seu sistema operacional, das suas preferências e de quanto você
quer empacotar junto.

Escolha **uma** das seções abaixo. Você só precisa instalar o backend que
pretende realmente usar.

> 📝 **Nota sobre os comandos:** onde você vê `uv add "pacote"`, o equivalente
> com `pip` é `pip install "pacote"`. Nos exemplos abaixo mostro os dois.

### Comportamento padrão (sem backend extra instalado)

O `pywebview` tenta detectar automaticamente um backend utilizável nesta ordem:

- **Windows:** `edgechromium` → `mshtml`
- **macOS:**   `cocoa`
- **Linux:**   `gtk` → `qt`

Se nenhum estiver disponível, o app falha com
`WebViewException: You must have either QT or GTK with Python extensions installed`.

No **Windows** e no **macOS**, o backend padrão já vem com o sistema, então
normalmente você não precisa instalar nada extra.

No **Linux**, você precisa instalar pelo menos um backend por conta própria.

---

### 🐧 Backends para Linux

#### GTK (padrão, mais nativo no Linux)

Dependências do sistema:

```bash
sudo apt install python3-gi python3-gi-cairo gir1.2-gtk-3.0 gir1.2-webkit2-4.0
```

Lado Python:

```bash
# uv
uv add "pywebview[gtk]; sys_platform == 'linux'"

# pip
pip install "pywebview[gtk]; sys_platform == 'linux'"
```

> O GTK usa `PyGObject`, que é difícil de empacotar dentro de um binário
> PyInstaller. Se você pretende distribuir um executável único para Linux,
> **o Qt é a escolha melhor**.

#### Qt (recomendado para distribuição no Linux)

```bash
# uv
uv add "pywebview[qt]; sys_platform != 'android'"

# pip
pip install "pywebview[qt]; sys_platform != 'android'"
```

Isso instala `qtpy`, `PyQt6` e `PyQt6-WebEngine`.

#### Qt5 (legado)

```bash
# uv
uv add "pywebview[qt5]; sys_platform != 'android'"

# pip
pip install "pywebview[qt5]; sys_platform != 'android'"
```

#### PySide6 (Qt6, LGPL)

```bash
# uv
uv add "pywebview[pyside6]; sys_platform != 'android'"

<<<<<<< Updated upstream
<details>
<summary><b>JavaScript API</b></summary>

The app injects a `window.WebToApk` object into every page. The methods are available at any time (a few, such as notifications, additionally require the user's permission).

| Method | Description |
|---|---|
| `showShortToast(message)` | Show a short Android toast. |
| `showLongToast(message)` | Show a long Android toast. |
| `share(title, text, url)` | Open the system share sheet. |
| `showNotification(title, message)` | Post a local Android notification. |
| `requestNotificationPermission()` | Ask for the notification permission. |
| `hasNotificationPermission()` | `true` if notifications are allowed. |
| `getNotificationPermissionState()` | `"granted"`, `"denied"` or `"prompt"`. |
| `clearAppCache()` | Clear the WebView HTTP cache. |
| `setPullToRefreshEnabled(enabled)` | Toggle the pull-to-refresh gesture at runtime. |
| `setPullToRefreshRefreshing(refreshing)` | Show/hide the refresh spinner. |
| `getDownloads()` | The app's downloads as a JSON string: `[{id, url, filename, mime, bytes, total, status}]`. |
| `removeDownload(id)` | Delete one of the app's downloads — record and file — and return `true` if it went. |
| `openDownloadedFile(id)` | Open a finished download with the system viewer. |
| `shareDownloadedFile(id)` | Send a finished download to another app. |
| `appendChunk(base64)` | One blob chunk, already base64-encoded. Returns `false` if the bytes did not land — no destination, a full disk, a revoked document. |
| `finishSave(ok)` | End a blob export; `ok = false` (or a failed chunk before it) shows the "download failed" toast. |

See also the hooks `WebToApk.onPullToRefresh` (above), `WebToApk.onDownloadStarted(id, url)` and `WebToApk.onDownloadComplete(id)`.

Downloads are handed to `DownloadManager`, so they keep going while the app is backgrounded or even killed. `getDownloads()` is therefore the source of truth: it reports `bytes`/`total` for an in-flight download, so a page can draw its own progress bar, plus a `status` of `pending`, `running`, `paused`, `successful` or `failed` (`total` stays `-1` until the size is known). The two hooks are best-effort — a page-defined callback only exists while the app is alive — so use them as a nudge to re-check `getDownloads()`, and poll it at a sane rate rather than in a tight loop. Downloads are scoped to the app: `getDownloads()` and `removeDownload()` only ever see files this app downloaded, and removing one deletes the file from `Downloads/` — no permission and no dialog are involved. The ids are remembered across restarts, so a page can clean up after itself. Every entry also carries the `url` the download was started from, and `onDownloadStarted(id, url)` hands it over at start time — that is the only way to tell which id belongs to which request when several run at once, since the hook itself carries nothing but the id.

Downloads a page generates itself — `blob:` and `data:` URLs, which `DownloadManager` cannot fetch — take the system file picker instead. Clicking an `<a download href="blob:...">` asks the user where to put it, then `helpers.js` streams the bytes there in 1 MB chunks, so memory stays flat no matter how large the file is. A page driving that protocol itself calls `appendChunk(base64)` and then `finishSave(ok)`; a chunk answers `false` when the bytes did not land (no destination, a full disk, a document the user or the provider revoked), and `finishSave` shows the "download failed" toast for either kind of failure. Nothing is reported as saved unless it was: the write is the only place that can tell, so its failure is not swallowed.

`navigator.mediaSession` (metadata, playback state, position, action handlers) is forwarded to the native media notification automatically — no direct calls needed.

`helpers.js` additionally polyfills a few web APIs:

| Global | Description |
|---|---|
| `toast(message)` | Alias of `showShortToast`. |
| `navigator.share(data)` | Web Share API on top of the native share sheet. |
| `Notification`, `Notification.requestPermission()`, `Notification.permission` | Web Notification API on top of local Android notifications. |
| `GM_addStyle(css)`, `GM_getValue`/`GM_setValue`/`GM_deleteValue`/`GM_listValues`, `GM_addValueChangeListener`, `GM_setClipboard`, `GM_addElement`, `GM_log`, `GM_notification`, `GM_openInTab`, `GM_info`, `GM_xmlhttpRequest` | The Greasemonkey helpers shipped in `helpers.js`; values live in `localStorage`. |

</details>

<details>
<summary><b>Bundling Custom CA Certificates</b></summary>

`trustUserCA = true` only trusts CAs the **user installed on the device**. To make the app trust a CA out of the box, bundle it:

```ini
ca = my_root_ca.pem     # Relative to the config file, space-separated list is allowed
=======
# pip
pip install "pywebview[pyside6]; sys_platform != 'android'"
>>>>>>> Stashed changes
```

#### PySide2 (Qt5, LGPL, legado)

<<<<<<< Updated upstream
A certificate error on the main page is **cancelled** by default. `allowSslErrors = true` shows a
dialog instead, which lets the user through anyway — the same escape hatch a browser offers, but it
is off unless you ask for it, and it never applies to background requests. The dialog names the site
and what is wrong with its certificate (expired, not valid yet, issued for a different host, or not
trusted), because "continue anyway" is not a question a user can answer without knowing whom they
are trusting.

Basic Auth is answered in one of two ways: `basicAuth = login:password` signs in automatically (once
per host and realm, and only for `mainURL`'s host, or its subdomains with `allowSubdomains`), and
anything else — a different host, a second challenge, no `basicAuth` at all — shows the prompt, which
names the host and the realm it is asking for.

</details>
=======
```bash
# uv
uv add "pywebview[pyside2]; sys_platform != 'android'"
>>>>>>> Stashed changes

# pip
pip install "pywebview[pyside2]; sys_platform != 'android'"
```

#### CEF (Chromium Embedded Framework)

```bash
# uv
uv add "pywebview[cef]; sys_platform == 'win32'"

# pip
pip install "pywebview[cef]; sys_platform == 'win32'"
```

> O CEF é primariamente um alvo Windows. Não é recomendado no Linux porque
> exige um binário `cefpython3` distribuído separadamente, que não está no
> PyPI para todas as versões de Python.

<<<<<<< Updated upstream
**Local notifications work.** While your page is open it can show real Android notifications through the web `Notification` API (`helpers.js` polyfills `new Notification(...)`, `Notification.requestPermission()` and `Notification.permission` on top of the native bridge), and `navigator.mediaSession` drives the media notification and the lock-screen controls.
=======
---
>>>>>>> Stashed changes

### 🪟 Backends para Windows

#### EdgeChromium (padrão, recomendado)

Já vem embutido no `pywebview` no Windows via `pythonnet`. Requer o
**Microsoft Edge WebView2 Runtime**, que já vem pré-instalado no Windows 11 e
na maioria dos Windows 10 atualizados.

```bash
# uv
uv add pywebview

# pip
pip install pywebview
```

<<<<<<< Updated upstream
- Target Android API: 33 (Android 13)
- Minimum Android API: 26 (Android 8.0)
- Build tools version: 33.0.2
- Gradle version: 7.4
- Required Java version: 17 or newer (a JDK, not a JRE)
=======
Nenhuma instalação extra de backend é necessária.
>>>>>>> Stashed changes

#### MSHTML (legado, motor do IE11)

<<<<<<< Updated upstream
- Web SQL is off and passwords are never saved, so those two settings are no longer config keys.
  `setSavePassword` has been an explicit no-op in the WebView implementation since at least WebView
  76 and there is no password store behind it. `setDatabaseEnabled` is a no-op on current WebView
  (Web SQL was removed from Chromium), but an old engine still honours it — so it is pinned to
  `false`, which makes an old device behave like a new one instead of exposing a capability that
  depends on the installed WebView. A config that still sets `SavePassword` or `DatabaseEnabled`
  fails to apply and the line has to be deleted
- All app data is stored in the app's private directory
- The keystore `my-release-key.jks` (in the repository root) password is set to "123456" by default
- Internet permission is required and automatically included
- `blockLocalhostRequests` covers loopback however it is spelled: `127.0.0.0/8`, `::1`, `0.0.0.0`, `localhost`
- `forceDarkTheme` reports a dark system to the page and themes the app dark, whatever the real system setting is: the WebView answers `prefers-color-scheme: dark` and the app uses its `values-night` colours. It does **not** restyle the page — a site without a dark theme stays as it is, so add a userscript if you want one
- If you need to support [different Android versions](https://apilevels.com/), edit `build.gradle` accordingly
- Based on the original work from: https://github.com/successtar/web-to-app  

## Media

The bridge has a media surface — `updateMediaMetadata`, `updateMediaPlaybackState`,
`updateMediaPositionState` and `setMediaActionHandlers` — which drives the foreground service that
owns the notification and the lock-screen controls. It is not meant to be called directly: a WebView
has no `navigator.mediaSession` integration at all, so `helpers.js` defines that standard object and
forwards it here. A page that uses `navigator.mediaSession` gets working system controls without
knowing this app exists.
=======
Também já vem embutido. Só é útil como fallback em sistemas muito antigos.
Para forçá-lo:

```python
webview.start(gui='mshtml')
```

#### Qt / PySide no Windows

```bash
# uv
uv add "pywebview[qt]; sys_platform != 'android'"
uv add "pywebview[pyside6]; sys_platform != 'android'"

# pip
pip install "pywebview[qt]; sys_platform != 'android'"
pip install "pywebview[pyside6]; sys_platform != 'android'"
```

#### CEF no Windows

```bash
# uv
uv add "pywebview[cef]; sys_platform == 'win32'"

# pip
pip install "pywebview[cef]; sys_platform == 'win32'"
```

#### Qt5 / PySide2 (legado) no Windows

```bash
# uv
uv add "pywebview[qt5]; sys_platform != 'android'"
uv add "pywebview[pyside2]; sys_platform != 'android'"

# pip
pip install "pywebview[qt5]; sys_platform != 'android'"
pip install "pywebview[pyside2]; sys_platform != 'android'"
```

---

### 🍎 Backends para macOS

#### Cocoa (padrão, recomendado)

Usa o `WKWebView` nativo. Em um Python instalado via **python.org** ou
**Homebrew**, os frameworks PyObjC necessários já estão presentes.

Se você estiver usando um Python mínimo ou embutido, instale-os manualmente:

```bash
# uv
uv add "pyobjc-core \
        pyobjc-framework-Cocoa \
        pyobjc-framework-Quartz \
        pyobjc-framework-WebKit \
        pyobjc-framework-security; sys_platform == 'darwin'"

# pip
pip install "pyobjc-core \
             pyobjc-framework-Cocoa \
             pyobjc-framework-Quartz \
             pyobjc-framework-WebKit \
             pyobjc-framework-security; sys_platform == 'darwin'"
```

#### Qt / PySide no macOS

```bash
# uv
uv add "pywebview[qt]; sys_platform != 'android'"
uv add "pywebview[pyside6]; sys_platform != 'android'"

# pip
pip install "pywebview[qt]; sys_platform != 'android'"
pip install "pywebview[pyside6]; sys_platform != 'android'"
```

#### Qt5 / PySide2 (legado) no macOS

```bash
# uv
uv add "pywebview[qt5]; sys_platform != 'android'"
uv add "pywebview[pyside2]; sys_platform != 'android'"

# pip
pip install "pywebview[qt5]; sys_platform != 'android'"
pip install "pywebview[pyside2]; sys_platform != 'android'"
```

---

## 🚀 Rodando a partir do código-fonte

### Com `uv`

```bash
uv run cobalto-webapp
```

Ou diretamente:

```bash
uv run python src/cobalto_webapp/__init__.py
```

### Com `pip`

Ative o ambiente virtual primeiro, depois rode:

```bash
cobalto-webapp
```

Ou diretamente:

```bash
python src/cobalto_webapp/__init__.py
```

> 💡 O comando `cobalto-webapp` só existe se você rodou `pip install -e .`
> (modo editável) dentro do venv ativado. Se não rodou, use a segunda forma.

Para forçar um backend específico (útil para testes):

```python
# dentro de main()
webview.start(gui='qt')       # ou 'gtk', 'cocoa', 'edgechromium', 'mshtml'
```

---

## 📱 Android e iOS

Android e iOS são compilados pelo workflow do GitHub Actions. Eles
exigem um conjunto de ferramentas móvel separado e estão fora do escopo deste
wrapper desktop.

## 🧱 Compilando um binário distribuível por conta própria

### Windows / Linux (PyInstaller)

Com `uv`:

```bash
uv add --dev pyinstaller
uv run pyinstaller main.py --windowed --onefile --name Cobalto
```

Com `pip`:

```bash
pip install pyinstaller
pyinstaller main.py --windowed --onefile --name Cobalto
```

A saída ficará em `dist/`.

Para reduzir o tamanho do binário, exclua backends que você não usa:

```bash
# uv
uv run pyinstaller main.py \
    --windowed --onefile --name Cobalto \
    --exclude-module PyQt6 \
    --exclude-module gi

# pip
pyinstaller main.py \
    --windowed --onefile --name Cobalto \
    --exclude-module PyQt6 \
    --exclude-module gi
```

### macOS (py2app)

Veja o `setup.py` na raiz do repositório.

Com `uv`:

```bash
uv add --dev py2app
uv run python setup.py py2app
```

Com `pip`:

```bash
pip install py2app
python setup.py py2app
```

O bundle `.app` ficará em `dist/`.

---

## 🐛 Solução de problemas

| Erro | Causa | Correção |
| :--- | :--- | :--- |
| `ModuleNotFoundError: No module named 'webview'` | Você importou `pywebview` em vez de `webview` | A distribuição se chama `pywebview`, mas o módulo é `webview`. |
| `WebViewException: You must have either QT or GTK...` | Nenhum backend instalado | Instale `pywebview[qt]` ou configure o GTK (veja a seção Linux). |
| `ModuleNotFoundError: No module named 'qtpy'` | Você instalou `PyQt6`, mas não o `pywebview[qt]` | Rode `uv add "pywebview[qt]"` (ou `pip install`). |
| `ModuleNotFoundError: No module named 'gi'` | GTK não instalado | Instale `python3-gi` e `gir1.2-webkit2-4.0`, ou troque para Qt. |
| `cobalto-webapp: command not found` | Você usou `pip` e não rodou `pip install -e .` | Rode `pip install -e .` dentro do venv ativado, ou use `python src/cobalto_webapp/__init__.py`. |
| App abre com janela em branco | O site remoto bloqueou a requisição ou usa APIs não suportadas | Tente `webview.start(debug=True)` e confira o console. |

---

## ⚠️ Aviso legal

**Este projeto é um wrapper não oficial e pessoal.**

Eu **não possuo qualquer afiliação, associação, autorização, endosso ou
vínculo oficial** com a administração institucional ou tecnológica da minha
universidade, nem com quaisquer subsidiárias, afiliadas ou parceiras.

O Cobalto, seu nome, seu logotipo e seu conteúdo são propriedade de
seus respectivos donos. Este wrapper apenas carrega o site público dentro de
uma janela nativa por conveniência. Ele não modifica, intercepta nem
redistribui quaisquer dados ou serviços da universidade. Nesse sentido, ele está licenciado

Se você é administrador da instituição e tem preocupações sobre este projeto,
por favor abra uma issue e eu tratarei o assunto prontamente.
>>>>>>> Stashed changes
