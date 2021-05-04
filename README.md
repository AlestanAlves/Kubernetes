# Kubernetes

O kubernetes vem para solucionar o problema de ter muitos containers rodando em uma máquina qualquer ou seja uma hora isso vai ter um estouro de memoria e de processamento 

![img](https://caelum-online-public.s3.amazonaws.com/kubernetes/Transcri%C3%A7%C3%A3o+Externa/Imagens/aula1_video2_imagem3.PNG)

Existe a escalabilidade vertical que resolve este caso

![img2](https://caelum-online-public.s3.amazonaws.com/kubernetes/Transcri%C3%A7%C3%A3o+Externa/Imagens/aula1_video2_imagem4.PNG)

Mas o Kubernetes vem para ajudar na escalabilidade vertical e horizontal 

![img3](https://caelum-online-public.s3.amazonaws.com/kubernetes/Transcri%C3%A7%C3%A3o+Externa/Imagens/aula1_video2_imagem5.PNG)

Caso alguma maquina pare o kubernetes sobe uma nova maquina, caso ocorra de acontecer estouro de mémoria o kubernetes é capaz de lidar com isso.

Então a questão é essa: o Kubernetes é capaz de criar e gerenciar um cluster para que nós consigamos manter a nossa aplicação escalável sempre que nós quisermos adicionar novos containers, sempre que nós quisermos reiniciar a nossa aplicação de maneira automática, caso ela tenha falhado. Então nós chamamos isso de orquestração de containers.

Ícone do Kubernetes conectado à área de cluster passando por AWS, Google Cloud e Azure. Nesta área, há três máquinas com um container acima cada, sendo que apenas dois apresentam medidores no valor máximo.

Então o Kubernetes é um orquestrador de containers capaz de resolver todos esses problemas que eu listei para vocês.

### Como funciona a API que controla todo o ambiente do Kubernetes

![image](https://user-images.githubusercontent.com/48387196/116601254-ac418000-a900-11eb-9bbf-62001c345858.png)

os pods são os containers, iguais os do docker e eu posso colocar dois container em um pod.

### Criar nosso cluster virtualizado

```
minikube start --vm-driver=virtualbox
```

Assim estamos criando um cluster dentro da máquina virtual que o VB vai criar.

### Comands 

![image](https://user-images.githubusercontent.com/48387196/116742918-81256200-a9ce-11eb-980a-3d9cdf899852.png)

Subir um pod com uma imagem 

```
kubectl run features --image=features
```

Ver se o pod já subiu

```
kubectl get pods 
```

Ver em tempo real

```
kubectl get pods --watch
```

Ver tudo que ocorreu dentro do pod

```
kubectl describe pod features
```

Ver os pods de forma wide com todas as informações:

```
kubectl get pods -o wide
```

Deletar um pod 

``` 
kubectl delete pod <nomedopod>
```
```
kubectl delete -f arquivo.yaml
```
Deletar todos os pods:
```
kubectl delete pods --all
```

## Services (SVC)

![image](https://user-images.githubusercontent.com/48387196/116743021-ab771f80-a9ce-11eb-98fd-939a84e98db4.png)

Serve para a comunicação entre dois pods, sem ele os pods não conseguem se comunicar.

![image](https://user-images.githubusercontent.com/48387196/116743299-1c1e3c00-a9cf-11eb-803c-06db4ddd6177.png)

Obter os valores de services:

```
kubectl get svc
```

**Coisas importantes que ele possui**

- ClusterIP
- NodePort
- LoadBalancer

### ClusterIP

Serve para a comunicação de diferentes clusters

![image](https://user-images.githubusercontent.com/48387196/116743408-4243dc00-a9cf-11eb-98e4-36f02dfc0a23.png)

### NodePort

Abre a comunicação para o mundo

![image](https://user-images.githubusercontent.com/48387196/116749208-f85ef400-a9d6-11eb-8103-1ef81552bcaa.png)

No linux precisamos utilizar o internalIP para acessar o local aonde se encontra a aplicacao no NodePort
Como achar o internalIP:
```
kubectl get pods -o wide
```

### LoadBalancer

![image](https://user-images.githubusercontent.com/48387196/116751821-fa2ab680-a9da-11eb-8468-1c09ec30ec4f.png)

### Acessando um POD

```
kubectl exec -it database -- bash
```

## ConfigMap

![image](https://user-images.githubusercontent.com/48387196/116913497-ee7c0180-ac1f-11eb-815d-905ec5769204.png)

Com o configmap conseguimos criar um arquivo de configuração de environments

Criando um configmap:

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: db-configmap
data:
    MYSQL_ROOT_PASSWORD: q1w2e3r4
    MYSQL_DATABASE: empresa
    MYSQL_PASSWORD: q1w2e3r4
```
```
kubectl apply -f configmap-db.yaml
```

Ver a descrição do configmap e ver os configmaps

```
kubectl describe configmap configmap-db
```
```
kubectl get configmap
```

## Docker login on Kubernetes

Criar secret key com base no arquivo /root/docker/.config.json 

```
kubectl create secret docker-registry regcred --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password=<your-pword> --docker-email=<your-email>
```
Pegar o arquivo .yaml criado
```
kubectl get secret regcred --output=yaml
```
Colocar dentro de spec as infos de configs

```
imagePullSecrets:
- name: regcred
 ```

## ReplicaSet

Definindo um replicaset conseguimos não parar a aplicação nunca, pois criamos replicas iguais dos pods que quisermos.

![image](https://user-images.githubusercontent.com/48387196/117041240-272fdf80-ace1-11eb-85af-cc66f18dcd7d.png)

![Screenshot from 2021-05-04 14-44-47](https://user-images.githubusercontent.com/48387196/117046693-5cd7c700-ace7-11eb-95bc-7e74518eb744.png)

## Deployment

![Screenshot from 2021-05-04 15-07-29](https://user-images.githubusercontent.com/48387196/117049431-7b8b8d00-acea-11eb-92ab-634325077d42.png)

Os deployments já criam automaticamente os ReplicaSet 
Na pratica os ReplicaSet e os deployments são diferentes pois com os Deployments conseguimos colocar mais configurações de fluxo.

```
kubectl rollout history deployment nginx-deployment
```
Com o comando acima conseguimos ver um historico dos deploy.
```
kubectl annotate deployment nginx-deployment kubernetes.io/change-cause="Definindo a imagem com a versão latest" 
```
Com este comando anotamos um commit no deployment de uma aplicação e conseguimos olhar cada anotação com o `kubectl rollout history deployment <nome do deploy>`

***Como dou um rollback na versão que eu quero?***

```
kubectl rollout undo deployment nginx-deployment --to-revision=2
```

Então, quando utilizamos nossos Deployments, o que conseguimos fazer? Conseguimos, simplesmente, ter uma camada extra acima de um ReplicaSet, que consegue gerenciar as imagens, todo o versionamento do que estamos definindo, controle de atualização em cima das nossas imagens e Pods. Que legal, não é verdade?

**Então, no fim das contas, a boa prática, o mais comum que vocês irão ver quando vocês forem criar Pods é criar eles através de Deployments, que eles já vão permitir todo esse controle de versionamento e também os benefícios de um ReplicaSet.**
