*******
# Sumário
0. Estrutura do Projeto
2. Instalação Kubernetes
    1. Instalação no Windows, por extenso (com Docker Desktop)
    2. Instalação no Ubuntu Linux
3. Configurar o ambiente
    1. Garantir que a máquina tem recursos suficientes
    2. Criar um namespace
4. Configurar a aplicação
    1. Services
        1. ClusterIP (MYSQL)
        2. ClusterIP (WORDPRESS)
    2. Ingress Controller (NGINX)
    3. Ingress Resources (Ingress-Nginx redirecionando para a app Wordpress)
    4. Persistent Volumes
        1. MYSQL-PV
        2. WORDPRESS-PV
    5. Persistent Volume Claim
        1. MYSQL-PV-claim
        2. WORDPRESS-PV-claim
    6. ConfigMaps
        1. MYSQL-configmap
        2. WORDPRESS-configmap
    7. Secrets
        1. MYSQL-secret
        2. WORDPRESS-secret
    8. Deployment
        1. MYSQL-deployment
        2. WORDPRESS-deployment
5. Monitoramento da aplicação wordpress
    1. Prometheus Deploy
    2. Coleta de métricas do wordpress
    3. Acesso ao dashboard e monitoramento
6. Bibliografia
*******

# 0. Estrutura do Projeto

![labwordpress-draw-png](https://user-images.githubusercontent.com/65841249/202715219-cde28bbd-3591-4b99-9cb1-11310d7ada41.png)

# 1. Instalação Kubernetes:

## 1. Instalação no Windows, por extenso (com Docker Desktop)

---

Para a subirmos a aplicação do Wordpress em Conjunto com o MySQL via K8's, usaremos as ferramentas a seguir para nos auxiliar a criar um Cluster Kubernetes na máquina local (Windows):

* [Docker Desktop](https://www.docker.com/products/docker-desktop/)

## 0. Requisitos Mínimos para a instalação do Docker e rodar aplicação Kubernetes na máquina local:

Recomendamos, para a instalação do Docker Desktop e para testar a aplicação na máquina local,
no Mínimo:
  * 8GB Memória RAM 
  * 25Gb de espaço Livre em Armazenamento.

Requisitos Mínimos para Instalação Do Docker Desktop:
  * Processador 64-bit com suporte à Virtualização de hardware, a virtualização pode ser ativada nas configurações da BIOS. [Documentação sobre Virtualização](https://docs.docker.com/desktop/troubleshoot/topics/#virtualization) 

    * Windows 10 64-bit: 
        * Versão 21H1 ou maior.
    * Windows 11 64-bit: 
        * Versão 21H2 ou maior.
    * Ativar o recurso WSL 2 no Windows. Para instruções detalhadas, siga a documentação da Microsoft: [Instalação WSL no Windows](https://docs.microsoft.com/en-us/windows/wsl/install-win10) 
    * Baixar e instalar o [Pacote de atualização de Kernel Linux](https://docs.microsoft.com/windows/wsl/wsl2-kernel)
    * Os Recursos de: Hyper-V e Containeres Windows devem estar ativados: [Instalação Hyper-V](https://learn.microsoft.com/pt-br/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)

### 1. Para a instalação do Docker no Windows:
Seguiremos as intruções da Documentação Oficial do Docker e da Microsoft: [Instalação Docker Desktop](https://docs.docker.com/desktop/install/windows-install/) [Instalação WSL](https://docs.microsoft.com/en-us/windows/wsl/install-win10).

Primeiro, devemos ativar o WSL (pode ser feito de formas diferentes), para isso, iniciaremos o PowerShell em modo Administrador.

Na aba de pesquisa do Windows, digite "powershell", e execute-o em modo administrador, como demonstrado a seguir:

<details>
 <summary><strong>Imagens: Como Abrir o PowerShell em modo Administrador</strong></summary>
 
![Procurar Powershell](https://user-images.githubusercontent.com/65841249/196262334-84674994-d036-4f37-a47a-f0d2427da686.png)
 
![Executar Powershell em modo ADM](https://user-images.githubusercontent.com/65841249/196262376-8e2a23e9-f4eb-4129-b6a0-7cd182a1a6a4.png)
 
</details>

---

### 2. Digite os Seguintes comandos no powershell para Ativar o WSL (Windows SubSystem for Linux): 

```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

```powershell
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

<details>
 <summary><strong>Imagens: Comandos no Powershell</strong></summary>
 
 ![Comando 1](https://user-images.githubusercontent.com/65841249/196264815-c207472b-120f-41f4-a4a5-f2292e9d89c6.png)
 
 ![Comando 2](https://user-images.githubusercontent.com/65841249/196264943-c8bd881b-463a-4e32-b8be-3784c41d5049.png)

</details>

---

### 3. Reinicie o Computador

---

### 4. Faça o Download e instalação do Pacote de atualização de Kernel Linux:
Pode ser encontrado no site oficial em: [Instalação WSL2](https://docs.microsoft.com/windows/wsl/wsl2-kernel)
(Também pode ser baixado diretamente em: [WSL2 Download MSI](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi))

---

### 5. Abra novamente o powershell em modo Administrador e Digite o comando:
```powershell
wsl --set-default-version 2
```
Assim, definindo o WSL2 como padrão ao invés do WSL.

<details>
 <summary><strong>Imagens: Definindo o WSL2 como padrão</strong></summary>

![Set WSL2 no PowerShell](https://user-images.githubusercontent.com/65841249/196265405-b12f186f-b314-44e8-964d-911eabf78df7.png)

</details>

---

### 6. Por fim, Baixe e instale o Docker Desktop, isso pode ser feito pelo [Site Oficial do Docker](https://www.docker.com/).

<details>
 <summary><strong>Imagens: Docker Desktop Download</strong></summary>

![Docker Desktop Windows Download](https://user-images.githubusercontent.com/65841249/196259705-d0fc1351-523b-4bcd-a0d7-efafc1b91958.png)

</details>


---

### 7. Reinicie o Computador, e abra o Docker Desktop!
<details>
 <summary><strong>Imagens: Docker Desktop Funcionando</strong></summary>

![Docker Desktop Instalado e funcionando](https://user-images.githubusercontent.com/65841249/196265723-bc870492-4442-4b83-92a4-5439f3116de0.png)

</details>

---

### 8. Ative o Kubernetes nas configurações do Docker Desktop: 
Com o Docker Desktop Funcionando, vá até as configurações, que está localizada no canto superior direito (cheque as imagens para referência visual),

E então, vá até a aba 'Kubernetes' e ative a opção 'Enable Kubernetes'

Confirme as alterações clicando em "Apply and restart",

E com isso, o Kubernetes será ativado no ambiente do Docker Desktop!

<details>
 <summary><strong>Imagens: Ativar Kubernetes nas Configurações do Docker Desktop</strong></summary>
    
![Docker Desktop configurações](https://user-images.githubusercontent.com/65841249/202702773-bdfcee16-6667-44c4-9d55-7afe36cd9b18.png)
    
![Docker Desktop Kubernetes Opção](https://user-images.githubusercontent.com/65841249/202703124-bf67033c-5634-425d-b5b4-ce6ed25dd5c7.png)

![Docker Desktop Kubernetes Ativação](https://user-images.githubusercontent.com/65841249/202703170-980d2ec9-cf14-42e4-b8f9-fb4abd1360fe.png)

![Kubernetes Funcionando OK](https://user-images.githubusercontent.com/65841249/202703183-9e412ae1-0f44-4839-a59f-85fa2f32a91c.png)

</details>

---

## 2. Configurar o ambiente
### 1. Garantir que a máquina possui recursos suficientes

### 2. Criar um Namespace

## 3. Configurar a Aplicação
---
### 1. Services

#### 1. CluesterIP (MYSQL)
#### 2. CluesterIP (WORDPRESS)
---

### 2. Ingress Controller (NGINX)
---
### 3. Ingress Resources (Ingress-NGINX Route)
---
### 4. Persistent Volumes
#### 1. MYSQL Persistent Volume
#### 2. WORDPRESS Persistent Volume
---
### 5. Persistent Volume Claim
#### 1. MYSQL-PV-claim
#### 2. WORDPRESS-PV-claim
---
### 6. ConfigMaps

#### 1. MYSQL-configmap
#### 2. WORDPRESS-configmap
---
###    7. Secrets
#### 1. MYSQL-secret
#### 2. WORDPRESS-secret
---
### 8. Deployment
#### 1. MYSQL-deployment
#### 2. WORDPRESS-deployment
---

## 4. Monitoramento da aplicação wordpress
###    1. Prometheus Deploy
###    2. Coleta de métricas do wordpress
###    3. Acesso ao dashboard e monitoramento

---

## 5. Bibliografia 

### Documentação K8's:

* [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)
* [Ingress Controller](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/)
* [Secrets](https://kubernetes.io/pt-br/docs/concepts/configuration/secret/)
* [Networking Windows](https://kubernetes.io/docs/concepts/services-networking/windows-networking/)
* [Services](https://kubernetes.io/docs/concepts/services-networking/service/)
* [Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)
* [Service internal traffic Policy](https://kubernetes.io/docs/concepts/services-networking/service-traffic-policy/)
* [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/)
* [Persistent Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)
* [Windows Storage](https://kubernetes.io/docs/concepts/storage/windows-storage/)
* [Config Best practices](https://kubernetes.io/docs/concepts/configuration/overview/)
* [Secrets](https://kubernetes.io/docs/concepts/configuration/secret/)
* [ConfigMaps](https://kubernetes.io/docs/concepts/configuration/configmap/)

---

### Documentação NGINX:

* [NGINX-Ingress Controller](https://docs.nginx.com/nginx-ingress-controller/)
* [NGINX-Ingress Controller Deploy](https://kubernetes.github.io/ingress-nginx/deploy/)