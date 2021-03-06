---

copyright:
  years: 2014, 2016
lastupdated: "2016-11-04"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Acessando o {{site.data.keyword.objectstorageshort}} usando a CLI Swift {: #using-swift-cli}


O serviço {{site.data.keyword.objectstorageshort}} baseia-se no OpenStack Swift e pode ser acessado usando qualquer aplicativo cliente compatível. Esta seção descreve como usar o cliente Python Swift, que é a interface de linha de comandos (CLI) para a API do {{site.data.keyword.objectstorageshort}} e suas extensões, para trabalhar com contêineres e arquivos.
{: shortdesc}




## Instalando o cliente Swift {: #install-swift-client}

Se não tiver feito isso ainda, instale o
[software
obrigatório](http://docs.openstack.org/user-guide/common/cli_install_openstack_command_line_clients.html#install-the-prerequisite-software){: new_window} a seguir.
* Python 2.7 ou mais recente
* Pacote setuptools
* Pacote pip


Para instalar o cliente Python Swift, execute o comando a seguir:
```
pip install python-swiftclient
```
{: pre}

Para instalar o cliente Python Keystone, execute o comando a seguir:
```
pip install python-keystoneclient
```
{: pre}




## Configurando o Cliente {: #setup-swift-client}

Para configurar o cliente Swift, deve-se `exportar` suas
informações de autenticação. É possível gerar credenciais [por meio da interface com o usuário](../ObjectStorage/os_security.html#understanding-credentials) ou [por meio da interface da linha de comandos](../ObjectStorage/os_cli.html#generating-cli).

Para exportar suas informações autenticação, insira suas credenciais e execute o comando a seguir:
```
export OS_USER_ID=
export OS_PASSWORD=
export OS_PROJECT_ID=
export OS_AUTH_URL=
export OS_REGION_NAME=
export OS_IDENTITY_API_VERSION=
export OS_AUTH_VERSION=

swift auth
```
{: codeblock}


Exemplo:
```
export OS_USER_ID=24a20b8e4e724f5fa9e7bfdc79ca7e85
  export OS_PASSWORD=*******
  export OS_PROJECT_ID=383ec90b22ff4ba4a78636f4e989d5b1
  export OS_AUTH_URL=https://identity.open.softlayer.com/v3
  export OS_REGION_NAME=dallas
  export OS_IDENTITY_API_VERSION=3
  export OS_AUTH_VERSION=3

swift auth
```
{: screen}




## Gerando credenciais de serviço do {{site.data.keyword.objectstorageshort}} {: #generating-cli}

Para gerar credenciais de serviço usando a CLI Swift, é possível usar as etapas a seguir.

1. Se você não tiver efetuado login no {{site.data.keyword.Bluemix_notm}}, efetue login como um usuário com uma função do desenvolvedor. Deve-se estar localizado dentro do espaço da instância de serviço que você deseja gerenciar.
  ```
  cf login -a api.ng.bluemix.net -u <userid> -p <password> -o <organization> -s <space>
  ```
  {: pre}

2. Gere credenciais de serviço. `service-key-name` é o nome que você dá à sua credencial. É possível usar o comando do Cloud Foundry ou o comando cURL.
  Comando do Cloud Foundry:
  ```
  cf create-service-key "<object_storage_service_instance_name>" <service-key-name> -c '{"role":"<object_storage_role>"}'
  ```
  {: pre}

  Comando de exemplo do Cloud Foundry:
  ```
  cf create-service-key "Object-Storage-AclTest" GeorgeKey -c '{"role":"member"}'
  ```
  {: screen}

  Comando cURL:
  ```
  curl "https://api.ng.bluemix.net/v2/service_keys" -d '{   "service_instance_guid": "<service_instance_guid>",   "name": "<user_name>", "role": "member"}' -X POST -H "Authorization: <bearer_token>" -H "Content-Type: " -H "Cookie: "
  ```
  {: pre}

3. Valide as credenciais para a chave de serviço criada.
  Comando do Cloud Foundry:
  ```
  cf service-keys <service_instance>
  ```
  {: pre}

  Exemplo de chave de serviço de membro para uma instância denominada Object-Storage-Acl-Test:

  ```
  {
    "auth_url": "https://identity.open.softlayer.com",
    "domainId": "8753ff40ac1a4f4a9f162ad8026b6ce0",
    "domainName": "757955",
    "password": "xxxxxxxx",
    "project": "object_storage_6046b6e2_ee53_4884_916f_89c65e4d408e",
    "projectId": "c727d7e248b448f6b268f31a1bd8691e",
    "region": "dallas",
    "role": "member",
    "userId": "3c5b516655e4479881d3d216895faedb",
    "username": "member_2afbeea1d58b1867f46c699553d1e4513e7df83a"
  }
  ```
  {: screen}

  Comando cURL:
  ```
  curl "https://api.ng.bluemix.net/v2/service_instances/b9656309-d994-4dec-a71f-8eac6e2fc7dc/service_keys" -X GET  -H "Authorization: <bearer_token>" -H "Cookie: "
  ```
  {: pre}




## Armazenando objetos em contêineres por meio da CLI {: #storing-cli}

1. Se você não tiver efetuado login no {{site.data.keyword.Bluemix_notm}}, efetue login na organização e no espaço que contêm sua instância do {{site.data.keyword.objectstorageshort}}.
  ```
  cf login -a api.ng.bluemix.net -u <userid> -p <password> -o <organization> -s <space>
  ```
  {: pre}

2. Crie um novo contêiner do {{site.data.keyword.objectstorageshort}} executando o comando a seguir. A variável *container_name* é configurada, por você, neste momento.
  ```
  swift post <container_name>
  ```
  {: pre}

**Nota**: se uma mensagem de erro for recebida, confirme se você instalou [o software obrigatório](../ObjectStorage/os_cli.html##install-swift-client).

3. (Opcional:) para verificar se o seu contêiner foi criado, execute o comando a seguir para listar seus contêineres.
  ```
  swift list
  ```
  {: pre}

4. Para evitar a destruição de dados por sobrescrições não intencionais, [configure o controle de versão do objeto](../ObjectStorage/os_versioning.html#work-with-object-versioning). Se você não desejar o controle de versão de objetos, liste seus arquivos existentes no armazenamento e, se necessário, renomeie o diretório ou os arquivos antes de fazer o upload.

4. Faça upload de um arquivo para seu contêiner executando o comando a seguir.
  ```
  swift upload <container_name> <file_name>
  ```
  {: pre}

**Nota**: para fazer upload de arquivos com tamanho superior a 5 GB, [são necessárias etapas extras](../ObjectStorage/os_large_files.html#large-files).


5. (Opcional:) para verificar se o upload foi bem-sucedido, visualize o conteúdo do seu contêiner executando o comando a seguir:
  ```
  swift list <container_name>
  ```
  {: pre}




## Fazendo download de objetos com a CLI Swift {: #downloading-cli}


1.  Se você não tiver efetuado login no {{site.data.keyword.Bluemix_notm}}, efetue login na organização e no espaço que contêm sua instância do {{site.data.keyword.objectstorageshort}}.

```
cf login -a api.ng.bluemix.net -u <userid> -p <password> -o <organization> -s <space>
```
{: pre}

2. Para evitar a destruição de dados por sobrescrições não intencionais, [configure o controle de versão do objeto](../ObjectStorage/os_versioning.html#work-with-object-versioning). Se você não desejar o controle de versão de objetos, liste seus arquivos existentes no armazenamento e, se necessário, renomeie o diretório ou os arquivos antes de fazer o download.

3. Faça download de um arquivo executando o comando a seguir:

```
swift download <container_name> <file_name>
```
{: pre}




## Excluindo objetos e contêineres por meio da CLI {: #deleting-cli}

**Atenção**: se você excluir seu contêiner, excluirá quaisquer objetos armazenados nele.

1.  Se você não tiver efetuado login no {{site.data.keyword.Bluemix_notm}}, efetue login na organização e no espaço que contêm sua instância do {{site.data.keyword.objectstorageshort}}.
  ```
  cf login -a api.ng.bluemix.net -u <userid> -p <password> -o <organization> -s <space>
  ```
  {: pre}

2. (Opcional:) confirme se você tem um backup dos seus objetos antes de excluir seus arquivos e contêineres.

3. Execute o comando a seguir para excluir um arquivo:
  ```
  swift delete <container_name> <file_name>
  ```
  {: pre}

3. Para excluir seu contêiner, execute o comando a seguir:
  ```
  swift delete <container_name>
  ```
  {: pre}

**Nota**: é possível planejar um
[tempo específico
para que seu objeto seja excluído](../ObjectStorage/os_deletion.html#schedule-object-deletion).




## Trabalhando com diretórios {: #directory-cli}

#### Incluindo um diretório em um contêiner com a CLI Swift

O Swift não tem uma estrutura de diretório verdadeira, mas usa nomenclatura para
representar um layout de diretório. Se você especificar um nome de diretório, ele será
anexado a todos os nomes de arquivo como parte do caminho relativo.

1. Localmente, crie um diretório e salve seu arquivo nele.
2. Execute o comando a seguir para fazer upload de um diretório em seu contêiner:
  ```
  swift upload <container_name> <directory_name>
  ```
  {: pre}

#### Fazendo download de um diretório
Para fazer download de uma estrutura de diretório, use o parâmetro `-prefix` para indicar o diretório ou a estrutura de diretório da qual você deseja fazer download.

1. Execute o comando a seguir para fazer download de um diretório:
  ```
  swift download <container_name> --prefix <directory>
  ```
  {: pre}
