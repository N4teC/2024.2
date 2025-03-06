# Docker compose com Django e banco de dados

## Informações gerais

- Assunto: Docker, conteinizar aplicativos
- Disciplina: *sistemas operacionais*
- **Tarefa**:
  1. Criar um projeto django com uma aplicação web
    - alternativa, criar uma _branch_ no repositório do projeto integrador para configurar o acesso ao repositório de dados
  2. Criar um `Dockerfile` para o projeto django
  3. Criar a imagem e testar o conteiner para testar
  4. OPCIONAL, porque dependendo de como pergunta ao assistente de IA; criar um `Dockerfile` para o repositório de banco de dados
  5. Criar um `docker-compose.yml` e configurar para 2 serviços: `webapp` e `db`
  6. Configurar o arquivo django de acesso ao repositório de dados para usar o serviço docker `db`
  7. Testar o `docker-compose.yml`
  8. Relatar minimamente o que foi feito.
- **Entrega**: copia desse aquivo markdown preenchido, no seu repositório _fork_ de https://github.com/sistemas-operacionais/2024.2


## Relatório

### Aluno

- **Nome:** Nathan Cavalcante de Lima  
- **Matrícula:** 20232014040036  

### Relato  
Realizei a integração do Docker no meu projeto do PDS, chamado **Arkheion**.  
Primeiramente, movi o arquivo `requirements.txt` para o diretório do projeto `mysite`. Em seguida, criei os arquivos `Dockerfile` e `docker-compose.yml`. Ao testar a configuração, encontrei um erro relacionado à cópia do arquivo `requirements.txt`. Como o arquivo original continha cerca de 600 a 700 linhas e eu não tinha certeza de quais dependências eram essenciais para o funcionamento da aplicação, consultei o **Deepseek** para ajudar na limpeza do arquivo, mantendo apenas as bibliotecas principais.  

Após a limpeza, executei o comando `docker-compose up --build` para construir e iniciar o contêiner Docker. Utilizei o comando especificado no arquivo `docker-compose.yml`:  
```bash  
bash -c "python manage.py migrate && python manage.py runserver 0.0.0.0:8000"  
