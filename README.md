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

### 1. Para a instalação do Docker Desktop no Windows:

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

### 1. Criar um Namespace
A partir de agora, os comandos executados serão no terminal, podendo ser no `Prompt de Comando` ou `PowerShell`.

Podemos criar um namespace de diversas formas, abaixo estão duas das opções para a criação do namespace `labwordpress`, que é o namespace onde a aplicação será construída.

#### 1. Comando: `kubectl create namespace`

Template:
```
kubectl create namespace <nome-do-namespace>
```

Criando o namespace 'labwordpress':
```
kubectl create namespace labwordpress
```
Dessa forma, você usara o terminal para criar o namespace.

#### 2. Execução do arquivo `yaml` com especificações do namespace

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: labwordpress
```

E então, executar o arquivo de configurações yaml:

```
kubectl apply -f namespace.yaml
```

* `apiVersion`: Define a versão da api, depende do Tipo de Objeto K8'S, nesse caso: v1.

* `kind`: Define o tipo de objeto do kubernetes, nesse caso: namespace.

* `metadata`: Especifica metadados e informações de objetos/configurações no cluster, nesse caso é um Namespace e no campo `name` inserimos o nome que será dado a esse namespace. Por isso, o campo `name` recebendo o valor `labwordpress`.

## 3. Configurar a Aplicação
---
### 1. Services

#### 1. CluesterIP (MYSQL)
Para fazer a conexão e comunicação entre a aplicação e o banco de dados (Wordpress + MySQL), usaremos o serviço de ClusterIP, que funciona, em resumo, como uma 'Comunicação interna no Cluster'.

Para isso, executaremos o serviço em questão aplicando o arquivo de configuração `mysql-service.yaml` que está localizado no diretório 'BANCO-MYSQL'

Então, iremos acessar o diretório em questão e executaremos o arquivo de configuração:

```
cd ./BANCO-MYSQL
```

```
kubectl apply -f mysql-service.yaml
```

O Arquivo em questão possui essas configurações:
```yaml
apiVersion: v1
kind: Service
metadata:
  namespace: labwordpress
  name: wordpress-mysql
  labels:
    app: wordpress
spec:
  ports:
    - port: 3308
  selector:
    app: wordpress
    tier: mysql
  clusterIP: None
   
```

Dessa forma, efetuando o deploy do serviço de comunicação do MYSQL.

#### 2. CluesterIP (WORDPRESS)
Para fazer a conexão e comunicação entre a aplicação e o banco de dados (Wordpress + MySQL), usaremos o serviço de ClusterIP, que funciona, em resumo, como uma 'Comunicação interna no Cluster'.

Para isso, executaremos o serviço em questão aplicando o arquivo de configuração `wordpress-service.yaml` que está localizado no diretório 'APP-WORDPRESS'

Então, iremos acessar o diretório em questão e executaremos o arquivo de configuração:

```
cd ./APP-WORDPRESS
```

```
kubectl apply -f wordpress-service.yaml
```

O Arquivo em questão possui essas configurações:
```yaml
apiVersion: v1
kind: Service
metadata:
  namespace: labwordpress
  name: wordpress
  labels:
    app: wordpress
spec:
  type: ClusterIP  
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  selector:
    app: wordpress
    tier: frontend
```

Dessa forma, efetuando o deploy do serviço de comunicação do WordPress.

---

### 2. Ingress Controller (NGINX)
Para deixarmos nossa aplicação acessível, usaremos um Ingress Resource (NGINX). 

E, para que o Ingress resource funcione, o cluster deve ter um ingress controller em execução.

---

### Inicializando o Ingress-Controller (NGINX)

Podemos dar deploy no `Ingress-Controller (NGINX)` dentro do cluster com o comando:

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.5.1/deploy/static/provider/cloud/deploy.yaml
```

ou, acessando a pasta 'Ingress-NGINX' e executando o arquivo de configurações `ingress-controller.yaml`

```
cd ./Ingress-NGINX
```
```
kubectl apply -f ingress-controller.yaml
```

Pode-se confirmar a criação do Ingress Controler conferindo o novo namespace `ingress-nginx` criado, com o comando:

Input:
```
kubectl get namespaces
```
Output:
```
NAME              STATUS   AGE
default           Active   5d
ingress-nginx     Active   1h
```

Nesse caso, o namespace `ingress-nginx` deve constar na saída do comando: `kubectl get namespaces`

---
### 3. Ingress Resources (Ingress-NGINX Route)
Como dito anteriormente, para deixarmos nossa aplicação acessível, usaremos um Ingress Resource (NGINX). 

---

Para isso, executaremos o arquivo `ingress-resource.yaml` na pasta 'APP-WORDPRESS'.

```
cd ./APP-WORDPRESS
```

```
kubectl apply -f ingress-resource.yaml
```

O arquivo em questão possui essas configurações:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  namespace: labwordpress
  name: ingress-template
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: localhost
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: wordpress
            port:
              number: 80
```

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
### 7. Secrets
#### 1. MYSQL-secret

Para injetarmos variáveis sensíveis em pods no Kubernetes, usaremos os objetos 'Secrets',

Template:
```yaml
apiVersion: v1  
kind: Secret  
metadata:  
  namespace: labwordpress
  name: mysql-secrets
type: Opaque  
data:  
  mysql-root-password: <senha-root-mysql-encriptada-em-base64>
```

Primeiramente, Crie uma senha para o banco de dados MySQL, 

Exemplo (Senha Ilustrativa):
```
I$desY9&11h9HiS76*ec33lb*eTUDe7v&i7!QrakDDwl$6ntvc
```

OBS: A Senha em questão é uma Senha aleatória de 50 caracteres, com simbolos, números, caracteres especiais, letras maisuculas e minusculas. Site usado para gerar a senha em questão: https://www.lastpass.com/pt/features/password-generator

> **Note**: lembre-se de criar uma senha com fortes padrões de segurança !!
(Exemplo: Muitos caracteres, letra maiúscula e minuscula, números, caracteres especiais)

E então, encripte sua senha para base64, você pode fazer isso no site:
https://www.base64encode.org

ou, no Ubuntu (e outras Distro Linux) com o comando:
```
echo  'I$desY9&11h9HiS76*ec33lb*eTUDe7v&i7!QrakDDwl$6ntvc' | base64
```

A saída desse comando é sua senha encriptada em base64:
```
SSRkZXNZOSYxMWg5SGlTNzYqZWMzM2xiKmVUVURlN3YmaTchUXJha0REd2wkNm50dmM=
```

Com isso, abra o arquivo `mysql-secret.yaml` e adicione sua senha encriptada como valor da variável `mysql-root-password`, 
Exemplo:

```yaml
apiVersion: v1  
kind: Secret  
metadata:  
  namespace: labwordpress
  name: mysql-secrets
type: Opaque  
data:  
  mysql-root-password: SSRkZXNZOSYxMWg5SGlTNzYqZWMzM2xiKmVUVURlN3YmaTchUXJha0REd2wkNm50dmM=
```

Em resumo:

Crie uma senha forte, com fortes padrões de segurança.

Template:
```
<Sua-Senha-Forte>
```
Converta essa senha para base64
```
echo  '<Sua-Senha-Forte>' | base64
```
Coloque essa senha encriptada em base64 no arquivo de secret, como valor da variável `mysql-root-password` ! 
#### 2. WORDPRESS-secret

---
### 8. Deployment

#### 1. MYSQL-deployment
Para darmos deploy na aplicação dentro do cluster, usaremos o objeto 'Deployment' do Kubernetes,

Nesse caso, daremos deploy na aplicação de banco de dados (MySQL)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: labwordpress
  name: wordpress-mysql
  labels:
    app: wordpress
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wordpress
      tier: mysql
  template:
    metadata:
      labels:
        app: wordpress
        tier: mysql
    spec:
      containers:
      - image: mysql:5.6
        name: mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secrets
              key: mysql-root-password
        ports:
        - containerPort: 3308
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage-lab
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-persistent-storage-lab
        persistentVolumeClaim:
          claimName: mysql-pv-claim
```
---

#### 2. WORDPRESS-deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wp-deployment
spec:
  template:
    metadata:
      name: wp-pod
      labels:
        app: wp-pod
    spec:
      containers:
        - name: wp-container
          image: wordpress:latest
          envFrom:
          - secretRef:
              name: wp-secret
          ports:
            - containerPort: 80
      volumeMounts:
        - name: wordpress-persistent-storage
          mountPath: /var/www/html
      volumes:
      - name: wordpress-persistent-storage
        persistentVolumeClaim:
          claimName: wp-pv-claim

  replicas: 2
  selector:
    matchLabels:
      app: wp-pod
```

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
