# Relatório Técnico: Implantação e Roteamento de Aplicação React com Nginx no Docker

## Informações do Projeto
- **Disciplina:** Gerência e Configuração de Serviços para Internet (GCSI)
- **Aluno:** Anthony Ryan Benigno Kiss
- **Ambiente:** Docker (Ubuntu 26.04 LTS com Nginx)
- **Container Utilizado:** `ubuntu2604_no4` (ID: `5bd7c0434d08`)

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
