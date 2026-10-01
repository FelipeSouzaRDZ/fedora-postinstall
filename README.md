---

# 🚀 Fedora Linux — Guia de Pós-Instalação para Desenvolvedores & Data Science

Este guia contém o passo a passo completo de pós-instalação do **Fedora Workstation**, otimizado para **Desenvolvimento Python, Ciência de Dados, Aprendizado de Máquina (NVIDIA/CUDA)** e gravação/transmissão de aulas.

---

## 📋 Pré-requisitos & Hardware Recomendado

* **SO:** Fedora Workstation (x86_64)
* **GPU:** NVIDIA (Série GTX/RTX)
* **Requisito de Rede:** Conexão com a internet para download dos pacotes.

---

## 🛠️ Passo a Passo de Configuração

### 1. Atualização do Sistema e Repositórios RPM Fusion

O repositório RPM Fusion é essencial para habilitar os drivers proprietários da NVIDIA e codecs de mídia no Fedora.

```bash
# Atualiza o sistema
sudo dnf upgrade --refresh -y

# Habilita os repositórios RPM Fusion (Free e Non-Free)
sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm -y

```

---

### 2. Drivers NVIDIA & Suporte CUDA

> **Nota sobre o nome do pacote:** O pacote `xorg-x11-drv-nvidia` mantém esse nome por convenção histórica de pacotes do Fedora, mas **inclui suporte completo e nativo ao Wayland, Vulkan e CUDA**.

```bash
# Instala o driver proprietário e o suporte a CUDA
sudo dnf install akmod-nvidia xorg-x11-drv-nvidia-cuda -y

```

⚠️ *Aguarde cerca de 5 minutos após a execução do comando acima antes de reiniciar, pois o módulo do Kernel (`akmod`) precisa ser compilado em segundo plano.*

---

### 3. Codecs de Mídia & Ferramentas Base de Compilação

Essencial para reprodução/gravação de vídeos (OBS Studio) e compilação de pacotes C/C++ necessários para bibliotecas Python.

```bash
# Habilita o FFmpeg completo e os codecs multimídia
sudo dnf swap ffmpeg-free ffmpeg --allowerasing -y
sudo dnf group install multimedia --setopt="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin -y
sudo dnf group install sound-and-video -y

# Ferramentas de compilação e suporte ao Python
sudo dnf install git gcc gcc-c++ make python3-devel -y

# Habilita o repositório Flathub
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo

```

---

### 4. Docker Oficial & Aceleração de GPU (NVIDIA Toolkit)

Configuração do Docker com permissão sem `sudo` e integração do runtime da NVIDIA para rodar contêineres de Machine Learning/Deep Learning com aceleração por GPU.

```bash
# Configura o repositório oficial do Docker
sudo curl -L https://download.docker.com/linux/fedora/docker-ce.repo -o /etc/yum.repos.d/docker-ce.repo
sudo dnf install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

# Habilita o serviço e adiciona seu usuário ao grupo docker
sudo systemctl enable --now docker
sudo usermod -aG docker $USER

# Adiciona o NVIDIA Container Toolkit
curl -s -L https://nvidia.github.io/libnvidia-container/stable/rpm/nvidia-container-toolkit.repo | sudo tee /etc/yum.repos.d/nvidia-container-toolkit.repo
sudo dnf install nvidia-container-toolkit -y

# Configura o Docker para usar a GPU por padrão e reinicia o serviço
sudo nvidia-ctk runtime configure --runtime=docker
sudo systemctl restart docker

```

---

### 5. Instalação do VS Code & OBS Studio

```bash
# Adiciona o repositório oficial da Microsoft
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
sudo sh -c 'echo -e "[code]\nname=Visual Studio Code\nbaseurl=https://packages.microsoft.com/yumrepos/vscode\nenabled=1\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/vscode.repo'

# Instala o VS Code e o OBS Studio
sudo dnf install code obs-studio -y

```

---

### 6. Tipografia para Programação: JetBrains Mono Nerd Font

Download e instalação automática da fonte com suporte a ícones para o terminal e ligaduras tipográficas no VS Code.

```bash
# Instalação da JetBrains Mono Nerd Font
mkdir -p ~/.local/share/fonts/JetBrainsMono
cd /tmp
wget https://github.com/ryanoasis/nerd-fonts/releases/latest/download/JetBrainsMono.zip
unzip JetBrainsMono.zip -d ~/.local/share/fonts/JetBrainsMono
rm JetBrainsMono.zip
fc-cache -fv

```

---

### 7. Gerenciador de Ambientes Python (Miniconda)

Instalação leve do Conda para gerenciamento de ambientes virtuais de Ciência de Dados de forma isolada.

```bash
# Baixa e instala o Miniconda na Home do usuário
cd /tmp
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh -b -p $HOME/miniconda3

# Inicializa o conda no terminal Bash
$HOME/miniconda3/bin/conda init bash

# Desativa a inicialização automática do ambiente (base) ao abrir o terminal
$HOME/miniconda3/bin/conda config --set auto_activate_base false

```

---

## 🔄 Finalização

Após rodar os comandos, **reinicie o computador** para carregar os módulos da NVIDIA no Kernel e aplicar as permissões do grupo Docker ao seu usuário:

```bash
sudo reboot

```

---

## 🧪 Comandos de Validação (Testes)

Para confirmar se o ambiente está 100% operacional pós-reboot:

1. **Verificar GPU & Driver NVIDIA:**
```bash
nvidia-smi

```


2. **Verificar se o Docker tem acesso à GPU:**
```bash
docker run --rm --gpus all nvidia/cuda:12.0.0-base-ubuntu22.04 nvidia-smi

```


3. **Verificar se a fonte está registrada no sistema:**
```bash
fc-list | grep -i "JetBrainsMono"

```



---
