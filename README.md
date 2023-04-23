# kubectl_to_gke
Sample repository deploying an application to GKE using kubectl

Tutorial básico para criar um cluster kubernetes na GCP e sunbindo uma aplição básica.

Na nossa conta devemos selecionar o projeto que estamos atuando para que assim os recursos sejem centralizados.
para isso podemos abrir o Cloud Shell do Google para executar o camando para definição do projectID

![image](https://user-images.githubusercontent.com/55896446/233866853-57138504-12bd-4f44-869c-f3f46e6f8804.png)

Devemos preencher com o seguinte PROJECT_ID: ![image](https://user-images.githubusercontent.com/55896446/233866889-0ddc7de3-c7c2-4ea4-93cf-941ff1df4864.png)

```
gcloud config set project PROJECT_ID
```
No meu caso o camando ficará desta forma: ![image](https://user-images.githubusercontent.com/55896446/233866968-c33423f9-46d7-47b5-9a4d-2a27f9de3cdd.png)
Note que logo no início da linha já aparecerá o projeto em que estamos atuando

Agora já podemos criar nosso cluster GKE com o seguinte comando:
```
gcloud container clusters create-auto hello-cluster --region=COMPUTE_REGION
```
Na opção COMPUTE_REGION devemos preencher onde que este cluster vai ser criado vou escolher: us-central1
![image](https://user-images.githubusercontent.com/55896446/233867090-270263ca-9ec4-49b9-9c56-0dd2e19f87b3.png)
Este comando irá criar um cluster mínimo para que possamos realizar os testes que desejamos.

Com o cluster criado precisamos nos conectar a ele para que possamos administrar nossas aplicações, para isso podemos fazer via front ou por linha de comando:
```
gcloud container clusters get-credentials hello-cluster --region COMPUTE_REGION
```
Desta forma podemos nos conectar ao cluster recem criado: ![image](https://user-images.githubusercontent.com/55896446/233870894-0808d7e4-a5d4-46d9-877d-45448ea87094.png)

################################
Finalizando essa conexão podemos seguir para a implantação da imágem de exemplo
Para criarmos uma aplicação usaremos também via terminal sem a necessidade de um arquivo yaml a fim de exemplo, mas em nosso dia a dia podemso utilizar arquivos YAMLs para gerenciar nossas aplicações.

```
kubectl create deployment hello-server --image=us-docker.pkg.dev/google-samples/containers/gke/hello-app:1.0
```
Esse comando irá criar um Deployment e um Pod onde a aplicação será inicializada: ![image](https://user-images.githubusercontent.com/55896446/233871124-c91ee271-cccb-4085-8b82-f56c79fc308f.png)
Para finalizar precisamos expor uma porta para que seja acesível externamente a aplicação contida dentro do pod:
```
kubectl expose deployment hello-server --type LoadBalancer --port 80 --target-port 8080
```
![image](https://user-images.githubusercontent.com/55896446/233871202-657e7cd5-0ea7-4062-943e-b107a72b162c.png)
E assim que o serviço reserve um IP externo para esta aplicação ele será informado na coluna de EXTERNAL-IP.

E assim poderemos acessar a aplicação:
```
http://EXTERNAL_IP
```

![image](https://user-images.githubusercontent.com/55896446/233871266-f61f08ee-284b-44ee-b776-d48a4f26b692.png)

########################################
Para finalizar como se trata de um cluster de exemplo iremos deletar nossas aplicações e também iremos desprovisionar o cluster assim não seremos cobrado pelo recurso após finalizarmos o tutorial

Vamos deletar o Serviço:
```
kubectl delete service hello-server
```
Com este comando executado podemos verificar que somente o serviço do cluster está ativo no momento:
![image](https://user-images.githubusercontent.com/55896446/233871390-8e875118-fe87-4889-9551-575059c9e431.png)

Vamos deletar o Deployment:
```
kubectl delete deployment hello-server
```
![image](https://user-images.githubusercontent.com/55896446/233871435-77ae38ca-8214-48c7-924d-0fa2ce0ce413.png)

E para finalizar vamos deletar o recurso que criamos no início
```
gcloud container clusters delete hello-cluster --region COMPUTE_REGION
```
![image](https://user-images.githubusercontent.com/55896446/233871510-be6015bd-987a-4a2e-8092-ca3c8e3c1da7.png)
Quando o comando ser executado será solicitado uma confirmação basta digitar Y para confirmar e o recurso será deletado.

# OBS.: como não utilizamos nenhum arquivo neste exemplo não é necessário realizar nenhum outro desprovisionamento pois não alocamos espaço em disco fisico e isso não será cobrado posteriormente, mas lembre sempre de conferir nas demais abas após a conclusão de suas atividades.

Para posteriores dúvidas segue documentação utilizada no tutorial: https://cloud.google.com/kubernetes-engine/docs/deploy-app-cluster?hl=pt-br#dockerfile
