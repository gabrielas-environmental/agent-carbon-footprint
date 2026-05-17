# Guia Completo: Registro e Autorização de App no Trello com Python

Este guia orienta você no processo de registrar, autorizar e usar as APIs do Trello com Python.

## Pré-requisitos

- Conta ativa no Trello
- Python 3.7 ou superior instalado
- pip (gerenciador de pacotes Python)
- Navegador web

---

## Instalação das Dependências

Primeiro, instale as bibliotecas necessárias:

```bash
pip install -r requirements.txt
```

Ou crie um arquivo `requirements.txt`:

```txt
google-adk
py-trello
datetime
dotenv
```

E instale com:

```bash
pip install -r requirements.txt
```

---

## Passo 1: Criar um Novo Power-Up (Aplicativo)

### 1.1 Acessar o Portal de Power-Ups

1. Acesse o portal de administração de Power-Ups do Trello:
   ```
   https://trello.com/power-ups/admin/
   ```

2. Faça login com sua conta Trello

3. Clique no botão **"New"** ou **"Criar novo Power-Up"**

### 1.2 Preencher Informações do Aplicativo

Na tela "Novo aplicativo", preencha os seguintes campos:

| Campo | Valor Exemplo | Descrição |
|-------|---------------|-----------|
| **Nome do aplicativo** | `AppDio` ou `Meu App Python` | Nome que identificará seu aplicativo |
| **Área de trabalho** | Selecione seu workspace | Workspace onde o app será gerenciado |
| **Email** | `me@company.com` | Email para contato sobre o aplicativo |
| **Contato de suporte** | `support@company.com` | Email ou link para suporte aos usuários |
| **Autor** | `Seu Nome` ou `Sua Empresa` | Nome do desenvolvedor/empresa |
| **URL de conector iframe** | `https://seu-dominio.com/` | URL do iframe (opcional para API básica) |

> 💡 **Dica:** Para uso apenas da API REST (sem interface visual), você pode deixar a "URL de conector iframe" em branco ou colocar uma URL placeholder.

### 1.3 Criar o Power-Up

1. Revise as informações preenchidas

2. Clique em **"Criar"** no canto inferior direito

3. Você será redirecionado para a página de gerenciamento do seu Power-Up

---

## Passo 2: Obter a API Key

Após criar o Power-Up:

1. Na página de gerenciamento do seu Power-Up, procure pela seção **"API Key"** ou **"Chave de API"**

2. Você verá sua **API Key** ou **chave de API**(uma string alfanumérica longa) e o ** Secret ** ou ** Segredo ** 

3. **Copie e guarde essa chave** em um local seguro

> ⚠️ **Importante:** A API Key é única para seu Power-Up e deve ser tratada como informação sensível.

**Formato da API Key:**
```
abc123def456ghi789jkl012mno345pqr678
```

---

## Passo 3: Gerar o Token de Autorização

### 3.1 Construir a URL de Autorização

Agora você precisa gerar um token de acesso para fazer requisições em nome do usuário.

Use a seguinte URL, substituindo `SUA_API_KEY_AQUI` pela sua API Key:

```
https://trello.com/1/authorize?expiration=never&name=AppDio&scope=read,write&response_type=token&key=SUA_API_KEY_AQUI
```

### 3.2 Parâmetros Explicados

| Parâmetro | Valor | Descrição |
|-----------|-------|-----------|
| `expiration` | `never` | Token não expira<br>Opções: `1hour`, `1day`, `30days`, `never` |
| `name` | `AppDio` | Nome do aplicativo (use o mesmo do Passo 1) |
| `scope` | `read,write` | Permissões solicitadas<br>Opções: `read`, `write`, `account` |
| `response_type` | `token` | Tipo de resposta (sempre `token`) |
| `key` | `SUA_API_KEY` | Sua API Key obtida no Passo 2 |

### 3.3 Exemplo de URL Completa

Se sua API Key for `abc123def456ghi789`, a URL ficaria:

```
https://trello.com/1/authorize?expiration=never&name=AppDio&scope=read,write&response_type=token&key=abc123def456ghi789
```

### 3.4 Escopos de Permissão Disponíveis

| Escopo | Descrição |
|--------|-----------|
| `read` | Permite ler informações de boards, cards, listas, etc. |
| `write` | Permite criar, editar e deletar recursos |
| `account` | Permite acesso a informações da conta do usuário |

Para múltiplos escopos, separe com vírgula: `scope=read,write,account`

---

## Passo 4: Autorizar o Aplicativo

### 4.1 Acessar a URL de Autorização

1. Cole a URL completa (com sua API Key) no navegador

2. Pressione **Enter**

### 4.2 Revisar Permissões

Você será redirecionado para uma página de autorização do Trello que mostrará:

- ✅ Nome do aplicativo (ex: "AppDio")
- ✅ Permissões solicitadas (read, write)
- ✅ Lista de boards e organizações acessíveis
- ✅ Duração do token (never = sem expiração)

### 4.3 Conceder Acesso

1. Revise cuidadosamente as permissões

2. Se estiver de acordo, clique no botão **"Permitir"** ou **"Allow"**

3. Você será redirecionado para uma página de sucesso

---

## Passo 5: Obter o Token

### 5.1 Copiar o Token

Após autorizar, o Trello exibirá seu **Token de Acesso** em texto simples na página.

O token será uma string alfanumérica longa, similar a:

```
a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0u1v2w3x4y5z6a7b8c9d0e1f2
```

### 5.2 Guardar com Segurança

**⚠️ CRÍTICO: Copie e guarde esse token imediatamente!**

Você precisará dele para todas as requisições à API. Se perder o token, será necessário gerar um novo seguindo os Passos 3 e 4 novamente.

---

## Passo 6: Configurar o Arquivo `.env`

Crie um arquivo `.env` no mesmo diretório do `agent.py` com as seguintes variáveis:

```env
TRELLO_API_KEY=sua_api_key_aqui
TRELLO_API_SECRET=seu_api_secret_aqui
TRELLO_TOKEN=seu_token_aqui
TRELLO_BOARD_NAME=DesafioDIOAgents
```

> 💡 **Dica:** `TRELLO_BOARD_NAME` é opcional — se omitido, o agente usará `DesafioDIOAgents` como padrão.

---

## Mudanças Recentes no `agent.py`

### Carregamento explícito do `.env`

**Antes:** `load_dotenv()` buscava o arquivo `.env` a partir do diretório de trabalho atual, o que causava falhas ao executar o agente de outro diretório.

**Depois:** O `.env` é carregado sempre a partir do diretório do próprio `agent.py`, independente de onde o script é chamado:

```python
from pathlib import Path

env_path = Path(__file__).parent / '.env'
load_dotenv(env_path)
```

---

### Nome do board configurável via `.env`

**Antes:** O nome do board estava fixo no código como `'DIO'` em todas as funções.

**Depois:** Lido da variável de ambiente `TRELLO_BOARD_NAME`, com fallback para `'DesafioDIOAgents'`:

```python
BOARD_NAME = os.getenv('TRELLO_BOARD_NAME', 'DesafioDIOAgents')
```

Para trocar de board, basta alterar o `.env` — sem tocar no código.

---

### Tratamento de erros e busca segura de board/lista

**Antes:** As funções usavam índice direto (`[0]`) ao buscar board e lista, o que gerava `IndexError` sem mensagem útil se o nome não fosse encontrado.

**Depois:** Substituído por `next(..., None)` com verificação explícita e mensagem de erro descritiva, inclusive listando os boards/listas disponíveis:

```python
meu_board = next((b for b in boards if b.name == BOARD_NAME), None)
if not meu_board:
    raise ValueError(f"Board '{BOARD_NAME}' não encontrado. Boards disponíveis: {[b.name for b in boards]}")
```

---

### `adicionar_tarefa` com retorno e remoção de chamada duplicada

**Antes:** A função chamava `client.list_boards()` duas vezes (bug) e não retornava nenhuma mensagem de confirmação.

**Depois:** Chamada duplicada removida, função envolvida em `try/except`, e retorna mensagem de sucesso ou erro:

```python
return f"Card '{nome_da_task}' criado com sucesso na lista '{minha_lista.name}'"
```

---

---

## Demonstração

### Agente em funcionamento (ADK Dev UI)

<img width="1151" height="710" alt="agente-funcionando" src="https://github.com/user-attachments/assets/e2a0d489-1d39-4289-b108-23534a9db824" />


### Quadro do Trello após interação com o agente

<img width="1905" height="989" alt="trello-board" src="https://github.com/user-attachments/assets/bca6f63d-7767-4ce2-b7c7-c1d1ed99dbe5" />


---

### Bibliotecas Python
- **py-trello:** https://github.com/sarumont/py-trello

---

**Última atualização:** Maio 2026  
**Versão da API Trello:** v1  
**Python:** 3.7+
