#  Detector de Palíndromos

![Python](https://img.shields.io/badge/Python-3.x-3776AB?style=flat&logo=python&logoColor=white)
![Status](https://img.shields.io/badge/status-conclu%C3%ADdo-brightgreen)
![License](https://img.shields.io/badge/license-MIT-blue)
![Disciplina](https://img.shields.io/badge/disciplina-Garantia%20da%20Qualidade%20de%20Software-orange)

> Fork da atividade **"Missão README"** — disciplina de Gestão e Qualidade de Software, Prof. Daniel Henrique Matos de Paiva.
> Este repositório documenta, analisa e testa um pequeno algoritmo em Python que verifica se um texto é palíndromo.

---

### O que o código faz?

O script implementa a função `analisar(entrada)`, cujo propósito é **verificar se um texto é um palíndromo** — ou seja, se ele se lê da mesma forma de trás para frente, ignorando espaços, pontuação e diferenças entre maiúsculas/minúsculas.

Frases como *"A sacada da casa de cadasa"* ou *"Socorram-me, subi no ônibus em Marrocos"* são exatamente o tipo de entrada clássica usada para testar esse tipo de lógica, porque à primeira vista *parecem* palíndromos, mas o resultado real depende de como cada caractere é tratado no código — e é aí que mora a pegadinha desta atividade.

### Como executar

O código é escrito em **Python 3** (não Java — não usa `javac`/`java`). Passo a passo:

```bash
# 1. Clone o repositório forkado
git clone https://github.com/SEU-USUARIO/gqs-algoritmo-01-py.git

# 2. Entre na pasta do projeto
cd gqs-algoritmo-01-py

# 3. Certifique-se de ter o Python 3 instalado
python3 --version

# 4. Execute o script
python3 algoritmo.py
```

### Exemplo de saída (resultado real)

```
Teste 1: False
Teste 2: True
```

---

## 🧠 Nível 2 — Engenharia reversa e análise de comportamento

### Desvendando os métodos

**O papel do bloco `if __name__ == "__main__":`**

Em Python, este bloco cumpre o mesmo papel do `main` em Java: é o **ponto de entrada** do programa. O código dentro dele só é executado quando o arquivo é rodado diretamente (`python3 algoritmo.py`) — e não quando o arquivo é importado como módulo em outro script. É aqui que os dois casos de teste (`texto1` e `texto2`) são definidos e impressos no console.

**O que `analisar(entrada)` faz, linha por linha**

```python
def analisar(entrada):
    if entrada is None:
        return False
```
Primeiro, uma validação de segurança: se a entrada for `None` (nenhum texto foi passado), a função já retorna `False` imediatamente, evitando erros nas linhas seguintes.

```python
    limpa = re.sub(r'[^a-zA-Z0-9]', '', entrada).lower()
```
Esta é a linha central do algoritmo. `re.sub(r'[^a-zA-Z0-9]', '', entrada)` usa uma **expressão regular** para varrer o texto e **substituir por vazio (remover)** qualquer caractere que **não** seja letra de `a` a `z`, `A` a `Z` ou número de `0` a `9`. Na prática, isso apaga espaços, vírgulas, hífens e sinais de pontuação. Em seguida, `.lower()` converte tudo para minúsculas, para que a comparação não diferencie "A" de "a". O resultado é armazenado em `limpa`.

⚠️ **Detalhe importante:** o intervalo `a-zA-Z` da expressão regular cobre apenas o alfabeto **ASCII**. Caracteres acentuados como `ã`, `ô`, `é` **não se encaixam nesse intervalo** e, portanto, são tratados como "não permitidos" e **removidos por completo** — em vez de serem convertidos para a letra sem acento (`ô` → `o`). Essa diferença é a chave para entender o resultado dos testes.

```python
    invertida = limpa[::-1]
```
Aqui a string é invertida usando **fatiamento (slicing)** com passo `-1`, uma forma idiomática do Python de percorrer a sequência de trás para frente sem precisar de um `StringBuilder` ou laço explícito (como seria necessário em Java). O resultado é armazenado em `invertida`.

```python
    return limpa == invertida
```
Por fim, a função retorna `True` se a string limpa for **idêntica** à sua versão invertida (ou seja, é um palíndromo) e `False` caso contrário.

### O mistério dos testes

| | Texto original | String após limpeza (`limpa`) | É palíndromo? |
|---|---|---|---|
| **Teste 1** | `A sacada da casa de cadasa` | `asacadadacasadecadasa` | ❌ **False** |
| **Teste 2** | `Socorram-me, subi no ônibus em Marrocos` | `socorrammesubinonibusemmarrocos` | ✅ **True** |

**Por que o Teste 1 deu `False`?**
Depois de remover espaços e converter para minúsculas, a string `asacadadacasadecadasa` **não é** simétrica: comparando `limpa` com `limpa[::-1]`, os caracteres não coincidem posição a posição. A frase foi construída para *parecer* um palíndromo à primeira leitura, mas estruturalmente não é — o algoritmo captura exatamente essa diferença.

**Por que o Teste 2 deu `True`?**
Aqui entra o detalhe sutil da regex explicado acima: a frase contém a palavra "**ô**nibus", com um caractere acentuado. Como `[^a-zA-Z0-9]` não reconhece `ô` como letra válida, ele é **removido inteiramente** do texto (e não substituído por `o`). Isso faz com que "no ônibus" vire `nonibus` em vez de `noonibus`, o que — por coincidência da estrutura da frase — mantém a simetria necessária para o texto ser considerado um palíndromo pelo algoritmo. Ou seja: o resultado `True` é tecnicamente correto **para a forma como o código trata acentos**, mas não necessariamente reflete o palíndromo "ideal" da língua portuguesa (que exigiria tratar `ô` como equivalente a `o`, não descartá-lo).

Essa é a essência da atividade: mostrar que um pequeno detalhe de implementação (regex ASCII-only) pode mudar completamente o resultado de um teste — um exemplo real de por que testes e revisão de código importam em Garantia da Qualidade de Software.

---

## 🎨 Nível 3 — Toque profissional

### Resumo em tabela

| Item | Valor |
|---|---|
| Linguagem | Python 3 |
| Função principal | `analisar(entrada)` |
| Técnica usada | Regex + slicing |
| Complexidade | O(n) |
| Testes automatizados no script | 2 |
| Resultado Teste 1 | `False` |
| Resultado Teste 2 | `True` |

### Trecho de código completo

```python
import re

def analisar(entrada):
    if entrada is None:
        return False

    # Remove tudo que não for letra ou número e converte para minúsculas
    limpa = re.sub(r'[^a-zA-Z0-9]', '', entrada).lower()

    # Inverte a string usando fatiamento (slicing)
    invertida = limpa[::-1]

    return limpa == invertida

if __name__ == "__main__":
    texto1 = "A sacada da casa de cadasa"
    texto2 = "Socorram-me, subi no ônibus em Marrocos"
    print(f"Teste 1: {analisar(texto1)}")
    print(f"Teste 2: {analisar(texto2)}")
```

---

## 👨‍💻 Sobre o autor

Fork, análise técnica e documentação realizados por **[Seu Nome] — RA [seu RA]**, como parte da Lista de Exercícios I da disciplina de Garantia da Qualidade de Software.

📎 Repositório original: [danhpaiva/gqs-algoritmo-01-py](https://github.com/danhpaiva/gqs-algoritmo-01-py)
