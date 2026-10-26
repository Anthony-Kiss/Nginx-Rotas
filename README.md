# Atividade - Configuração de Rotas no Nginx

- **Disciplina:** Gerência e Configuração de Serviços para Internet (GCSI)
- **Aluno:** Anthony Ryan Benigno Kiss
- **Ambiente:** Docker (Ubuntu 26.04 LTS e Nginx)

---

## Objetivo
Documentar a configuração do Nginx para hospedar uma aplicação React rodando em um container Docker, resolvendo o problema de erro 404 em rotas virtuais através do redirecionamento para o arquivo `index.html`.

---

## Pré-requisitos (Preparação do Ambiente)

1. **Instalação dos pacotes no container:**
   Acesse o container como `root` e execute os comandos para atualizar os repositórios e instalar o Nginx, Nano e Curl:
   
```bash
   apt update
   apt install -y nginx nano curl
   service nginx start
