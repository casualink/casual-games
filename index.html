import os
import sys
import json
import subprocess
import random
import re
import time
import threading
import urllib.request
import zipfile
import io
import mimetypes
import shutil
import tempfile
import customtkinter as ctk
from tkinter import messagebox
from PIL import Image, ImageTk  # Adicionado para suporte a capas reais dos jogos

# --- PALETA DE CORES PREMIUM CYBERPUNK ---
COR_BG_SISTEMA = "#08090f"     
COR_SIDEBAR = "#0f111a"        
COR_CARD = "#161926"           
COR_TEXTO_MAIN = "#f5f6fa"
COR_TEXTO_MUTED = "#8b949e"
COR_NEON = "#00e6ff"           
COR_GOLD = "#ffb86c"           
COR_PINK = "#ff79c6"           
COR_GREEN = "#50fa7b"          

ARQUIVO_SAVE = os.path.join(os.path.dirname(os.path.abspath(__file__)), "save.json")


# --- GOOGLE DRIVE / JOGOS ---
# O ID abaixo é o arquivo que receberá as publicações automáticas.
DRIVE_PUBLISH_TARGET_ID = "14BaYTQ2I7aVqdbNs0A--gFnoMWCp0din"
# Pacote antigo, usado como fallback caso o novo arquivo ainda não seja um ZIP.
DRIVE_FALLBACK_FILE_ID = "1xenBSQpoji6FFmUh5BOBVj_vF-aVr1q6"
DRIVE_FILE_ID = DRIVE_PUBLISH_TARGET_ID
DRIVE_FILE_NAME = "casual_games.py"
PASTA_JOGOS = os.path.join(os.path.dirname(os.path.abspath(__file__)), "games")
ARQUIVO_DRIVE_LOCAL = os.path.join(os.path.dirname(os.path.abspath(__file__)), DRIVE_FILE_NAME)
ARQUIVO_CATALOGO = os.path.join(PASTA_JOGOS, "catalog.json")
PASTA_PUBLICADOS = os.path.join(os.path.dirname(os.path.abspath(__file__)), "publicados")
ARQUIVO_MEUS_JOGOS = os.path.join(os.path.dirname(os.path.abspath(__file__)), "meus_jogos.json")
CODIGO_DESENVOLVEDOR = "CASUAL"

BASE_DIR = os.path.dirname(os.path.abspath(__file__))
CREDENTIALS_FILE = os.path.join(BASE_DIR, "credentials.json")
TOKEN_FILE = os.path.join(BASE_DIR, "token.json")
DRIVE_SCOPES = ["https://www.googleapis.com/auth/drive"]

LINGUAGENS_SUPORTADAS = [
    "Python", "Java", "JavaScript", "C", "C++", "C#", "TypeScript", "Go", "Rust", "PHP",
    "Swift", "Kotlin", "Ruby", "R", "Scala", "Shell/Bash", "Perl", "Lua", "Elixir", "Dart",
    "Haskell", "Clojure", "Julia", "Assembly", "Fortran", "COBOL", "Pascal", "Ada", "Objective-C",
    "Groovy", "Matlab", "Crystal", "Nim", "Zig", "V", "F#", "Erlang", "Scheme", "Common Lisp",
    "Prolog", "Solidity", "ABAP", "Apex", "PowerShell", "Scratch", "Delphi/Object Pascal",
    "ActionScript", "CoffeeScript", "D", "SQL", "Outro / não listado"
]

def baixar_pacote_http(file_id, destino):
    url = f"https://drive.usercontent.google.com/download?id={file_id}&export=download&confirm=t"
    req = urllib.request.Request(url, headers={"User-Agent": "Mozilla/5.0"})
    with urllib.request.urlopen(req, timeout=90) as r:
        dados = r.read()
    if not dados:
        raise RuntimeError("Download vazio.")
    with open(destino, "wb") as f:
        f.write(dados)
    return destino

def baixar_pacote_drive():
    erros = []
    for file_id in (DRIVE_FILE_ID, DRIVE_FALLBACK_FILE_ID):
        try:
            baixar_pacote_http(file_id, ARQUIVO_DRIVE_LOCAL)
            if zipfile.is_zipfile(ARQUIVO_DRIVE_LOCAL):
                return file_id
            erros.append(f"{file_id}: arquivo baixado não é ZIP")
        except Exception as e:
            erros.append(f"{file_id}: {e}")
    raise RuntimeError("Não foi possível baixar um pacote ZIP válido. " + " | ".join(erros))

def extrair_pacote_drive():
    if not zipfile.is_zipfile(ARQUIVO_DRIVE_LOCAL):
        raise RuntimeError("casual_games.py baixado não é um ZIP válido.")
    os.makedirs(PASTA_JOGOS, exist_ok=True)
    with zipfile.ZipFile(ARQUIVO_DRIVE_LOCAL, "r") as z:
        base = os.path.abspath(PASTA_JOGOS)
        for info in z.infolist():
            nome = info.filename.replace("\\", "/")
            destino = os.path.abspath(os.path.join(PASTA_JOGOS, nome))
            if nome.startswith("/") or ".." in nome.split("/") or not destino.startswith(base + os.sep):
                raise RuntimeError(f"Caminho inseguro no ZfIP: {nome}")
        z.extractall(PASTA_JOGOS)

def obter_servico_drive():
    try:
        from google.auth.transport.requests import Request
        from google.oauth2.credentials import Credentials
        from google_auth_oauthlib.flow import InstalledAppFlow
        from googleapiclient.discovery import build
    except ImportError as e:
        raise RuntimeError(
            "As bibliotecas do Google Drive não estão instaladas.\n\n"
            "Execute no Prompt de Comando:\n"
            "python -m pip install --upgrade google-api-python-client google-auth-httplib2 google-auth-oauthlib"
        ) from e

    if not os.path.isfile(CREDENTIALS_FILE):
        raise RuntimeError(
            "Arquivo credentials.json não encontrado.\n\n"
            "Crie uma credencial OAuth para aplicativo de computador no Google Cloud, "
            "baixe o JSON e coloque-o ao lado deste launcher."
        )

    creds = None
    if os.path.isfile(TOKEN_FILE):
        try:
            creds = Credentials.from_authorized_user_file(TOKEN_FILE, DRIVE_SCOPES)
        except Exception:
            creds = None

    if creds and creds.expired and creds.refresh_token:
        creds.refresh(Request())
    elif not creds or not creds.valid:
        flow = InstalledAppFlow.from_client_secrets_file(CREDENTIALS_FILE, DRIVE_SCOPES)
        creds = flow.run_local_server(port=0)

    with open(TOKEN_FILE, "w", encoding="utf-8") as f:
        f.write(creds.to_json())

    return build("drive", "v3", credentials=creds)

def drive_obter_usuario(service):
    """Retorna a conta Google usada pelo launcher."""
    try:
        dados = service.about().get(
            fields="user(emailAddress,displayName,permissionId)"
        ).execute()
        usuario = dados.get("user", {})
        return {
            "email": usuario.get("emailAddress", "").strip().lower(),
            "nome": usuario.get("displayName", "").strip(),
            "permission_id": usuario.get("permissionId", "")
        }
    except Exception:
        return {"email": "", "nome": "", "permission_id": ""}

def carregar_meus_jogos():
    if not os.path.isfile(ARQUIVO_MEUS_JOGOS):
        return []
    try:
        with open(ARQUIVO_MEUS_JOGOS, "r", encoding="utf-8") as f:
            dados = json.load(f)
        return dados if isinstance(dados, list) else []
    except Exception:
        return []

def salvar_meus_jogos(jogos):
    with open(ARQUIVO_MEUS_JOGOS, "w", encoding="utf-8") as f:
        json.dump(jogos, f, ensure_ascii=False, indent=4)

def registrar_meu_jogo(slug, dados_game, usuario):
    jogos = carregar_meus_jogos()
    jogos = [j for j in jogos if j.get("slug") != slug]
    jogos.append({
        "slug": slug,
        "nome": dados_game.get("nome", ""),
        "autor": dados_game.get("autor", ""),
        "linguagem": dados_game.get("linguagem", ""),
        "email": usuario.get("email", "").lower(),
        "permission_id": usuario.get("permission_id", ""),
        "publicado_em": time.strftime("%Y-%m-%d %H:%M:%S")
    })
    salvar_meus_jogos(jogos)

def remover_registro_meu_jogo(slug):
    salvar_meus_jogos([j for j in carregar_meus_jogos() if j.get("slug") != slug])

def drive_baixar_arquivo(service, file_id, destino):
    from googleapiclient.http import MediaIoBaseDownload
    request = service.files().get_media(fileId=file_id, supportsAllDrives=True)
    with io.FileIO(destino, "wb") as fh:
        downloader = MediaIoBaseDownload(fh, request)
        done = False
        while not done:
            _, done = downloader.next_chunk()

def drive_atualizar_arquivo(service, file_id, caminho_zip):
    from googleapiclient.http import MediaFileUpload
    media = MediaFileUpload(
        caminho_zip,
        mimetype="application/zip",
        resumable=True
    )
    resultado = service.files().update(
        fileId=file_id,
        media_body=media,
        fields="id,name,webViewLink,size,modifiedTime",
        supportsAllDrives=True
    ).execute()
    return resultado

def validar_caminho_zip(nome):
    nome = nome.replace("\\", "/")
    return not nome.startswith("/") and ".." not in nome.split("/")

def criar_zip_da_pasta_jogos(destino_zip):
    if not os.path.isdir(PASTA_JOGOS):
        raise RuntimeError("A pasta games/ ainda não existe.")
    arquivos = []
    for raiz, _, nomes in os.walk(PASTA_JOGOS):
        for nome in nomes:
            if nome == "catalog.json":
                continue
            caminho = os.path.join(raiz, nome)
            rel = os.path.relpath(caminho, PASTA_JOGOS).replace("\\", "/")
            if validar_caminho_zip(rel):
                arquivos.append((caminho, rel))
    if not arquivos:
        raise RuntimeError("Não há jogos para colocar no pacote.")

    with zipfile.ZipFile(destino_zip, "w", zipfile.ZIP_DEFLATED) as z:
        for caminho, rel in arquivos:
            z.write(caminho, rel)
    return destino_zip

def instalar_jogo_no_catalogo(nome, autor, linguagem, descricao, arquivo_principal, pasta_jogo=None, usuario=None):
    os.makedirs(PASTA_JOGOS, exist_ok=True)
    slug = re.sub(r"[^a-zA-Z0-9_-]+", "_", nome).strip("_") or "jogo"
    destino = os.path.join(PASTA_JOGOS, slug)
    if os.path.exists(destino):
        shutil.rmtree(destino, ignore_errors=True)
    os.makedirs(destino, exist_ok=True)

    if pasta_jogo and os.path.isdir(pasta_jogo):
        for item in os.listdir(pasta_jogo):
            origem = os.path.join(pasta_jogo, item)
            destino_item = os.path.join(destino, item)
            if os.path.isdir(origem):
                shutil.copytree(origem, destino_item)
            else:
                shutil.copy2(origem, destino_item)
        principal_rel = os.path.basename(arquivo_principal)
        if not os.path.isfile(os.path.join(destino, principal_rel)):
            shutil.copy2(arquivo_principal, os.path.join(destino, principal_rel))
    else:
        principal_rel = os.path.basename(arquivo_principal)
        shutil.copy2(arquivo_principal, os.path.join(destino, principal_rel))

    dados_game = {
        "nome": nome,
        "versao": "1.0",
        "autor": autor,
        "linguagem": linguagem or "Não informada",
        "descricao": descricao,
        "arquivo": principal_rel,
        "slug": slug,
        "publicador_email": usuario.get("email", "").lower() if usuario else "",
        "publicador_permission_id": usuario.get("permission_id", "") if usuario else ""
    }
    with open(os.path.join(destino, "game.json"), "w", encoding="utf-8") as f:
        json.dump(dados_game, f, ensure_ascii=False, indent=4)
    if usuario:
        registrar_meu_jogo(slug, dados_game, usuario)
    return slug

def encontrar_capa(caminho):
    pasta = os.path.dirname(caminho)
    base = os.path.splitext(os.path.basename(caminho))[0]
    for nome in (base+".png", base+".jpg", base+".jpeg", base+".webp",
                 "cover.png", "cover.jpg", "cover.jpeg", "cover.webp",
                 "capa.png", "capa.jpg", "capa.jpeg", "capa.webp"):
        p = os.path.join(pasta, nome)
        if os.path.exists(p):
            return p
    return None

def construir_catalogo():
    os.makedirs(PASTA_JOGOS, exist_ok=True)
    ignorados = {"launcher_drive.py", "launcher(3).py", DRIVE_FILE_NAME.lower(), "casual_app_store.py"}
    catalogo = []
    for raiz, _, arquivos in os.walk(PASTA_JOGOS):
        for arquivo in arquivos:
            if not arquivo.lower().endswith(".py") or arquivo.lower() in ignorados:
                continue
            caminho = os.path.join(raiz, arquivo)
            rel = os.path.relpath(caminho, PASTA_JOGOS).replace("\\", "/")
            capa = encontrar_capa(caminho)
            catalogo.append({
                "arquivo": rel,
                "nome": os.path.splitext(arquivo)[0].replace("_", " ").replace("-", " ").title(),
                "capa": os.path.relpath(capa, PASTA_JOGOS).replace("\\", "/") if capa else None
            })
    catalogo.sort(key=lambda x: x["nome"].lower())
    with open(ARQUIVO_CATALOGO, "w", encoding="utf-8") as f:
        json.dump(catalogo, f, ensure_ascii=False, indent=4)
    return catalogo

class GameLauncherV3(ctk.CTk):
    def __init__(self):
        super().__init__()
        self.title("Steam 2.0 🌐 Casual Games Suite")
        self.geometry("1100x720") 
        self.minsize(900, 600)  # Define um tamanho mínimo seguro
        self.configure(fg_color=COR_BG_SISTEMA)
        
        self.carregar_dados_sistema()
        
        # --- MOTOR DE BACKGROUND DINÂMICO ---
        self.canvas_bg = ctk.CTkCanvas(self, bg=COR_BG_SISTEMA, highlightthickness=0)
        self.canvas_bg.place(x=0, y=0, relwidth=1, relheight=1)
        # Gerado em lote maior para preencher telas expandidas/4K
        self.gotas_bg = [{"x": random.randint(0, 2560), "y": random.randint(0, 1440), "vel": random.uniform(0.5, 2), "tam": random.randint(1, 3)} for _ in range(60)]
        
        # ---- ESTRUTURA DE LAYOUT ----
        self.sidebar = ctk.CTkFrame(self, width=250, corner_radius=0, fg_color=COR_SIDEBAR, border_width=0)
        self.sidebar.pack(side="left", fill="y")
        self.sidebar.pack_propagate(False)
        
        self.painel_principal = ctk.CTkFrame(self, fg_color="transparent", border_width=0)
        self.painel_principal.pack(side="right", fill="both", expand=True, padx=30, pady=25)
        
        self.construir_sidebar_v3()
        self.abrir_aba_biblioteca()
        self.sincronizar_dados_em_background()
        self.animar_background_sistema()

        self.after(500, self.sincronizar_pacote_drive)

    def sincronizar_pacote_drive(self):
        def trabalho():
            try:
                print("[DRIVE] Baixando pacote...")
                origem_id = baixar_pacote_drive()
                print(f"[DRIVE] Pacote recebido de: {origem_id}")
                print("[DRIVE] Extraindo jogos...")
                os.makedirs(PASTA_JOGOS, exist_ok=True)
                # A pasta games/ é um cache do pacote do Drive.
                # Limpamos somente o conteúdo dela antes de extrair a versão nova.
                for item in os.listdir(PASTA_JOGOS):
                    p = os.path.join(PASTA_JOGOS, item)
                    if item == "catalog.json":
                        continue
                    try:
                        if os.path.isdir(p):
                            shutil.rmtree(p, ignore_errors=True)
                        else:
                            os.remove(p)
                    except Exception as limpeza_erro:
                        print(f"[DRIVE] Não foi possível limpar {p}: {limpeza_erro}")
                extrair_pacote_drive()
                catalogo = construir_catalogo()
                print(f"[DRIVE] {len(catalogo)} jogo(s) encontrado(s).")
                self.after(0, self.abrir_aba_biblioteca)
            except Exception as e:
                print(f"[DRIVE] Erro: {e}")
                # Mantém o catálogo anterior, se existir.
                try:
                    self.after(0, self.abrir_aba_biblioteca)
                except Exception:
                    pass
        threading.Thread(target=trabalho, daemon=True).start()

    def carregar_catalogo(self):
        try:
            with open(ARQUIVO_CATALOGO, "r", encoding="utf-8") as f:
                return json.load(f)
        except:
            return construir_catalogo()

    # --- 👤 SIDEBAR COM NÍVEL, XP E CARTEIRA ---
    def construir_sidebar_v3(self):
        for widget in self.sidebar.winfo_children():
            widget.destroy()
            
        avatar_atual = self.dados.get("avatar_equipado", "👾")
        ctk.CTkLabel(self.sidebar, text=avatar_atual, font=ctk.CTkFont(size=50)).pack(pady=(25, 2))
        ctk.CTkLabel(self.sidebar, text="PLAYER CASUAL", font=ctk.CTkFont(size=15, weight="bold"), text_color=COR_TEXTO_MAIN).pack()
        
        horas_totais = sum(self.dados.get("horas_jogadas", {}).values()) / 3600
        nivel = int(1 + (horas_totais * 2) + (len(self.dados.get("avatares_comprados", [])) * 0.5))
        xp_progresso = (horas_totais * 2) % 1.0 if horas_totais > 0 else 0.0
        
        ctk.CTkLabel(self.sidebar, text=f"LVL {nivel} CONTROLLER", font=ctk.CTkFont(size=11, weight="bold"), text_color=COR_PINK).pack(pady=(2, 0))
        
        barra_xp = ctk.CTkProgressBar(self.sidebar, width=160, height=6, progress_color=COR_PINK, fg_color="#1d2030")
        barra_xp.pack(pady=(4, 15))
        barra_xp.set(xp_progresso)
        
        self.lbl_wallet = ctk.CTkLabel(self.sidebar, text=f"🪙 {self.dados['moedas']} Moedas", font=ctk.CTkFont(size=14, weight="bold"), text_color=COR_GOLD)
        self.lbl_wallet.pack(pady=2)
        
        ctk.CTkFrame(self.sidebar, height=2, width=180, fg_color="#222538").pack(pady=15)
        
        self.criar_btn_menu("🎮 Minha Biblioteca", COR_NEON, "#000", self.abrir_aba_biblioteca)
        self.criar_btn_menu("🛒 Casual App Store", "#1d2030", COR_TEXTO_MAIN, lambda: self.abrir_subprocesso_jogo("casual_app_store.py"))
        self.criar_btn_menu("🏅 Conquistas V3", "#1d2030", COR_TEXTO_MAIN, self.abrir_aba_conquistas)
        self.criar_btn_menu("⚙️ Configurações", "#1d2030", COR_TEXTO_MAIN, self.abrir_aba_configuracoes)
        self.criar_btn_menu("☁️ Sincronizar Jogos", "#1d2030", COR_TEXTO_MAIN, self.sincronizar_pacote_drive)
        self.criar_btn_menu("🎮 Meus Jogos", "#1d2030", COR_TEXTO_MAIN, self.abrir_aba_meus_jogos)
        self.criar_btn_menu("🧑‍💻 Publicar Jogo", "#1d2030", COR_TEXTO_MAIN, self.abrir_publicador)
        
        ctk.CTkButton(self.sidebar, text="Sair do Sistema ❌", width=200, height=36, corner_radius=8, fg_color="#e74c3c", hover_color="#c0392b", font=ctk.CTkFont(weight="bold"), command=self.destroy).pack(side="bottom", pady=30)

    def criar_btn_menu(self, texto, bg_color, text_color, comando):
        btn = ctk.CTkButton(self.sidebar, text=texto, width=200, height=40, corner_radius=8, fg_color=bg_color, text_color=text_color, font=ctk.CTkFont(weight="bold"), command=comando)
        btn.pack(pady=6)

    # --- ⚙️ RASTREADOR DE TEMPO ATIVO ---
    def abrir_subprocesso_jogo(self, script_nome):
        """Abre um jogo usando o caminho relativo salvo no catalog.json."""
        def monitorar_jogo():
            try:
                inicio = time.time()

                # A Casual App Store continua ficando ao lado do launcher.
                if script_nome == "casual_app_store.py":
                    caminho = os.path.join(
                        os.path.dirname(os.path.abspath(__file__)),
                        script_nome
                    )
                elif os.path.isabs(script_nome):
                    caminho = script_nome
                else:
                    caminho = os.path.join(PASTA_JOGOS, script_nome)

                caminho = os.path.abspath(caminho)
                dir_atual = os.path.dirname(caminho)

                if not os.path.isfile(caminho):
                    print(f"[JOGO] Arquivo não encontrado: {caminho}")
                    return

                print(f"[JOGO] Abrindo: {caminho}")
                processo = subprocess.Popen([sys.executable, caminho], cwd=dir_atual)
                processo.wait()

                tempo_decorrido = int(time.time() - inicio)

                self.carregar_dados_sistema()
                if "horas_jogadas" not in self.dados:
                    self.dados["horas_jogadas"] = {}

                self.dados["horas_jogadas"][script_nome] = (
                    self.dados["horas_jogadas"].get(script_nome, 0)
                    + tempo_decorrido
                )

                moedas_ganhas = (tempo_decorrido // 10) * 5
                if moedas_ganhas > 0:
                    self.dados["moedas"] += moedas_ganhas

                self.salvar_dados_sistema()

                try:
                    self.after(0, self.construir_sidebar_v3)
                except Exception:
                    pass

            except Exception as e:
                print(f"[JOGO] Erro ao abrir jogo: {e}")

        threading.Thread(target=monitorar_jogo, daemon=True).start()

    # --- 🎮 TELA 1: BIBLIOTECA COM SISTEMA DE CAPAS INTELIGENTES ---
    def abrir_aba_biblioteca(self):
        self.limpar_painel_principal()

        ctk.CTkLabel(
            self.painel_principal,
            text="🚀 SUA BIBLIOTECA VIRTUAL",
            font=ctk.CTkFont(size=22, weight="bold"),
            text_color=COR_TEXTO_MAIN
        ).pack(anchor="w", pady=(0, 5))

        ctk.CTkLabel(
            self.painel_principal,
            text="Jogos encontrados automaticamente no pacote do Google Drive.",
            font=ctk.CTkFont(size=13),
            text_color=COR_TEXTO_MUTED
        ).pack(anchor="w", pady=(0, 15))

        self.grid_scroll = ctk.CTkScrollableFrame(
            self.painel_principal,
            fg_color="transparent"
        )
        self.grid_scroll.pack(fill="both", expand=True)

        # IMPORTANTE:
        # Não procuramos mais os .py ao lado do launcher.
        # O catálogo é criado a partir da pasta games/ após a sincronização.
        catalogo = self.carregar_catalogo()
        self.catalogo = catalogo
        self.arquivos = [jogo.get("arquivo", "") for jogo in catalogo if jogo.get("arquivo")]

        if not self.arquivos:
            ctk.CTkLabel(
                self.grid_scroll,
                text="Nenhum jogo encontrado. Clique em ☁️ Sincronizar Jogos.",
                font=ctk.CTkFont(size=14),
                text_color=COR_TEXTO_MUTED
            ).pack(pady=40)
            return

        self.grid_scroll.grid_columnconfigure((0, 1, 2), weight=1, uniform="grade")

        for idx, jogo in enumerate(catalogo):
            arquivo_py = jogo.get("arquivo", "")
            if not arquivo_py:
                continue

            nome_limpo = jogo.get("nome") or os.path.splitext(
                os.path.basename(arquivo_py)
            )[0].replace("_", " ").replace("-", " ").title()

            segundos_jogados = self.dados.get("horas_jogadas", {}).get(arquivo_py, 0)
            minutos = segundos_jogados // 60
            txt_tempo = f"⏱️ {minutos} min jogados" if minutos > 0 else "⏱️ Nunca jogado"

            card = ctk.CTkFrame(
                self.grid_scroll,
                fg_color=COR_CARD,
                height=260,
                corner_radius=12,
                border_width=1,
                border_color="#222538"
            )
            card.grid(
                row=idx // 3,
                column=idx % 3,
                padx=12,
                pady=12,
                sticky="nsew"
            )
            card.grid_propagate(False)

            card.bind(
                "<Enter>",
                lambda e, c=card: c.configure(
                    border_color=COR_NEON,
                    fg_color="#1a1e30"
                )
            )
            card.bind(
                "<Leave>",
                lambda e, c=card: c.configure(
                    border_color="#222538",
                    fg_color=COR_CARD
                )
            )

            # A capa vem do catalog.json.
            capa = jogo.get("capa")
            caminho_imagem = (
                os.path.join(PASTA_JOGOS, capa)
                if capa else None
            )

            imagem_carregada = None
            if caminho_imagem and os.path.isfile(caminho_imagem):
                try:
                    img_pil = Image.open(caminho_imagem)
                    imagem_carregada = ctk.CTkImage(
                        light_image=img_pil,
                        dark_image=img_pil,
                        size=(200, 100)
                    )
                except Exception as e:
                    print(f"[CAPA] Erro ao carregar {caminho_imagem}: {e}")

            if imagem_carregada:
                lbl_capa = ctk.CTkLabel(
                    card,
                    image=imagem_carregada,
                    text=""
                )
                lbl_capa.pack(pady=(12, 5), padx=10, fill="x")
            else:
                frame_fallback = ctk.CTkFrame(
                    card,
                    height=100,
                    fg_color="#0f111a",
                    corner_radius=6
                )
                frame_fallback.pack(
                    pady=(12, 5),
                    padx=12,
                    fill="x"
                )
                frame_fallback.pack_propagate(False)
                ctk.CTkLabel(
                    frame_fallback,
                    text="🕹️",
                    font=ctk.CTkFont(size=36)
                ).pack(expand=True)

            ctk.CTkLabel(
                card,
                text=nome_limpo[:22],
                font=ctk.CTkFont(size=14, weight="bold"),
                text_color=COR_TEXTO_MAIN
            ).pack(pady=(5, 1))

            ctk.CTkLabel(
                card,
                text=txt_tempo,
                font=ctk.CTkFont(size=11),
                text_color=COR_TEXTO_MUTED
            ).pack(pady=1)

            btn_play = ctk.CTkButton(
                card,
                text="JOGAR AGORA",
                width=170,
                height=32,
                corner_radius=8,
                fg_color=COR_GREEN,
                hover_color="#27ae60",
                text_color="#051c08",
                font=ctk.CTkFont(size=11, weight="bold"),
                command=lambda a=arquivo_py: self.abrir_subprocesso_jogo(a)
            )
            btn_play.pack(side="bottom", pady=(4, 8))

            btn_details = ctk.CTkButton(
                card, text="👁 VER DETALHES", width=170, height=28, corner_radius=8,
                fg_color="#20263a", hover_color="#2d3652", text_color=COR_TEXTO_MAIN,
                font=ctk.CTkFont(size=10, weight="bold"),
                command=lambda d=jogo: self.abrir_detalhes_jogo(d)
            )
            btn_details.pack(side="bottom", pady=(0, 8))

            card.bind("<Button-1>", lambda e, d=jogo: self.abrir_detalhes_jogo(d))

    # --- 🏪 DETALHES DO JOGO / ESTILO PLAY STORE ---
    def abrir_detalhes_jogo(self, jogo):
        """Abre uma página de detalhes inspirada em lojas de aplicativos."""
        if not isinstance(jogo, dict):
            return

        self.limpar_painel_principal()
        arquivo = jogo.get("arquivo", "")
        nome = jogo.get("nome") or os.path.splitext(os.path.basename(arquivo))[0].replace("_", " ").replace("-", " ").title()
        slug = jogo.get("slug") or os.path.dirname(arquivo).replace("\\", "/").split("/")[0]
        pasta = os.path.join(PASTA_JOGOS, slug) if slug else os.path.dirname(os.path.join(PASTA_JOGOS, arquivo))
        game_json = os.path.join(pasta, "game.json")
        dados = dict(jogo)
        if os.path.isfile(game_json):
            try:
                with open(game_json, "r", encoding="utf-8") as f:
                    dados.update(json.load(f))
            except Exception:
                pass

        nome = dados.get("nome") or nome
        autor = dados.get("autor", "Desconhecido")
        linguagem = dados.get("linguagem", "Não informada")
        descricao = dados.get("descricao", "Este jogo ainda não possui uma descrição.")
        capa_rel = dados.get("capa") or jogo.get("capa")
        capa = os.path.join(PASTA_JOGOS, capa_rel) if capa_rel else None
        if not capa or not os.path.isfile(capa):
            principal = os.path.join(pasta, dados.get("arquivo", ""))
            capa = encontrar_capa(principal) if os.path.isfile(principal) else None

        # Cabeçalho estilo loja
        topo = ctk.CTkFrame(self.painel_principal, fg_color="transparent")
        topo.pack(fill="x", pady=(0, 12))
        ctk.CTkButton(topo, text="← Voltar", width=90, height=34, fg_color="#1d2030",
                      hover_color="#292e45", command=self.abrir_aba_biblioteca).pack(side="left")
        ctk.CTkLabel(topo, text="LOJA • DETALHES DO JOGO", font=ctk.CTkFont(size=12, weight="bold"),
                     text_color=COR_NEON).pack(side="right", pady=7)

        hero = ctk.CTkFrame(self.painel_principal, fg_color=COR_CARD, corner_radius=18,
                            border_width=1, border_color="#252a40")
        hero.pack(fill="x", pady=4)

        capa_frame = ctk.CTkFrame(hero, width=300, height=180, fg_color="#0d1019", corner_radius=12)
        capa_frame.pack(side="left", padx=20, pady=20)
        capa_frame.pack_propagate(False)
        imagem = None
        if capa and os.path.isfile(capa):
            try:
                pil = Image.open(capa)
                imagem = ctk.CTkImage(light_image=pil, dark_image=pil, size=(280, 160))
            except Exception:
                imagem = None
        if imagem:
            ctk.CTkLabel(capa_frame, image=imagem, text="").pack(expand=True)
        else:
            ctk.CTkLabel(capa_frame, text="🎮", font=ctk.CTkFont(size=62)).pack(expand=True)

        info = ctk.CTkFrame(hero, fg_color="transparent")
        info.pack(side="left", fill="both", expand=True, padx=(0, 25), pady=22)
        ctk.CTkLabel(info, text=nome, font=ctk.CTkFont(size=28, weight="bold"),
                     text_color=COR_TEXTO_MAIN, anchor="w", wraplength=500).pack(anchor="w")
        ctk.CTkLabel(info, text=f"por {autor}", font=ctk.CTkFont(size=13),
                     text_color=COR_TEXTO_MUTED).pack(anchor="w", pady=(5, 12))
        badges = ctk.CTkFrame(info, fg_color="transparent")
        badges.pack(anchor="w", pady=(0, 15))
        for texto_badge in (f"💻 {linguagem}", f"📦 v{dados.get('versao', '1.0')}"):
            ctk.CTkLabel(badges, text=texto_badge, fg_color="#20263a", corner_radius=8,
                         padx=10, pady=5, text_color=COR_TEXTO_MAIN,
                         font=ctk.CTkFont(size=11, weight="bold")).pack(side="left", padx=(0, 7))

        caminho_jogo = os.path.join(pasta, dados.get("arquivo", ""))
        botoes = ctk.CTkFrame(info, fg_color="transparent")
        botoes.pack(anchor="w", fill="x")
        ctk.CTkButton(botoes, text="▶ JOGAR", width=150, height=42, corner_radius=10,
                      fg_color=COR_GREEN, hover_color="#27ae60", text_color="#051c08",
                      font=ctk.CTkFont(weight="bold"),
                      command=lambda c=caminho_jogo: self.abrir_subprocesso_jogo(c)).pack(side="left", padx=(0, 8))
        if slug:
            ctk.CTkButton(botoes, text="✏️ EDITAR", width=120, height=42, corner_radius=10,
                          fg_color=COR_NEON, hover_color="#00b8cc", text_color="#001014",
                          font=ctk.CTkFont(weight="bold"),
                          command=lambda s=slug: self.abrir_editor_jogo(s)).pack(side="left", padx=8)

        descricao_box = ctk.CTkFrame(self.painel_principal, fg_color=COR_CARD, corner_radius=16,
                                     border_width=1, border_color="#252a40")
        descricao_box.pack(fill="both", expand=True, pady=(16, 0))
        ctk.CTkLabel(descricao_box, text="Sobre este jogo", font=ctk.CTkFont(size=18, weight="bold"),
                     text_color=COR_TEXTO_MAIN).pack(anchor="w", padx=22, pady=(18, 8))
        ctk.CTkLabel(descricao_box, text=descricao or "Sem descrição.", font=ctk.CTkFont(size=13),
                     text_color=COR_TEXTO_MUTED, justify="left", anchor="nw", wraplength=760).pack(
                         fill="both", expand=True, padx=22, pady=(0, 20))

    # --- ✏️ EDITOR DE JOGOS ---
    def abrir_editor_jogo(self, slug):
        pasta = os.path.join(PASTA_JOGOS, slug)
        game_json = os.path.join(pasta, "game.json")
        if not os.path.isfile(game_json):
            messagebox.showerror("Editar jogo", "game.json não foi encontrado para este jogo.", parent=self)
            return
        try:
            with open(game_json, "r", encoding="utf-8") as f:
                dados = json.load(f)
        except Exception as e:
            messagebox.showerror("Editar jogo", f"Não foi possível ler os dados do jogo.\n\n{e}", parent=self)
            return

        janela = ctk.CTkToplevel(self)
        janela.title(f"✏️ Editar • {dados.get('nome', slug)}")
        janela.geometry("650x650")
        janela.configure(fg_color=COR_BG_SISTEMA)
        janela.transient(self)
        janela.grab_set()

        ctk.CTkLabel(janela, text="✏️ EDITAR JOGO", font=ctk.CTkFont(size=24, weight="bold"),
                     text_color=COR_TEXTO_MAIN).pack(pady=(22, 4))
        ctk.CTkLabel(janela, text="As alterações serão publicadas automaticamente no Google Drive.",
                     font=ctk.CTkFont(size=12), text_color=COR_TEXTO_MUTED).pack(pady=(0, 12))

        form = ctk.CTkScrollableFrame(janela, fg_color=COR_CARD, corner_radius=14)
        form.pack(fill="both", expand=True, padx=24, pady=8)

        def campo(rotulo, valor):
            ctk.CTkLabel(form, text=rotulo, font=ctk.CTkFont(size=12, weight="bold"),
                         text_color=COR_TEXTO_MAIN).pack(anchor="w", padx=20, pady=(12, 4))
            e = ctk.CTkEntry(form, height=38)
            e.pack(fill="x", padx=20)
            e.insert(0, valor or "")
            return e

        e_nome = campo("Nome do jogo", dados.get("nome", ""))
        e_autor = campo("Autor", dados.get("autor", ""))
        ctk.CTkLabel(form, text="Linguagem / tecnologia", font=ctk.CTkFont(size=12, weight="bold"),
                     text_color=COR_TEXTO_MAIN).pack(anchor="w", padx=20, pady=(12, 4))
        e_lang = ctk.CTkComboBox(form, values=LINGUAGENS_SUPORTADAS, height=38)
        e_lang.pack(fill="x", padx=20)
        e_lang.set(dados.get("linguagem", "Python"))
        ctk.CTkLabel(form, text="Descrição", font=ctk.CTkFont(size=12, weight="bold"),
                     text_color=COR_TEXTO_MAIN).pack(anchor="w", padx=20, pady=(12, 4))
        e_desc = ctk.CTkTextbox(form, height=120)
        e_desc.pack(fill="x", padx=20, pady=(0, 15))
        e_desc.insert("1.0", dados.get("descricao", ""))

        status = ctk.CTkLabel(janela, text="Pronto para salvar.", font=ctk.CTkFont(size=12),
                              text_color=COR_TEXTO_MUTED)
        status.pack(pady=5)
        btn = ctk.CTkButton(janela, text="☁️ SALVAR ALTERAÇÕES", width=280, height=44,
                            fg_color=COR_GREEN, hover_color="#27ae60", text_color="#051c08",
                            font=ctk.CTkFont(weight="bold"))
        btn.pack(pady=(3, 18))

        def salvar():
            nome = e_nome.get().strip()
            autor = e_autor.get().strip()
            linguagem = e_lang.get().strip() or "Não informada"
            descricao = e_desc.get("1.0", "end").strip()
            if not nome or not autor:
                messagebox.showwarning("Editar jogo", "Nome e autor são obrigatórios.", parent=janela)
                return
            btn.configure(state="disabled")
            status.configure(text="⏳ Atualizando jogo e preparando pacote...", text_color=COR_GOLD)
            novos = dict(dados)
            novos.update({"nome": nome, "autor": autor, "linguagem": linguagem, "descricao": descricao})

            def trabalho():
                temp_root = tempfile.mkdtemp(prefix="casual_edit_")
                pacote = os.path.join(temp_root, "pacote.zip")
                novo_zip = os.path.join(temp_root, "novo.zip")
                try:
                    service = obter_servico_drive()
                    usuario = drive_obter_usuario(service)
                    owner_email = dados.get("publicador_email", "").lower()
                    owner_perm = dados.get("publicador_permission_id", "")
                    if (owner_email or owner_perm) and not ((owner_email and owner_email == usuario.get("email", "").lower()) or (owner_perm and owner_perm == usuario.get("permission_id", ""))):
                        raise RuntimeError("Este jogo pertence a outra conta Google.")

                    with open(game_json, "w", encoding="utf-8") as f:
                        json.dump(novos, f, ensure_ascii=False, indent=4)
                    construir_catalogo()

                    drive_baixar_arquivo(service, DRIVE_PUBLISH_TARGET_ID, pacote)
                    if not zipfile.is_zipfile(pacote):
                        raise RuntimeError("O pacote do Google Drive não é um ZIP válido.")
                    extracao = os.path.join(temp_root, "extraido")
                    os.makedirs(extracao, exist_ok=True)
                    with zipfile.ZipFile(pacote, "r") as z:
                        for info in z.infolist():
                            n = info.filename.replace("\\", "/")
                            destino = os.path.abspath(os.path.join(extracao, n))
                            if n.startswith("/") or ".." in n.split("/") or not destino.startswith(os.path.abspath(extracao) + os.sep):
                                raise RuntimeError(f"Caminho inseguro no ZIP: {n}")
                        z.extractall(extracao)
                    pasta_drive = os.path.join(extracao, slug)
                    if not os.path.isdir(pasta_drive):
                        raise RuntimeError("A pasta do jogo não foi encontrada no pacote do Drive.")
                    with open(os.path.join(pasta_drive, "game.json"), "w", encoding="utf-8") as f:
                        json.dump(novos, f, ensure_ascii=False, indent=4)
                    arquivos = []
                    for raiz, _, nomes in os.walk(extracao):
                        for arq in nomes:
                            if arq == "catalog.json":
                                continue
                            caminho = os.path.join(raiz, arq)
                            rel = os.path.relpath(caminho, extracao).replace("\\", "/")
                            if validar_caminho_zip(rel):
                                arquivos.append((caminho, rel))
                    with zipfile.ZipFile(novo_zip, "w", zipfile.ZIP_DEFLATED) as z:
                        for caminho, rel in arquivos:
                            z.write(caminho, rel)
                    drive_atualizar_arquivo(service, DRIVE_PUBLISH_TARGET_ID, novo_zip)
                    registros = carregar_meus_jogos()
                    for r in registros:
                        if r.get("slug") == slug:
                            r.update({"nome": nome, "autor": autor, "linguagem": linguagem})
                    salvar_meus_jogos(registros)
                    self.after(0, lambda: status.configure(text="✅ Alterações publicadas!", text_color=COR_GREEN))
                    self.after(0, lambda: messagebox.showinfo("Editar jogo", f"🎉 {nome} foi atualizado no Google Drive!", parent=janela))
                    self.after(0, janela.destroy)
                    self.after(0, self.sincronizar_pacote_drive)
                    self.after(0, lambda: self.abrir_detalhes_jogo({"slug": slug, **novos}))
                except Exception as e:
                    erro = str(e)
                    self.after(0, lambda: status.configure(text=f"❌ {erro}", text_color="#ff5555"))
                    self.after(0, lambda: messagebox.showerror("Editar jogo", "Não foi possível salvar.\n\n" + erro, parent=janela))
                    self.after(0, lambda: btn.configure(state="normal"))
                finally:
                    shutil.rmtree(temp_root, ignore_errors=True)
            threading.Thread(target=trabalho, daemon=True).start()

        btn.configure(command=salvar)

    # --- 🎮 MEUS JOGOS V2.5 ---
    def abrir_aba_meus_jogos(self):
        self.limpar_painel_principal()
        ctk.CTkLabel(self.painel_principal, text="🎮 MEUS JOGOS", font=ctk.CTkFont(size=24, weight="bold"), text_color=COR_TEXTO_MAIN).pack(anchor="w", pady=(0, 3))
        ctk.CTkLabel(self.painel_principal, text="Sua coleção de jogos publicados — gerencie, edite e acompanhe seus projetos.", font=ctk.CTkFont(size=13), text_color=COR_TEXTO_MUTED).pack(anchor="w", pady=(0, 15))
        registros = carregar_meus_jogos()
        scroll = ctk.CTkScrollableFrame(self.painel_principal, fg_color="transparent")
        scroll.pack(fill="both", expand=True)
        if not registros:
            ctk.CTkLabel(scroll, text="🕹️\n\nNenhum jogo publicado nesta instalação ainda.\n\nPublique seu primeiro jogo para ele aparecer aqui.", font=ctk.CTkFont(size=16), text_color=COR_TEXTO_MUTED, justify="center").pack(pady=80)
            return
        for registro in registros:
            slug = registro.get("slug", "")
            pasta = os.path.join(PASTA_JOGOS, slug)
            if not os.path.isdir(pasta):
                continue
            game_json = os.path.join(pasta, "game.json")
            dados = {}
            if os.path.isfile(game_json):
                try:
                    with open(game_json, "r", encoding="utf-8") as f:
                        dados = json.load(f)
                except Exception:
                    pass
            nome = dados.get("nome") or registro.get("nome") or slug
            autor = dados.get("autor") or registro.get("autor") or "Desconhecido"
            linguagem = dados.get("linguagem") or registro.get("linguagem") or "Não informada"
            arquivo = dados.get("arquivo", "")
            capa = encontrar_capa(os.path.join(pasta, arquivo)) if arquivo and os.path.isfile(os.path.join(pasta, arquivo)) else None

            item = ctk.CTkFrame(scroll, fg_color=COR_CARD, corner_radius=16, border_width=1, border_color="#252a40", height=120)
            item.pack(fill="x", padx=5, pady=7)
            item.pack_propagate(False)
            if capa:
                try:
                    pil = Image.open(capa)
                    thumb = ctk.CTkImage(light_image=pil, dark_image=pil, size=(125, 82))
                    ctk.CTkLabel(item, image=thumb, text="").pack(side="left", padx=14, pady=18)
                except Exception:
                    ctk.CTkLabel(item, text="🎮", font=ctk.CTkFont(size=38)).pack(side="left", padx=25)
            else:
                ctk.CTkLabel(item, text="🎮", font=ctk.CTkFont(size=38)).pack(side="left", padx=25)

            info = ctk.CTkFrame(item, fg_color="transparent")
            info.pack(side="left", fill="both", expand=True, pady=15)
            ctk.CTkLabel(info, text=nome, font=ctk.CTkFont(size=16, weight="bold"), text_color=COR_TEXTO_MAIN).pack(anchor="w")
            ctk.CTkLabel(info, text=f"👤 {autor}  •  💻 {linguagem}", font=ctk.CTkFont(size=11), text_color=COR_TEXTO_MUTED).pack(anchor="w", pady=(5, 0))
            ctk.CTkLabel(info, text="Clique para abrir a página do jogo", font=ctk.CTkFont(size=10), text_color="#65708a").pack(anchor="w", pady=(4, 0))

            botoes = ctk.CTkFrame(item, fg_color="transparent")
            botoes.pack(side="right", padx=15, pady=12)
            ctk.CTkButton(botoes, text="👁 Detalhes", width=95, height=32, fg_color="#20263a", hover_color="#2d3652",
                          command=lambda d={"slug": slug, **dados}: self.abrir_detalhes_jogo(d)).pack(pady=3)
            ctk.CTkButton(botoes, text="✏️ Editar", width=95, height=32, fg_color=COR_NEON, hover_color="#00b8cc", text_color="#001014",
                          command=lambda s=slug: self.abrir_editor_jogo(s)).pack(pady=3)
            ctk.CTkButton(botoes, text="🗑 Excluir", width=95, height=32, fg_color="#c0392b", hover_color="#e74c3c",
                          command=lambda s=slug, n=nome: self.confirmar_exclusao_jogo(s, n)).pack(pady=3)

            # Clique no cartão abre a página de detalhes.
            dados_card = {"slug": slug, **dados}
            for widget in (item, info):
                widget.bind("<Button-1>", lambda e, d=dados_card: self.abrir_detalhes_jogo(d))

    def confirmar_exclusao_jogo(self, slug, nome):
        if not slug:
            return
        if not messagebox.askyesno("Excluir jogo", f"Tem certeza que deseja excluir:\n\n🎮 {nome}\n\nO jogo será removido do pacote da plataforma e do Google Drive.\n\nEsta ação não pode ser desfeita.", parent=self):
            return
        threading.Thread(target=self._excluir_jogo_drive, args=(slug, nome), daemon=True).start()

    # --- 🧑‍💻 PUBLICADOR V2 + GOOGLE DRIVE ---
    def abrir_publicador(self):
        janela = ctk.CTkToplevel(self)
        janela.title("🧑‍💻 Publicar Jogo - Casual Games")
        janela.geometry("560x440")
        janela.minsize(500, 400)
        janela.configure(fg_color=COR_BG_SISTEMA)
        janela.transient(self)
        janela.grab_set()

        ctk.CTkLabel(janela, text="🧑‍💻 PUBLICAR UM JOGO",
                     font=ctk.CTkFont(size=24, weight="bold"),
                     text_color=COR_TEXTO_MAIN).pack(pady=(25, 5))
        ctk.CTkLabel(janela, text="Digite o código de desenvolvedor para continuar.",
                     font=ctk.CTkFont(size=13),
                     text_color=COR_TEXTO_MUTED).pack(pady=(0, 18))

        entrada = ctk.CTkEntry(janela, width=360, height=42,
                               placeholder_text="Código de desenvolvedor", show="•")
        entrada.pack(pady=8)
        entrada.focus()

        status = ctk.CTkLabel(janela, text="",
                              font=ctk.CTkFont(size=12),
                              text_color=COR_TEXTO_MUTED)
        status.pack(pady=8)

        def verificar():
            if entrada.get().strip() == CODIGO_DESENVOLVEDOR:
                janela.destroy()
                self.abrir_formulario_publicacao()
            else:
                status.configure(text="❌ Código inválido.", text_color="#ff5555")

        ctk.CTkButton(janela, text="🔓 VERIFICAR CÓDIGO", width=220, height=40,
                      fg_color=COR_NEON, hover_color="#00b8cc",
                      text_color="#000000",
                      font=ctk.CTkFont(weight="bold"),
                      command=verificar).pack(pady=10)

        ctk.CTkLabel(janela, text="V2.5 • Publicação + Meus Jogos",
                     font=ctk.CTkFont(size=11),
                     text_color=COR_TEXTO_MUTED).pack(side="bottom", pady=18)
        entrada.bind("<Return>", lambda e: verificar())

    def abrir_formulario_publicacao(self):
        janela = ctk.CTkToplevel(self)
        janela.title("☁️ Publicador V2.5.5 - Google Drive")
        janela.geometry("760x780")
        janela.minsize(680, 700)
        janela.configure(fg_color=COR_BG_SISTEMA)
        janela.transient(self)
        janela.grab_set()

        ctk.CTkLabel(janela, text="☁️ PUBLICADOR V2.5",
                     font=ctk.CTkFont(size=24, weight="bold"),
                     text_color=COR_TEXTO_MAIN).pack(pady=(18, 3))
        ctk.CTkLabel(
            janela,
            text="Preencha os dados. O jogo será colocado no pacote e enviado ao Drive automaticamente.",
            font=ctk.CTkFont(size=12), text_color=COR_TEXTO_MUTED
        ).pack(pady=(0, 10))

        form = ctk.CTkScrollableFrame(janela, fg_color=COR_CARD, corner_radius=12)
        form.pack(fill="both", expand=True, padx=25, pady=8)

        def linha(label, placeholder):
            ctk.CTkLabel(form, text=label,
                         font=ctk.CTkFont(size=12, weight="bold"),
                         text_color=COR_TEXTO_MAIN).pack(anchor="w", padx=20, pady=(10, 3))
            e = ctk.CTkEntry(form, placeholder_text=placeholder, height=34)
            e.pack(fill="x", padx=20)
            return e

        entrada_nome = linha("Nome do jogo", "Meu Jogo")
        entrada_autor = linha("Autor", "Seu nome")

        ctk.CTkLabel(form, text="Linguagem / tecnologia",
                     font=ctk.CTkFont(size=12, weight="bold"),
                     text_color=COR_TEXTO_MAIN).pack(anchor="w", padx=20, pady=(10, 3))
        entrada_linguagem = ctk.CTkComboBox(form, values=LINGUAGENS_SUPORTADAS, height=34)
        entrada_linguagem.pack(fill="x", padx=20)
        entrada_linguagem.set("Python")

        ctk.CTkLabel(form, text="Descrição",
                     font=ctk.CTkFont(size=12, weight="bold"),
                     text_color=COR_TEXTO_MAIN).pack(anchor="w", padx=20, pady=(10, 3))
        entrada_desc = ctk.CTkTextbox(form, height=75)
        entrada_desc.pack(fill="x", padx=20)

        caminhos = {"arquivo": "", "pasta": ""}
        frame_arquivo = ctk.CTkFrame(form, fg_color="transparent")
        frame_arquivo.pack(fill="x", padx=20, pady=10)
        lbl_arquivo = ctk.CTkLabel(frame_arquivo, text="Nenhum arquivo principal selecionado.",
                                   text_color=COR_TEXTO_MUTED, anchor="w")
        lbl_arquivo.pack(side="left", fill="x", expand=True)

        def escolher_arquivo():
            from tkinter import filedialog
            escolhido = filedialog.askopenfilename(
                title="Escolha o arquivo principal do jogo",
                filetypes=[
                    ("Arquivos de jogo", "*.exe;*.jar;*.py;*.js;*.html;*.htm;*.bin;*.com;*.bat;*.cmd;*.sh"),
                    ("Todos os arquivos", "*.*")
                ],
                parent=janela
            )
            if escolhido:
                caminhos["arquivo"] = escolhido
                lbl_arquivo.configure(text=f"📄 {os.path.basename(escolhido)}", text_color=COR_GREEN)

        ctk.CTkButton(frame_arquivo, text="📂 Arquivo principal", width=175,
                      command=escolher_arquivo).pack(side="right")

        frame_pasta = ctk.CTkFrame(form, fg_color="transparent")
        frame_pasta.pack(fill="x", padx=20, pady=5)
        lbl_pasta = ctk.CTkLabel(frame_pasta, text="Pasta do jogo opcional: nenhuma (só o arquivo principal)",
                                 text_color=COR_TEXTO_MUTED, anchor="w")
        lbl_pasta.pack(side="left", fill="x", expand=True)

        def escolher_pasta():
            from tkinter import filedialog
            escolhido = filedialog.askdirectory(title="Escolha a pasta completa do jogo", parent=janela)
            if escolhido:
                caminhos["pasta"] = escolhido
                lbl_pasta.configure(text=f"📁 {os.path.basename(escolhido)}", text_color=COR_GREEN)

        ctk.CTkButton(frame_pasta, text="📁 Pasta completa (opcional)", width=175,
                      command=escolher_pasta).pack(side="right")

        ctk.CTkLabel(
            form,
            text="⚠️ O publicador não executa o código enviado. Ele apenas copia e empacota arquivos.",
            font=ctk.CTkFont(size=11), text_color="#ffb86c", wraplength=620, justify="left"
        ).pack(anchor="w", padx=20, pady=8)

        status = ctk.CTkLabel(janela, text="Pronto para publicar.",
                              font=ctk.CTkFont(size=12), text_color=COR_TEXTO_MUTED)
        status.pack(pady=5)

        btn_publicar = ctk.CTkButton(
            janela, text="☁️ PUBLICAR AUTOMATICAMENTE NO DRIVE", width=350, height=44,
            fg_color=COR_GREEN, hover_color="#27ae60", text_color="#051c08",
            font=ctk.CTkFont(weight="bold")
        )
        btn_publicar.pack(pady=(3, 15))

        def publicar():
            nome = entrada_nome.get().strip()
            autor = entrada_autor.get().strip()
            linguagem = entrada_linguagem.get().strip()
            descricao = entrada_desc.get("1.0", "end").strip()
            arquivo = caminhos["arquivo"]
            pasta = caminhos["pasta"] or None

            if not nome:
                messagebox.showwarning("Publicador", "Digite o nome do jogo.", parent=janela)
                return
            if not autor:
                messagebox.showwarning("Publicador", "Digite o autor.", parent=janela)
                return
            if not arquivo or not os.path.isfile(arquivo):
                messagebox.showwarning("Publicador", "Escolha o arquivo principal do jogo.", parent=janela)
                return
            if pasta and os.path.commonpath([os.path.abspath(arquivo), os.path.abspath(pasta)]) != os.path.abspath(pasta):
                # O arquivo principal pode ficar fora da pasta; nesse caso ele será copiado para ela.
                pass

            btn_publicar.configure(state="disabled")
            status.configure(text="⏳ Preparando pacote...", text_color=COR_GOLD)

            def trabalho():
                temp_zip = os.path.join(PASTA_PUBLICADOS, "_casual_games_publicacao.zip")
                try:
                    os.makedirs(PASTA_PUBLICADOS, exist_ok=True)
                    os.makedirs(PASTA_JOGOS, exist_ok=True)

                    self.after(0, lambda: status.configure(text="🔐 Conectando ao Google Drive...", text_color=COR_GOLD))
                    service = obter_servico_drive()
                    usuario = drive_obter_usuario(service)

                    # Se ainda não houver catálogo, baixa o pacote atual antes de adicionar o novo jogo.
                    if not any(os.path.isfile(os.path.join(raiz, arq)) for raiz, _, arqs in os.walk(PASTA_JOGOS) for arq in arqs if arq != "catalog.json"):
                        self.after(0, lambda: status.configure(text="☁️ Baixando pacote atual...", text_color=COR_GOLD))
                        baixar_pacote_drive()
                        for item in os.listdir(PASTA_JOGOS):
                            if item == "catalog.json":
                                continue
                            p = os.path.join(PASTA_JOGOS, item)
                            if os.path.isdir(p):
                                shutil.rmtree(p, ignore_errors=True)
                            else:
                                try: os.remove(p)
                                except OSError: pass
                        extrair_pacote_drive()

                    self.after(0, lambda: status.configure(text="📦 Adicionando jogo ao catálogo...", text_color=COR_GOLD))
                    instalar_jogo_no_catalogo(nome, autor, linguagem, descricao, arquivo, pasta, usuario)
                    construir_catalogo()

                    if os.path.exists(temp_zip):
                        os.remove(temp_zip)
                    criar_zip_da_pasta_jogos(temp_zip)

                    self.after(0, lambda: status.configure(text="☁️ Enviando pacote...", text_color=COR_GOLD))
                    resultado = drive_atualizar_arquivo(service, DRIVE_PUBLISH_TARGET_ID, temp_zip)

                    nome_resultado = resultado.get("name", "casual_games.py")
                    link = resultado.get("webViewLink", "")
                    msg = (
                        "🎉 JOGO PUBLICADO COM SUCESSO!\n\n"
                        f"Jogo: {nome}\n"
                        f"Autor: {autor}\n"
                        f"Linguagem: {linguagem or 'Não informada'}\n\n"
                        f"Pacote atualizado: {nome_resultado}\n"
                        "O arquivo do Drive foi atualizado."
                    )
                    if link:
                        msg += f"\n\nLink do arquivo:\n{link}"

                    self.after(0, lambda: status.configure(text="✅ Publicado no Google Drive!", text_color=COR_GREEN))
                    self.after(0, lambda: messagebox.showinfo("Publicador V2.5", msg, parent=janela))
                    self.after(0, self.abrir_aba_biblioteca)
                except Exception as erro:
                    erro_txt = str(erro)
                    self.after(0, lambda: status.configure(text=f"❌ {erro_txt}", text_color="#ff5555"))
                    self.after(0, lambda: messagebox.showerror(
                        "Publicador V2.5",
                        "Não foi possível publicar.\n\n" + erro_txt,
                        parent=janela
                    ))
                finally:
                    try:
                        if os.path.exists(temp_zip):
                            os.remove(temp_zip)
                    except OSError:
                        pass
                    self.after(0, lambda: btn_publicar.configure(state="normal"))

            threading.Thread(target=trabalho, daemon=True).start()

        btn_publicar.configure(command=publicar)

    # --- ⚙️ TELA 2: CONFIGURAÇÕES ---
    def abrir_aba_configuracoes(self):
        self.limpar_painel_principal()
        ctk.CTkLabel(self.painel_principal, text="⚙️ CONFIGURAÇÕES CENTRALIZADAS", font=ctk.CTkFont(size=22, weight="bold"), text_color=COR_TEXTO_MAIN).pack(anchor="w", pady=(0, 15))

        scroll_config = ctk.CTkScrollableFrame(self.painel_principal, fg_color="transparent")
        scroll_config.pack(fill="both", expand=True)

        frame_avatar = ctk.CTkFrame(scroll_config, fg_color=COR_CARD, corner_radius=12, border_width=1, border_color="#222538")
        frame_avatar.pack(fill="x", pady=10, padx=5)
        
        ctk.CTkLabel(frame_avatar, text="🛒 Loja de Avatares Pixel Art (Preço: 80 moedas cada)", font=ctk.CTkFont(size=14, weight="bold"), text_color=COR_NEON).pack(anchor="w", padx=20, pady=12)

        opcoes_avatar = [("Sapo Saltador 🐸", "🐸"), ("Nave Laser 🚀", "🚀"), ("Pac-Man Retro 🐤", "🐤"), ("Cogumelo Wonder 🍄", "🍄")]
        
        for nome_av, emoji in opcoes_avatar:
            f_linha = ctk.CTkFrame(frame_avatar, fg_color="transparent")
            f_linha.pack(fill="x", padx=20, pady=6)
            
            ctk.CTkLabel(f_linha, text=f"{emoji} {nome_av}", font=ctk.CTkFont(size=13), text_color=COR_TEXTO_MAIN).pack(side="left")
            
            ja_tem = emoji in self.dados.get("avatares_comprados", ["👾"])
            esta_equipado = self.dados.get("avatar_equipado", "👾") == emoji
            
            txt_btn = "Equipado ✅" if esta_equipado else "Equipar 📦" if ja_tem else "Comprar 🛒"
            cor_btn = "#2b2e44" if esta_equipado else "#34495e" if ja_tem else COR_PINK
            state_btn = "disabled" if esta_equipado else "normal"

            btn_av = ctk.CTkButton(f_linha, text=txt_btn, width=110, height=28, fg_color=cor_btn, state=state_btn, font=ctk.CTkFont(weight="bold"), command=lambda e=emoji: self.gerenciar_sistema_avatar(e))
            btn_av.pack(side="right")

        frame_bg_ctrl = ctk.CTkFrame(scroll_config, fg_color=COR_CARD, corner_radius=12, border_width=1, border_color="#222538")
        frame_bg_ctrl.pack(fill="x", pady=15, padx=5)
        
        ctk.CTkLabel(frame_bg_ctrl, text="✨ Customização Visual & Gráficos", font=ctk.CTkFont(size=14, weight="bold"), text_color=COR_TEXTO_MAIN).pack(anchor="w", padx=20, pady=12)
        
        self.switch_bg = ctk.CTkSwitch(frame_bg_ctrl, text="Ativar Partículas Cyberpunk em Tempo Real", progress_color=COR_NEON, text_color=COR_TEXTO_MAIN, font=ctk.CTkFont(size=12))
        self.switch_bg.pack(anchor="w", padx=20, pady=12)
        self.switch_bg.select()

    def gerenciar_sistema_avatar(self, emoji):
        self.carregar_dados_sistema()
        comprados = self.dados.get("avatares_comprados", ["👾"])
        
        if emoji in comprados:
            self.dados["avatar_equipado"] = emoji
            self.salvar_dados_sistema()
            self.construir_sidebar_v3()
            self.abrir_aba_configuracoes()
        else:
            if self.dados["moedas"] >= 80:
                self.dados["moedas"] -= 80
                self.dados["avatares_comprados"].append(emoji)
                self.dados["avatar_equipado"] = emoji
                self.salvar_dados_sistema()
                self.construir_sidebar_v3()
                self.abrir_aba_configuracoes()
            else:
                messagebox.showwarning("Banco de Dados", "Saldo insuficiente! Consiga mais moedas jogando.")

    # --- 🏅 TELA 3: QUADRO DE CONQUISTAS ---
    def abrir_aba_conquistas(self):
        self.limpar_painel_principal()
        ctk.CTkLabel(self.painel_principal, text="🏅 QUADRO DE CONQUISTAS V3", font=ctk.CTkFont(size=22, weight="bold"), text_color=COR_TEXTO_MAIN).pack(anchor="w", pady=(0, 15))

        scroll_conq = ctk.CTkScrollableFrame(self.painel_principal, fg_color="transparent")
        scroll_conq.pack(fill="both", expand=True)

        metas = [
            {"nome": "🥇 Elite da Casual", "req": 100, "desc": "Garantir a primeira centena de moedas unificadas."},
            {"nome": "👑 Rei do Filtro Digital", "req": 500, "desc": "Alcançar a grandiosa marca de 500 moedas ativas."},
            {"nome": "🌌 Conquistador das Galáxias", "req": 1200, "desc": "Zerar o save.json juntando 1200 moedas totais!"}
        ]

        saldo = self.dados.get("moedas", 0)

        for m in metas:
            liberado = saldo >= m["req"]
            bg_c = "#0f2e1a" if liberado else COR_CARD
            border_c = COR_GREEN if liberado else "#222538"
            txt_status = "LIBERADO ✓" if liberado else f"Trancado 🔒 ({saldo}/{m['req']})"
            cor_status = COR_GREEN if liberado else COR_TEXTO_MUTED

            item = ctk.CTkFrame(scroll_conq, fg_color=bg_c, border_color=border_c, border_width=1, height=85, corner_radius=12)
            item.pack(fill="x", pady=8, padx=5)
            item.pack_propagate(False)

            f_txt = ctk.CTkFrame(item, fg_color="transparent")
            f_txt.pack(side="left", padx=20, pady=12)
            ctk.CTkLabel(f_txt, text=m["nome"], font=ctk.CTkFont(size=14, weight="bold"), text_color=COR_TEXTO_MAIN).pack(anchor="w")
            ctk.CTkLabel(f_txt, text=m["desc"], font=ctk.CTkFont(size=11), text_color=COR_TEXTO_MUTED).pack(anchor="w")

            ctk.CTkLabel(item, text=txt_status, font=ctk.CTkFont(size=12, weight="bold"), text_color=cor_status).pack(side="right", padx=25)

    # --- 🤖 PROCESSAMENTO AUTOMATIZADO DE BACKGROUND (CORRIGIDO) ---
    def animar_background_sistema(self):
        self.canvas_bg.delete("all")
        
        # Coleta as dimensões em tempo de execução para evitar quebras visuais ao redimensionar
        largura_atual = self.winfo_width()
        altura_atual = self.winfo_height()
        
        if hasattr(self, "switch_bg") and self.switch_bg.get():
            for g in self.gotas_bg:
                g["y"] += g["vel"]
                if g["y"] > altura_atual:
                    g["y"] = -10
                    g["x"] = random.randint(0, largura_atual)
                self.canvas_bg.create_oval(g["x"], g["y"], g["x"]+g["tam"], g["y"]+g["tam"], fill="#1b2838", outline="")
        self.after(30, self.animar_background_sistema)

    def sincronizar_dados_em_background(self):
        self.carregar_dados_sistema()
        self.lbl_wallet.configure(text=f"🪙 {self.dados['moedas']} Moedas")
        self.after(2000, self.sincronizar_dados_em_background)

    def carregar_dados_sistema(self):
        if os.path.exists(ARQUIVO_SAVE):
            try:
                with open(ARQUIVO_SAVE, "r") as f: self.dados = json.load(f)
                if "moedas" not in self.dados: self.dados["moedas"] = 400
                if "avatares_comprados" not in self.dados: self.dados["avatares_comprados"] = ["👾"]
                if "avatar_equipado" not in self.dados: self.dados["avatar_equipado"] = "👾"
                if "horas_jogadas" not in self.dados: self.dados["horas_jogadas"] = {}
                if "apps_comprados" not in self.dados: self.dados["apps_comprados"] = []
            except: self.criar_save_padrao()
        else: self.criar_save_padrao()

    def criar_save_padrao(self):
        self.dados = {"moedas": 400, "apps_comprados": [], "avatares_comprados": ["👾"], "avatar_equipado": "👾", "horas_jogadas": {}}
        self.salvar_dados_sistema()

    def salvar_dados_sistema(self):
        try:
            with open(ARQUIVO_SAVE, "w") as f: json.dump(self.dados, f, indent=4)
        except Exception as e: print(f"Erro ao salvar: {e}")

    def limpar_painel_principal(self):
        for widget in self.painel_principal.winfo_children(): widget.destroy()

if __name__ == "__main__":
    app = GameLauncherV3()
    app.mainloop()

