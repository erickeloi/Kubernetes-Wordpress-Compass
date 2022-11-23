*******
# Sumário
0. Estrutura do Projeto
1. Instalação Kubernetes
    1. Instalação no Windows, por extenso (com Docker Desktop)
2. Configurar o ambiente
    1. Criar um namespace
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
    7. Secrets
        1. MYSQL-secret
    8. Deployment
        1. MYSQL-deployment
        2. WORDPRESS-deployment
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
      port: 80
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

### 5. Persistent Volume Claim

O Persistent Volume Claim(PVC) é Utilizado para requisitar uma Persistent Volume que atenda seus requisitos, o PV é uma parte do armazenamento dentro do cluster.

Ele pode ser tanto criada manualmente ou dinamicamente, nesse caso utilizando o Docker Desktop.

Temos também uma StorageClass que é responsavel pela criação do nosso PV de maneira dinamica. 

Sendo assim, Teremos em nossa estrutura de arquivos somente os YAML relacionados ao PVC.

#### 1. MYSQL-PV-claim

Template:
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
  labels:
    app: wordpress
  namespace: labwordpress
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
```

#### 2. WORDPRESS-PV-claim

Template:
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: wp-pv-claim
  labels:
    app: wordpress
  namespace: labwordpress
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
```
A estrutura dos nossos PersistentVolumeClaim se dão da seguinte maneira: 

`kind: PersistentVolumeClaim` Corresponde ao tipo do objeto yaml. 

`accessModes:ReadWriteOnce` Está relacionado ao fato de que o volume pode ser montado como leitura-escrita por um nó único. 

`storage: 3Gi` Requisitamos que o volume atenda o tamanho de 3 GigaBytes. 

>OBS: A Diferança entre o MYSQL-PV-claim e o WORDPRESS-PV-claim, nesse caso ficaria no campo `name` que nomeia o objeto. 

Esses objetos também precisam ser executados para funcionarem no cluster !!!

Sobre o Deploy desses Objetos:
Encaminhe-se para o diretório do serviço em questão (wordpress-app e mysql-banco) e execute os pvcs com:

```
kubectl apply -f mysql-pvc.yaml
```

```
kubectl apply -f wordpress-pvc.yaml
```

---
### 6. ConfigMaps

Um ConfigMap é um objeto usado para armazenar dados não-confidenciais em pares chave-valor.

Utilizaremos o seguinte arquivo  ```ConfigMap ```:

Template:
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysql-configmap
  namespace: labwordpress
data:
  mysql-database: wordpressdb
  mysql-user: wordpress
  mysql-service: mysql-service
```

Observe que nos campos abaixo de ```data``` teremos as chaves e os valores para armazenar respectivamente:

```mysql-database: wordpressdb``` o nome do banco de dados

```mysql-user: wordpress``` o usuario do banco de dados

```mysql-service: mysql-service``` o serviço utilizado do banco de dados

Após a criação, dê o apply no arquivo ```kubectl apply -f configmap.yaml```

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

> OBS: A Senha em questão é uma Senha aleatória de 50 caracteres, com simbolos, números, caracteres especiais, letras maisuculas e minusculas. Site usado para gerar a senha em questão: https://www.lastpass.com/pt/features/password-generator

> **Note**: lembre-se de criar uma senha com fortes padrões de segurança !!
(Exemplo: Muitos caracteres, letra maiúscula e minuscula, números, caracteres especiais)

E então, encripte sua senha para base64, você pode fazer isso no site:
https://www.base64encode.org

ou, no Ubuntu (e outras Distro Linux) com o comando:

```
echo '<sua-senha-forte> | base64
```

Exemplo (Senha Ilustrativa):
```
echo  'I$desY9&11h9HiS76*ec33lb*eTUDe7v&i7!QrakDDwl$6ntvc' | base64
```

A saída desse comando é sua senha encriptada em base64:

Output:
```
SSRkZXNZOSYxMWg5SGlTNzYqZWMzM2xiKmVUVURlN3YmaTchUXJha0REd2wkNm50dmM=
```

Com isso, abra o arquivo `mysql-secret.yaml` e adicione sua senha encriptada como valor da variável `mysql-root-password`.

Exemplo (Senha Ilustrativa):

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

---

> OBS:Caso o arquivo de configuração yaml não exista , Crie-o.

Sobre o Deploy desse Objeto:

Encaminhe-se para o diretório onde esse arquivo de configuração yaml está localizado (mysql-banco) e execute o arquivo de configuração `mysql-secret.yaml` com o comando:

```
kubectl apply -f mysql-secret.yaml
```

E pronto, agora ele está em execução no Cluster.
---
### 8. Deployment

#### 1. MYSQL-deployment
Para darmos deploy na aplicação dentro do cluster, usaremos o objeto 'Deployment' do Kubernetes, Nesse caso, daremos deploy na aplicação de banco de dados (MySQL)

Para isso é utilizado o arquivo com os seguintes campos demonstrados abaixo, nele possuimos as ```matchLabels```, que seram usadas para ligar o objeto com o seu respectivo serviço. 

Temos também o campo ```Strategy``` com o ```tipo(type)``` definido como ```recreate```, caso aconteça algo com o Pod teremos a recriação do mesmo até que seja concertado o erro. 

Logo abaixo de ```containers``` temos o campo ```image``` onde denifimos a imagem que utilizaremos sendo nesse caso ```mysql: 8.0.31```, temos também as variaveis de ambientes que seram necessarias para definir o nosso container. 

Sendo elas e suas respectivas definições:

```MYSQL_ROOT_PASSWORD``` = Senha do usuario root

```MYSQL_DATABASE``` = Nome do banco de dados

```MYSQL_USER``` = O usuario 

```MYSQL_PASSWORD``` = Senha

Já nos campos ```VolumeMounts``` e ```Volumes``` definimos o caminho do volume dentro do container e em ```Volumes``` colocamos o PVC para solicitar o PV respectivos para o mysql.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-deployment
  labels:
    app: wordpress
  namespace: labwordpress
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wordpress
      tier: mysql
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: wordpress
        tier: mysql
    spec:
      containers:
      - image: mysql:8.0.31
        name: mysql
        env:
        - name: MYSQL_DATABASE
          valueFrom:
            configMapKeyRef:
              name: mysql-configmap
              key: mysql-database
        - name: MYSQL_USER
          valueFrom:
            configMapKeyRef:
              name: mysql-configmap
              key: mysql-user
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secrets
              key: mysql-password
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

Com o deployment do MYSQL pronto, teremos uma base parecida porém com algumas diferenças para nosso deployment da aplicação Wordpress, sendo elas nas ```matchLabels``` que teram valores correspondentes para se conectarem com o service da aplicação wordpress,`image` que sera utilizada ```wordpress:6.1.1```, as variaveis de ambiente que serão as demonstradas abaixo com as suas respectivas definições: 

```WORDPRESS_DB_HOST = nome do service do mysql```

```WORDPRESS_DB_USER = nome do usuario do banco de dados mysql```

```WORDPRESS_DB_PASSWORD = a senha do usuario do banco de dados mysql```

```WORDPRESS_DB_NAME = o nome do banco de dados```
 
Já nos campos ```VolumeMounts``` e ```Volumes``` definimos o caminho do volume dentro do container e em ```Volumes``` colocamos o PVC para solicitar o PV, porém desta vez respectivos para o wordpress. 

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress-deployment
  labels:
    app: wordpress
  namespace: labwordpress  
spec:
  replicas: 2
  selector:
    matchLabels:
      app: wordpress
      tier: frontend
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: wordpress
        tier: frontend
    spec:
      containers:
      - image: wordpress:6.1.1
        name: wordpress
        env:
        - name: WORDPRESS_DB_HOST
          valueFrom:
            configMapKeyRef:
              name: mysql-configmap
              key: mysql-service
        - name: WORDPRESS_DB_NAME
          valueFrom:
            configMapKeyRef:
              name: mysql-configmap
              key: mysql-database      
        - name: WORDPRESS_DB_USER
          valueFrom:
            configMapKeyRef:
              name: mysql-configmap
              key: mysql-user        
        - name: WORDPRESS_DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secrets
              key: mysql-root-password
        ports:
        - containerPort: 80
          name: wordpress
        volumeMounts:
        - name: wordpress-persistent-storage-lab
          mountPath: /var/www/html
      volumes:
      - name: wordpress-persistent-storage-lab
        persistentVolumeClaim:
          claimName: wp-pv-claim
```
---

Sobre o Deploy desse Objeto:

Encaminhe-se para o diretório onde os arquivos de configuração yaml estão localizados (mysql-banco e wordpress-app) e execute os arquivos de configuração `mysql-deployment.yaml` e `wordpress-deployment.yaml` com o comando:

```
kubectl apply -f mysql-deployment.yaml
```

```
kubectl apply -f wordpress-deployment.yaml
```

E pronto, agora ele está em execução no Cluster.
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
