# Tutorial: Criando um Servidor TCP Simples em Python

## Informações gerais
- **Público alvo**: alunos da disciplina de SO (Sistemas Operacionais) do curso de TADS (Superior em Tecnologia em Análise e Desenvolvimento de Sistemas) no CNAT-IFRN (Instituto Federal de Educação, Ciência e Tecnologia do Rio Grande do Norte - Campus Natal-Central).
- disciplina: **SO** Sistemas Operacionais
- professor: [Leonardo A. Minora](https://github.com/leonardo-minora)
- texto gerado pelo [Microsoft Copilot](https://copilot.microsoft.com/)

## Sumário
1. Servidor HTTP sem thread
2. Experimento 1 - usando thread no servidor
2. Experimento 2 - usando containeres

### 1. Servidor HTTP sem thread


### 2. Experimento 1
1. executar o servidor http, código abaixo **sem thread**, subseção 2.1.
2. executar cliente, código abaixo, subseção 2.3.
   1. apenas 1 cliente
   2. 2 clientes simultâneos
   3. 5 clientes simultâneos
   4. 10 cliente simultâneos
3. analizar e explicar o comportamento do cliente e do **servidor sem thread** para cada um dos 4 casos acima
4. parar servidor sem thread e executar o servidor http, código abaixo **com thread**, subseção 2.2.
5. executar cliente, código abaixo, subseção 2.3.
   1. apenas 1 cliente
   2. 2 clientes simultâneos
   3. 5 clientes simultâneos
   4. 10 cliente simultâneos
6. analizar e explicar o comportamento do cliente e do **servidor com thread** para cada um dos 4 casos acima
7. se tiver diferença no funcionamento dos servidores **sem** e **com** threads, analisar a diferença


**data da entrega** 13/02/2025

#### 2.1. código servidor sem thread
```python
import http.server

# Definir o conteúdo HTML fixo
html_fixo = """
<!DOCTYPE html>
<html>
<head>
    <title>Servidor HTTP Multithread</title>
</head>
<body>
    <h1>Olá, mundo!</h1>
    <p>Este é um servidor HTTP multithread em Python.</p>
</body>
</html>
"""

class MeuManipulador(http.server.SimpleHTTPRequestHandler):
    def do_GET(self):
        # Imprimir informações da requisição no console
        print(f"Requisição recebida de: {self.client_address}")
        print(f"Caminho solicitado: {self.path}")
        print("Cabeçalhos da requisição:")
        for nome, valor in self.headers.items():
            print(f"{nome}: {valor}")

        # Enviar resposta de código 200
        self.send_response(200)
        self.send_header("Content-type", "text/html")
        self.end_headers()
        # Enviar o conteúdo HTML fixo
        self.wfile.write(html_fixo.encode("utf-8"))

# Definir o endereço e a porta do servidor
endereco = ("", 8000)

# Criar o servidor
with http.server.HTTPServer(endereco, MeuManipulador) as httpd:
    print("Servidor HTTP rodando na porta 8000...")
    # Manter o servidor rodando
    httpd.serve_forever()

```

**Explicação do Código**

Este código cria um servidor HTTP simples em Python que responde a requisições GET com um conteúdo HTML fixo. Vamos detalhar cada parte do código:

1. Importar o Módulo `import http.server`
  - Fornece classes para criar servidores HTTP.
2. Define uma string `html_fixo` com conteúdo HTML Fixo `html_fixo = """..."""`
3. Criar a Classe Manipuladora de Requisições `class MeuManipulador(http.server.SimpleHTTPRequestHandler): ...`
  - **Classe `MeuManipulador`**: Herda de `http.server.SimpleHTTPRequestHandler` e sobrescreve o método `do_GET` para tratar requisições GET.
  - **Método `do_GET`**:
    - **Imprimir Informações da Requisição**: Imprime no console o endereço IP do cliente (`self.client_address`), o caminho solicitado (`self.path`) e os cabeçalhos da requisição (`self.headers`).
    - **Enviar Resposta de Código 200**: Usa `self.send_response(200)` para enviar uma resposta de código 200 (OK).
    - **Definir o Cabeçalho `Content-type`**: Usa `self.send_header("Content-type", "text/html")` para definir o tipo de conteúdo como HTML.
    - **Finalizar os Cabeçalhos**: Usa `self.end_headers()` para finalizar os cabeçalhos da resposta.
    - **Enviar o Conteúdo HTML Fixo**: Usa `self.wfile.write(html_fixo.encode("utf-8"))` para enviar o conteúdo HTML fixo como resposta.
4. Definir o Endereço e a Porta do Servidor `endereco = ("", 8000)`
   - Define o endereço e a porta do servidor. Neste caso, o servidor escuta em todas as interfaces (`""`) na porta 8000.
5. Criar e Executar o Servidor `with http.server.HTTPServer(endereco, MeuManipulador) as httpd: ...`
   - **Criar o Servidor**: Usa `http.server.HTTPServer(endereco, MeuManipulador)` para criar uma instância do servidor HTTP, passando o endereço e a classe manipuladora.
   - **Bloco `with`**: Garante que o servidor seja fechado corretamente ao final.
   - **Manter o Servidor Rodando**: Usa `httpd.serve_forever()` para manter o servidor rodando indefinidamente, pronto para tratar requisições.


#### 2.2. código servidor com thread
```python
import http.server
import socketserver
from socketserver import ThreadingMixIn

# Definir o conteúdo HTML fixo
html_fixo = """
<!DOCTYPE html>
<html>
<head>
    <title>Servidor HTTP Multithread</title>
</head>
<body>
    <h1>Olá, mundo!</h1>
    <p>Este é um servidor HTTP multithread em Python.</p>
</body>
</html>
"""

class MeuManipulador(http.server.SimpleHTTPRequestHandler):
    def do_GET(self):
        # Imprimir informações da requisição no console
        print(f"Requisição recebida de: {self.client_address}")
        print(f"Caminho solicitado: {self.path}")
        print("Cabeçalhos da requisição:")
        for nome, valor in self.headers.items():
            print(f"{nome}: {valor}")

        # Enviar resposta de código 200
        self.send_response(200)
        self.send_header("Content-type", "text/html")
        self.end_headers()
        # Enviar o conteúdo HTML fixo
        self.wfile.write(html_fixo.encode("utf-8"))

class ThreadingHTTPServer(ThreadingMixIn, http.server.HTTPServer):
    """Servidor HTTP que trata cada requisição em uma nova thread."""
    daemon_threads = True

# Definir o endereço e a porta do servidor
endereco = ("", 8000)

# Criar o servidor
with ThreadingHTTPServer(endereco, MeuManipulador) as httpd:
    print("Servidor HTTP multithread rodando na porta 8000...")
    # Manter o servidor rodando
    httpd.serve_forever()

```

**Explicação do código**

1. **Importar Módulos**:
   - Importamos os módulos `http.server`, `socketserver` e `ThreadingMixIn`.
2. **Definir o Conteúdo HTML Fixo**:
   - Definimos uma string `html_fixo` contendo o HTML que será enviado como resposta.
3. **Criar a Classe Manipuladora**:
   - Criamos uma classe `MeuManipulador` que herda de `http.server.SimpleHTTPRequestHandler`.
   - Sobrescrevemos o método `do_GET` para tratar requisições GET, imprimir informações da requisição no console e enviar o conteúdo HTML fixo.
4. **Criar a Classe do Servidor Multithread**:
   - Criamos uma classe `ThreadingHTTPServer` que herda de `ThreadingMixIn` e `http.server.HTTPServer`.
   - `ThreadingMixIn` permite que cada requisição seja tratada em uma nova thread.
   - `daemon_threads = True` garante que as threads daemon sejam encerradas quando o servidor principal for encerrado.
5. **Definir o Endereço e a Porta do Servidor**:
   - Definimos o endereço e a porta do servidor. Neste caso, o servidor escuta em todas as interfaces (`""`) na porta 8000.
6. **Criar e Executar o Servidor**:
   - Criamos uma instância de `ThreadingHTTPServer` passando o endereço e a classe manipuladora.
   - Usamos um bloco `with` para garantir que o servidor seja fechado corretamente ao final.
   - Chamamos `serve_forever` para manter o servidor rodando e pronto para tratar requisições.


#### 2.3. código cliente
```python
import http.client

def fazer_requisicao_get():
    # Conectar ao servidor localhost na porta 8000
    conexao = http.client.HTTPConnection("localhost", 8000)

    # Fazer a requisição GET
    conexao.request("GET", "/")

    # Obter a resposta
    resposta = conexao.getresponse()

    # Ler o conteúdo da resposta
    conteudo = resposta.read()

    # Imprimir o status e o conteúdo da resposta
    print(f"Status: {resposta.status}")
    print(f"Motivo: {resposta.reason}")
    print("Conteúdo:")
    print(conteudo.decode("utf-8"))

    # Fechar a conexão
    conexao.close()

# Chamar a função para fazer a requisição GET
fazer_requisicao_get()

```

**Explicação do código**

1. **Importar o Módulo `http.client`**:
    - Importamos o módulo `http.client`, que fornece classes para fazer requisições HTTP.

2. **Definir a Função `fazer_requisicao_get`**:
    - Criamos uma função para fazer a requisição GET.

3. **Conectar ao Servidor**:
    - Usamos `http.client.HTTPConnection("localhost", 8000)` para conectar ao servidor `localhost` na porta 8000.

4. **Fazer a Requisição GET**:
    - Usamos `conexao.request("GET", "/")` para fazer a requisição GET para a raiz (`/`) do servidor.

5. **Obter a Resposta**:
    - Usamos `conexao.getresponse()` para obter a resposta do servidor.

6. **Ler o Conteúdo da Resposta**:
    - Usamos `resposta.read()` para ler o conteúdo da resposta.

7. **Imprimir o Status e o Conteúdo da Resposta**:
    - Imprimimos o status (`resposta.status`), o motivo (`resposta.reason`) e o conteúdo da resposta (`conteudo.decode("utf-8")`).

8. **Fechar a Conexão**:
    - Usamos `conexao.close()` para fechar a conexão.


## 3. Análise do Experimento 1:

### 1. **Executar o servidor HTTP sem thread (Subseção 2.1)**

No código, o servidor é criado e o método `serve_forever()` do `HTTPServer` é chamado, que mantém o servidor ouvindo e processando requisições de maneira síncrona, ou seja, uma requisição de cada vez.

### 2. **Executar o cliente (Subseção 2.3)**

Aqui, podemos criar um cliente HTTP simples para fazer as requisições ao servidor. Um exemplo básico de código para o cliente seria o seguinte:

```python
import requests

# URL do servidor
url = "http://localhost:8000"

# Fazer uma requisição GET ao servidor
response = requests.get(url)

# Imprimir a resposta recebida
print("Código de status da resposta:", response.status_code)
print("Conteúdo da resposta:", response.text)
```

Esse código realiza uma requisição GET ao servidor e imprime o código de status e o conteúdo da resposta recebida.

### 3. **Cenários a serem analisados com o servidor sem threads**

#### a) **Apenas 1 cliente**
- O servidor, sem threads, lida com a requisição de apenas um cliente por vez. O servidor vai processar a requisição, enviar a resposta e então aguardar a próxima. Quando um cliente faz a requisição, ele obtém a resposta normalmente, enquanto o servidor fica ocupado com essa requisição.
- O comportamento do servidor será o de atender as requisições uma a uma, sem qualquer simultaneidade.

#### b) **2 clientes simultâneos**
- Quando dois clientes tentam fazer requisições simultaneamente, o servidor irá processá-las de forma sequencial. Isso significa que um cliente terá que aguardar o outro ser atendido, e não haverá simultaneidade no atendimento. Isso pode fazer com que o tempo de resposta de um cliente aumente dependendo de quando ele envia sua requisição.
  
#### c) **5 clientes simultâneos**
- Com cinco clientes simultâneos, o servidor continuará a processar as requisições de forma sequencial, o que pode causar um aumento no tempo de espera para os clientes. O servidor não conseguirá atender mais de um cliente ao mesmo tempo, o que resulta em latência maior com o aumento do número de clientes.

#### d) **10 clientes simultâneos**
- Com dez clientes simultâneos, o servidor ainda irá processar as requisições de forma sequencial, mas a latência será ainda mais evidente. Se o servidor estiver sobrecarregado, ele poderá até mesmo falhar em conseguir atender todos os clientes de maneira eficiente, resultando em tempos de espera muito altos.

### 4. **Parar servidor sem thread e executar servidor com thread (Subseção 2.2)**

O servidor pode ser modificado para usar threads e processar as requisições de forma simultânea. Isso é possível utilizando ThreadingHTTPServer, que cria uma nova thread para cada requisição recebida, permitindo que várias requisições sejam atendidas ao mesmo tempo.

Aqui está o código do servidor com threads:

```python
import http.server
import socketserver
import threading

# Definir o conteúdo HTML fixo
html_fixo = """
<!DOCTYPE html>
<html>
<head>
    <title>Servidor HTTP com Thread</title>
</head>
<body>
    <h1>Olá, mundo!</h1>
    <p>Este é um servidor HTTP com thread em Python.</p>
</body>
</html>
"""

class MeuManipulador(http.server.SimpleHTTPRequestHandler):
    def do_GET(self):
        # Imprimir informações da requisição no console
        print(f"Requisição recebida de: {self.client_address}")
        print(f"Caminho solicitado: {self.path}")
        print("Cabeçalhos da requisição:")
        for nome, valor in self.headers.items():
            print(f"{nome}: {valor}")

        # Enviar resposta de código 200
        self.send_response(200)
        self.send_header("Content-type", "text/html")
        self.end_headers()
        # Enviar o conteúdo HTML fixo
        self.wfile.write(html_fixo.encode("utf-8"))

# Definir o endereço e a porta do servidor
endereco = ("", 8000)

# Criar o servidor com threading
httpd = socketserver.ThreadingTCPServer(endereco, MeuManipulador)
print("Servidor HTTP rodando na porta 8000 com Thread...")
# Manter o servidor rodando
httpd.serve_forever()
```

Neste código, foi utilizado `ThreadingTCPServer`, que cria uma thread para cada requisição.

### 5. **Executar o cliente novamente (Subseção 2.3)**

Com o servidor utilizando threads, o mesmo cliente de antes deve ser executado. O comportamento do cliente permanece o mesmo, mas a forma como o servidor responde a ele é alterada.

### 6. **Cenários a serem analisados com o servidor com threads**

#### a) **Apenas 1 cliente**
- O servidor ainda processa a requisição normalmente, mas com threads, a aplicação é mais eficiente em termos de resposta, porque o servidor pode lidar com novas requisições enquanto ainda processa outras. No entanto, com apenas um cliente, a melhoria de performance não será notada.

#### b) **2 clientes simultâneos**
- Quando dois clientes fazem requisições ao mesmo tempo, o servidor agora pode processar ambas as requisições ao mesmo tempo, pois cada requisição é tratada por uma thread diferente. O tempo de espera para o segundo cliente será reduzido em comparação ao servidor sem threads.

#### c) **5 clientes simultâneos**
- Agora, o servidor pode lidar com múltiplas requisições simultâneas. Cada cliente é atendido em uma thread separada, o que reduz consideravelmente o tempo de espera. O desempenho do servidor com threads é muito melhor, permitindo atender vários clientes simultaneamente sem bloqueios.

#### d) **10 clientes simultâneos**
- Com 10 clientes simultâneos, o servidor com threads será mais eficiente do que o servidor sem threads. O número de threads que o servidor pode criar é muito maior do que a capacidade de processar requisições de forma sequencial, então o tempo de resposta será muito melhor. No entanto, dependendo dos recursos do sistema, o servidor pode começar a ficar lento quando muitas threads estão sendo criadas.

### 7. **Diferença no funcionamento dos servidores sem e com threads**

- **Sem threads**: O servidor processa as requisições de forma sequencial, o que significa que um cliente terá que esperar até que a requisição do cliente anterior seja totalmente processada. Isso pode causar delays significativos conforme o número de clientes aumenta.
  
- **Com threads**: O servidor cria uma nova thread para cada requisição, permitindo que múltiplas requisições sejam atendidas simultaneamente. Isso melhora a performance do servidor e reduz o tempo de espera dos clientes, especialmente quando há múltiplos clientes fazendo requisições ao mesmo tempo.

### Conclusão

Com a introdução de threads, o servidor torna-se mais eficiente ao lidar com múltiplas requisições simultâneas, o que resulta em um melhor desempenho em ambientes de maior carga. O uso de threads é uma forma eficaz de escalabilidade, permitindo que o servidor lide com vários clientes ao mesmo tempo, sem bloquear o processamento de uma requisição enquanto espera a conclusão de outra.


## Links
- [http.server — HTTP servers](https://docs.python.org/3/library/http.server.html)

