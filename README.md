# Assistência Cell

Sistema web para gerenciamento de uma assistência técnica de celulares. A aplicação permite autenticar usuários, cadastrar clientes, registrar aparelhos e controlar agendamentos de serviços de reparo.

## Funcionalidades

- Login de usuários com senha protegida por **bcrypt**.
- Sessão autenticada com expiração após 30 minutos.
- Cadastro, edição, busca e exclusão de clientes.
- Armazenamento do CPF somente como hash bcrypt.
- Cadastro, edição e exclusão de aparelhos.
- Validação de IMEI com 15 dígitos e prevenção de duplicidade.
- Associação de cada aparelho a um cliente cadastrado.
- Cadastro e acompanhamento de agendamentos.
- Seleção de aparelho e serviço, com data, hora, status e observações.
- Interface web responsiva construída com HTML, CSS e JavaScript puro.
- API JSON para as operações do sistema.
- Validações de regras de negócio e mensagens de erro em português.

## Tecnologias utilizadas

- **Python 3.10 ou superior**
- **Flask 3.0.3**
- **MySQL 8 ou superior**
- **bcrypt 4.2.0**
- **mysql-connector-python 9.0.0**
- HTML5, CSS3 e JavaScript puro

## Estrutura do projeto

```text
assistencia_cell/
├── assistencia_db.sql          # Criação e carga inicial do banco de dados
├── der.png                     # Diagrama entidade-relacionamento
├── documentacao.pdf            # Documentação complementar
├── documentacao_assistencia.docx
├── README.md
└── sistema/
    ├── app.py                  # Ponto de entrada da aplicação Flask
    ├── autenticacao.py         # Login, bcrypt e controle de sessão
    ├── clientes.py             # Regras de negócio dos clientes
    ├── aparelhos.py            # Regras de negócio dos aparelhos
    ├── agendamento.py          # Regras de negócio dos agendamentos
    ├── db.py                   # Conexão e operações com o MySQL
    ├── rotas.py                # Páginas e endpoints da API
    ├── requirements.txt        # Dependências Python
    ├── testar_conexao.py       # Teste de conexão com o banco
    ├── ROTEIRO.md              # Roteiro de testes manuais
    ├── templates/              # Páginas HTML
    └── static/                 # Arquivos CSS e JavaScript
```

> A pasta `venv/`, caso exista, é um ambiente virtual local e não precisa ser versionada. O mesmo vale para a pasta `__pycache__/`.

## Pré-requisitos

Antes de iniciar, instale Python 3.10 ou superior e MySQL Server 8 ou superior. O MySQL deve estar em execução antes de iniciar a aplicação.

## Instalação

### 1. Clone ou copie o projeto

```bash
git clone <URL_DO_REPOSITORIO>
cd assistencia_cell
```

Se o projeto já estiver no computador, apenas abra um terminal na pasta raiz.

### 2. Crie e ative o ambiente virtual

#### Windows — PowerShell

```powershell
cd sistema
python -m venv venv
.\venv\Scripts\Activate.ps1
```

#### Windows — Prompt de Comando

```bat
cd sistema
python -m venv venv
venv\Scripts\activate.bat
```

#### Linux ou macOS

```bash
cd sistema
python3 -m venv venv
source venv/bin/activate
```

### 3. Instale as dependências

Com o ambiente virtual ativado:

```bash
pip install -r requirements.txt
```

## Configuração do banco de dados

O arquivo `assistencia_db.sql` cria o banco `assistencia_db`, suas tabelas e registros iniciais para testes.

> **Atenção:** o script começa com `DROP DATABASE IF EXISTS assistencia_db`. Ele apaga o banco existente antes de recriá-lo. Use-o somente em um ambiente de desenvolvimento ou teste.

### Criar o banco

```bash
mysql -u root -p < assistencia_db.sql
```

Para verificar se os registros foram carregados:

```bash
mysql -u root -p -e "USE assistencia_db; SELECT COUNT(*) AS usuarios FROM usuarios; SELECT COUNT(*) AS clientes FROM clientes; SELECT COUNT(*) AS aparelhos FROM aparelhos; SELECT COUNT(*) AS servicos FROM servicos; SELECT COUNT(*) AS agendamentos FROM agendamentos;"
```

O banco de teste possui as tabelas `usuarios`, `clientes`, `aparelhos`, `servicos` e `agendamentos`.

## Variáveis de ambiente

A aplicação lê as configurações do banco e da sessão por meio das variáveis abaixo:

| Variável | Padrão | Descrição |
|---|---|---|
| `DB_HOST` | `localhost` | Endereço do servidor MySQL |
| `DB_PORT` | `3306` | Porta do MySQL |
| `DB_USER` | `root` | Usuário do banco |
| `DB_PASSWORD` | `Senai@118` | Senha do banco no código de desenvolvimento |
| `DB_NAME` | `assistencia_db` | Nome do banco |
| `SECRET_KEY` | valor de desenvolvimento | Chave usada para assinar as sessões Flask |

Por segurança, configure os valores no ambiente em vez de depender dos padrões do código.

### Linux ou macOS

```bash
export DB_HOST=localhost
export DB_PORT=3306
export DB_USER=root
export DB_PASSWORD="sua_senha_mysql"
export DB_NAME=assistencia_db
export SECRET_KEY="coloque-uma-chave-longa-e-aleatoria"
```

### Windows PowerShell

```powershell
$env:DB_HOST="localhost"
$env:DB_PORT="3306"
$env:DB_USER="root"
$env:DB_PASSWORD="sua_senha_mysql"
$env:DB_NAME="assistencia_db"
$env:SECRET_KEY="coloque-uma-chave-longa-e-aleatoria"
```

## Testar a conexão

Na pasta `sistema`, com o ambiente virtual ativado:

```bash
python testar_conexao.py
```

Saída esperada:

```text
Conexão OK: {'banco': 'assistencia_db', 'versao': '8.x'}
```

Se ocorrer um erro, verifique se o MySQL está ligado, se o banco foi criado e se as credenciais configuradas estão corretas.

## Executar a aplicação

Ainda dentro da pasta `sistema`:

```bash
python app.py
```

Depois, abra no navegador `http://localhost:5000`.

## Usuários de teste

O banco inicial possui os seguintes usuários:

| Usuário | Senha | Perfil exibido |
|---|---|---|
| `admin` | `admin123` | Administrador |
| `carlos` | `carlos123` | Carlos Técnico |
| `ana` | `ana12345` | Ana Atendente |

Altere as credenciais antes de utilizar o sistema em produção.

## Fluxo de utilização

1. Acesse `http://localhost:5000`.
2. Entre com um dos usuários de teste.
3. Na tela principal, escolha entre **Clientes**, **Aparelhos** e **Agendamentos**.
4. Cadastre um cliente antes de cadastrar seu aparelho.
5. Cadastre o aparelho informando cliente, IMEI, marca, modelo e defeito.
6. Crie um agendamento selecionando o aparelho, o serviço e a data/hora.
7. Use a opção **Reagendar** para alterar a data de um atendimento.
8. Clique em **Sair** para encerrar a sessão.

## Regras importantes

- O nome do cliente deve possuir pelo menos três caracteres.
- O CPF deve conter 11 dígitos. Pontos e hífen são removidos antes da validação.
- O CPF não é exibido na listagem e é armazenado como hash bcrypt.
- O IMEI deve possuir exatamente 15 dígitos e não pode estar duplicado.
- Todo aparelho deve estar vinculado a um cliente existente.
- Um cliente que possui aparelhos não pode ser excluído antes que esses aparelhos sejam removidos.
- Um aparelho que possui agendamentos não pode ser excluído antes que esses agendamentos sejam removidos.
- Os status possíveis para um agendamento são `AGENDADO`, `EM_ANDAMENTO`, `CONCLUIDO` e `CANCELADO`.
- A sessão expira 30 minutos após o login.

## Endpoints principais da API

Todos os endpoints abaixo, exceto login, logout e teste de conexão, exigem uma sessão autenticada.

| Método | Endpoint | Descrição |
|---|---|---|
| `POST` | `/api/login` | Autentica um usuário |
| `POST` | `/api/logout` | Encerra a sessão |
| `GET` | `/api/me` | Retorna o usuário autenticado |
| `GET` | `/api/teste-conexao` | Testa a conexão com o MySQL |
| `GET` | `/api/servicos` | Lista os serviços disponíveis |
| `GET` | `/api/clientes` | Lista clientes |
| `GET` | `/api/clientes?q=termo` | Busca clientes por nome, e-mail, telefone ou CPF |
| `POST` | `/api/clientes` | Cadastra um cliente |
| `PUT` | `/api/clientes/<id>` | Edita um cliente |
| `DELETE` | `/api/clientes/<id>` | Exclui um cliente |
| `GET` | `/api/aparelhos` | Lista aparelhos |
| `POST` | `/api/aparelhos` | Cadastra um aparelho |
| `PUT` | `/api/aparelhos/<id>` | Edita um aparelho |
| `DELETE` | `/api/aparelhos/<id>` | Exclui um aparelho |
| `GET` | `/api/agendamentos` | Lista agendamentos por data |
| `POST` | `/api/agendamentos` | Cadastra um agendamento |
| `PUT` | `/api/agendamentos/<id>` | Edita um agendamento |
| `DELETE` | `/api/agendamentos/<id>` | Exclui um agendamento |

### Exemplo de uso da API com curl

```bash
# Fazer login e salvar o cookie da sessão
curl -c cookies.txt \
  -H "Content-Type: application/json" \
  -d '{"login":"admin","senha":"admin123"}' \
  http://localhost:5000/api/login

# Testar a conexão com o banco usando a sessão salva
curl -b cookies.txt http://localhost:5000/api/teste-conexao

# Buscar clientes
curl -b cookies.txt "http://localhost:5000/api/clientes?q=maria"
```

## Testes manuais

O arquivo [`sistema/ROTEIRO.md`](sistema/ROTEIRO.md) contém um roteiro detalhado para testar login, validações de CPF e IMEI, cadastro, edição, exclusão, agendamento, conexão com o banco e expiração da sessão.

## Segurança

- As senhas dos usuários são comparadas usando bcrypt.
- Os CPFs são armazenados como hashes bcrypt, sem exposição do valor original.
- As consultas ao banco utilizam parâmetros, reduzindo o risco de SQL Injection.
- Os cookies de sessão utilizam `HttpOnly` e `SameSite=Lax`.
- Em produção, defina uma `SECRET_KEY` forte e não mantenha credenciais padrão no código.
- Recomenda-se executar a aplicação atrás de um servidor WSGI e utilizar HTTPS em ambientes reais.

## Licença

Este projeto foi desenvolvido para fins acadêmicos e de demonstração. Caso seja necessário publicar ou reutilizar o sistema, defina a licença apropriada para o projeto.
