# Relatório Técnico: Implantação e Roteamento de Aplicação React com Nginx no Docker

## Informações do Projeto
- **Disciplina:** Gerência e Configuração de Serviços para Internet (GCSI)
- **Aluno:** Anthony Ryan Benigno Kiss
- **Ambiente:** Docker (Ubuntu 26.04 LTS com Nginx)
- **Container Utilizado:** `ubuntu2604_no4`

---

## 1. Visão Geral
Este documento descreve a hospedagem e a configuração do servidor **Nginx** para servir uma aplicação web desenvolvida em **React** (SPA - *Single Page Application*) dentro de um container Docker.

O objetivo principal foi resolver o problema de erro **HTTP 404 Not Found** ao acessar rotas virtuais do React Router diretamente ou ao recarregar a página (F5), além de garantir o correto apontamento dos arquivos de *build* (`dist`).

---

## 2. Preparação do Ambiente

### 2.1 Instalação dos Pacotes no Container
Acesse o container em modo root e instale o Nginx e utilitários de suporte:

```bash
apt update
apt install -y nginx nano curl
service nginx start
``` 
### 2.2 Transferência dos Arquivos de Build
No terminal da máquina hospedeira (host), envie os arquivos estáticos compilados da aplicação React (pasta dist) para o diretório de hospedagem dentro do container:

```bash
docker cp ~/Documentos/Censo_Escolar/Minha-App-React/dist ubuntu2604_no4:/var/www/html/
```
vale lembra que aqui eu utilizei pro dist meu projeto  vcs vão precisar colocar o de vcs

# 3. Diagnóstico e Resolução dos Problemas

### 3.1 Identificação do Erro
A aplicação apresentou falhas de carregamento por dois motivos principais:

Caminho da Raiz (Root): O Nginx por padrão busca os arquivos em /var/www/html/, mas os arquivos da aplicação foram copiados para a subpasta /var/www/html/dist.

Tratamento de Rotas do React (SPA): Por padrão, a instrução try_files $uri $uri/ =404; faz o Nginx procurar um arquivo físico no servidor. Como as rotas em SPAs são gerenciadas pelo JavaScript no lado do cliente, recarregar a página em rotas internas gerava o erro 404.


# 3.2 Ajuste na Configuração do Nginx
Edite o arquivo de configuração de site padrão do Nginx:

```bash
nano /etc/nginx/sites-available/default
```
Altere a diretiva root para apontar para a pasta dist e configure a instrução try_files no bloco location / para repassar as requisições ao index.html:

apos fazer as edições siga o proximo passo abaixo!!!!

```bash
nginx -t
service nginx restart
```
# 4. Validação e Acesso à Aplicação

## 4.1 Teste de Conexão e Funcionamento
O Nginx está escutando nativamente na porta 80 dentro do container. No nosso caso, como o container não foi iniciado com o mapeamento explícito de portas para a máquina hospedeira (-p 80:80), o acesso foi realizado via o IP da rede interna do Docker

1:Identificação do IP interno do container (executado no host):

```bash
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' 5bd7c0434d08
```
em seguida ele vai retorna o ip de vcs claro caso vcs n usem a porta 80:80

2:Acesso ao Navegador:
Navegando até http://172.17.0.2, a aplicação React carregou normalmente na porta 80 interna. O roteamento entre as páginas e a atualização via F5 funcionaram sem apresentar o erro 404.

### 4.2 Observação sobre Mapeamento de Portas (http://localhost)
É importante ressaltar que o Nginx está devidamente configurado e rodando na porta 80.

Forma utilizada: Acesso via IP da rede interna do container (http://172.17.0.2:80).

Acesso via http://localhost: Para acessar diretamente pelo localhost:80 do computador principal, basta recriar ou mapear o container vinculando as portas com o parâmetro -p 80:80 no comando docker run:

```bash
docker run -d -p 80:80 --name container-react ubuntu:26.04
```

# 5. Conclusão
A redefinição da diretiva root para /var/www/html/dist somada à instrução try_files $uri $uri/ /index.html; solucionou integralmente o problema de roteamento em SPAs. O Nginx passa a delegar a gestão de rotas para o index.html, garantindo a navegação contínua no React sem quedas ou erros 404.

